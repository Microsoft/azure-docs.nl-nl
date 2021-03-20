---
title: Back-up en herstel van data bases-Azure SQL Edge
description: Meer informatie over de mogelijkheden voor back-up en herstel in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395237"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Back-up en herstel van data bases in Azure SQL Edge 

Azure SQL Edge is gebaseerd op de nieuwste versies van de micro soft SQL Database-Engine. Het biedt vergelijk bare mogelijkheden voor back-up en herstel van data bases die beschikbaar zijn in SQL Server on Linux en SQL Server worden uitgevoerd in containers. Het onderdeel back-up maken en herstellen biedt een essentiële beveiliging voor het beveiligen van gegevens die zijn opgeslagen in uw Azure SQL Edge-data bases. 

Als u het risico op onherstelbaar gegevens verlies wilt beperken, moet u regel matig een back-up van uw data bases maken om de wijzigingen in uw gegevens regel matig te hand haven. Een goed geplande strategie voor back-up en herstel beschermt databases tegen gegevensverlies veroorzaakt door een verscheidenheid aan fouten. Test uw strategie door een reeks back-ups te herstellen en vervolgens uw data base te herstellen, om u voor te bereiden om effectief te reageren op een nood geval.

Zie [back-up en herstel van SQL server-data bases](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)voor meer informatie over waarom back-ups belang rijk zijn.

Met Azure SQL Edge kunt u een back-up maken van en herstellen vanuit lokale opslag en Azure-blobs. Zie [SQL Server Backup en Restore with Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) (Engelstalig) en [SQL Server back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)voor meer informatie.

## <a name="back-up-a-database-in-azure-sql-edge"></a>Back-up maken van een data base in Azure SQL Edge

Azure SQL Edge ondersteunt dezelfde back-uptypen als SQL Server. Zie [overzicht van back-ups](/sql/relational-databases/backup-restore/backup-overview-sql-server/)voor een volledige lijst.

> [!IMPORTANT] 
> Data bases die zijn gemaakt in Azure SQL Edge maken standaard gebruik van het eenvoudige herstel model. U kunt geen logboek back-ups uitvoeren op deze data bases. Als u dit wilt doen, moet u een beheerder hebben om het herstel model van de data base te wijzigen in het volledige herstel model. Zie [recovery model Overview (overzicht van herstel](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)modellen) voor een volledige lijst met door SQL Server worden ondersteund.

### <a name="back-up-to-local-disk"></a>Back-up naar lokale schijf

In het volgende voor beeld gebruikt u de `BACKUP DATABASE` Transact-SQL-opdracht om een back-up van de data base in de container te maken. In dit voor beeld maakt u een nieuwe map met de naam *Backup* om de back-upbestanden op te slaan.

1. Maak een map voor de back-ups. Voer deze opdracht uit op de host waar de Azure SQL Edge-container wordt uitgevoerd. Vervang in de volgende opdracht **<AzureSQLEdge_Container_Name>** door de naam van de Azure SQL Edge-container in uw implementatie.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Maak verbinding met de Azure SQL Edge-instantie met behulp van SQL Server Management Studio (SSMS) of met behulp van Azure Data Studio. Voer de `BACKUP DATABASE` opdracht uit om de back-up van uw gebruikers database te maken. In het volgende voor beeld maakt u een back-up van de *IronOreSilicaPrediction* -data base.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Nadat u de opdracht hebt uitgevoerd en de back-up van de data base is voltooid, ziet u berichten die vergelijkbaar zijn met de volgende in de sectie met resultaten van SSMS of Azure Data Studio.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Back-up naar URL

Azure SQL Edge ondersteunt back-ups naar pagina-blobs en blok-blobs. Zie [een back-up maken om de BLOB tegenover de pagina te blok keren](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob)voor meer informatie. In het volgende voor beeld wordt er een back-up van de Data Base *IronOreSilicaPrediction* gemaakt naar een blok-blob. 

1. Als u back-ups wilt configureren om blobs te blok keren, moet u eerst een SAS-token (Shared Access Signature) genereren dat u kunt gebruiken om een SQL Server referentie te maken op Azure SQL Edge. Het script maakt een SAS die is gekoppeld aan een opgeslagen toegangs beleid. Zie [Shared Access Signatures, Part 1: uitleg over het SAS-model](../storage/common/storage-sas-overview.md)voor meer informatie. Het script schrijft ook de T-SQL-opdracht die is vereist om de referentie te maken op SQL Server. In het volgende script wordt ervan uitgegaan dat u al een Azure-abonnement hebt met een opslag account en een opslag container voor de back-ups.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Nadat het script is uitgevoerd, kopieert `CREATE CREDENTIAL` u de opdracht naar een query hulp programma. Vervolgens maakt u verbinding met een exemplaar van SQL Server en voert u de opdracht uit om de referentie met de SAS te maken.

2. Maak verbinding met het exemplaar van de Azure SQL-rand door SSMS of Azure Data Studio te gebruiken en de referentie te maken met behulp van de opdracht uit de vorige stap. Zorg ervoor dat u de `CREATE CREDENTIAL` opdracht vervangt door de werkelijke uitvoer van de vorige stap.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Met de volgende opdracht maakt u een back-up van de *IronOreSilicaPrediction* naar de Azure-opslag container.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Een data base in Azure SQL Edge herstellen

In Azure SQL Edge kunt u herstellen vanaf een lokale schijf, een netwerk locatie of een Azure Blob Storage-account. Zie [overzicht van herstellen en](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server)herstellen voor meer informatie over herstel en herstel in SQL Server. Zie [volledige database herstel (Simple Recovery model)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model)voor een overzicht van het eenvoudige herstel model in SQL Server.

> [!IMPORTANT] 
> Data bases die zijn gemaakt in Azure SQL Edge, kunnen niet worden hersteld op een exemplaar van Microsoft SQL Server of Azure SQL. Daarnaast kan een Data Base die is gemaakt op Microsoft SQL Server of Azure SQL worden hersteld op Azure SQL Edge, op voor hand dat de data base geen van de functies bevat die niet worden ondersteund door Azure SQL Edge. 

### <a name="restore-from-a-local-disk"></a>Herstellen vanaf een lokale schijf

In dit voor beeld wordt de *IronOreSilicaPrediction* -back-up gebruikt die u in het vorige voor beeld hebt gemaakt. Nu herstelt u deze als een nieuwe Data Base met een andere naam.

1. Als het back-upbestand van de data base zich nog niet in de container bevindt, kunt u de volgende opdracht gebruiken om het bestand naar de container te kopiëren. In het volgende voor beeld wordt ervan uitgegaan dat het back-upbestand aanwezig is op de lokale host en wordt gekopieerd naar de map/var/opt/MSSQL/Backup naar een Azure SQL Edge-container met de naam *sql1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Maak verbinding met het exemplaar van de Azure SQL-rand door SSMS of Azure Data Studio te gebruiken om de opdracht Restore uit te voeren. In het volgende voor beeld wordt **IronOrePredictDB. bak** teruggezet om een nieuwe Data Base te maken, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Wanneer u de herstel opdracht hebt uitgevoerd en de herstel bewerking is geslaagd, ziet u de berichten die vergelijkbaar zijn met de volgende in het uitvoer venster. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Herstellen vanaf URL

Azure SQL Edge biedt ook ondersteuning voor het herstellen van een Data Base vanuit een Azure Storage-account. U kunt herstellen vanuit de blok-blobs of pagina-BLOB-back-ups. In het volgende voor beeld wordt het back-upbestand van de *IronOreSilicaPrediction_2020_04_16. bak* -Data Base op een blok-BLOB teruggezet om de data base te maken, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```