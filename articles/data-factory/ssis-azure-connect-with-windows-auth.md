---
title: Toegang tot gegevens archieven en bestands shares met Windows-verificatie
description: Meer informatie over het configureren van de SSIS-catalogus in Azure SQL Database en Azure-SSIS Integration Runtime in Azure Data Factory voor het uitvoeren van pakketten die toegang hebben tot gegevens archieven en bestands shares met Windows-verificatie.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 77283596e4b0d7e25d757c9de76c284ca4f5d4f2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556389"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Toegang tot gegevensarchieven en bestandsshares met Windows-verificatie van SSIS-pakketten in Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

U kunt Windows-verificatie gebruiken voor toegang tot gegevens archieven, zoals SQL-servers, bestands shares, Azure Files, enzovoort, van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). Uw gegevens archieven kunnen zich op locatie bevinden, worden gehost op Azure-Virtual Machines (Vm's) of worden uitgevoerd in azure als beheerde services. Als ze on-premises zijn, moet u uw Azure-SSIS IR lid maken van een Virtual Network (Microsoft Azure Virtual Network) die is verbonden met uw on-premises netwerk. Zie [Azure-SSIS IR toevoegen aan een Microsoft Azure Virtual Network voor](./join-azure-ssis-integration-runtime-virtual-network.md)meer informatie. Er zijn vier methoden om toegang te krijgen tot gegevens archieven met Windows-verificatie vanaf SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR:

| Verbindingsmethode | Effectief bereik | Setup-stap | Toegangs methode in pakketten | Aantal referentie sets en verbonden resources | Type van verbonden resources | 
|---|---|---|---|---|---|
| Een uitvoerings context op activiteit niveau instellen | Per uitvoering van SSIS-pakket activiteit | Configureer de **Windows-verificatie** -eigenschap voor het instellen van een ' uitvoering/uitvoeren als-context wanneer SSIS-pakketten worden uitgevoerd als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen.<br/><br/> Zie [Configure SSIS package-activiteit](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie. | Toegang tot bronnen rechtstreeks in pakketten, bijvoorbeeld gebruik van UNC-pad voor toegang tot bestands shares of Azure Files: `\\YourFileShareServerName\YourFolderName` of `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Ondersteuning voor slechts één referentieset voor alle verbonden resources | -Bestands shares op locatie/Azure Vm's<br/><br/> -Azure Files, Zie [een Azure-bestands share gebruiken](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-servers on-premises/Azure-Vm's met Windows-verificatie<br/><br/> -Andere bronnen met Windows-verificatie |
| Een uitvoerings context op catalogus niveau instellen | Per Azure-SSIS IR, maar wordt overschreven bij het instellen van een uitvoerings context op activiteit niveau (zie hierboven) | Voer `catalog.set_execution_credential` de opgeslagen procedure SSISDB uit om een context voor uitvoeren/uitvoeren als in te stellen.<br/><br/> Zie de rest van dit artikel voor meer informatie. | Toegang tot bronnen rechtstreeks in pakketten, bijvoorbeeld gebruik van UNC-pad voor toegang tot bestands shares of Azure Files: `\\YourFileShareServerName\YourFolderName` of `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Ondersteuning voor slechts één referentieset voor alle verbonden resources | -Bestands shares op locatie/Azure Vm's<br/><br/> -Azure Files, Zie [een Azure-bestands share gebruiken](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-servers on-premises/Azure-Vm's met Windows-verificatie<br/><br/> -Andere bronnen met Windows-verificatie |
| Referenties blijven behouden via de `cmdkey` opdracht | Per Azure-SSIS IR, maar wordt overschreven bij het instellen van een activiteit/uitvoerings context op catalogus niveau (zie hierboven) | `cmdkey`Opdracht uitvoeren in een aangepast installatie script ( `main.cmd` ) bij het inrichten van uw Azure-SSIS IR, bijvoorbeeld als u bestands shares, Azure Files of SQL Server gebruikt:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`, of<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Zie [Customize setup for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie. | Toegang tot bronnen rechtstreeks in pakketten, bijvoorbeeld gebruik van UNC-pad voor toegang tot bestands shares of Azure Files: `\\YourFileShareServerName\YourFolderName` of `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Ondersteuning voor meerdere referentie sets voor verschillende verbonden bronnen | -Bestands shares op locatie/Azure Vm's<br/><br/> -Azure Files, Zie [een Azure-bestands share gebruiken](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -SQL-servers on-premises/Azure-Vm's met Windows-verificatie<br/><br/> -Andere bronnen met Windows-verificatie |
| Schijven koppelen aan de uitvoerings tijd van het pakket (niet-persistent) | Per pakket | `net use`Opdracht uitvoeren in taak uitvoeren proces die aan het begin van de controle stroom in uw pakketten wordt toegevoegd, bijvoorbeeld`net use D: \\YourFileShareServerName\YourFolderName` | Toegang tot bestands shares via toegewezen stations | Ondersteuning voor meerdere stations voor verschillende bestands shares | -Bestands shares op locatie/Azure Vm's<br/><br/> -Azure Files, Zie [een Azure-bestands share gebruiken](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Als u geen van de bovenstaande methoden gebruikt om toegang te krijgen tot gegevens archieven met Windows-verificatie, hebben uw pakketten die afhankelijk zijn van Windows-authenticatie, geen toegang tot ze en kunnen ze niet worden uitgevoerd tijdens runtime. 

In de rest van dit artikel wordt beschreven hoe u de SSIS-catalogus (SSISDB) die wordt gehost in SQL Database/SQL Managed Instance kunt configureren voor het uitvoeren van pakketten op Azure-SSIS IR die gebruikmaken van Windows-verificatie voor toegang tot gegevens archieven. 

## <a name="you-can-only-use-one-set-of-credentials"></a>U kunt slechts één set met referenties gebruiken

Wanneer u Windows-verificatie gebruikt in een SSIS-pakket, kunt u slechts één set met referenties gebruiken. De domein referenties die u opgeeft wanneer u de stappen in dit artikel uitvoert, zijn van toepassing op alle pakket uitvoeringen: interactief of gepland op uw Azure-SSIS IR totdat u ze wijzigt of verwijdert. Als uw pakket verbinding moet maken met meerdere gegevens archieven met verschillende sets met referenties, moet u rekening houden met de bovenstaande alternatieve methoden.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Domein referenties opgeven voor Windows-verificatie

Ga als volgt te werk om domein referenties op te geven waarmee pakketten Windows-verificatie kunnen gebruiken voor toegang tot gegevens archieven op locatie:

1. Met SQL Server Management Studio (SSMS) of een ander hulp programma kunt u verbinding maken met SQL Database/SQL Managed instance die als host fungeert voor SSISDB. Zie [verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een query venster met SSISDB als de huidige data base.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domein referenties op:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Voer uw SSIS-pakketten uit. De pakketten gebruiken de referenties die u hebt gegeven om toegang te krijgen tot gegevens archieven op locatie met Windows-verificatie.

### <a name="view-domain-credentials"></a>Domein referenties weer geven

Ga als volgt te werk om de referenties van het actieve domein weer te geven:

1. Met SSMS of een ander hulp programma kunt u verbinding maken met SQL Database/SQL Managed instance die als host fungeert voor SSISDB. Zie [verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een query venster met SSISDB als de huidige data base.

3. Voer de volgende opgeslagen procedure uit en controleer de uitvoer:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Domein referenties wissen
Ga als volgt te werk om de referenties die u hebt gegeven, zoals beschreven in dit artikel, te wissen en te verwijderen:

1. Met SSMS of een ander hulp programma kunt u verbinding maken met SQL Database/SQL Managed instance die als host fungeert voor SSISDB. Zie [verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een query venster met SSISDB als de huidige data base.

3. Voer de volgende opgeslagen procedure uit:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Verbinding maken met een on-premises SQL Server

Ga als volgt te werk om te controleren of u verbinding kunt maken met een SQL Server op locatie:

1. Als u deze test wilt uitvoeren, zoekt u een computer die niet lid is van het domein.

2. Voer op de computer die niet lid is van het domein de volgende opdracht uit om SSMS te starten met de domein referenties die u wilt gebruiken:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Controleer vanuit SSMS of u verbinding kunt maken met de SQL Server on-premises.

### <a name="prerequisites"></a>Vereisten

Ga als volgt te werk om toegang te krijgen tot een SQL Server op locatie van pakketten die worden uitgevoerd in Azure:

1.  Schakel in SQL Server Configuration Manager het TCP/IP-protocol in.

2. Toegang via Windows Firewall toestaan. Zie [Windows Firewall configureren voor toegang tot SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)voor meer informatie.

3. Voeg uw Azure-SSIS IR toe aan een Microsoft Azure Virtual Network dat is verbonden met de SQL Server on-premises.  Zie [Azure-SSIS IR toevoegen aan een Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

4. Gebruik `catalog.set_execution_credential` de opgeslagen procedure SSISDB om referenties op te geven zoals beschreven in dit artikel.

## <a name="connect-to-a-file-share-on-premises"></a>Verbinding maken met een bestands share op locatie

Ga als volgt te werk om te controleren of u verbinding kunt maken met een bestands share op locatie:

1. Als u deze test wilt uitvoeren, zoekt u een computer die niet lid is van het domein.

2. Voer de volgende opdrachten uit op de computer die niet lid is van het domein. Met deze opdrachten opent u een opdracht prompt venster met de domein referenties die u wilt gebruiken en test u de verbinding met de bestands share op locatie door een mapweergave op te halen.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Controleer of de mapweergave voor de bestands share on-premises wordt geretourneerd.

### <a name="prerequisites"></a>Vereisten

Ga als volgt te werk om toegang te krijgen tot een bestands share op locatie van pakketten die worden uitgevoerd in Azure:

1. Toegang via Windows Firewall toestaan.

2. Voeg uw Azure-SSIS IR toe aan een Microsoft Azure Virtual Network dat is verbonden met de bestands share on-premises.  Zie [Azure-SSIS IR toevoegen aan een Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

3. Gebruik `catalog.set_execution_credential` de opgeslagen procedure SSISDB om referenties op te geven zoals beschreven in dit artikel.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Verbinding maken met een bestands share op een virtuele Azure-machine

Ga als volgt te werk om toegang te krijgen tot een bestands share op Azure VM vanuit pakketten die worden uitgevoerd in Azure:

1. Met SSMS of een ander hulp programma kunt u verbinding maken met SQL Database/SQL Managed instance die als host fungeert voor SSISDB. Zie [verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een query venster met SSISDB als de huidige data base.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domein referenties op:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Verbinding maken met een bestands share in Azure Files

Zie [Azure files](https://azure.microsoft.com/services/storage/files/)voor meer informatie over Azure files.

Ga als volgt te werk om toegang te krijgen tot een bestands share in Azure Files van pakketten die worden uitgevoerd in Azure:

1. Met SSMS of een ander hulp programma kunt u verbinding maken met SQL Database/SQL Managed instance die als host fungeert voor SSISDB. Zie [verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een query venster met SSISDB als de huidige data base.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domein referenties op:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Volgende stappen

- Implementeer uw pakketten. Zie [een SSIS-project implementeren in azure met SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)voor meer informatie.
- Voer uw pakketten uit. Zie [SSIS-pakketten uitvoeren in azure met SSMS](/sql/integration-services/ssis-quickstart-run-ssms)voor meer informatie.
- Plan uw pakketten. Zie [SSIS-pakketten plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)voor meer informatie.
