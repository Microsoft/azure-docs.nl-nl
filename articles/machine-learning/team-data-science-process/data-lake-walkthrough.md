---
title: Schaal bare gegevens wetenschap met Azure Data Lake-team data Science process
description: Azure Data Lake gebruiken om gegevens te verkennen en binaire classificatie taken uit te voeren op een gegevensset.
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
ms.openlocfilehash: e6eb0be4d9946907dc5bb2f22b27530a27a37aec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021449"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Schaal bare gegevens wetenschap met Azure Data Lake: een end-to-end-procedure
In dit scenario ziet u hoe u Azure Data Lake kunt gebruiken om gegevens te verkennen en binaire classificatie taken uit te voeren op basis van een steek proef van de NYC taxi en de ritbedrag-gegevensset om te voors pellen of een tip wordt betaald door een tarief. U wordt begeleid bij de stappen van het [team data Science process](./index.yml), end-to-end, van data acquisition to model training en vervolgens naar de implementatie van een webservice die het model publiceert.

## <a name="technologies"></a>Technologieën

Deze technologieën worden in dit scenario gebruikt.
* Azure Data Lake Analytics
* U-SQL en Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
De [Microsoft Azure-data Lake](https://azure.microsoft.com/solutions/data-lake/) biedt alle mogelijkheden die nodig zijn om gegevens wetenschappers eenvoudig te maken voor het opslaan van gegevens van elke grootte, vorm en snelheid, en voor het uitvoeren van gegevens verwerking, geavanceerde analyses en machine learning modellen met een hoge schaal baarheid op een rendabele manier.   U betaalt per taak, alleen wanneer er daad werkelijk gegevens worden verwerkt. Azure Data Lake Analytics bevat U-SQL, een taal die de declaratieve aard van SQL combineert met de uitdrukkings kracht van C# om schaal bare gedistribueerde query mogelijkheden te bieden. Hiermee kunt u ongestructureerde gegevens verwerken door schema toe te passen op lezen, aangepaste logica en door de gebruiker gedefinieerde functies (Udf's) in te voeren en uitbreid baarheid te bieden om nauw keurige controle te krijgen over de uitvoering op schaal. Zie [Visual Studio blog post](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)(Engelstalig) voor meer informatie over het ontwerp van filosofie achter U-SQL.

Data Lake Analytics is ook een belang rijk onderdeel van Cortana Analytics Suite en werkt met Azure Synapse Analytics, Power BI en Data Factory. Deze combi natie biedt een volledige Cloud big data en een geavanceerd analyse platform.

In dit scenario wordt beschreven hoe u de vereiste onderdelen en resources installeert die nodig zijn voor het uitvoeren van taken voor het proces van gegevens wetenschap. Vervolgens worden de gegevens verwerkings stappen beschreven met U-SQL en wordt geconcludeerd door te laten zien hoe u python en Hive kunt gebruiken met Azure Machine Learning Studio (klassiek) om voorspellende modellen te bouwen en te implementeren.

### <a name="u-sql-and-visual-studio"></a>U-SQL en Visual Studio
In dit scenario wordt geadviseerd Visual Studio te gebruiken om U-SQL-scripts te bewerken voor het verwerken van de gegevensset. De U-SQL-scripts worden hier beschreven en opgenomen in een afzonderlijk bestand. Het proces omvat het opnemen, verkennen en bemonsteren van de gegevens. Ook wordt uitgelegd hoe u een U-SQL-script taak uitvoert vanuit het Azure Portal. Er worden Hive-tabellen gemaakt voor de gegevens in een gekoppeld HDInsight-cluster om het maken en implementeren van een binair classificatie model in Azure Machine Learning Studio te vergemakkelijken.

### <a name="python"></a>Python
Dit overzicht bevat ook een sectie waarin wordt uitgelegd hoe u een voorspellend model bouwt en implementeert met behulp van python met Azure Machine Learning Studio. Het biedt een Jupyter-notebook met de python-scripts voor de stappen in dit proces. Het notitie blok bevat code voor een aantal aanvullende functies en modellen van een functie, zoals de classificatie van meerdere klassen en regressie modellen, naast het binaire classificatie model dat hier wordt beschreven. De regressie taak is om de hoeveelheid fooien te voors pellen op basis van andere tip-functies.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (klassiek) wordt gebruikt voor het bouwen en implementeren van voorspellende modellen met behulp van twee benaderingen: eerst met python-scripts en vervolgens met hive-tabellen op een HDInsight-cluster (Hadoop).

### <a name="scripts"></a>Scripts
In dit overzicht worden alleen de belangrijkste stappen beschreven. U kunt het volledige **U-SQL-script** en **Jupyter notebook** downloaden van [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Vereisten
Voordat u met deze onderwerpen begint, moet u het volgende hebben:

* Een Azure-abonnement. Als u er nog geen hebt, raadpleegt u [gratis proef versie van Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Aanbevelingen Visual Studio 2013 of hoger. Als u een van deze versies nog niet hebt geïnstalleerd, kunt u een gratis Community-versie downloaden van de [Visual Studio-Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> In plaats van Visual Studio kunt u ook de Azure Portal gebruiken om Azure Data Lake query's in te dienen. Er worden instructies gegeven over hoe u dit doet met Visual Studio en op de portal in de sectie **proces gegevens met U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Data Science-omgeving voorbereiden voor Azure Data Lake
Maak de volgende resources om de data Science-omgeving voor te bereiden voor deze walkthrough:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob Storage-account
* Azure Machine Learning Studio-account (klassiek)
* Azure Data Lake-Hulpprogram Ma's voor Visual Studio (aanbevolen)

In deze sectie vindt u instructies voor het maken van elk van deze resources. Als u ervoor kiest om Hive-tabellen te gebruiken met Azure Machine Learning in plaats van python, moet u ook een HDInsight-cluster (Hadoop) inrichten om een model te bouwen. Deze alternatieve procedure wordt beschreven in de sectie optie 2.


> [!NOTE]
> De **Azure data Lake Store** kan afzonderlijk worden gemaakt of wanneer u de **Azure data Lake Analytics** maakt als standaard opslag. Er wordt naar instructies verwezen om elk van deze resources afzonderlijk te maken, maar het Data Lake Storage-account hoeft niet afzonderlijk te worden gemaakt.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Een Azure Data Lake Storage maken


Een ADLS maken op basis van de [Azure Portal](https://portal.azure.com). Zie [een HDInsight-cluster met data Lake Store maken met behulp van Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)voor meer informatie. Zorg ervoor dat u de id van het cluster AAD instelt op de Blade **Data Source** van de Blade **optionele configuratie** waarin wordt beschreven.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Een Azure Data Lake Analytics-account maken
Maak een ADLA-account op basis van de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [zelf studie: aan de slag met Azure data Lake Analytics met behulp van Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob-opslag account maken
Maak een Azure Blob-opslag account op basis van de [Azure Portal](https://portal.azure.com). Zie de sectie een opslag account maken in [over Azure Storage-accounts](../../storage/common/storage-account-create.md)voor meer informatie.

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Een Azure Machine Learning Studio-account (klassiek) instellen
Meld u aan bij de [Azure machine learning Studio](https://azure.microsoft.com/services/machine-learning/) -pagina op Azure machine learning Studio (klassiek). Klik op de knop **nu aan de slag** en kies vervolgens een "gratis werk ruimte" of "standaard werkruimte". Nu bent u klaar om experimenten te maken in Azure Machine Learning Studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Azure Data Lake-Hulpprogram Ma's installeren [aanbevolen]
Installeer Azure Data Lake-Hulpprogram Ma's voor uw versie van Visual Studio vanuit [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Nadat de installatie is voltooid, opent u Visual Studio. U ziet nu het tabblad Data Lake het menu bovenaan. Uw Azure-resources moeten in het linkerdeel venster worden weer gegeven wanneer u zich aanmeldt bij uw Azure-account.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>De NYC-gegevensset voor taxi trips
De gegevensset die hier wordt gebruikt, is een openbaar beschik bare gegevensset: de NYC van de [taxi trips](https://www.andresmh.com/nyctaxitrips/). De NYC-gegevens over de taxi bestaan uit ongeveer 20 GB gecomprimeerde CSV-bestanden (~ 48 GB niet-gecomprimeerd), waarbij meer dan 173.000.000 afzonderlijke reizen en de betaalde tarieven voor elke reis worden opgenomen. Elke reis record bevat de locaties en tijden voor ophalen en dropoff, het licentie nummer van geanonimiseerd Hack (rijbewijs) en het Medallion (unieke ID-nummer van de taxi). De gegevens omvatten alle reizen in het jaar 2013 en worden in de volgende twee gegevens sets voor elke maand vermeld:

Het CSV-bestand ' trip_data ' bevat details over reizen, zoals het aantal reizigers, de ophaal-en dropoff punten, de duur van de reis en de lengte van de reis. Hier volgen enkele voorbeeld records:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

De trip_fare CSV bevat details over het tarief dat voor elke reis is betaald, zoals het betalings type, het tarief bedrag, de toeslag en belastingen, fooien en het aantal te betalen bedragen. Hier volgen enkele voorbeeld records:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

De unieke sleutel voor deelname aan reis \_ gegevens en reis \_ tarief bestaat uit de volgende drie velden: Medallion, Hack \_ License en \_ datum/tijd van ophalen. De onbewerkte CSV-bestanden kunnen worden geopend vanuit een Azure Storage blob. Het U-SQL-script voor deze samen voeging bevindt zich in de sectie [koppelings reis en ritbedrag tabellen](#join) .

## <a name="process-data-with-u-sql"></a>Gegevens verwerken met U-SQL
De gegevens verwerkings taken die in deze sectie worden geïllustreerd, zijn opname, kwaliteit controleren, verkennen en bemonsteren van de gegevens. Hoe u kunt toevoegen aan reis-en ritbedrag tabellen, wordt ook weer gegeven. In de laatste sectie wordt een U-SQL-script taak uitgevoerd vanuit het Azure Portal. Hier vindt u koppelingen naar elke Subsectie:

* [Gegevens opname: gegevens uit open bare BLOB lezen](#ingest)
* [Controles van gegevens kwaliteit](#quality)
* [Gegevens verkennen](#explore)
* [Reis-en ritbedrag tabellen samen voegen](#join)
* [Gegevens bemonstering](#sample)
* [U-SQL-taken uitvoeren](#run)

De U-SQL-scripts worden hier beschreven en opgenomen in een afzonderlijk bestand. U kunt de volledige **u-SQL-scripts** downloaden van [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Om U-SQL uit te voeren, opent U Visual Studio, klikt u op **bestand--> nieuw--> project**, kiest **U-SQL project**, naam en slaat u het op in een map.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> U kunt Azure Portal gebruiken om U-SQL uit te voeren in plaats van Visual Studio. U kunt naar de Azure Data Lake Analytics-resource in de portal navigeren en query's rechtstreeks verzenden, zoals wordt geïllustreerd in de volgende afbeelding:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Gegevens opname: gegevens uit open bare BLOB lezen

Er wordt verwezen naar de locatie van de gegevens in de Azure-Blob als **wasb://container- \_ naam \@ BLOB \_ Storage- \_ account \_ name.blob.core.Windows.net/blob_name** en kunnen worden geëxtraheerd met behulp van **Extractors.Csv ()**. Vervang uw eigen container naam en de naam van het opslag account in de volgende scripts voor container \_ name \@ BLOB \_ Storage \_ account \_ name in het adres wasb. Omdat de bestands namen zich in dezelfde indeling bevinden, is het mogelijk **reis \_ gegevens \_ \{ \* \} . CSV** te gebruiken om alle 12 de bestanden te lezen.

```sql
///Read in Trip data
@trip0 =
    EXTRACT
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string
// This is reading 12 trip data from blob
FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
USING Extractors.Csv();
```

Omdat er kopteksten zijn in de eerste rij, moet u de kopteksten verwijderen en de kolom typen wijzigen in de gewenste waarden. U kunt de verwerkte gegevens opslaan op Azure Data Lake Storage met behulp van **swebhdfs://data_lake_storage_name. azuredatalakestorage. net/folder_name/file_name** _ of naar een Azure Blob Storage-account met  **wasb://container_name \@ blob_storage_account_name. blob. core. windows. net/blob_name**.

```sql
// change data types
@trip =
    SELECT
    medallion,
    hack_license,
    vendor_id,
    rate_code,
    store_and_fwd_flag,
    DateTime.Parse(pickup_datetime) AS pickup_datetime,
    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
    Int32.Parse(passenger_count) AS passenger_count,
    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
    Double.Parse(trip_distance) AS trip_distance,
    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
FROM @trip0
WHERE medallion != "medallion";

////output data to ADL
OUTPUT @trip
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
USING Outputters.Csv();

////Output data to blob
OUTPUT @trip
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
USING Outputters.Csv();
```

Op dezelfde manier kunt u de ritbedrag gegevens sets lezen. Klik met de rechter muisknop op Azure Data Lake Storage. u kunt ervoor kiezen om uw gegevens te bekijken in **Azure Portal--> Data Explorer** of **bestanden Verkenner** in Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Controles van gegevens kwaliteit
Nadat reis-en ritbedrag tabellen zijn gelezen in, kunnen de gegevens kwaliteits controles op de volgende manier worden uitgevoerd. De resulterende CSV-bestanden kunnen worden uitgevoerd naar Azure Blob-opslag of Azure Data Lake Storage.

Zoek het aantal Medallions en het unieke aantal Medallions:

```sql
///check the number of medallions and unique number of medallions
@trip2 =
    SELECT
    medallion,
    vendor_id,
    pickup_datetime.Month AS pickup_month
    FROM @trip;

@ex_1 =
    SELECT
    pickup_month,
    COUNT(medallion) AS cnt_medallion,
    COUNT(DISTINCT(medallion)) AS unique_medallion
    FROM @trip2
    GROUP BY pickup_month;
    OUTPUT @ex_1
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
USING Outputters.Csv();
```

Zoek de Medallions die meer dan 100 trips hebben:

```sql
///find those medallions that had more than 100 trips
@ex_2 =
    SELECT medallion,
           COUNT(medallion) AS cnt_medallion
    FROM @trip2
    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
    GROUP BY medallion
    HAVING COUNT(medallion) > 100;
    OUTPUT @ex_2
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
USING Outputters.Csv();
```

Zoek deze ongeldige records in termen van pickup_longitude:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Ontbrekende waarden voor sommige variabelen zoeken:

```sql
//check missing values
@res =
    SELECT *,
           (medallion == null? 1 : 0) AS missing_medallion
    FROM @trip;

@trip_summary6 =
    SELECT
        vendor_id,
    SUM(missing_medallion) AS medallion_empty,
    COUNT(medallion) AS medallion_total,
    COUNT(DISTINCT(medallion)) AS medallion_total_unique
    FROM @res
    GROUP BY vendor_id;
OUTPUT @trip_summary6
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
USING Outputters.Csv();
```

### <a name="data-exploration"></a><a name="explore"></a>Gegevens verkennen
Bekijk enkele gegevens met de volgende scripts om een beter inzicht te krijgen in de gegevens.

De verdeling van gekantelde en niet-gekantelde reizen zoeken:

```sql
///tipped vs. not tipped distribution
@tip_or_not =
    SELECT *,
           (tip_amount > 0 ? 1: 0) AS tipped
    FROM @fare;

@ex_4 =
    SELECT tipped,
           COUNT(*) AS tip_freq
    FROM @tip_or_not
    GROUP BY tipped;
    OUTPUT @ex_4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
USING Outputters.Csv();
```

De verdeling van het fooien-bedrag met afbreek waarden zoeken: 0, 5, 10 en 20 dollar.

```sql
//tip class/range distribution
@tip_class =
    SELECT *,
           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @fare;
@ex_5 =
    SELECT tip_class,
           COUNT(*) AS tip_freq
    FROM @tip_class
    GROUP BY tip_class;
    OUTPUT @ex_5
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
USING Outputters.Csv();
```

Basis statistieken van reis afstand zoeken:

```sql
// find basic statistics for trip_distance
@trip_summary4 =
    SELECT
        vendor_id,
        COUNT(*) AS cnt_row,
        MIN(trip_distance) AS min_trip_distance,
        MAX(trip_distance) AS max_trip_distance,
        AVG(trip_distance) AS avg_trip_distance
    FROM @trip
    GROUP BY vendor_id;
OUTPUT @trip_summary4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
USING Outputters.Csv();
```

De percentielen van de reis afstand zoeken:

```sql
// find percentiles of trip_distance
@trip_summary3 =
    SELECT DISTINCT vendor_id AS vendor,
                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
    FROM @trip;
   // group by vendor_id;
OUTPUT @trip_summary3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
USING Outputters.Csv();
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Reis-en ritbedrag tabellen samen voegen
Reis-en ritbedrag tabellen kunnen worden gekoppeld door Medallion, hack_license en pickup_time.

```sql
//join trip and fare table

@model_data_full =
SELECT t.*,
f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
(f.tip_amount > 0 ? 1: 0) AS tipped,
(f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
FROM @trip AS t JOIN  @fare AS f
ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

//// output to blob
OUTPUT @model_data_full
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
USING Outputters.Csv();

////output data to ADL
OUTPUT @model_data_full
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
USING Outputters.Csv();
```

Bereken voor elk niveau van het aantal reizigers het aantal records, het gemiddelde fooien bedrag, de variantie van het fooien bedrag, het percentage gekantelde reizen.

```sql
// contingency table
@trip_summary8 =
    SELECT passenger_count,
           COUNT(*) AS cnt,
           AVG(tip_amount) AS avg_tip_amount,
           VAR(tip_amount) AS var_tip_amount,
           SUM(tipped) AS cnt_tipped,
           (float)SUM(tipped)/COUNT(*) AS pct_tipped
    FROM @model_data_full
    GROUP BY passenger_count;
    OUTPUT @trip_summary8
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
USING Outputters.Csv();
```

### <a name="data-sampling"></a><a name="sample"></a>Gegevens bemonstering
Selecteer eerst 0,1% van de gegevens uit de gekoppelde tabel, wille keurig.

```sql
//random select 1/1000 data for modeling purpose
@addrownumberres_randomsample =
SELECT *,
        ROW_NUMBER() OVER() AS rownum
FROM @model_data_full;

@model_data_random_sample_1_1000 =
SELECT *
FROM @addrownumberres_randomsample
WHERE rownum % 1000 == 0;

OUTPUT @model_data_random_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
USING Outputters.Csv();
```

Voer vervolgens stratified-steek proeven uit met een binaire variabele tip_class:

```sql
//stratified random select 1/1000 data for modeling purpose
@addrownumberres_stratifiedsample =
SELECT *,
        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
FROM @model_data_full;

@model_data_stratified_sample_1_1000 =
SELECT *
FROM @addrownumberres_stratifiedsample
WHERE rownum % 1000 == 0;
//// output to blob
OUTPUT @model_data_stratified_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
////output data to ADL
OUTPUT @model_data_stratified_sample_1_1000
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>U-SQL-taken uitvoeren
Nadat u-SQL-scripts hebt bewerkt, kunt u deze naar de server verzenden met uw Azure Data Lake Analytics-account. Klik op **Data Lake**, **taak verzenden**, selecteer uw **Analytics-account**, kies **parallellisme** en klik op **verzenden** .

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Wanneer de taak is voltooid, wordt de status van uw taak weer gegeven in Visual Studio voor bewaking. Nadat de taak is voltooid, kunt u het proces voor het uitvoeren van taken zelfs opnieuw uitvoeren en de knel punten voor het verbeteren van de efficiëntie van uw taak bekijken. U kunt ook naar Azure Portal gaan om de status van uw U-SQL-taken te controleren.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nu kunt u de uitvoer bestanden controleren in Azure Blob-opslag of Azure Portal. Gebruik de voorbeeld gegevens van stratified voor onze model lering in de volgende stap.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Modellen bouwen en implementeren in Azure Machine Learning
Er zijn twee opties beschikbaar waarmee u gegevens kunt ophalen in Azure Machine Learning om te bouwen en

* In de eerste optie gebruikt u de voorbeeld gegevens die zijn geschreven naar een Azure-Blob (in de bovenstaande stap voor **gegevens steekproef** ) en gebruikt u python om modellen van Azure machine learning te bouwen en te implementeren.
* In de tweede optie kunt u rechtstreeks een query uitvoeren op de gegevens in Azure Data Lake met behulp van een Hive-query. Voor deze optie moet u een nieuw HDInsight-cluster maken of een bestaand HDInsight-cluster gebruiken waarbij de Hive-tabellen verwijzen naar de NY-taxi gegevens in Azure Data Lake Storage.  Beide opties worden in de volgende secties besproken.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Optie 1: gebruik python om machine learning modellen te bouwen en te implementeren
Als u machine learning modellen wilt bouwen en implementeren met behulp van python, maakt u een Jupyter Notebook op uw lokale machine of in Azure Machine Learning Studio. De Jupyter Notebook in [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) bevat de volledige code voor het verkennen, visualiseren van gegevens, functie techniek, model lering en implementatie. In dit artikel worden alleen de modellen en implementatie besproken.

### <a name="import-python-libraries"></a>Python-bibliotheken importeren
De volgende python-pakketten zijn nodig om de voorbeeld Jupyter Notebook of het python-script bestand uit te voeren. Als u de Azure Machine Learning Notebook-Service gebruikt, zijn deze pakketten vooraf geïnstalleerd.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
import sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from __future__ import division
from sklearn import linear_model
from azureml import services
```

### <a name="read-in-the-data-from-blob"></a>De gegevens uit de BLOB lezen
* Verbindingsreeks

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* In als tekst lezen

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Kolom namen en afzonderlijke kolommen toevoegen

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Enkele kolommen wijzigen in numeriek

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>machine learning modellen bouwen
Hier bouwt u een binair classificatie model om te voors pellen of een reis wordt gekanteld of niet. In de Jupyter Notebook kunt u andere twee modellen vinden: classificaties met meerdere klassen en regressie modellen.

* Eerst moet u dummy variabelen maken die kunnen worden gebruikt in scikit-Learn-modellen

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Gegevens frame maken voor de model lering

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Training en tests 60-40 splitsen

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Logistiek regressie in Trainingsset

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Gegevensset voor het testen van scores

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Metrische gegevens van evaluatie berekenen

  ```python
  fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
  print fpr_train, tpr_train, thresholds_train

  fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
  print fpr_test, tpr_test, thresholds_test

  #AUC
  print metrics.auc(fpr_train,tpr_train)
  print metrics.auc(fpr_test,tpr_test)

  #Confusion Matrix
  print metrics.confusion_matrix(Y_train,Y_train_pred)
  print metrics.confusion_matrix(Y_test,Y_test_pred)
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Web Service-API maken en deze gebruiken in python
U wilt het machine learning model operationeel maken nadat het is gemaakt. Het model voor binaire logistiek wordt hier als voor beeld gebruikt. Zorg ervoor dat de scikit-leer versie op uw lokale computer 0.15.1 is (Azure Machine Learning Studio al ten minste op deze versie).

* Zoek uw werkruimte referenties van Azure Machine Learning Studio (klassieke) instellingen. Klik in azure machine learning Studio op **instellingen**  -->  **naam**  -->  **autorisatie tokens**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Webservice maken

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Referenties voor webservice ophalen

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Roep webservice-API aan. Wacht normaal gesp roken 5-10 seconden na de vorige stap.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Optie 2: modellen rechtstreeks in Azure Machine Learning maken en implementeren
Azure Machine Learning Studio (klassiek) kunnen gegevens rechtstreeks vanuit Azure Data Lake Storage lezen en vervolgens worden gebruikt om modellen te maken en te implementeren. Deze benadering maakt gebruik van een Hive-tabel die verwijst naar de Azure Data Lake Storage. Er moet een afzonderlijk Azure HDInsight-cluster worden ingericht voor de Hive-tabel. 

### <a name="create-an-hdinsight-linux-cluster"></a>Een HDInsight Linux-cluster maken
Een HDInsight-cluster (Linux) maken op basis van de [Azure Portal](https://portal.azure.com). Zie de sectie **een hdinsight-cluster maken met toegang tot Azure data Lake Storage** in [een Hdinsight-cluster maken met data Lake Store met behulp van Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)voor meer informatie.

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Hive-tabel maken in HDInsight
U maakt nu Hive-tabellen die worden gebruikt in Azure Machine Learning Studio (klassiek) in het HDInsight-cluster met behulp van de gegevens die zijn opgeslagen in Azure Data Lake Storage in de vorige stap. Ga naar het HDInsight-cluster dat u hebt gemaakt. Klik op **instellingen**  -->  **Eigenschappen**  -->  **cluster Aad-identiteit**  -->  **ADLS toegang**, Controleer of uw Azure data Lake Storage account is toegevoegd in de lijst met rechten voor lezen, schrijven en uitvoeren.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klik vervolgens op **dash board** naast de knop **instellingen** en een venster dat verschijnt. Klik op **Hive-weer gave** in de rechter bovenhoek van de pagina en de **query-editor** wordt weer gegeven.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Plak de volgende Hive-scripts om een tabel te maken. De locatie van de gegevens bron bevindt zich in Azure Data Lake Storage verwijzing op deze manier: **ADL://data_lake_store_name. azuredatalakestore. net: 443/folder_name/file_name**.

```hiveql
CREATE EXTERNAL TABLE nyc_stratified_sample
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string,
  payment_type string,
  fare_amount string,
  surcharge string,
  mta_tax string,
  tolls_amount string,
  total_amount string,
  tip_amount string,
  tipped string,
  tip_class string,
  rownum string
  )
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';
```

Wanneer de query is voltooid, ziet u de resultaten als volgt:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Modellen bouwen en implementeren in Azure Machine Learning Studio
U bent nu klaar om een model te bouwen en te implementeren waarmee wordt gedicteerd of een tip wordt betaald met Azure Machine Learning. De stratified-voorbeeld gegevens zijn gereed voor gebruik in dit probleem met de binaire classificatie (tip of niet). De voorspellende modellen die gebruikmaken van multiklasses classificatie (tip_class) en regressie (tip_amount) kunnen ook worden gebouwd en geïmplementeerd met Azure Machine Learning Studio, maar hier wordt alleen weer gegeven hoe u het probleem kunt afhandelen met het binaire classificatie model.

1. De gegevens ophalen in Azure Machine Learning Studio (klassiek) met behulp van de module **import data** , die beschikbaar is in de sectie **gegevens invoer en-uitvoer** . Zie de pagina referentie [gegevens module importeren](/azure/machine-learning/studio-module-reference/import-data) voor meer informatie.
2. Selecteer **Hive query** als de **gegevens bron** in het deel venster **Eigenschappen** .
3. Plak het volgende Hive-script in de query-editor van de **Hive-data base**

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Voer de URI van het HDInsight-cluster in (deze URI kan worden gevonden in Azure Portal), Hadoop-referenties, locatie van uitvoer gegevens en Azure Storage account naam/sleutel/container naam.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Een voor beeld van een experiment met binaire classificatie voor het lezen van gegevens uit een Hive-tabel wordt weer gegeven in de volgende afbeelding:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Wanneer het experiment is gemaakt, klikt **u op webservice** voor  -->  **Predictive Web** service instellen

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Het automatisch gemaakte Score experiment uitvoeren, wanneer dit is voltooid, klikt u op **webservice implementeren**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Het web service-dash board wordt binnenkort weer gegeven:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Samenvatting
Door deze procedure uit te voeren, hebt u een Data Science-omgeving gemaakt voor het bouwen van schaal bare end-to-end-oplossingen in Azure Data Lake. Deze omgeving werd gebruikt voor het analyseren van een grote open bare gegevensset, door het gebruik van de canonieke stappen van het data Science-proces, van gegevens overname via model training en vervolgens naar de implementatie van het model als een webservice. U-SQL is gebruikt om de gegevens te verwerken, te verkennen en voor te bereiden. Python en Hive zijn gebruikt met Azure Machine Learning Studio (klassiek) voor het bouwen en implementeren van voorspellende modellen.

## <a name="whats-next"></a>Volgende stappen
Het leer traject voor het [team data Science process (TDSP)](./index.yml) bevat koppelingen naar onderwerpen waarin elke stap in het geavanceerde analyse proces wordt beschreven. Er zijn een aantal instructies op de pagina met procedures voor [team data Science proces](walkthroughs.md) waarin wordt uitgelegd hoe u resources en services gebruikt in verschillende Predictive Analytics scenario's:

* [Het proces van de team data Science in actie: Azure Synapse Analytics gebruiken](sqldw-walkthrough.md)
* [Het proces van de team data Science in actie: HDInsight Hadoop-clusters gebruiken](hive-walkthrough.md)
* [Het team data Science process: met behulp van SQL Server](sql-walkthrough.md)
* [Overzicht van het data Science-proces met behulp van Spark in azure HDInsight](spark-overview.md)