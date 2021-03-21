---
title: Parallelle bulk gegevens importeren in SQL-partitie tabellen-team data Science process
description: Bouw gepartitioneerde tabellen voor snelle, parallelle bulk import van gegevens in een SQL Server-Data Base.
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
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322406"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Tabellen maken en optimaliseren voor snelle parallelle invoer van gegevens in een SQL Server op een virtuele machine van Azure

In dit artikel wordt beschreven hoe u gepartitioneerde tabellen maakt voor snelle, parallelle bulk import van gegevens in een SQL Server-Data Base. Voor het big data van het laden/overdragen naar een SQL database kunnen gegevens worden geïmporteerd in de SQL database en de volgende query's worden verbeterd met behulp van *gepartitioneerde tabellen en weer gaven*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Een nieuwe data base en een set bestands groepen maken
* [Maak een nieuwe data base](/sql/t-sql/statements/create-database-transact-sql), als deze nog niet bestaat.
* Voeg database bestands groepen toe aan de data base, die de gepartitioneerde fysieke bestanden bevat. 
* U kunt dit doen met [Create Data](/sql/t-sql/statements/create-database-transact-sql) Base als New of [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-set-options) als de data base al bestaat.
* Voeg een of meer bestanden (indien nodig) toe aan elke Data Base-bestands groep.
  
  > [!NOTE]
  > Geef de doel groep op. Deze bevat gegevens voor deze partitie en de fysieke database bestandsnaam (n) waarin de bestands groeps gegevens worden opgeslagen.
  > 
  > 

In het volgende voor beeld wordt een nieuwe data base gemaakt met drie andere bestands groepen dan de primaire en logboek groepen, die één fysiek bestand bevatten. De database bestanden worden gemaakt in de standaardmap SQL Server Data, zoals geconfigureerd in het SQL Server-exemplaar. Zie [Bestands locaties voor standaard-en benoemde exemplaren van SQL Server](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server)voor meer informatie over de standaard bestands locaties.

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Een gepartitioneerde tabel maken
Als u gepartitioneerde tabel (len) wilt maken op basis van het gegevens schema, toegewezen aan de database bestands groepen die in de vorige stap zijn gemaakt, moet u eerst een partitie functie en-schema maken. Wanneer gegevens bulksgewijs worden geïmporteerd in de gepartitioneerde tabel (len), worden records verdeeld over de bestands groepen volgens een partitie schema, zoals hieronder wordt beschreven.

### <a name="1-create-a-partition-function"></a>1. een partitie functie maken
[Een partitie functie maken](/sql/t-sql/statements/create-partition-function-transact-sql) Deze functie definieert het bereik van waarden/grenzen die moeten worden opgenomen in elke afzonderlijke partitie tabel, bijvoorbeeld om de partities per maand (een \_ datum \_ veld) in het jaar 2013:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. een partitie schema maken
[Maak een partitie schema](/sql/t-sql/statements/create-partition-scheme-transact-sql). Dit schema wijst elk partitie bereik in de partitie functie toe aan een fysieke bestands groep, bijvoorbeeld:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Voer de volgende query uit om te controleren of de bereiken van invloed zijn op elke partitie op basis van de functie/het schema:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. Maak een partitie tabel
[Maak gepartitioneerde tabel](/sql/t-sql/statements/create-table-transact-sql)(len) op basis van uw gegevens schema en geef het partitie schema en het beperkings veld op dat wordt gebruikt voor het partitioneren van de tabel, bijvoorbeeld:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Zie [gepartitioneerde tabellen en indexen maken](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes)voor meer informatie.

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>De gegevens voor elke afzonderlijke partitie tabel bulksgewijs importeren

* U kunt BCP, BULK INSERT of andere methoden gebruiken, zoals [SQL Server wizard Migratie](https://sqlazuremw.codeplex.com/). In het voor beeld wordt de methode BCP gebruikt.
* Wijzig [de data base om het](/sql/t-sql/statements/alter-database-transact-sql-set-options) transactie logboek schema te wijzigen in BULK_LOGGED om de overhead van logboek registratie te minimaliseren, bijvoorbeeld:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Om het laden van gegevens te versnellen, start u de bulksgewijze import bewerkingen parallel. Zie [1 TB in minder dan 1 uur laden](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour)voor tips over het versnellen van het bulksgewijs importeren van big data in SQL server-data bases.

Het volgende Power shell-script is een voor beeld van het laden van parallelle gegevens met behulp van BCP.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Indexen maken om samen voegingen en query prestaties te optimaliseren
* Als u gegevens ophaalt voor model lering uit meerdere tabellen, maakt u indexen op de koppelings sleutels om de prestaties van de verbinding te verbeteren.
* [Maak indexen](/sql/t-sql/statements/create-index-transact-sql) (geclusterd of niet-geclusterd) die dezelfde bestands groep voor elke partitie hebben, bijvoorbeeld:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > U kunt ervoor kiezen om de indexen te maken voordat u de gegevens bulksgewijs importeert. Het maken van een index voor het bulksgewijs importeren vertraagt het laden van gegevens.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Geavanceerd analyse proces en technologie in actie voorbeeld
Voor een end-to-end-scenario voor beeld met behulp van het team data Science-proces met een open bare gegevensset raadpleegt u [team data Science process in actie: using SQL Server](sql-walkthrough.md).