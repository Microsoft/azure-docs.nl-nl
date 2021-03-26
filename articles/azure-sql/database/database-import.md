---
title: Een BACPAC-bestand importeren om een database in Azure SQL Database te maken
description: Maak een nieuwe database in Azure SQL Database of Azure SQL Managed Instance vanuit een BACPAC-bestand.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: b88c7aabe135b8e64869980b806852d92191440b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568678"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Een BACPAC-bestand importeren in een database in Azure SQL Database of Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

U kunt een SQL Server-database importeren in Azure SQL Database of SQL Managed Instance met behulp van een [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-bestand. U kunt de gegevens importeren uit een BACPAC-bestand dat is opgeslagen in Azure Blob-opslag (alleen standaardopslag), of vanuit een lokale opslag op een on-premises locatie. Als u de snelheid van het importeren wilt maximaliseren door meer en snellere resources te bieden, schaalt u de database tijdens het importproces naar een hogere servicelaag en een grotere rekenkracht. U kunt vervolgens omlaag schalen nadat het importeren is voltooid.

> [!NOTE]
> Het compatibiliteitsniveau van de geïmporteerde database is gebaseerd op het compatibiliteitsniveau van de brondatabase.

> [!IMPORTANT]
> Nadat u de database hebt geïmporteerd, kunt u ervoor kiezen om de database te gebruiken op het huidige compatibiliteitsniveau (niveau 100 voor de AdventureWorks2008R2-database) of op een hoger niveau. Zie [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Compatibiliteitsniveau ALTER DATABASE) voor meer informatie over de implicaties en opties bij het hanteren van een database op een bepaald compatibiliteitsniveau. Zie ook [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) voor informatie over aanvullende instellingen op databaseniveau in verband met compatibiliteitsniveaus.

## <a name="using-azure-portal"></a>Azure Portal gebruiken

Bekijk deze video om te zien hoe u in Azure Portal vanuit een BACPAC-bestand kunt importeren of ga hieronder door met lezen:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) biedt *alleen* ondersteuning voor het maken van één database in Azure SQL Database en *alleen* vanuit een BACPAC-bestand dat is opgeslagen in Azure Blob Storage.

Als u een database wilt migreren naar een [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) vanuit een BACPAC-bestand, gebruikt u SQL Server Management Studio of SQLPackage. Het gebruik van Azure Portal of Azure PowerShell wordt momenteel niet ondersteund.

> [!NOTE]
> Op machines waarmee import-/exportaanvragen worden verwerkt die zijn verzonden via Azure Portal of PowerShell, moet het BACPAC-bestand worden opgeslagen, evenals tijdelijke bestanden die worden gegenereerd door Data-Tier Application Framework (DacFX). De benodigde schijfruimte varieert aanzienlijk tussen databases met dezelfde grootte. Er kan tot drie keer zoveel schijfruimte nodig zijn als de grootte van de database. Machines waarmee de import-/exportaanvraag wordt uitgevoerd, hebben slechts 450 GB lokale schijfruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout `There is not enough space on the disk`. Dit kan worden opgelost door sqlpackage.exe uit te voeren op een machine met voldoende lokale schijfruimte. Om dit probleem te voorkomen wordt u aangeraden SqlPackage te gebruiken om databases te importeren/exporteren die groter zijn dan 150 GB.

1. Als u vanuit een BACPAC-bestand wilt importeren in één nieuwe database met behulp van Azure Portal, opent u de juiste serverpagina en selecteert u vervolgens op de werkbalk **Database importeren**.  

   ![Database-import1](./media/database-import/sql-server-import-database.png)

1. Selecteer het opslagaccount en de container voor het BACPAC-bestand en selecteer vervolgens het BACPAC-bestand waaruit u wilt importeren.

1. Geef de grootte van de nieuwe database op (meestal hetzelfde als die van de oorspronkelijke) en geef de doel-SQL Server-referenties op. Zie [Database maken](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) voor een lijst met mogelijke waarden voor een nieuwe database in Azure SQL Database.

   ![Database-import2](./media/database-import/sql-server-import-database-settings.png)

1. Klik op **OK**.

1. Als u de voortgang van het importeren wilt volgen, opent u de serverpagina van de database en selecteert u bij **Instellingen** de optie **Geschiedenis van importeren/exporteren**. Wanneer het importeren is gelukt, wordt de status **Voltooid** weergegeven.

   ![Status van database importeren](./media/database-import/sql-server-import-database-history.png)

1. Als u wilt controleren of de database live is op de server, selecteert u **SQL-databases** en controleert u of de nieuwe database **online** is.

## <a name="using-sqlpackage"></a>SqlPackage gebruiken

Voor het importeren van een SQL Server-database met behulp van het opdrachtregelprogramma [SqlPackage](/sql/tools/sqlpackage) raadpleegt u [importparameters en -eigenschappen](/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools voor Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) bevatten SqlPackage. U kunt de nieuwste [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) ook downloaden uit het Microsoft Downloadcentrum. 

Voor schaalbaarheid en prestaties wordt u aangeraden in de meeste productieomgevingen SqlPackage te gebruiken in plaats van Azure Portal. Ga voor een blogartikel van het SQL Server-klantadviesteam over migratie met behulp van `BACPAC`-bestanden naar [migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Het op DTU gebaseerde inrichtingsmodel ondersteunt de waarden voor geselecteerde databases met een maximale grootte voor elke laag. Bij het importeren van een database [moet u een van deze ondersteunde waarden gebruiken](/sql/t-sql/statements/create-database-transact-sql). 

Met de volgende SqlPackage-opdracht wordt de **AdventureWorks2008R2**-database uit de lokale opslag in een logische SQL-server met de naam **mynewserver20170403** geïmporteerd. Er wordt een nieuwe database gemaakt met de naam **myMigratedDatabase** met een **Premium**-servicelaag en een **P6** Service Objective. Wijzig deze waarden in waarden die geschikt zijn voor uw omgeving.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om verbinding te maken met Azure SQL Database achter een firewall van het bedrijf, moet de firewall voor poort 1433 zijn geopend. Als u verbinding wilt maken met SQL Managed Instance, moet u een [punt-naar-site-verbinding](../managed-instance/point-to-site-p2s-configure.md) of een ExpressRoute-verbinding hebben.

In dit voorbeeld ziet u hoe u een database importeert met behulp van SqlPackage met Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell gebruiken

> [!NOTE]
> [Een SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) biedt momenteel geen ondersteuning voor het migreren van een database naar een instantiedatabase vanuit een BACPAC-bestand met behulp van Azure PowerShell. Als u in een SQL Managed Instance wilt importeren, gebruikt u SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Op de machines waarmee import-/exportaanvragen worden verwerkt die zijn verzonden via de portal of PowerShell, moet het BACPAC-bestand worden opgeslagen, evenals tijdelijke bestanden die worden gegenereerd door Data-Tier Application Framework (DacFX). De benodigde schijfruimte varieert aanzienlijk tussen databases met dezelfde grootte. Er kan tot drie keer zoveel schijfruimte nodig zijn als de grootte van de database. Machines waarmee de import-/exportaanvraag wordt uitgevoerd, hebben slechts 450 GB lokale schijfruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout 'Er is niet voldoende ruimte beschikbaar op de schijf.'. Dit kan worden opgelost door sqlpackage.exe uit te voeren op een machine met voldoende lokale schijfruimte. Bij het importeren/exporteren van databases die groter zijn dan 150 GB, gebruikt u SqlPackage om dit probleem te voorkomen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager (RM) wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

Gebruik de [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)-cmdlet om een aanvraag voor het importeren van een database naar Azure te verzenden. Afhankelijk van de grootte van de database kan het enige tijd duren voordat de importbewerking is voltooid. Het op DTU gebaseerde inrichtingsmodel ondersteunt de waarden voor geselecteerde databases met een maximale grootte voor elke laag. Bij het importeren van een database [moet u een van deze ondersteunde waarden gebruiken](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

U kunt de [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus)-cmdlet gebruiken om de voortgang van het importeren te controleren. Als de cmdlet direct na de aanvraag wordt uitgevoerd, wordt er meestal `Status: InProgress` geretourneerd. Het importeren is voltooid wanneer `Status: Succeeded` wordt weergegeven.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) om een aanvraag voor het importeren van een database te verzenden naar Azure. Afhankelijk van de grootte van de database kan het enige tijd duren voordat de importbewerking is voltooid. Het op DTU gebaseerde inrichtingsmodel ondersteunt de waarden voor geselecteerde databases met een maximale grootte voor elke laag. Bij het importeren van een database [moet u een van deze ondersteunde waarden gebruiken](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Zie [Database uit een BACPAC-bestand importeren](scripts/import-from-bacpac-powershell.md) voor een ander scriptvoorbeeld.

## <a name="cancel-the-import-request"></a>De import aanvraag annuleren

Gebruik de [API data base-annuleren](/rest/api/sql/databaseoperations/cancel) of de Power shell [-opdracht stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity). Hier volgt een voor beeld van een Power shell-opdracht.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Beperkingen

- Importeren naar een database in een elastische pool wordt niet ondersteund. U kunt gegevens importeren in één database en deze database vervolgens verplaatsen naar een elastische pool.
- Import Export Service werkt niet wanneer Toegang tot Azure-services toestaan is ingesteld op UIT. U kunt het probleem echter omzeilen door sqlpackage.exe handmatig uit te voeren vanaf een Azure-VM of door de export rechtstreeks in uw code uit te voeren met behulp van de DACFx-API.
- Importeren biedt geen ondersteuning voor het opgeven van een back-upopslagredundantie tijdens het maken van een nieuwe database en er wordt een standaardredundantie voor de geografisch redundante back-upopslag gemaakt. U lost dit op door eerst een lege database met de gewenste redundantie voor back-upopslag te maken met behulp van Azure Portal of PowerShell waarna u de BACPAC in deze lege database importeert. 

> [!NOTE]
> Azure SQL Database Configurable Backup Storage Redundancy is momenteel alleen in de Azure-regio Azië - zuidoost beschikbaar als openbare preview.

## <a name="import-using-wizards"></a>Importeren met behulp van wizards

U kunt ook gebruikmaken van deze wizards.

- [Wizard DAC importeren in SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server-wizard voor importeren en exporteren](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Volgende stappen

- Ontdek hoe u verbinding maakt met een Azure SQL-database en vervolgens een query uitvoert in [Quickstart: Azure SQL Database: SQL Server Management Studio gebruiken om verbinding te maken en query's uit te voeren voor gegevens](connect-query-ssms.md).
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [Migratie van de SQL Server-database naar Azure SQL Database](migrate-to-database-from-sql-server.md) voor een bespreking van het volledige migratieproces van SQL Server-databases, inclusief aanbevelingen voor prestaties.
- Zie de [Azure Storage-beveiligingshandleiding](../../storage/blobs/security-recommendations.md) voor meer informatie over het veilig beheren en delen van opslagsleutels en handtekeningen voor gedeelde toegang.