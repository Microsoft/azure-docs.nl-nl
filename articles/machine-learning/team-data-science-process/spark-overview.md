---
title: Data Wetenschappen met Spark op Azure HDInsight-team data Science process
description: De Spark MLlib Toolkit brengt aanzienlijk machine learning model functies in de gedistribueerde HDInsight-omgeving.
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
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314739"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Overzicht van data Science met behulp van Spark in azure HDInsight

In deze suite met onderwerpen wordt uitgelegd hoe u HDInsight Spark kunt gebruiken voor het volt ooien van algemene gegevens Science-taken, zoals gegevens opname, functie techniek, model lering en model evaluatie. De gebruikte gegevens zijn een voor beeld van de 2013-NYC van de taxi en de ritbedrag-gegevensset. De modellen zijn gebaseerd op logistieke en lineaire regressie, wille keurige forests en verlopen structuren. De onderwerpen laten ook zien hoe u deze modellen opslaat in Azure Blob Storage (WASB) en hoe u de voorspellende prestaties kunt beoordelen en evalueren. Meer geavanceerde onderwerpen hebben betrekking op hoe modellen kunnen worden getraind met kruis validatie en Hyper-para meter sweep. In dit overzicht vindt u ook een verwijzing naar de onderwerpen waarin wordt beschreven hoe u het Spark-cluster instelt dat u nodig hebt om de stappen in de opgegeven instructies uit te voeren.

## <a name="spark-and-mllib"></a>Spark en MLlib
[Spark](https://spark.apache.org/) is een open-source framework voor parallelle verwerking dat ondersteuning biedt voor in-Memory verwerking om de prestaties van toepassingen voor de analyse van Big data te verbeteren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. Met de mogelijkheden van de gedistribueerde reken kracht van Spark in het geheugen is het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning-en grafiek berekeningen. [MLlib](https://spark.apache.org/mllib/) is een schaal bare machine learning-bibliotheek van Spark die de algoritmen voor het samen brengen van modellen naar deze gedistribueerde omgeving brengt.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) is de door Azure gehoste aanbieding van open-source Spark. Het biedt ook ondersteuning voor **Jupyter PySpark-notebooks** op het Spark-cluster, waarmee Spark SQL Interactive-query's kunnen worden uitgevoerd voor het transformeren, filteren en visualiseren van gegevens die zijn opgeslagen in azure-blobs (WASB). PySpark is de python-API voor Spark. De code fragmenten die de oplossingen bieden en de relevante grafieken weer geven om de gegevens te visualiseren die hier worden uitgevoerd in Jupyter-notebooks die zijn geïnstalleerd op de Spark-clusters. De stappen voor model lering in deze onderwerpen bevatten code die laat zien hoe u elk type model kunt trainen, evalueren, opslaan en gebruiken.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instellen: Spark-clusters en Jupyter-notebooks
In dit overzicht vindt u instructies en code voor gebruik van een HDInsight Spark 1.6-cluster. Er zijn echter ook Jupyter-notebooks voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Ga naar de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslagplaats met de notebooks voor een beschrijving van de notebooks en koppelingen ernaartoe. De code in dit overzicht en in de gekoppelde notebooks is bovendien generiek en moet op ieder Spark-cluster werken. Als u geen gebruikmaakt van HDInsight Spark, kunnen de stappen voor het instellen en beheren van het cluster enigszins afwijken van wat hier wordt beschreven. Hier volgen de koppelingen naar de Jupyter-notebooks voor Spark 1,6 (om te worden uitgevoerd in de pySpark-kernel van de Jupyter Notebook-server) en Spark 2,0 (wordt uitgevoerd in de pySpark3-kernel van de Jupyter Notebook-server):

### <a name="spark-16-notebooks"></a>Spark 1,6-notebooks
Deze notitie blokken moeten worden uitgevoerd in de pySpark-kernel van Jupyter notebook server.

- [pySpark-machine learning-data-Science-Spark-data-Explore-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): biedt informatie over het verkennen, model leren en scoren van gegevens met verschillende algoritmen.
- [pySpark-machine learning-data-Science-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): bevat onderwerpen in notebook #1 en model ontwikkeling met afstemming tuning en kruislings validatie.
- [pySpark-machine learning-data-wetenschappen-Spark-model-verbruik. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): laat zien hoe u een opgeslagen model kunt operationeel maken met behulp van python op HDInsight-clusters.

### <a name="spark-20-notebooks"></a>Spark 2,0-notebooks
Deze notitie blokken moeten worden uitgevoerd in de pySpark3-kernel van Jupyter notebook server.

- [Spark 2.0-pySpark3-machine-learning-data-Science-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): dit bestand bevat informatie over het verkennen, model leren en bepalen van gegevens in Spark 2,0-clusters met behulp van de NYC taxi trip en ritbedrag data-set die [hier](#the-nyc-2013-taxi-data)wordt beschreven. Dit notitie blok is mogelijk een goed uitgangs punt voor het snel verkennen van de code die u hebt ingesteld voor Spark 2,0. Zie het volgende notitie blok in deze lijst voor een meer gedetailleerde notebook-analyse van de NYC taxi-gegevens. Zie de opmerkingen na deze lijst waarin deze notitie blokken worden vergeleken.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): dit bestand bevat informatie over het uitvoeren van gegevens Wrangling (Spark SQL-en data frame-bewerkingen), exploratie, model leren en scoren met behulp van de NYC-reis-en ritbedrag gegevensverzameling die [hier](#the-nyc-2013-taxi-data)wordt beschreven.
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): dit bestand bevat informatie over het uitvoeren van gegevens Wrangling (Spark SQL en data frame Operations), exploratie, modellering en scoreing met behulp van de bekende luchtvaart maatschappij op tijd van 2011 en 2012. We hebben de gegevensset van de luchtvaart maatschappij geïntegreerd met de weers gegevens van de lucht haven (bijvoorbeeld windspeed, Tempe ratuur, hoogte enz.) voordat ze worden gemodelleerd, zodat deze weers functies kunnen worden opgenomen in het model.

<!-- -->

> [!NOTE]
> De gegevensset van de luchtvaart maatschappij is toegevoegd aan de Spark 2,0-notebooks om het gebruik van classificatie algoritmen beter te illustreren. Raadpleeg de volgende koppelingen voor informatie over de intime-gegevensset voor de verzen ding van de luchtvaart maatschappij en weer gegevensset:
> 
> - Gegevens van de luchtvaart maatschappij op tijd: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Weer gegevens van lucht haven: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> De Spark 2,0-notebooks op de NYC-taxi en vliegtuig vlucht vertraging gegevens sets kunnen 10 min of meer duren om uit te voeren (afhankelijk van de grootte van uw HDI-cluster). In het eerste notitie blok in de bovenstaande lijst ziet u veel aspecten van het verkennen van gegevens, visualisaties en MLs modellen in een notebook dat minder tijd kost om te worden uitgevoerd met een voor beeld van een NYC-gegevensset, waarbij de taxi-en ritbedrag bestanden vooraf zijn gekoppeld: [Spark 2.0-pySpark3-machine-learning-data-Science-Spark-Advanced-Data-Explore-model](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Het volt ooien van deze laptop duurt veel korter (2-3 minuten) en het kan een goed uitgangs punt zijn voor het snel verkennen van de code die wij hebben ingesteld voor Spark 2,0.

<!-- -->

Zie het [spark 1,6-document over verbruik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) voor een voor beeld van een overzicht van de vereiste stappen voor meer informatie over de uitoefening van een Spark 2,0-model en model verbruik voor het scoren. Als u dit voor beeld wilt gebruiken op Spark 2,0, vervangt u het python-code bestand door [dit bestand](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Vereisten

De volgende procedures zijn gerelateerd aan Spark 1,6. Voor de Spark 2,0-versie gebruikt u de notebooks die zijn beschreven en zijn gekoppeld aan eerdere versies.

1. U hebt een abonnement op Azure nodig. Als u er nog geen hebt, raadpleegt u [gratis proef versie van Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. U hebt een Spark 1,6-cluster nodig om deze procedure te volt ooien. Als u een wilt maken, raadpleegt u de instructies in [aan de slag: create Apache Spark op Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Het cluster type en de versie worden opgegeven in het menu **cluster type selecteren** .

![Cluster configureren](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Voor een onderwerp waarin wordt getoond hoe u scala in plaats van python kunt gebruiken om taken voor een end-to-end proces voor gegevens wetenschap te volt ooien, raadpleegt [u de gegevens wetenschap met scala met Spark op Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>De NYC 2013-taxi gegevens
De NYC-gegevens over de taxi zijn ongeveer 20 GB aan gecomprimeerde bestanden met door komma's gescheiden waarden (CSV) (~ 48 GB niet-gecomprimeerd), bestaande uit meer dan 173.000.000 afzonderlijke reizen en de voor elke reis betaalde tarieven. Elke reis record bevat de locatie en tijd van de ophaling en de dropoff, geanonimiseerd Hack (rijbewijs) en Medallion (unieke id van de taxi). De gegevens omvatten alle reizen in het jaar 2013 en worden in de volgende twee gegevens sets voor elke maand vermeld:

1. De CSV-bestanden van de trip_data bevatten reis details, zoals het aantal reizigers, het ophalen en dropoff punten, de duur van de reis en de lengte van de reis. Hier volgen enkele voorbeeld records:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. De CSV-bestanden van de trip_fare bevatten gegevens over het ritbedrag dat voor elke reis is betaald, zoals het betalings type, het tarief bedrag, de toeslag en belastingen, fooien en het aantal te betalen bedragen. Hier volgen enkele voorbeeld records:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

We hebben een voor beeld van een 0,1% van deze bestanden genomen en zijn samengevoegd met de reis \_ gegevens en de \_ CVS-bestanden van het reis tarief in één gegevensset om te gebruiken als de invoer gegevensset voor deze walkthrough. De unieke sleutel voor deelname aan reis \_ gegevens en reis \_ tarief bestaat uit de velden: Medallion, Hack- \_ licentie en \_ datum/tijd van ophalen. Elke record van de gegevensset bevat de volgende kenmerken die een NYC-taxi retour vertegenwoordigen:

| Veld | Korte beschrijving |
| --- | --- |
| medallion |Geanonimiseerde Taxi Medallion (unieke taxi-id) |
| hack_license |Geanonimiseerd Hackney-rijbewijs nummer |
| vendor_id |Id van de taxi leverancier |
| rate_code |NYCe taxi van het tarief |
| store_and_fwd_flag |Vlag voor opslaan en door sturen |
| pickup_datetime |Datum van opname & tijd |
| dropoff_datetime |Dropoff datum & tijd |
| pickup_hour |Een uur ophalen |
| pickup_week |De week van het jaar ophalen |
| dagen |Weekday (bereik 1-7) |
| passenger_count |Aantal reizigers in een taxi |
| trip_time_in_secs |Reis tijd in seconden |
| trip_distance |Reis afstand in mijlen |
| pickup_longitude |Lengte graad ophalen |
| pickup_latitude |Latitude ophalen |
| dropoff_longitude |Lengte graad Dropoff |
| dropoff_latitude |Dropoff Latitude |
| direct_distance |Directe afstand tussen locaties voor ophalen en dropoff |
| payment_type |Betalings type (kas, credit card enz.) |
| fare_amount |Ritbedrag bedrag in |
| toeslag |Toeslag |
| mta_tax |MTA-transport belasting voor metro |
| tip_amount |Fooien hoeveelheid |
| tolls_amount |Aantal niet-gratis |
| total_amount |Totaalbedrag |
| gekanteld |Gekanteld (0/1 voor Nee of Ja) |
| tip_class |Tip-klasse (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Code uitvoeren vanuit een Jupyter-notebook in het Spark-cluster
U kunt de Jupyter Notebook vanuit de Azure Portal starten. Zoek uw Spark-cluster op uw dash board en klik hierop om de beheer pagina voor uw cluster in te voeren. Als u het notitie blok wilt openen dat is gekoppeld aan het Spark-cluster, klikt u op **cluster dashboards**  ->  **Jupyter notebook**.

![Cluster dashboards](./media/spark-overview/spark-jupyter-on-portal.png)

U kunt ook bladeren naar ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** om toegang te krijgen tot de Jupyter-notebooks. Vervang het gedeelte CLUSTERNAME van deze URL door de naam van uw eigen cluster. U hebt het wacht woord voor uw beheerders account nodig om toegang te krijgen tot de notitie blokken.

![Bladeren in Jupyter-notebooks](./media/spark-overview/spark-jupyter-notebook.png)

Selecteer PySpark om een map te bekijken met een aantal voor beelden van vooraf verpakte notitie blokken die gebruikmaken van de PySpark-API. De notitie blokken die de code voorbeelden voor dit pakket met Spark bevatten, zijn beschikbaar op [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

U kunt de notebooks rechtstreeks vanuit [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) uploaden naar de Jupyter notebook-server op uw Spark-cluster. Klik op de start pagina van uw Jupyter op de knop **uploaden** aan de rechter kant van het scherm. Er wordt een Verkenner geopend. Hier kunt u de GitHub-URL (onbewerkte inhoud) van het notitie blok plakken en op **openen** klikken.

U ziet de bestands naam in de lijst met Jupyter bestanden met een knop **uploaden** . Klik op de knop **uploaden** . Nu hebt u het notitie blok geïmporteerd. Herhaal deze stappen om de andere notitie blokken uit deze walkthrough te uploaden.

> [!TIP]
> U kunt met de rechter muisknop op de koppelingen in uw browser klikken en **koppeling kopiëren** selecteren om de GitHub onbewerkte INHOUDS-URL op te halen. U kunt deze URL in het dialoog venster Jupyter Upload File Explorer plakken.
> 
> 

U kunt nu:

* Zie de code door te klikken op het notitie blok.
* Voer elke cel uit door op **SHIFT + ENTER** te drukken.
* Voer het hele notitie blok uit door te klikken op de knop voor het uitvoeren van de **cel**  ->  .
* Gebruik de automatische visualisatie van query's.

> [!TIP]
> De PySpark-kernel visualiseert automatisch de uitvoer van SQL-query's (HiveQL). U krijgt de mogelijkheid om te kiezen uit verschillende soorten visualisaties (tabel, cirkel, lijn, gebied of balk) met behulp van de menu knoppen van het **type** in het notitie blok:
>
>

![Logistieke regressie-curve voor algemene aanpak](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Volgende stappen
Nu u een HDInsight Spark-cluster hebt ingesteld en de Jupyter-notebooks hebt geüpload, kunt u de onderwerpen door lopen die overeenkomen met de drie PySpark-notebooks. Ze laten zien hoe u uw gegevens kunt verkennen en hoe u modellen kunt maken en gebruiken. De geavanceerde laptop voor gegevens exploratie en-modellering laat zien hoe u kruis validatie, Hyper-para meters opruimen en model evaluatie kunt gebruiken.

**Gegevens verkennen en model leren met Spark:** Verken de gegevensset en maak, beoordeel en evalueer de machine learning modellen door te werken met behulp van het onderwerp [binaire classificatie maken en regressie modellen voor gegevens met behulp van de Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

**Model verbruik:** Zie voor meer informatie over het beoordelen van de classificatie en regressie modellen die in dit onderwerp zijn gemaakt, de [Score en evalueren van met Spark ontwikkelde machine learning modellen](spark-model-consumption.md).

**Kruisvalidatie en sweeping van hyperparameters**: Zie [Advanced data exploration and modeling with Spark](spark-advanced-data-exploration-modeling.md) (Geavanceerde gegevensverkenning en -modellering met Spark) over de manier waarop modellen kunnen worden getraind met behulp van kruisvalidatie en sweeping van hyperparameters.