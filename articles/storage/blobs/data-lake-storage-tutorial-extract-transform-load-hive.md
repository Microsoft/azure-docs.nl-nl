---
title: 'Zelfstudie: Gegevens extraheren, transformeren en laden met behulp van Azure HDInsight'
description: In deze zelfstudie leert u hoe u gegevens uit een set met onbewerkte CSV-gegevens extraheert, de gegevens met Apache Hive in Azure HDInsight transformeert en de getransformeerde gegevens ten slotte laadt in Azure SQL Database met behulp van Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f8210c3bc0437180ace110f8decd9f83e18650ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661930"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Zelfstudie: Gegevens extraheren, transformeren en laden met behulp van Azure HDInsight

In deze zelfstudie voert u een ETL-bewerking uit: gegevens extraheren, transformeren en laden. U neemt een CSV-bestand met onbewerkte gegevens, importeert dit in een Azure HDInsight-cluster, transformeert het met Apache Hive en laadt de gegevens in Azure SQL Database met Apache Sqoop.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens extraheren en uploaden naar een HDInsight-cluster.
> * De gegevens transformeren met behulp van Apache Hive.
> * De gegevens laden in Azure SQL Database met behulp van Sqoop.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* **Een Azure Data Lake Storage Gen2-opslagaccount dat is geconfigureerd voor HDInsight**

    Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).

* **Een Hadoop-cluster op basis van Linux in HDInsight**

    Zie [Quickstart: Aan de slag met Apache Hadoop en Apache Hive in Azure HDInsight met behulp van de Azure-portal](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).

* **Azure SQL Database**: U gebruikt Azure SQL Database als een doelgegevensarchief. Als u geen database in SQL Database hebt, raadpleegt u het artikel [Een database in Azure SQL Database maken in Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).

* **Azure CLI**: Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [Azure CLI installeren](/cli/azure/install-azure-cli).

* **Een SSH-client (Secure Shell)** : Zie voor meer informatie [Verbinding maken met HDInsight (Hadoop) via SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>De vluchtgegevens downloaden

1. Blader naar [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Selecteer de volgende waarden op de pagina:

   | Naam | Waarde |
   | --- | --- |
   | Filterjaar |2013 |
   | Filterperiode |Januari |
   | Velden |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Wis alle andere velden.

3. Selecteer **Download**. U krijgt een ZIP-bestand met de gegevensvelden die u hebt geselecteerd.

## <a name="extract-and-upload-the-data"></a>De gegevens extraheren en uploaden

In deze sectie gaat u gegevens uploaden in uw HDInsight-cluster en vervolgens die gegevens kopiëren in uw Data Lake Storage Gen2-account.

1. Open een opdrachtprompt en gebruik de volgende Secure Copy-opdracht om het ZIP-bestand te uploaden naar het hoofdknooppunt van het HDInsight-cluster:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Vervang de tijdelijke plaatsaanduiding `<file-name>` door de naam van het ZIP-bestand.
   * Vervang de tijdelijke plaatsaanduiding `<ssh-user-name>` door de SSH-aanmelding voor het HDInsight-cluster.
   * Vervang de tijdelijke plaatsaanduiding `<cluster-name>` door de naam van het HDInsight-cluster.

   Als u een wachtwoord gebruikt om uw SSH-aanmelding te verifiëren, wordt u gevraagd om het wachtwoord.

   Als u een openbare sleutel gebruikt, moet u mogelijk de parameter `-i` gebruiken en het pad naar de bijbehorende persoonlijke sleutel opgeven. Bijvoorbeeld `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Nadat het uploaden is voltooid, maakt u via SSH verbinding met het cluster. Voer op de opdrachtprompt de volgende opdracht in:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Gebruik de volgende opdracht om ZIP-bestand uit te pakken:

   ```bash
   unzip <file-name>.zip
   ```

   Met deze opdracht wordt een **CSV**-bestand uitgepakt.

4. Gebruik de volgende opdracht om de Data Lake Storage Gen2-container te maken.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Vervang de tijdelijke aanduiding `<container-name>` door de naam die u aan uw container wilt geven.

   Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

5. Gebruik de volgende opdracht om een map te maken:

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Gebruik de volgende opdracht om het *CSV*-bestand naar de map te kopiëren:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Gebruik aanhalingstekens rond de bestandsnaam als de bestandsnaam spaties of speciale tekens bevat.

## <a name="transform-the-data"></a>De gegevens transformeren

In deze sectie gebruikt u Beeline om een Apache Hive-taak uit te voeren.

Als onderdeel van de Apache Hive-taak, importeert u de gegevens uit het CSV-bestand in een Apache Hive-tabel met de naam **delays**.

1. Gebruik op de SSH-prompt die al is geopend voor het HDInsight-cluster de volgende opdracht om een nieuw bestand met de naam **flightdelays.hql** te maken en bewerken:

   ```bash
   nano flightdelays.hql
   ```

2. Vervang in de volgende tekst de tijdelijke aanduidingen `<container-name>` en `<storage-account-name>` door de naam van de container en de naam van het opslagaccount. Kopieer en plak de tekst in de nano-console, door de SHIFT-toets ingedrukt te houden en op de rechtermuisknop te klikken.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Sla het bestand op met behulp van CTRL+X en typ vervolgens `Y` wanneer hierom wordt gevraagd.

4. Gebruik de volgende opdracht om Hive te starten en het bestand **flightdelays.hql** uit te voeren:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Als het script __flightdelays.hql__ is voltooid, gebruikt u de volgende opdracht om een interactieve Beeline-sessie te openen:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. Wanneer u de prompt `jdbc:hive2://localhost:10001/>` ziet, gebruikt u de volgende query om gegevens op te halen uit de geïmporteerde gegevens van vertraagde vluchten:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Met deze query haalt u een lijst op met plaatsen waar er vertragingen door het weer zijn ontstaan, samen met de gemiddelde vertragingstijd. Deze gegevens worden opgeslagen in `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Later worden de gegevens met Sqoop vanaf deze locatie gelezen en geëxporteerd naar Azure SQL Database.

7. Sluit Beeline af door `!quit` in te voeren bij de opdrachtprompt.

## <a name="create-a-sql-database-table"></a>Een SQL-databasetabel maken

Voor deze bewerking hebt u de servernaam van SQL Database nodig. Voer deze stappen uit om de servernaam te achterhalen.

1. Ga naar de [Azure Portal](https://portal.azure.com).

2. Selecteer **SQL-databases**.

3. Filter op de naam van de database die u wilt kiezen. De naam van de server wordt vermeld in de kolom **Server**.

4. Filter op de naam van de database die u wilt gebruiken. De naam van de server wordt vermeld in de kolom **Server**.

    ![Details van Azure SQL Server ophalen](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Details van Azure SQL-server ophalen")

    Er zijn veel manieren om verbinding te maken met SQL Database en een tabel te maken. In de volgende stappen wordt [FreeTDS](https://www.freetds.org/) gebruikt vanuit het HDInsight-cluster.

5. Gebruik de volgende opdracht vanuit een SSH-verbinding met het cluster om FreeTDS te installeren:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Nadat de installatie is voltooid, gebruikt u de volgende opdracht om verbinding te maken met SQL Database.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Vervang de tijdelijke aanduiding `<server-name>` door de naam van de logische SQL-server.

   * Vervang de tijdelijke aanduiding `<admin-login>` door de aanmeldingsgegevens van de SQL Database-beheerder.

   * Vervang de tijdelijke aanduiding `<database-name>` door de naam van de database.

   Voer desgevraagd het wachtwoord voor de SQL Database-beheerder in.

   U ziet uitvoer die vergelijkbaar is met de volgende tekst:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Voer de volgende instructies in bij de prompt `1>`:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Wanneer u de instructie `GO` invoert, worden de vorige instructies geëvalueerd.

   Met de query maakt u een tabel met de naam **delays**, met een geclusterde index.

9. Gebruik de volgende query om te controleren of de tabel is gemaakt:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   De uitvoer lijkt op het volgende:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Voer `exit` als reactie op de prompt `1>` om het hulpprogramma tsql af te sluiten.

## <a name="export-and-load-the-data"></a>De gegevens exporteren en laden

In de vorige secties hebt u de getransformeerde gegevens op de locatie `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` gekopieerd. In deze sectie gebruikt u Sqoop om de gegevens in `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` te exporteren naar de tabel die u hebt gemaakt in Azure SQL Database.

1. Gebruik de volgende opdracht om te controleren of Sqoop de SQL-database kan zien:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   De opdracht retourneert een lijst met databases, met inbegrip van de database waarin u de tabel **delays** hebt gemaakt.

2. Gebruik de volgende opdracht om gegevens uit de tabel **hivesampletable** te exporteren naar de tabel **delays**:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop maakt verbinding met de database met de tabel **delays** en exporteert gegevens uit de map `/tutorials/flightdelays/output` naar de tabel **delays**.

3. Als de `sqoop`-opdracht is voltooid, gebruikt u het hulpprogramma tsql om verbinding te maken met de database:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Gebruik de volgende instructies om te controleren of de gegevens zijn geëxporteerd naar de tabel **delays**:

   ```sql
   SELECT * FROM delays
   GO
   ```

   U ziet als het goed is een lijst met gegevens in de tabel. De tabel bevat de plaatsnaam en de gemiddelde vertragingstijd voor vluchten van en naar die plaats.

5. Typ `exit` en druk op Enter om het hulpprogramma tsql af te sluiten.

## <a name="clean-up-resources"></a>Resources opschonen

Alle resources die in deze zelfstudie worden gebruikt, zijn bestaande resources. Opschonen is niet nodig.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor andere manieren om te werken met gegevens in HDInsight:

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)