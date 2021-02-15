---
title: Gegevens migreren van Amazon S3 naar Azure Data Lake Storage Gen2
description: Informatie over het gebruik van een oplossings sjabloon voor het migreren van gegevens van Amazon S3 met behulp van een externe beheer tabel om een partitie lijst op te slaan in AWS S3 met Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361980"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Gegevens migreren van Amazon S3 naar Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Gebruik de sjablonen voor het migreren van PETA bytes met honderden miljoenen bestanden van Amazon S3 naar Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Als u het kleine gegevens volume van AWS S3 naar Azure wilt kopiëren (bijvoorbeeld minder dan 10 TB), is het efficiënter en eenvoudig om het [Azure Data Factory gegevens kopiëren tool](copy-data-tool.md)te gebruiken. De sjabloon die in dit artikel wordt beschreven, is meer dan u nodig hebt.

## <a name="about-the-solution-templates"></a>Over de oplossings sjablonen

Gegevens partities worden vooral aanbevolen bij het migreren van meer dan 10 TB aan gegevens. Als u de gegevens wilt partitioneren, maakt u gebruik van de instelling voor voegsel om de mappen en bestanden op Amazon S3 op naam te filteren. vervolgens kan elke ADF-Kopieer taak één partitie per keer kopiëren. U kunt gelijktijdig meerdere ADF-Kopieer taken uitvoeren voor een betere door voer.

Voor gegevens migratie is normaal gesp roken een historische gegevens migratie vereist, plus regel matig de wijzigingen van AWS S3 naar Azure. Er zijn twee onderstaande sjablonen, waarbij één sjabloon een eenmalige historische gegevens migratie omvat en een andere sjabloon gaat over het synchroniseren van de wijzigingen van AWS S3 naar Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om historische gegevens van Amazon S3 te migreren naar Azure Data Lake Storage Gen2

Met deze sjabloon (*sjabloon naam: historische gegevens van AWS S3 naar Azure data Lake Storage Gen2*) wordt ervan uitgegaan dat u een partitie lijst hebt geschreven in een tabel voor externe controle in Azure SQL database. Daarom wordt er een *opzoek* activiteit gebruikt om de partitie lijst op te halen uit de tabel voor externe controle, een herhaalde failover van elke partitie uit te voeren en elke taak voor het kopiëren van de ADF één partitie per keer te kopiëren. Zodra een Kopieer taak is voltooid, gebruikt deze een *opgeslagen procedure* activiteit om de status van het kopiëren van elke partitie in de controle tabel bij te werken.

De sjabloon bevat vijf activiteiten:
- Met **lookup** worden de partities opgehaald die niet zijn gekopieerd naar Azure data Lake Storage Gen2 uit een externe beheer tabel. De tabel naam is *s3_partition_control_table* en de query voor het laden van gegevens uit de tabel is *' SELECT PARTITIONPREFIX from s3_partition_control_table where SuccessOrFailure = 0 '*.
- **Foreach** haalt de partitie lijst op uit de *opzoek* activiteit en herhaalt elke partitie met de *TriggerCopy* -activiteit. U kunt de *batchCount* zo instellen dat gelijktijdig meerdere ADF-Kopieer taken worden uitgevoerd. We hebben in deze sjabloon 2 ingesteld.
- **ExecutePipeline** voert *CopyFolderPartitionFromS3* -pijp lijn uit. De reden hiervoor is dat er een andere pijp lijn wordt gemaakt om elke Kopieer taak een partitie te laten kopiëren is omdat u de mislukte Kopieer taak eenvoudig opnieuw kunt uitvoeren om die specifieke partitie opnieuw te laden vanuit AWS S3. Alle andere Kopieer taken voor het laden van andere partities worden niet beïnvloed.
- **Copy** kopieert elke partitie van AWS S3 naar Azure data Lake Storage Gen2.
- **SqlServerStoredProcedure** de status van het kopiëren van elke partitie in de beheer tabel bij te werken.

De sjabloon bevat twee para meters:
- **AWS_S3_bucketName** is de Bucket naam op de AWS S3 waar u de gegevens wilt migreren. Als u gegevens wilt migreren uit meerdere buckets op AWS S3, kunt u nog één kolom in de tabel met externe controle toevoegen om de Bucket naam voor elke partitie op te slaan en ook de pijp lijn bij te werken om de gegevens uit die kolom dienovereenkomstig op te halen.
- **Azure_Storage_fileSystem** is de naam van het bestands systeem op Azure data Lake Storage Gen2 waarnaar u gegevens wilt migreren.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon alleen gewijzigde bestanden van Amazon S3 naar Azure Data Lake Storage Gen2 kopiëren

Deze sjabloon (*sjabloon naam: verschil gegevens kopiëren van AWS S3 naar Azure data Lake Storage Gen2*) gebruikt LastModifiedTime van elk bestand om de nieuwe of bijgewerkte bestanden alleen van AWS S3 naar Azure te kopiëren. Houd rekening met het volgende als uw bestanden of mappen al zijn gepartitioneerd met timeslice-informatie als onderdeel van de bestands-of mapnaam op AWS S3 (bijvoorbeeld/yyyy/mm/dd/file.csv), u kunt naar deze [zelf studie](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) gaan om de meer uitgebreide benadering te verkrijgen voor het stapsgewijs laden van nieuwe bestanden. Bij deze sjabloon wordt ervan uitgegaan dat u een partitie lijst hebt geschreven in een externe beheer tabel in Azure SQL Database. Daarom wordt er een *opzoek* activiteit gebruikt om de partitie lijst op te halen uit de tabel voor externe controle, een herhaalde failover van elke partitie uit te voeren en elke taak voor het kopiëren van de ADF één partitie per keer te kopiëren. Wanneer elke Kopieer taak begint met het kopiëren van de bestanden van AWS S3, is het afhankelijk van de eigenschap LastModifiedTime om de nieuwe of bijgewerkte bestanden te identificeren en te kopiëren. Zodra een Kopieer taak is voltooid, gebruikt deze een *opgeslagen procedure* activiteit om de status van het kopiëren van elke partitie in de controle tabel bij te werken.

De sjabloon bevat zeven activiteiten:
- Met **lookup** worden de partities opgehaald uit een externe beheer tabel. De tabel naam is *s3_partition_delta_control_table* en de query voor het laden van gegevens uit de tabel is *' Select distinct PartitionPrefix from s3_partition_delta_control_table '*.
- **Foreach** haalt de partitie lijst op uit de *opzoek* activiteit en herhaalt elke partitie met de *TriggerDeltaCopy* -activiteit. U kunt de *batchCount* zo instellen dat gelijktijdig meerdere ADF-Kopieer taken worden uitgevoerd. We hebben in deze sjabloon 2 ingesteld.
- **ExecutePipeline** voert *DeltaCopyFolderPartitionFromS3* -pijp lijn uit. De reden hiervoor is dat er een andere pijp lijn wordt gemaakt om elke Kopieer taak een partitie te laten kopiëren is omdat u de mislukte Kopieer taak eenvoudig opnieuw kunt uitvoeren om die specifieke partitie opnieuw te laden vanuit AWS S3. Alle andere Kopieer taken voor het laden van andere partities worden niet beïnvloed.
- Met **lookup** wordt de laatste kopie van de uitvoer tijd van de taak opgehaald uit de tabel met externe controle, zodat de nieuwe of bijgewerkte bestanden kunnen worden geïdentificeerd via LastModifiedTime. De tabel naam is *s3_partition_delta_control_table* en de query voor het laden van gegevens uit de tabel is *' Select Max (JobRunTime) als LastModifiedTime van s3_partition_delta_control_table waarbij PartitionPrefix = @ {pipeline (). para meters. PrefixStr} en SuccessOrFailure = 1*.
- **Kopie** kopieert nieuwe of gewijzigde bestanden alleen voor elke partitie van AWS S3 naar Azure data Lake Storage Gen2. De eigenschap van *modifiedDatetimeStart* is ingesteld op de laatste keer dat de taak wordt uitgevoerd. De eigenschap van *modifiedDatetimeEnd* wordt ingesteld op de huidige uitvoerings tijd van de taak copy. Houd er rekening mee dat de tijd wordt toegepast op de UTC-tijd zone.
- **SqlServerStoredProcedure** bijwerken de status van het kopiëren van elke partitie en het kopiëren van de uitvoerings tijd in de controle tabel wanneer deze slaagt. De kolom van SuccessOrFailure is ingesteld op 1.
- **SqlServerStoredProcedure** bijwerken de status van het kopiëren van elke partitie en het kopiëren van de uitvoerings tijd in de controle tabel wanneer deze mislukt. De kolom van SuccessOrFailure is ingesteld op 0.

De sjabloon bevat twee para meters:
- **AWS_S3_bucketName** is de Bucket naam op de AWS S3 waar u de gegevens wilt migreren. Als u gegevens wilt migreren uit meerdere buckets op AWS S3, kunt u nog één kolom in de tabel met externe controle toevoegen om de Bucket naam voor elke partitie op te slaan en ook de pijp lijn bij te werken om de gegevens uit die kolom dienovereenkomstig op te halen.
- **Azure_Storage_fileSystem** is de naam van het bestands systeem op Azure data Lake Storage Gen2 waarnaar u gegevens wilt migreren.

## <a name="how-to-use-these-two-solution-templates"></a>Deze twee oplossings sjablonen gebruiken

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon om historische gegevens van Amazon S3 te migreren naar Azure Data Lake Storage Gen2 

1. Maak een controle tabel in Azure SQL Database om de partitie lijst van AWS S3 op te slaan. 

    > [!NOTE]
    > De tabel naam is s3_partition_control_table.
    > Het schema van de beheer tabel is PartitionPrefix en SuccessOrFailure, waarbij PartitionPrefix de voorvoegsel instelling in S3 is om de mappen en bestanden in Amazon S3 op naam te filteren. SuccessOrFailure is de status van het kopiëren van elke partitie: 0 betekent dat deze partitie niet is gekopieerd naar Azure en 1 betekent dat deze partitie naar Azure is gekopieerd.
    > Er zijn vijf partities gedefinieerd in de controle tabel en de standaard status van het kopiëren van elke partitie is 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Maak een opgeslagen procedure op hetzelfde Azure SQL Database voor de controle tabel. 

    > [!NOTE]
    > De naam van de opgeslagen procedure is sp_update_partition_success. Deze wordt aangeroepen door de SqlServerStoredProcedure-activiteit in uw ADF-pijp lijn.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Ga naar de sjabloon **historische gegevens van AWS S3 migreren naar Azure data Lake Storage Gen2** . Voer de verbindingen met uw externe beheer tabel in AWS S3 als de gegevens bron opslag en Azure Data Lake Storage Gen2 als doel archief. Houd er rekening mee dat de tabel met externe controle en de opgeslagen procedure verwijzen naar dezelfde verbinding.

    ![Scherm opname van de gemigreerde historische gegevens van AWS S3 naar Azure Data Lake Storage Gen2-sjabloon.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecteer **deze sjabloon gebruiken**.

    ![Scherm afbeelding met de knop deze sjabloon gebruiken.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. U ziet dat er twee pijp lijnen en 3 gegevens sets zijn gemaakt, zoals wordt weer gegeven in het volgende voor beeld:

    ![Scherm afbeelding waarin de twee pijp lijnen en drie gegevens sets worden weer gegeven die zijn gemaakt met behulp van de sjabloon.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Ga naar de pijp lijn ' BulkCopyFromS3 ' en selecteer **fout opsporing**, voer de **para meters** in. Selecteer vervolgens **Voltooien**.

    ![Scherm afbeelding die laat zien waar u debug kunt selecteren en de para meters invoert voordat u volt ooien selecteert.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Er worden resultaten weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

    ![Scherm opname waarin de geretourneerde resultaten worden weer gegeven.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Voor de sjabloon alleen gewijzigde bestanden van Amazon S3 naar Azure Data Lake Storage Gen2 kopiëren

1. Maak een controle tabel in Azure SQL Database om de partitie lijst van AWS S3 op te slaan. 

    > [!NOTE]
    > De tabel naam is s3_partition_delta_control_table.
    > Het schema van de beheer tabel is PartitionPrefix, JobRunTime en SuccessOrFailure, waarbij PartitionPrefix de voorvoegsel instelling in S3 is om de mappen en bestanden in Amazon S3 op naam te filteren, JobRunTime de datum/tijd-waarde bij het kopiëren van taken en SuccessOrFailure de status is van het kopiëren van elke partitie: 0 betekent dat deze partitie niet naar Azure is gekopieerd en 1 betekent dat deze partitie is gekopieerd naar Azure.
    > Er zijn vijf partities gedefinieerd in de controle tabel. De standaard waarde voor JobRunTime kan de tijd zijn wanneer een historische gegevens migratie wordt gestart. Met de ADF Copy-activiteit worden de bestanden op AWS S3 gekopieerd die na die tijd voor het laatst zijn gewijzigd. De standaard status van het kopiëren van elke partitie is 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Maak een opgeslagen procedure op hetzelfde Azure SQL Database voor de controle tabel. 

    > [!NOTE]
    > De naam van de opgeslagen procedure is sp_insert_partition_JobRunTime_success. Deze wordt aangeroepen door de SqlServerStoredProcedure-activiteit in uw ADF-pijp lijn.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Ga naar de sjabloon **Delta gegevens van AWS S3 kopiëren naar Azure data Lake Storage Gen2** . Voer de verbindingen met uw externe beheer tabel in AWS S3 als de gegevens bron opslag en Azure Data Lake Storage Gen2 als doel archief. Houd er rekening mee dat de tabel met externe controle en de opgeslagen procedure verwijzen naar dezelfde verbinding.

    ![Een nieuwe verbinding maken](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. U ziet dat er twee pijp lijnen en 3 gegevens sets zijn gemaakt, zoals wordt weer gegeven in het volgende voor beeld:

    ![De pijp lijn controleren](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  Ga naar de pijp lijn ' DeltaCopyFromS3 ' en selecteer **fout opsporing** en voer de **para meters** in. Selecteer vervolgens **Voltooien**.

    ![Klik op * * fout opsporing * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Er worden resultaten weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

    ![Bekijk het resultaat](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. U kunt ook de resultaten van de controle tabel controleren met een query *"Select * from s3_partition_delta_control_table"*. de uitvoer ziet er ongeveer uit als in het volgende voor beeld:

    ![Scherm opname van de resultaten van de controle tabel nadat u de query hebt uitgevoerd.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Bestanden kopiëren vanuit meerdere containers](solution-template-copy-files-multiple-containers.md)
- [Bestanden verplaatsen](solution-template-move-files.md)
