---
title: Transactionele replicatie tussen Azure SQL Managed Instance en SQL Server configureren
description: Een zelfstudie waarin de replicatie wordt geconfigureerd tussen een beheerd exemplaar voor de uitgever, een beheerd exemplaar voor de distributeur en een SQL Server-abonnee op een Azure-VM, samen met de benodigde netwerkonderdelen, zoals een privé-DNS-zone en VNet-peering.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593923"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Zelfstudie: Transactionele replicatie tussen Azure SQL Managed Instance en SQL Server configureren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Met transactionele replicatie kunt u gegevens van de ene database repliceren naar een andere database die op ofwel SQL Server of op een [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) wordt gehost. Een SQL Managed Instance kan een uitgever, distributeur of abonnee zijn in de replicatietopologie. Zie [configuraties voor transactionele replicatie](replication-transactional-overview.md#common-configurations) voor de beschikbare configuraties. 

Transactionele replicatie is momenteel beschikbaar als openbare preview-versie voor SQL Managed Instance. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een beheerd exemplaar configureren als een uitgever van replicaties.
> - Een beheerd exemplaar configureren als een distributeur van replicaties.
> - SQL Server configureren als een abonnee.

![Replicatie tussen een uitgever voor een beheerde exemplaar, een distributeur voor een beheerd exemplaar en de SQL Server-abonnee](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Deze zelfstudie is bedoeld voor een ervaren doelgroep en er wordt verondersteld dat de gebruiker bekend is met het implementeren van en het verbinding maken met zowel beheerde exemplaren als SQL Server-VM's in Azure. 


> [!NOTE]
> In dit artikel wordt het gebruik van [transactionele replicatie](/sql/relational-databases/replication/transactional/transactional-replication) in Azure SQL Managed Instance beschreven. Dit is niet verwant aan [failovergroepen](../database/auto-failover-group-overview.md), een functie van Azure SQL Managed Instance waarmee u volledige, leesbare replica's van afzonderlijke instanties kunt maken. Er zijn zaken waarmee u extra rekening moet houden wanneer u [transactionele replicatie met failovergroepen](replication-transactional-overview.md#with-failover-groups) configureert.

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Ervaring met het implementeren van twee beheerde exemplaren in hetzelfde virtuele netwerk.
- Een SQL Server-abonnee, on-premises of op een Azure-VM. In deze zelfstudie wordt een Azure-VM gebruikt.  
- [SQL Server Management Studio (SSMS) 18.0 of hoger](/sql/ssms/download-sql-server-management-studio-ssms).
- De nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps).
- Poorten 445 en 1433 staan SQL-verkeer toe voor zowel de Azure-firewall als de Windows-firewall.

## <a name="create-the-resource-group"></a>De resourcegroep maken

Gebruik het volgende PowerShell-codefragment om een nieuwe resourcegroep te maken:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Twee beheerde exemplaren maken

Maak twee beheerde exemplaren in deze nieuwe resourcegroep via [Azure Portal](https://portal.azure.com).

- De naam van het beheerde exemplaar voor de uitgever moet `sql-mi-publisher` zijn (aangevuld met een aantal tekens voor randomisering) en de naam van het virtuele netwerk moet `vnet-sql-mi-publisher` zijn.
- De naam van het beheerde exemplaar voor de distributeur moet `sql-mi-distributor` zijn (aangevuld met een aantal tekens voor randomisering) en het moet zich _in hetzelfde virtuele netwerk als het beheerde exemplaar voor de uitgever_ bevinden.

   ![Het VNet van de uitgever gebruiken voor de distributeur](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Zie [Een beheerd exemplaar maken in de portal](instance-create-quickstart.md) voor meer informatie over het maken van een beheerd exemplaar.

  > [!NOTE]
  > Omdat het eenvoudig en de meest voorkomende configuratie is, wordt in deze zelfstudie geadviseerd om het beheerde exemplaar voor de distributeur in hetzelfde virtuele netwerk als de uitgever te plaatsen. U kunt de distributeur ook in een afzonderlijk virtueel netwerk maken. Hiervoor moet u VNet-peering configureren tussen de virtuele netwerken van de uitgever en de distributeur en vervolgens VNet-peering configureren tussen de virtuele netwerken van de distributeur en de abonnee.

## <a name="create-a-sql-server-vm"></a>Een SQL Server-VM maken

Maak een SQL Server-VM via [Azure Portal](https://portal.azure.com). De SQL Server-VM moet de volgende kenmerken hebben:

- Naam: `sql-vm-sub`
- Installatiekopie: SQL Server 2016 of hoger
- Resourcegroep: hetzelfde als het beheerde exemplaar
- Virtueel netwerk: `sql-vm-sub-vnet`

Zie voor meer informatie over het implementeren van een SQL Server-VM in Azure [Quickstart: Een SQL Server-VM maken](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>VNet-peering configureren

Configureer VNet-peering om communicatie mogelijk te maken tussen het virtuele netwerk van de twee beheerde exemplaren en het virtuele netwerk van SQL Server. Gebruik hiervoor het volgende PowerShell-codefragment:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Wanneer VNet-peering tot stand is gebracht, test u de verbinding door SQL Server Management Studio (SSMS) in SQL Server te starten en verbinding te maken met beide beheerde exemplaren. Zie [SSMS gebruiken om verbinding te maken met SQL Managed Instance](point-to-site-p2s-configure.md#connect-with-ssms) voor meer informatie over het verbinding maken met een beheerd exemplaar met behulp van SSMS.

![De verbinding met de beheerde exemplaren testen](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

Met een privé-DNS-zone is DNS-routering tussen de beheerde exemplaren en SQL Server mogelijk.

### <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken** om een nieuwe Azure-resource te maken.
1. Zoek naar `private dns zone` op Azure Marketplace.
1. Kies de resource **Privé-DNS-zone** die door Microsoft wordt gepubliceerd en selecteer **Maken** om de DNS-zone te maken.
1. Kies in de vervolgkeuzelijsten het abonnement en de resourcegroep.
1. Geef een willekeurige naam op voor uw DNS-zone, zoals `repldns.com`.

   ![Privé-DNS-zone maken](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Selecteer **Controleren + maken**. Controleer de parameters voor uw privé-DNS-zone en selecteer vervolgens **Maken** om uw resource te maken.

### <a name="create-an-a-record"></a>Een A-record maken

1. Ga naar uw nieuwe **privé-DNS-zone** en selecteer **Overzicht**.
1. Selecteer **+ Recordset** om een nieuwe A-record te maken.
1. Geef de naam van uw SQL Server-VM en het interne privé IP-adres op.

   ![Een A-record configureren](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Selecteer **OK** om de A-record te maken.

### <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

1. Ga naar uw nieuwe **privé-DNS-zone** en selecteer **Koppelingen voor het virtuele netwerk**.
1. Selecteer **+ Toevoegen**.
1. Geef een naam op voor de koppeling, zoals `Pub-link`.
1. Selecteer uw abonnement in de vervolgkeuzelijst en selecteer vervolgens het virtuele netwerk voor het beheerde exemplaar voor de uitgever.
1. Schakel het selectievakje naast **Automatische registratie inschakelen** in.

   ![VNet-koppeling maken](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Selecteer **OK** om het virtuele netwerk te koppelen.
1. Herhaal deze stappen om een koppeling toe te voegen voor het virtuele netwerk van de abonnee, met een naam als `Sub-link`.

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

[Maak een Azure-opslagaccount](../../storage/common/storage-account-create.md#create-a-storage-account) voor de werkmap en maak vervolgens een [bestandsshare](../../storage/files/storage-how-to-create-file-share.md) in het opslagaccount.

Kopieer het pad naar de bestandsshare in de volgende notatie: `\\storage-account-name.file.core.windows.net\file-share-name`

Voorbeeld: `\\replstorage.file.core.windows.net\replshare`

Kopieer de verbindingstekenreeks voor de toegangssleutel voor de opslag in de volgende notatie: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Voorbeeld: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Zie [Toegangssleutels voor een opslagaccount beheren](../../storage/common/storage-account-keys-manage.md) voor meer informatie.

## <a name="create-a-database"></a>Een database maken

Maak een nieuwe database voor het beheerde exemplaar voor de uitgever. Voer hiervoor de volgende stappen uit:

1. Start SQL Server Management Studio in SQL Server.
1. Maak verbinding met het beheerde exemplaar `sql-mi-publisher`.
1. Open het venster **Nieuwe query** en voer de volgende T-SQL-query uit om de database te maken.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Distributie configureren

Wanneer de verbinding tot stand is gebracht en u over een voorbeelddatabase beschikt, kunt u de distributie voor het beheerde exemplaar `sql-mi-distributor` configureren. Voer hiervoor de volgende stappen uit:

1. Start SQL Server Management Studio in SQL Server.
1. Maak verbinding met het beheerde exemplaar `sql-mi-distributor`.
1. Open het venster **Nieuwe query** en voer de volgende Transact-SQL-code uit om de distributie te configureren voor het beheerde exemplaar voor de distributeur:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Zorg ervoor dat u alleen backslashes (`\`) gebruikt voor de parameter @working_directory. Het gebruik van een slash (`/`) kan een fout veroorzaken wanneer er verbinding wordt gemaakt met de bestandsshare.

1. Maak verbinding met het beheerde exemplaar `sql-mi-publisher`.
1. Open het venster **Nieuwe query** en voer de volgende Transact-SQL-code uit om de distributeur te registreren bij de uitgever:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>De publicatie maken

Wanneer de distributie is geconfigureerd, kunt u de publicatie maken. Voer hiervoor de volgende stappen uit:

1. Start SQL Server Management Studio in SQL Server.
1. Maak verbinding met het beheerde exemplaar `sql-mi-publisher`.
1. Vouw in **Objectverkenner** het knooppunt **Replicatie** uit en klik met de rechtermuisknop op de map **Lokale publicatie**. Selecteer **Nieuwe publicatie...** .
1. Selecteer **Volgende** om vanaf de welkomstpagina verder te gaan.
1. Selecteer op de pagina **Publicatiedatabase** de database `ReplTutorial` die u eerder hebt gemaakt. Selecteer **Next**.
1. Selecteer op de pagina **Publicatietype** de optie **Transactionele publicatie**. Selecteer **Next**.
1. Schakel op de pagina **Artikelen** het selectievakje naast **Tabellen** in. Selecteer **Next**.
1. Op de pagina **Tabelrijen filteren** selecteert u **Volgende** zonder filters toe te voegen.
1. Schakel op de pagina **Momentopname-agent** het selectievakje in naast **Onmiddellijk momentopname maken en de momentopname beschikbaar houden voor het initialiseren van abonnementen**. Selecteer **Next**.
1. Selecteer op de pagina **Agentbeveiliging** de optie **Beveiligingsinstellingen...** . Geef de SQL Server-aanmeldingsgegevens op die moeten worden gebruikt voor de momentopname-agent en om verbinding te maken met de uitgever. Selecteer **OK** om de pagina **Beveiliging momentopname-agent** te sluiten. Selecteer **Next**.

   ![Beveiliging van de momentopname-agent configureren](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Kies op de pagina **Wizard-acties** de optie **De publicatie maken** en (optioneel) de optie **Een scriptbestand genereren met stappen voor het maken van de publicatie** als u dit script voor later gebruik wilt opslaan.
1. Geef op de pagina **De wizard voltooien** de naam `ReplTest` op voor uw publicatie en selecteer **Volgende** om uw publicatie te maken.
1. Wanneer uw publicatie is gemaakt, vernieuwt u het knooppunt **Replicatie** in **Objectverkenner** en vouwt u **Lokale publicaties** uit om uw nieuwe publicatie te bekijken.

## <a name="create-the-subscription"></a>Het abonnement maken

Wanneer de publicatie is gemaakt, kunt u het abonnement maken. Voer hiervoor de volgende stappen uit:

1. Start SQL Server Management Studio in SQL Server.
1. Maak verbinding met het beheerde exemplaar `sql-mi-publisher`.
1. Open het venster **Nieuwe query** en voer de volgende Transact-SQL-code uit om het abonnement en de distributieagent toe te voegen. Gebruik het DNS als onderdeel van de abonneenaam.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Replicatie testen

Wanneer de replicatie eenmaal is geconfigureerd, kunt u deze testen door nieuwe items in te voegen in de instantie voor de uitgever en te bekijken hoe de wijzigingen worden doorgegeven aan de abonnee.

Voer het volgende T-SQL-codefragment uit om de rijen in de instantie voor de abonnee weer te geven:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Voer het volgende T-SQL-codefragment uit om extra rijen in te voegen in de instantie voor de uitgever en bekijk vervolgens de rijen weer in de instantie voor de abonnee.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Resources opschonen

1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw resourcegroep.
1. Selecteer het beheerde exemplaar of de beheerde exemplaren en selecteer vervolgens **Verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. Het kan enige tijd duren voordat dit proces op de achtergrond is voltooid. Zolang het nog niet is voltooid, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer de verwijdering op het tabblad **Activiteit** om na te gaan of uw beheerde exemplaar is verwijderd.
1. Wanneer het beheerde exemplaar is verwijderd, verwijdert u het *virtuele cluster* door dit te selecteren in uw resourcegroep en vervolgens **Verwijderen** te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**.
1. Verwijder eventuele andere resources. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**.
1. Verwijder de resourcegroep door **Resourcegroep verwijderen** te selecteren, de naam van de resourcegroep, `myResourceGroup`, in te voeren en vervolgens **Verwijderen** te selecteren.

## <a name="known-errors"></a>Bekende fouten

### <a name="windows-logins-are-not-supported"></a>Windows-aanmeldingen worden niet ondersteund

`Exception Message: Windows logins are not supported in this version of SQL Server.`

De agent is geconfigureerd met een Windows-aanmelding, maar moet gebruikmaken van een SQL Server-aanmelding. Ga naar de pagina **Agentbeveiliging** van het dialoogvenster **Publicatie-eigenschappen** om de aanmeldingsgegevens te wijzigen in een SQL Server-aanmelding.

### <a name="failed-to-connect-to-azure-storage"></a>Verbinding maken met Azure Storage is mislukt

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Obtained Azure Storage Connection String for replstorage 2019-11-19 02:21:05.07 Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Failed to connect to Azure Storage '' with OS error: 53.

Dit komt waarschijnlijk doordat poort 445 in de Azure-firewall, de Windows-firewall of beide is gesloten.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Het gebruik van een slash in plaats van een backslash in het bestandspad voor de bestandsshare kan deze fout veroorzaken.
  
  - Dit is de juiste notatie: `\\replstorage.file.core.windows.net\replshare`
  - Dit kan een OS 55-fout veroorzaken: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Er kan geen verbinding worden gemaakt met abonnee

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Mogelijke oplossingen:

- Zorg ervoor dat poort 1433 is geopend.
- Zorg ervoor dat TCP/IP is ingeschakeld voor de abonnee.
- Controleer of de DNS-naam is gebruikt bij het maken van de abonnee.
- Controleer of de virtuele netwerken juist zijn gekoppeld in de privé-DNS-zone.
- Controleer of de A-record juist is geconfigureerd.
- Controleer of de VNet-peering juist is geconfigureerd.

### <a name="no-publications-to-which-you-can-subscribe"></a>Er zijn geen publicaties waarop u zich kunt abonneren

Wanneer u een nieuw abonnement toevoegt met de wizard **Nieuw abonnement**, op de pagina **Publicatie**, worden er mogelijk geen databases en publicaties als beschikbare opties vermeld en wordt mogelijk het volgende foutbericht weergegeven:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Het is mogelijk dat dit foutbericht klopt en dat er echt geen publicaties beschikbaar zijn in de instantie voor de uitgever waarmee u verbinding hebt gemaakt of dat u niet over voldoende machtigingen beschikt. Deze fout kan echter ook worden veroorzaakt door een oudere versie van SQL Server Management Studio. Voer een upgrade uit naar SQL Server Management Studio 18.0 of hoger om dit als hoofdoorzaak uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

### <a name="enable-security-features"></a>Beveiligingsfuncties inschakelen

Raadpleeg het artikel [Wat is Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) voor een uitgebreide lijst met manieren waarop u uw database kunt beveiligen. De volgende beveiligingsfuncties worden besproken:

- [SQL Managed Instance-controle](auditing-configure.md)
- [Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detectie van bedreigingen](threat-detection-configure.md)
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking)
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)
- [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Mogelijkheden van SQL Managed Instance

Een volledig overzicht met mogelijkheden voor beheerde exemplaren vindt u hier:

> [!div class="nextstepaction"]
> [Mogelijkheden van SQL Managed Instance](sql-managed-instance-paas-overview.md)