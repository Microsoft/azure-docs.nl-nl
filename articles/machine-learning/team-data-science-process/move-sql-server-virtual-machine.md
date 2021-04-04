---
title: Gegevens verplaatsen naar een SQL Server virtuele machine-team data Science process
description: Verplaats gegevens van platte bestanden of van on-premises SQL Server naar SQL Server op Azure VM.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320340"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Gegevens verplaatsen naar SQL Server op een virtuele Azure-machine

In dit artikel vindt u een overzicht van de opties voor het verplaatsen van gegevens van platte bestanden (CSV-of TSV-indeling) of van een on-premises SQL Server naar SQL Server op een virtuele machine van Azure. Deze taken voor het verplaatsen van gegevens naar de Cloud maken deel uit van het team data Science process.

Zie [gegevens verplaatsen naar een Azure SQL database voor Azure machine learning](move-sql-azure.md)voor een onderwerp met een overzicht van de opties voor het verplaatsen van gegevens naar een Azure SQL Database voor machine learning.

De volgende tabel bevat een overzicht van de opties voor het verplaatsen van gegevens naar SQL Server op een virtuele machine van Azure.

| <b>Bron</b> | <b>DOEL: SQL Server op Azure VM</b> |
| --- | --- |
| <b>Plat bestand</b> |1. <a href="#insert-tables-bcp">opdracht regel programma voor bulksgewijs kopiëren (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">SQL-query bulksgewijs invoegen </a><br> 3. <a href="#sql-builtin-utilities">ingebouwde grafische Hulpprogram ma's in SQL Server</a> |
| <b>On-premises SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">een SQL Server-Data Base implementeren op een Microsoft Azure VM-wizard</a><br> 2. <a href="#export-flat-file">exporteren naar een plat bestand </a><br> 3. <a href="#sql-migration">SQL database wizard Migratie </a> <br> 4. <a href="#sql-backup">back-up en herstel van data base </a><br> |

In dit document wordt ervan uitgegaan dat SQL-opdrachten worden uitgevoerd vanuit SQL Server Management Studio of Visual Studio Database Explorer.

> [!TIP]
> Als alternatief kunt u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om een pijp lijn te maken en te plannen waarmee gegevens naar een SQL Server VM in Azure worden verplaatst. Zie [gegevens kopiëren met Azure Data Factory (Kopieer activiteit)](../../data-factory/copy-activity-overview.md)voor meer informatie.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
In deze zelf studie wordt ervan uitgegaan dat u het volgende hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslag account**. U gebruikt een Azure-opslag account voor het opslaan van de gegevens in deze zelf studie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslag account hebt gemaakt, moet u de account sleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Ingericht **SQL Server op een virtuele machine van Azure**. Zie [een virtuele machine van Azure SQL Server instellen als IPython-notebook server voor geavanceerde analyses](../data-science-virtual-machine/overview.md)voor instructies.
* **Azure PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Gegevens verplaatsen van een bron met een plat bestand naar SQL Server op een virtuele machine van Azure
Als uw gegevens zich in een plat bestand bevindt (gerangschikt in een rij-of kolom indeling), kan het worden verplaatst naar SQL Server VM op Azure via de volgende methoden:

1. [Opdracht regel programma voor bulksgewijs kopiëren (BCP)](#insert-tables-bcp)
2. [SQL-query bulksgewijs invoegen](#insert-tables-bulkquery)
3. [Grafische ingebouwde Hulpprogram Ma's in SQL Server (importeren/exporteren, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Opdracht regel programma voor bulksgewijs kopiëren (BCP)
BCP is een opdracht regel programma dat wordt geïnstalleerd met SQL Server en is een van de snelste manieren om gegevens te verplaatsen. Het werkt op alle drie SQL Server varianten (on-premises SQL Server, SQL Azure en SQL Server VM in Azure).

> [!NOTE]
> **Waar moeten mijn gegevens voor BCP voor komen?**  
> Hoewel dit niet vereist is, waarbij bestanden met bron gegevens op dezelfde computer als de doel-SQL Server een snellere overdracht (netwerk snelheid versus een lokale schijf-i/o-snelheid) mogelijk maakt. U kunt de platte bestanden met gegevens verplaatsen naar de computer waarop SQL Server wordt geïnstalleerd met behulp van verschillende hulpprogram ma's voor het kopiëren van bestanden, zoals [AZCopy](../../storage/common/storage-use-azcopy-v10.md), [Azure Storage Explorer](https://storageexplorer.com/) of Windows copy/paste via Remote Desktop Protocol (RDP).
>
>

1. Zorg ervoor dat de data base en de tabellen zijn gemaakt op de doel-SQL Server Data Base. Hier volgt een voor beeld van hoe u dit kunt doen met behulp van de- `Create Database` `Create Table` opdrachten:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Genereer het indelings bestand waarin het schema voor de tabel wordt beschreven door de volgende opdracht uit te geven vanaf de opdracht regel van de computer waarop BCP is geïnstalleerd.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Voeg de gegevens in de data base in met behulp van de BCP-opdracht, die vanaf de opdracht regel zou moeten werken wanneer SQL Server op dezelfde computer is geïnstalleerd:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Bcp-invoeg bladen optimaliseren** Raadpleeg het volgende artikel [' richt lijnen voor het optimaliseren van bulksgewijs importeren '](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) om dergelijke toevoegingen te optimaliseren.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Gelijktijdig voegt voor snellere gegevens verplaatsing
Als de gegevens die u verplaatst, groot zijn, kunt u sneller meerdere BCP-opdrachten tegelijk uitvoeren in een Power shell-script.

> [!NOTE]
> **Opname van Big Data** Voor het optimaliseren van het laden van gegevens voor grote en zeer grote gegevens sets kunt u de logische en fysieke database tabellen partitioneren met meerdere bestands groepen en partitie tabellen. Zie voor meer informatie over het maken en laden van gegevens in partitie tabellen [parallelle load SQL-partitie tabellen](parallel-load-sql-partitioned-tables.md).
>
>

In het volgende Power shell-voorbeeld script worden parallelle invoeg bladen gedemonstreerd met behulp van BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-query bulksgewijs invoegen
[Bulksgewijs invoegen SQL-query](/sql/t-sql/statements/bulk-insert-transact-sql) kan worden gebruikt om gegevens te importeren in de data base van op rij/kolom gebaseerde bestanden (de ondersteunde typen worden behandeld in het onderwerp[gegevens voorbereiden voor bulk export of importeren (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)).

Hier volgen enkele voor beelden van opdrachten voor bulksgewijs invoegen:  

1. Analyseer uw gegevens en stel vóór het importeren aangepaste opties in om ervoor te zorgen dat de SQL Server-Data Base dezelfde indeling krijgt als speciale velden zoals datums. Hier volgt een voor beeld van het instellen van de datum notatie als jaar-maand-dag (als uw gegevens de datum in de jaar-maand-dag notatie bevatten):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Gegevens importeren met behulp van de instructies voor bulk import:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Ingebouwde Hulpprogram Ma's in SQL Server
U kunt SQL Server Integration Services (SSIS) gebruiken om gegevens te importeren in SQL Server VM op Azure vanuit een plat bestand.
SSIS is beschikbaar in twee studio omgevingen. Zie voor meer informatie, [Integration Services (SSIS) en Studio omgevingen](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Zie [Microsoft SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (Engelstalig) voor meer informatie over SQL Server Data tools.  
* Zie [SQL Server wizard Importeren en exporteren](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) voor meer informatie over de wizard Importeren/exporteren.

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Gegevens verplaatsen van on-premises SQL Server naar SQL Server op een Azure VM
U kunt ook de volgende migratie strategieën gebruiken:

1. [Een SQL Server-Data Base implementeren op een Microsoft Azure VM-wizard](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporteren naar plat bestand](#export-flat-file)
3. [Wizard Migratie SQL Database](#sql-migration)
4. [Back-up en herstel van data base](#sql-backup)

Hieronder vindt u een beschrijving van elk van deze opties:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Een SQL Server-Data Base implementeren op een Microsoft Azure VM-wizard
De **wizard een SQL Server-Data Base implementeren naar een Microsoft Azure VM** is een eenvoudige en aanbevolen manier om gegevens van een on-premises SQL Server exemplaar te verplaatsen naar SQL Server op een virtuele machine van Azure. Zie [een Data Base migreren naar SQL Server op een virtuele machine van Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)voor gedetailleerde stappen en een bespreking van andere alternatieven.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exporteren naar plat bestand
Verschillende methoden kunnen worden gebruikt om gegevens van een on-premises SQL Server bulksgewijs te exporteren, zoals wordt beschreven in het onderwerp [bulksgewijs importeren en exporteren van gegevens (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) . In dit document wordt het bulksgewijze Kopieer programma (BCP) als voor beeld behandeld. Wanneer gegevens worden geëxporteerd naar een plat bestand, kunnen ze worden geïmporteerd in een andere SQL-Server met behulp van bulk import.

1. De gegevens van on-premises SQL Server exporteren naar een bestand met behulp van het BCP-hulp programma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Maak de data base en de tabel op SQL Server VM in azure met behulp van de `create database` en `create table` voor het tabel schema dat u in stap 1 hebt geëxporteerd.
3. Maak een indelings bestand voor het beschrijven van het tabel schema van de gegevens die worden geëxporteerd/geïmporteerd. Details van het indelings bestand worden beschreven in [een indelings bestand maken (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Het genereren van bestanden opmaken bij het uitvoeren van BCP vanaf de SQL Server computer

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Het genereren van bestanden opmaken bij het extern uitvoeren van BCP op een SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Gebruik een van de methoden die worden beschreven in de sectie [gegevens verplaatsen van de bestands bron](#filesource_to_sqlonazurevm) om de gegevens in platte bestanden te verplaatsen naar een SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Wizard Migratie SQL Database
[SQL Server wizard Database migratie](https://sqlazuremw.codeplex.com/) biedt een gebruiks vriendelijke manier om gegevens te verplaatsen tussen twee SQL Server-exemplaren. Hiermee kan de gebruiker het gegevens schema tussen bronnen en doel tabellen toewijzen, kolom typen en verschillende andere functies kiezen. Het maakt gebruik van bulksgewijs kopiëren (BCP) onder de kaften. Hieronder ziet u een scherm afbeelding van het welkomst scherm van de wizard Migratie van SQL Database.  

![Wizard Migratie SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Back-up en herstel van data base
SQL Server ondersteunt:

1. [Data Base back-up en herstel](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (zowel naar een lokaal bestand of Bacpac exporteren naar blob) als [gegevenslaag toepassingen](/sql/relational-databases/data-tier-applications/data-tier-applications) (met behulp van Bacpac).
2. De mogelijkheid om rechtstreeks SQL Server Vm's in azure te maken met een gekopieerde data base of een kopie te kopiëren naar een bestaande data base in SQL Database. Zie [de wizard Data Base kopiëren gebruiken](/sql/relational-databases/databases/use-the-copy-database-wizard)voor meer informatie.

Hieronder ziet u een scherm opname van de opties data base back-up/herstellen van SQL Server Management Studio.

![Hulp programma SQL Server importeren][1]

## <a name="resources"></a>Resources
[Een Data Base migreren naar SQL Server op een virtuele Azure-machine](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Overzicht van SQL Server op virtuele machines in Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png