---
title: 'Zelfstudie: Een server ontwerpen - Azure PowerShell - Azure Database for MySQL'
description: In deze zelfstudie wordt uitgelegd hoe u een Azure Database for MySQL-server en -database maakt en beheert met PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: fd8294d60ed0af4e8d1eeb8a3cd07c737b69aadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011452"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>Zelfstudie: Een Azure Database for MySQL ontwerpen met PowerShell

Azure Database voor MySQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de MySQL Community Edition database engine. In deze zelfstudie gebruikt u PowerShell en andere hulpprogramma's om het volgende te leren:

> [!div class="checklist"]
> - Een Azure Database voor MySQL maken
> - De serverfirewall configureren
> - Het [opdrachtregelprogramma mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) gebruiken om een database te maken
> - Voorbeeldgegevens laden
> - Querygegevens
> - Gegevens bijwerken
> - Gegevens terugzetten

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell.

> [!IMPORTANT]
> Zo lang de PowerShell-module Az.MySQL in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Zodra de PowerShell-module Az.MySQL algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als dit de eerste keer is dat u de Azure Database for MySQL-service gebruikt, moet u de resourceprovider van **Microsoft.DBforMySQ:** registreren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifieke abonnementen-id met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myresourcegroup** gemaakt in de regio **US – West**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Maak een Azure Database for MySQL-server met de cmdlet `New-AzMySqlServer`. Een server kan meerdere databases beheren. Een aparte database wordt doorgaans gebruikt voor elk project of voor elke gebruiker.

In het volgende voorbeeld wordt een MySQL-server gemaakt in de regio **US - west**. De server heeft de naam **mydemoserver** en bevindt zich in de resourcegroep **myresourcegroup**. De serverbeheerder kan zich aanmelden met **myadmin**. De server is een Gen 5-server in de prijscategorie Algemeen en beschikt over twee vCores. Daarnaast is geografisch redundante back-ups ingeschakeld. Noteer het wachtwoord dat in de eerste regel van het voorbeeld wordt gebruikt, aangezien dit het wachtwoord voor het beheerdersaccount van de MySQL-server is.

> [!TIP]
> Een servernaam wordt toegewezen aan een DNS-naam en moet wereldwijd uniek zijn in Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

De parameterwaarde voor de **SKU** volgt de conventie **prijscategorie\_compute-generatie\_vCores** zoals is te zien in de volgende voorbeelden.

- `-Sku B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
- `-Sku GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
- `-Sku MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Meer informatie over geldige **SKU**-waarden per regio en de categorieën vindt u in [Prijscategorieën voor Azure Database for MySQL](./concepts-pricing-tiers.md).

Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload.

> [!IMPORTANT]
> Servers die zijn gemaakt in de prijscategorie Basic, kunnen later niet meer worden geschaald voor algemeen gebruik of worden geoptimaliseerd voor het geheugen. Daarnaast is het niet mogelijk om geo-replicatie te gebruiken.

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren

Maak een firewallregel op Azure Database for MySQL-serverniveau voor Azure Database met de cmdlet `New-AzMySqlFirewallRule`. Met een firewallregel op serverniveau is een externe toepassing mogelijk, zoals het opdrachtregelprogramma `mysql` of MySQL Workbench om verbinding te maken met uw server via de Azure Database for MySQL-servicefirewall.

In het volgende voorbeeld wordt een firewallregel met de naam **AllowMyIP** gemaakt waarmee verbindingen van een specifiek IP-adres, 192.168.0.1, worden toegestaan. Vervang dit door een IP-adres of een bereik van IP-adressen dat overeenkomt met de locatie van waaruit u verbinding maakt.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dit scenario kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 opent.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven. Gebruik het volgende voorbeeld om de verbindingsgegevens te bepalen. Noteer de waarden voor **FullyQualifiedDomainName** en de **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Verbinding maken met de server met het opdrachtregelprogramma mysql

Maak verbinding met de server met behulp van het `mysql`opdrachtregelprogramma. Zie [MySQL Community Downloads](https://dev.mysql.com/downloads/shell/) als u het opdrachtregelprogramma wilt downloaden en installeren. U kunt ook een vooraf geïnstalleerde versie openen van het `mysql` opdrachtregelhulpprogramma in Azure Cloud Shell door de knop **Proberen** in een codevoorbeeld in dit artikel te selecteren. Andere manieren om toegang te krijgen tot Azure Cloud Shell zijn de selectie van de knop **> _** in de werkbalk rechtsboven in de Azure Portal of een bezoek aan [shell.azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Een database maken

Zodra u met de server bent verbonden, maakt u een lege database.

```sql
mysql> CREATE DATABASE mysampledb;
```

Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database

U weet nu hoe u verbinding kunt maken met de Azure Database for MySQL-database en dus is het tijd om enkele eenvoudige taken uit te voeren.

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen

De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

We hebben nu twee rijen met voorbeeldgegevens in de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken

Voer de volgende query uit om gegevens op te halen uit de databasetabel.

```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt dan meteen bijgewerkt wanneer u gegevens ophaalt.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip

U kunt de server naar een eerder tijdstip herstellen. De herstelde gegevens worden gekopieerd naar een nieuwe server. De bestaande server blijft ongewijzigd. Als een tabel bijvoorbeeld per ongeluk wordt verwijderd, kunt u de server herstellen naar een tijdstip net voordat de tabel werd verwijderd. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server.

Als u de server wilt herstellen, gebruikt u de PowerShell-cmdlet `Restore-AzMySqlServer`.

### <a name="run-the-restore-command"></a>De herstelopdracht uitvoeren

Voer het volgende voorbeeld uit in PowerShell uit om de server te herstellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende databases van het opgegeven tijdstip worden naar de nieuwe server gekopieerd.

De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de oorspronkelijke server.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De aanmeldingsnaam en het wachtwoord voor de nieuwe server zijn hetzelfde als voor de bestaande server op het moment dat de herstelbewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor de nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources uit deze zelfstudie niet voor een andere quickstart of zelfstudie nodig hebt, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van deze zelfstudie in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Als u alleen de server wilt verwijderen die u in deze zelfstudie hebt gemaakt zonder de resourcegroep te verwijderen, gebruikt u de cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een back-up van Azure Database for MySQL-server maken en deze terugzetten met behulp van PowerShell](howto-restore-server-powershell.md)