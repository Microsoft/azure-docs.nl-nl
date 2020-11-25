---
title: Gegevens verkennen in een Hadoop-cluster-team data Science process
description: Het team data Science-proces gebruiken voor een end-to-end-scenario, waarbij een HDInsight Hadoop-cluster wordt gebruikt voor het bouwen en implementeren van een model.
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
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002218"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Het proces van de team data Science in actie: Azure HDInsight Hadoop clusters gebruiken
In dit scenario gebruiken we het [team data Science process (TDSP)](overview.md) in een end-to-end-scenario. We gebruiken een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) om gegevens op te slaan, te verkennen en te onderbouwen vanuit de openbaar beschik bare NYC van de [taxi](https://www.andresmh.com/nyctaxitrips/) en om de gegevens voor te bereiden. Voor het afhandelen van binaire en multiklasse-classificatie en regressie taken, maken we modellen van de gegevens met Azure Machine Learning. 

Zie voor een overzicht waarin wordt getoond hoe u een grotere gegevensset kunt verwerken, [team data Science process: Using Azure HDInsight Hadoop clusters in een gegevensset van 1 TB](hive-criteo-walkthrough.md).

U kunt ook een IPython-notebook gebruiken om de taken uit te voeren die worden weer gegeven in de walkthrough die gebruikmaakt van de gegevensset van 1 TB. Zie voor meer informatie [Criteo-overzicht met behulp van een Hive ODBC-verbinding](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beschrijving van gegevensset voor NYCe taxi trips
De NYC-gegevens over de taxi zijn ongeveer 20 GB aan gecomprimeerde bestanden met door komma's gescheiden waarden (CSV) (~ 48 GB niet-gecomprimeerd). Het heeft meer dan 173.000.000 afzonderlijke reizen en omvat de tarieven die voor elke reis worden betaald. Elke reis record bevat de locatie en tijd van de ophaal-en dropoff, geanonimiseerd Hack (rijbewijs) en Medallion nummer (de unieke ID van de taxi). De gegevens omvatten alle reizen in het jaar 2013 en worden in de volgende twee gegevens sets voor elke maand vermeld:

- De trip_data CSV-bestanden bevatten reis Details: het aantal reizigers, het ophaal-en dropoff punten, de duur van de reis en de lengte van de reis. Hier volgen enkele voorbeeld records:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- De trip_fare CSV-bestanden bevatten details over het tarief dat voor elke reis wordt betaald: betalings type, ritbedrag bedrag, toeslag en belastingen, tips en tolgelden, en het totale betaalde bedrag. Hier volgen enkele voorbeeld records:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

De unieke sleutel voor deelname aan reis \_ gegevens en reis \_ tarief bestaat uit de velden: Medallion, Hack \_ License en \_ datum/tijd van ophalen. Om alle informatie te verkrijgen die relevant is voor een bepaalde reis, is het voldoende om deel te nemen aan deze drie sleutels.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voor beelden van voorspellings taken
Bepaal het soort voor spellingen dat u wilt maken op basis van gegevens analyse, zodat u de vereiste proces taken kunt verduidelijken. Hier vindt u drie voor beelden van voorspellings problemen die we in deze walkthrough verpakken, allemaal op basis van het *fooien \_ bedrag*:

- **Binaire classificatie**: voor spelt of er een tip voor een reis is betaald. Dat wil zeggen dat een *Tip- \_ bedrag* dat groter is dan $0 een positief voor beeld is, terwijl een *tip- \_ bedrag* van $0 een negatief voor beeld is.

  - Klasse 0: tip_amount = $0
  - Klasse 1: tip_amount > $0

- **Classificatie** van verschillende klassen: de hoeveelheid fooien voor de reis voors pellen. We delen het *fooien \_ bedrag* in vijf klassen:

  - Klasse 0: tip_amount = $0
  - Klasse 1: tip_amount > $0 en tip_amount <= $5
  - Klasse 2: tip_amount > $5 en tip_amount <= $10
  - Klasse 3: tip_amount > $10 en tip_amount <= $20
  - Klasse 4: tip_amount > $20

- **Regressie taak**: voor spelt u het aantal fooien dat voor een reis is betaald.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Een HDInsight Hadoop-cluster instellen voor geavanceerde analyse
> [!NOTE]
> Dit is doorgaans een beheer taak.
> 
> 

U kunt een Azure-omgeving instellen voor geavanceerde analyses die in drie stappen een HDInsight-cluster gebruiken:

1. [Een opslag account maken](../../storage/common/storage-account-create.md): dit opslag account wordt gebruikt voor het opslaan van gegevens in Azure Blob-opslag. De gegevens die in HDInsight-clusters worden gebruikt, bevinden zich hier ook.
2. [Pas Azure HDInsight Hadoop clusters aan voor het geavanceerde analyse proces en de technologie](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Met deze stap maakt u een HDInsight Hadoop-cluster met 64-bits Anaconda python 2,7 geïnstalleerd op alle knoop punten. Er zijn twee belang rijke stappen die u moet onthouden bij het aanpassen van uw HDInsight-cluster.
   
   * Vergeet niet om het opslag account dat u in stap 1 hebt gemaakt te koppelen aan uw HDInsight-cluster wanneer u het maakt. Dit opslag account heeft toegang tot gegevens die in het cluster zijn verwerkt.
   * Nadat u het cluster hebt gemaakt, schakelt u externe toegang in voor het hoofd knooppunt van het cluster. Ga naar het tabblad **configuratie** en selecteer **Extern inschakelen**. Met deze stap geeft u de gebruikers referenties op die worden gebruikt voor externe aanmelding.
3. [Een Azure machine learning-werk ruimte maken](../classic/create-workspace.md): u kunt deze werk ruimte gebruiken om machine learning modellen te maken. Deze taak is gericht op het volt ooien van een initiële gegevens exploratie en een lagere steek proef, met behulp van het HDInsight-cluster.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>De gegevens ophalen uit een open bare bron
> [!NOTE]
> Dit is doorgaans een beheer taak.
> 
> 

Als u de [NYC van de taxi trips](https://www.andresmh.com/nyctaxitrips/) naar uw computer wilt kopiëren vanaf de open bare locatie, gebruikt u een van de methoden die worden beschreven in [gegevens verplaatsen van en naar Azure Blob-opslag](move-azure-blob.md).

Hier wordt beschreven hoe u AzCopy kunt gebruiken om de bestanden met gegevens over te dragen. Volg de instructies in aan [de slag met het AzCopy-opdracht regel programma](../../storage/common/storage-use-azcopy-v10.md)om AzCopy te downloaden en te installeren.

1. Voer vanuit een opdracht prompt venster de volgende AzCopy-opdrachten uit, vervangen *\<path_to_data_folder>* door de gewenste bestemming:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. Wanneer de Kopieer bewerking is voltooid, ziet u in totaal 24 ingepakte bestanden in de map Data. Pak de gedownloade bestanden uit naar dezelfde map op uw lokale computer. Noteer de map waarin de niet-gecomprimeerde bestanden zich bevinden. In deze map wordt de volgende informatie genoemd *\<path\_to\_unzipped_data\_files\>* .

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>De gegevens uploaden naar de standaard container van het HDInsight Hadoop-cluster
> [!NOTE]
> Dit is doorgaans een beheer taak.
> 
> 

Vervang in de volgende AzCopy-opdrachten de volgende para meters door de werkelijke waarden die u hebt opgegeven bij het maken van het Hadoop-cluster en uitgepakt de gegevens bestanden.

* ***\<path_to_data_folder>** _ De map (samen met het pad) op de computer die de ongecomprimeerde gegevens bestanden bevat.  
_ * **\<storage account name of Hadoop cluster>** _ Het opslag account dat is gekoppeld aan uw HDInsight-cluster.
_ * **\<default container of Hadoop cluster>** _ De standaard container die door uw cluster wordt gebruikt. De naam van de standaard container is doorgaans dezelfde naam als het cluster zelf. Als het cluster bijvoorbeeld ' abc123.azurehdinsight.net ' wordt genoemd, is de standaard container abc123.
_ * **\<storage account key>** _ De sleutel voor het opslag account dat door uw cluster wordt gebruikt.

Voer de volgende twee AzCopy-opdrachten uit vanaf een opdracht prompt of een Windows Power shell-venster.

Met deze opdracht worden de reis gegevens geüpload naar de map _*_nyctaxitripraw_*_ in de standaard container van het Hadoop-cluster.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data__.csv
```

Met deze opdracht worden de ritbedrag gegevens geüpload naar de map ***nyctaxifareraw** _ in de standaard container van het Hadoop-cluster.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare__.csv
```

De gegevens moeten zich nu in Blob Storage benemen en kunnen worden gebruikt in het HDInsight-cluster.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Meld u aan bij het hoofd knooppunt van een Hadoop-cluster en bereid u voor op het analyseren van experimentele gegevens
> [!NOTE]
> Dit is doorgaans een beheer taak.
> 
> 

Als u toegang wilt krijgen tot het hoofd knooppunt van het cluster voor experimentele gegevens analyse en een lagere steek proef van de gegevens, volgt u de procedure die wordt beschreven in [toegang tot het hoofd knooppunt van het Hadoop-cluster](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

In dit scenario gebruiken we voornamelijk query's die zijn geschreven in [Hive](https://hive.apache.org/), een SQL-achtige query taal, om voorlopige gegevens onderzoek uit te voeren. De Hive-query's worden opgeslagen in. HQL-bestanden. We kunnen deze gegevens in Machine Learning voor het bouwen van modellen gebruiken.

Als u het cluster wilt voorbereiden op experimentele gegevens analyse, downloadt u de HQL-bestanden met de relevante Hive-scripts van [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) naar een lokale map (C:\temp) op het hoofd knooppunt. Open de opdracht prompt vanuit het hoofd knooppunt van het cluster en voer de volgende twee opdrachten uit:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Deze twee opdrachten downloaden alle '. HQL-bestanden die in dit overzicht nodig zijn voor de lokale map ***C:\temp&#92;** _ in het hoofd knooppunt.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Hive-data base en tabellen gepartitioneerd per maand maken
> [!NOTE]
> Deze taak is doorgaans voor een beheerder.
> 
> 

U bent nu klaar om Hive-tabellen voor de NYC taxi-gegevensset te maken.
Open in het hoofd knooppunt van het Hadoop-cluster de Hadoop-opdracht regel op het bureau blad van het hoofd knooppunt. Voer de Hive-map in door de volgende opdracht uit te voeren:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Alle Hive-opdrachten uitvoeren in deze walkthrough van de Hive bin/directory-prompt. Hiermee wordt het pad automatisch opgelost. We gebruiken de termen ' hive directory prompt ', ' hive bin/directory prompt ' en ' Hadoop-opdracht regel ' door elkaar in dit overzicht.
> 
> 

Voer de volgende opdracht uit in de opdracht regel van het Hive-bericht van het hoofd knooppunt waarmee de Hive-data base en-tabellen worden gemaakt:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Dit is de inhoud van de *C:\temp\sample- \_ component \_ Create \_ DB \_ and \_ Tables. HQL** file waarmee de Hive-data **Base nyctaxidb** wordt gemaakt en de tabel **reis** en **ritbedrag**.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Met dit Hive-script worden twee tabellen gemaakt:

* De **reis** tabel bevat de reis Details van elke onderdrukking (Details van Stuur Programma's, ophaal tijd, reis afstand en tijden).
* De tabel **ritbedrag** bevat details over de ritbedrag (tarief, fooien, tolgelden en toeslagen).

Als u aanvullende hulp nodig hebt bij deze procedures of als u andere informatie wilt onderzoeken, raadpleegt u de sectie [Hive-query's rechtstreeks vanuit de Hadoop-opdracht regel verzenden](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Gegevens laden naar Hive-tabellen per partitie
> [!NOTE]
> Deze taak is doorgaans voor een beheerder.
> 
> 

De NYC taxi-gegevensset heeft een natuurlijke partitionering per maand, die we gebruiken om sneller verwerkings-en query tijden mogelijk te maken. Met de volgende Power shell-opdrachten (uitgegeven vanuit de Hive-map door gebruik te maken van de Hadoop-opdracht regel) worden gegevens geladen in de tabel met reis-en ritbedrag componenten, gepartitioneerd per maand.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

De **\_ component sample \_ laadt \_ gegevens \_ per \_ partitions. HQL** -bestand bevat de volgende **laad** opdrachten:

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Een aantal van de Hive-query's die hier in het onderzoek proces worden gebruikt, omvat alleen maar één of twee partities. Deze query's kunnen echter worden uitgevoerd over de hele gegevensset.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Data bases weer geven in het HDInsight Hadoop-cluster
Als u de data bases wilt weer geven die zijn gemaakt in HDInsight Hadoop-cluster in het Hadoop-opdracht regel venster, voert u de volgende opdracht uit op de Hadoop-opdracht regel:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>De Hive-tabellen in de **nyctaxidb** -Data Base weer geven
Als u de tabellen in de **nyctaxidb** -Data Base wilt weer geven, voert u de volgende opdracht uit op de Hadoop-opdracht regel:

```console
hive -e "show tables in nyctaxidb;"
```

We kunnen controleren of de tabellen zijn gepartitioneerd door de volgende opdracht uit te voeren:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Dit is de verwachte uitvoer:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

We kunnen er ook voor zorgen dat de ritbedrag tabel is gepartitioneerd door de volgende opdracht uit te voeren:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Dit is de verwachte uitvoer:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Technische voorzieningen voor het verkennen van gegevens en onderdelen in Hive
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

U kunt Hive-query's gebruiken om technische taken voor het verkennen van gegevens en functies uit te voeren voor de gegevens die in de Hive-tabellen worden geladen. Hier volgen enkele voor beelden van taken:

* Bekijk de Top 10 records in beide tabellen.
* Verken gegevens distributies van een paar velden in verschillende tijd Vensters.
* Onderzoek de kwaliteit van de gegevens van de velden lengte graad en breedte graad.
* Genereer binaire en classificatie labels voor multi klassen op basis van het fooien bedrag.
* Functies genereren door de afstanden van de directe reis te berekenen.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploratie: de Top 10 records in tabel reis weer geven
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Bekijk 10 records uit elke tabel om te zien hoe de gegevens eruitzien. Als u de records wilt controleren, voert u de volgende twee query's afzonderlijk uit vanaf de Hive-prompt in de Hadoop-opdracht regel console.

De Top 10 records in de tabel reis ophalen uit de eerste maand:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

De Top 10 records in de tabel ritbedrag ophalen uit de eerste maand:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

U kunt de records opslaan in een bestand voor een handige weer gave met een kleine wijziging in de voor gaande query:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploratie: het aantal records in elk van de 12 partities weer geven
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Van belang is hoe het aantal trips varieert tijdens het kalender jaar. Groeperen op maand toont de verdeling van reizen.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Met deze opdracht wordt de volgende uitvoer gegenereerd:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Hier is de eerste kolom de maand en de tweede is het aantal trips voor die maand.

We kunnen ook het totale aantal records in onze trip-gegevensset tellen door de volgende opdracht uit te voeren op de Hive-prompt:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Met deze opdracht geeft u het volgende resultaat:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Door gebruik te maken van opdrachten die vergelijkbaar zijn met die van de trip-gegevensset, kunnen we Hive-query's uitgeven vanuit de Hive-opdracht prompt voor de ritbedrag-gegevensset voor het valideren van het aantal records.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Met deze opdracht wordt de volgende uitvoer gegenereerd:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

Het exacte aantal trips per maand wordt geretourneerd voor beide gegevens sets, waarbij de eerste validatie wordt geboden dat de gegevens correct zijn geladen.

U kunt het totale aantal records in de ritbedrag-gegevensset tellen met behulp van de volgende opdracht uit de Hive-prompt:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Met deze opdracht geeft u het volgende resultaat:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

Het totale aantal records in beide tabellen is ook hetzelfde, waardoor een tweede validatie wordt geboden dat de gegevens correct zijn geladen.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: reis distributie per Medallion
> [!NOTE]
> Deze analyse is doorgaans een taak voor gegevens wetenschapper.
> 
> 

In dit voor beeld wordt het Medallions (taxi nummer) geïdentificeerd dat groter is dan 100 trips binnen een bepaalde tijds periode. De query heeft voor delen van de gepartitioneerde tabel toegang, omdat deze wordt voor bereid op de partitie variabele **Month**. De query resultaten worden geschreven naar een lokaal bestand, **queryoutput. TSV**, in `C:\temp` op het hoofd knooppunt.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Dit is de inhoud van het **\_ \_ \_ \_ \_ Medallion. HQL** -bestand van het onderdeel aantal voor beelden voor inspectie.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

De Medallion in de NYC taxi-gegevensset duidt een unieke cab aan. U kunt nagaan welke cab-bestanden relatief bezig zijn door te vragen welke u meer dan een bepaald aantal reizen in een bepaalde periode hebt gemaakt. In het volgende voor beeld worden de cab-bestanden geïdentificeerd die tijdens de eerste drie maanden meer dan honderden zijn gemaakt en worden de query resultaten opgeslagen in een lokaal bestand, **C:\temp\queryoutput.TSV**.

Dit is de inhoud van het **\_ \_ \_ \_ \_ Medallion. HQL** -bestand van het onderdeel aantal voor beelden voor inspectie.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Voer de volgende opdracht uit op de Hive-prompt:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploratie: reis distributie door Medallion en hack License
> [!NOTE]
> Deze taak is doorgaans voor een gegevens wetenschapper.
> 
> 

Bij het verkennen van een gegevensset willen we vaak de distributies van groepen waarden onderzoeken. Deze sectie bevat een voor beeld van hoe u deze analyse kunt uitvoeren voor Cab-en stuur Programma's.

Met **het \_ \_ \_ \_ \_ Medallion \_ License. HQL** -bestand wordt de ritbedrag-gegevensset op **Medallion** en **hack_license** gegroepeerd en worden de aantallen van elke combi natie geretourneerd. Hier vindt u de inhoud:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Deze query retourneert combi Naties van Cab-en stuur Programma's, gesorteerd op aflopende aantal trips.

Voer de volgende opdracht uit vanaf de Hive-prompt:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

De resultaten van de query worden geschreven naar een lokaal bestand, **C:\temp\queryoutput.TSV**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Exploratie: kwaliteit van de gegevens controleren door een ongeldige lengte van een of meer Latitude-records aan te vinken
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Een gemeen schappelijke doel stelling voor de analyse van experimentele gegevens is om te voor komen dat er ongeldige of onjuiste records worden gebruikt. Het voor beeld in deze sectie bepaalt of de velden lengte graad en breedte graad uiterst buiten het gebied NYC bevatten. Omdat dergelijke records waarschijnlijk een onjuiste lengte graad-breedte hebben, willen we deze verwijderen uit de gegevens die moeten worden gebruikt voor model lering.

Dit is de inhoud van een voor beeld van een **\_ \_ \_ HQL** -bestand van de Hive voor inspectie.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

Voer de volgende opdracht uit vanaf de Hive-prompt:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

Het argument *-S* dat is opgenomen in deze opdracht onderdrukt de status scherm afdruk van de Hive-toewijzing/verminderen taken. Deze opdracht is handig omdat de scherm afdruk van de Hive-query-uitvoer beter leesbaar is.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploratie: binaire klasse-distributies van reis tips
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Voor het probleem van de binaire classificatie dat wordt beschreven in de sectie [voor beelden van voorspellings taken](hive-walkthrough.md#mltasks) , is het handig om te weten of een tip is opgegeven of niet. Deze verdeling van tips is binair:

* Tip gegeven (klasse 1, tip \_ -bedrag > $0)  
* geen tip (klasse 0, fooi \_ -waarde = $0)

In het volgende voor beeld van de **\_ component \_ gekantelde \_ frequenties. HQL** -bestand ziet u de opdracht die moet worden uitgevoerd:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

Voer de volgende opdracht uit vanaf de Hive-prompt:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploratie: klasse-distributies in de multi klasse-instelling
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Voor het probleem met multi klasse-classificatie dat wordt beschreven in de sectie [voor beelden van voorspellings taken](hive-walkthrough.md#mltasks) , ondervindt deze gegevensset zich ook aan een natuurlijke classificatie om het aantal fooien te voors pellen. We kunnen met behulp van opslag locaties Tip-bereiken definiëren in de query. Als u de klasse-distributies voor de verschillende penpuntstijl-bereiken wilt ophalen, gebruikt u het HQL-bestand van de **voorbeeld \_ component \_ Tip \_ Range \_ .** Hier vindt u de inhoud.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Voer de volgende opdracht uit vanaf de Hadoop-opdracht regel console:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Exploratie: de directe afstand tussen twee lengte graad berekenen-Latitude-locaties
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Mogelijk wilt u weten of er een verschil is tussen de directe afstand tussen twee locaties en de werkelijke reis afstand van de taxi. Een passagier is mogelijk minder kans op fooien als er wordt uitgegaan dat het stuur programma deze met opzet heeft overgenomen door een langere route.

Als u de vergelijking tussen de werkelijke reis afstand en de [Haversine-afstand](https://en.wikipedia.org/wiki/Haversine_formula) tussen twee lengte graad-Latitude-punten (de "fantastische cirkel") wilt bekijken, kunt u de trigonometrische functies gebruiken die beschikbaar zijn in Hive:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

In de voor gaande query is R de RADIUS van de aarde in mijlen en wordt pi geconverteerd naar radialen. De lengte graad-Latitude-punten worden gefilterd om waarden te verwijderen uit het gebied NYC.

In dit geval schrijven we de resultaten naar een map met de naam **queryoutputdir**. De volg orde van de volgende opdrachten maakt eerst deze uitvoermap en voert vervolgens de Hive-opdracht uit.

Voer de volgende opdracht uit vanaf de Hive-prompt:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

De query resultaten worden geschreven naar negen Azure-blobs (**queryoutputdir/000000 \_ 0** to  **queryoutputdir/000008 \_ 0**), onder de standaard container van het Hadoop-cluster.

Als u de grootte van de afzonderlijke blobs wilt zien, voert u de volgende opdracht uit vanaf de Hive-prompt:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Als u de inhoud van een bepaald bestand wilt bekijken, zegt u **000000 \_ 0**, gebruikt u de `copyToLocal` opdracht Hadoop.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` kan erg traag zijn voor grote bestanden en wordt niet aanbevolen voor gebruik met ze.  
> 
> 

Een belang rijk voor deel van het gebruik van deze gegevens bevindt zich in een Azure-Blob. Dit kan de gegevens in Machine Learning verkennen met behulp van de module [gegevens importeren][import-data] .

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Voor beelden van gegevens en modellen bouwen in Machine Learning
> [!NOTE]
> Dit is meestal een Data wetenschapper-taak.
> 
> 

Na de experimentele gegevens analyse fase zijn we nu klaar om de gegevens voor het bouwen van modellen in Machine Learning voor te bereiden. In deze sectie laten we zien hoe u een Hive-query gebruikt om de gegevens te bemonsteren. Machine Learning opent deze vervolgens vanuit de module [gegevens importeren][import-data] .

### <a name="down-sampling-the-data"></a>Down sampling van de gegevens
Er zijn twee stappen in deze procedure. Eerst voegen we de tabellen **nyctaxidb. trip** en **nyctaxidb. ritbedrag** toe op drie sleutels die aanwezig zijn in alle records: **Medallion**, **Hack \_ License** en **\_ datum/tijd van ophalen**. We genereren vervolgens een label met een binaire classificatie, **gekanteld** en een classificatie label met een klasse, **Tip- \_ klasse**.

Als u de gegevens van de voor gaande steek proef rechtstreeks vanuit de module [gegevens importeren][import-data] in machine learning wilt gebruiken, moet u de resultaten van de vorige query opslaan in een interne Hive-tabel. In de volgende stappen maken we een interne Hive-tabel en vullen ze de inhoud ervan met de gekoppelde en bemonsterde gegevens.

De query past standaard Hive-functies rechtstreeks toe om de volgende tijd parameters te genereren vanuit het veld **\_ datum/** tijd van ophalen:
- uur van de dag
- week van jaar
- weekdag (' 1 ' staat voor maandag en ' 7 ' staat voor zondag)

Met de query wordt ook de directe afstand tussen de locaties voor ophalen en dropoff gegenereerd. Zie [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)voor een volledige lijst met dergelijke functies.

De query vervolgens omlaag: voor beelden van de gegevens, zodat de query resultaten in Azure Machine Learning Studio kunnen passen. Alleen ongeveer 1 procent van de oorspronkelijke gegevensset wordt in de Studio geïmporteerd.

Hier vindt u de inhoud van een voor beeld van een component die is **\_ \_ \_ voor bereid op \_ AML \_ Full. HQL** -bestand dat gegevens voorbereidt voor het bouwen van modellen in machine learning:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

Deze query uit te voeren vanuit de prompt van de Hive-map:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

We hebben nu een interne tabel, **nyctaxidb.nyctaxi_downsampled_dataset**, die toegankelijk is via de module [gegevens importeren][import-data] van machine learning. Daarnaast kunnen we deze gegevensset gebruiken om Machine Learning modellen te bouwen.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>De module gegevens importeren in Machine Learning gebruiken om toegang te krijgen tot de omlaag gesamplede gegevens
Als u Hive-query's wilt uitgeven in de module [gegevens importeren][import-data] van machine learning, moet u toegang hebben tot een machine learning-werk ruimte. U hebt ook toegang tot de referenties van het cluster en het bijbehorende opslag account nodig.

Hier volgen enkele details over de module [gegevens importeren][import-data] en de para meters die moeten worden ingevoerd:

**URI van HCatalog-server**: als de cluster naam **abc123** is, gebruikt u: https: \/ /abc123.azurehdinsight.net.

**Hadoop-gebruikers accountnaam**: de gebruikers naam die voor het cluster is gekozen (niet de gebruikers naam voor externe toegang).

**Hadoop-gebruikers account voor wacht woord**: het wacht woord dat u hebt gekozen voor het cluster (niet het wacht woord voor externe toegang).

**Locatie van uitvoer gegevens**: gekozen als Azure.

**Azure Storage account naam**: de naam van het standaard opslag account dat aan het cluster is gekoppeld.

**Azure-container naam**: de standaard container naam voor het cluster en is doorgaans hetzelfde als de naam van het cluster. Voor een cluster met de naam **abc123** is abc123.

> [!IMPORTANT]
> Elke tabel die u wilt opvragen met behulp van de module [gegevens importeren][import-data] in machine learning moet een interne tabel zijn.
> 
> 

U kunt als volgt bepalen of een tabel **T** in een Data Base **D. db** een interne tabel is. Voer de volgende opdracht uit op de Hive-prompt:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Als de tabel een interne tabel is en deze is ingevuld, moet de inhoud hier worden weer gegeven.

Een andere manier om te bepalen of een tabel een interne tabel is, is het gebruik van Azure Storage Explorer. Gebruik deze om naar de standaard container naam van het cluster te gaan en filter vervolgens op de naam van de tabel. Als de tabel en de inhoud ervan worden weer gegeven, wordt hiermee bevestigd dat het een interne tabel is.

Hier volgt een scherm opname van de Hive-query en de module [gegevens importeren][import-data] :

![Scherm afbeelding van Hive-query voor module gegevens importeren](./media/hive-walkthrough/1eTYf52.png)

Omdat de bemonsterde gegevens zich in de standaard container bevinden, is de resulterende Hive-query van Machine Learning eenvoudig. Het is slechts een **Select * from nyctaxidb. nyctaxi \_ down sampling \_ Data**.

De gegevensset kan nu worden gebruikt als uitgangs punt voor het bouwen van Machine Learning modellen.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Modellen maken in Machine Learning
U kunt nu door gaan met het model leren van het bouwen en model implementeren in [machine learning](https://studio.azureml.net). De gegevens kunnen worden gebruikt om de voorspelde problemen op te lossen die eerder zijn geïdentificeerd:

- **Binaire classificatie**: om te voors pellen of er al dan niet een tip voor een reis is betaald.

  **Gebruikte informatieer:** Logistiek regressie met twee klassen

  a. Voor dit probleem wordt het doel label (of klasse) **gekanteld**. De oorspronkelijke voor beeld van een gegevensset bevat enkele kolommen met een doel lekkage voor dit classificatie experiment. Met name **Tip \_ Class**, **Tip- \_ hoeveelheid** en **totaal \_ bedrag** geven informatie weer over het doel label dat niet beschikbaar is op het moment van testen. We verwijderen deze kolommen van overweging met behulp van de module [select columns in dataset][select-columns] .

  In het volgende diagram ziet u het experiment om te voors pellen of er voor een bepaalde reis een tip is betaald:

  ![Diagram van het experiment waarmee wordt voor speld dat tip is betaald](./media/hive-walkthrough/QGxRz5A.png)

  b. Voor dit experiment waren onze doel label distributies ongeveer 1:1.

   In het volgende diagram ziet u de verdeling van de tip-klassen voor het probleem met de binaire classificatie:

  ![Grafiek van de distributie van labels van punt klassen](./media/hive-walkthrough/9mM4jlD.png)

    Als gevolg hiervan verkrijgen we een gebied onder de curve (AUC) van 0,987, zoals wordt weer gegeven in de volgende afbeelding:

  ![Grafiek van AUC-waarde](./media/hive-walkthrough/8JDT0F8.png)

- **Classificatie** van verschillende klassen: om het bereik van de fooie bedragen te voors pellen met behulp van de eerder gedefinieerde klassen.

  **Gebruikte informatieer:** Multiklasse-logistieke regressie

  a. Voor dit probleem is ons doel label (of klasse) een **Tip- \_ klasse**, die een van de vijf waarden kan hebben (0, 1, 2, 3, 4). Net als bij het binaire classificatie geval hebben we een aantal kolommen met een doel lekkage voor dit experiment. Met name, **gekanteld**, aantal **fooien \_** en **totaal \_ bedrag** wordt informatie weer geven over het doel label dat niet beschikbaar is tijdens de test tijd. Deze kolommen worden verwijderd met behulp van de module [select columns in dataset][select-columns] .

  In het volgende diagram ziet u het experiment waarbij wordt voor speld dat bin een tip waarschijnlijk zal vallen. De bakken zijn: klasse 0: Tip = $0, klasse 1: Tip > $0 en tip <= $5, klasse 2: Tip > $5 en tip <= $10, klasse 3: Tip > $10 en tip <= $20 en Class 4: Tip > $20.

  ![Diagram van experiment voor voor speld bin voor Tip](./media/hive-walkthrough/5ztv0n0.png)

  We laten nu zien hoe de echte distributie klasse-verdeling eruit ziet. Klasse 0 en klasse 1 zijn een gang bare, en de andere klassen zijn zeldzaam.

  ![Grafiek van de distributie van test klassen](./media/hive-walkthrough/Vy1FUKa.png)

  b. Voor dit experiment gebruiken we een Verwar ring matrix om de Voorspellings keurigheden te bekijken, zoals hier wordt weer gegeven:

  ![Verwarringsmatrix](./media/hive-walkthrough/cxFmErM.png)

  Hoewel de klasse keurigheden op de voorgangte klassen goed is, heeft het model geen goede taak van ' learning ' in de rarer-klassen.

- **Regressie taak**: voor het voors pellen van de hoeveelheid fooien die voor een reis wordt betaald.

  **Gebruikte informatieer:** Versterkte beslissings structuur

  a. Voor dit probleem is het doel label (of klasse) een **Tip- \_ hoeveelheid**. De doel lekken in dit geval zijn: **gekanteld**, **Tip- \_ klasse** en **totaal \_ bedrag**. Al deze variabelen geven informatie over het fooie bedrag dat doorgaans niet beschikbaar is tijdens het testen. Deze kolommen worden verwijderd met behulp van de module [select columns in dataset][select-columns] .

  Het volgende diagram toont het experiment om de hoeveelheid van de gegeven tip te voors pellen:

  ![Diagram van het experiment om de hoeveelheid fooien te voors pellen](./media/hive-walkthrough/11TZWgV.png)

  b. Voor regressie problemen meten we de keurigheden van de voor spelling door te kijken naar de gekwadrateerde fout in de voor spellingen en de coëfficiënt voor het bepalen van de berekening:

  ![Scherm afbeelding van voorspellings statistieken](./media/hive-walkthrough/Jat9mrz.png)

  Hier is de determinatie coëfficiënt 0,709, wat inhoudt dat ongeveer 71 procent van de afwijking wordt uitgelegd door de model coëfficiënten.

> [!IMPORTANT]
> Zie [Wat is er machine learning](../classic/index.yml)voor meer informatie over machine learning en hoe u deze kunt openen en gebruiken. Daarnaast bevat de [Azure AI Gallery](https://gallery.cortanaintelligence.com/) een kleur omvang van experimenten en een uitgebreide inleiding tot de mogelijkheden van machine learning.
> 
> 

## <a name="license-information"></a>Licentie gegevens
Dit voorbeeld scenario en de bijbehorende scripts worden gedeeld door micro soft onder de MIT-licentie. Zie het **LICENSE.txt** -bestand in de map van de voorbeeld code op github voor meer informatie.

## <a name="references"></a>Referenties
•    [Download pagina voor Andrés Monroy NYCe taxi](https://www.andresmh.com/nyctaxitrips/)  
•    [De taxi-reis gegevens van NYC door Chris Whong te folie](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC van de taxi en limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) van de Commissie

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data