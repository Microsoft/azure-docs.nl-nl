---
title: Azure HDInsight Hadoop cluster op 1-TB gegevensset gebruiken-team data Science process
description: Het team data Science-proces gebruiken voor een end-to-end-scenario dat gebruikmaakt van een HDInsight Hadoop-cluster om een model te bouwen en te implementeren met behulp van een grote (1 TB) openbaar beschik bare gegevensset
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305935"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Het proces voor team gegevens wetenschap in actie-een Azure HDInsight Hadoop cluster op een gegevensset van 1 TB gebruiken

In deze walkthrough wordt gedemonstreerd hoe u het team data Science-proces in een end-to-end-scenario met een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) kunt gebruiken om een van de openbaar beschik bare [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -gegevens sets op te slaan, te verkennen, te bouwen en te bemonsteren. Er wordt gebruikgemaakt van Azure Machine Learning voor het bouwen van een binair classificatie model op basis van deze gegevens. Ook wordt uitgelegd hoe u een van deze modellen publiceert als een webservice.

Het is ook mogelijk om een IPython-notebook te gebruiken voor het uitvoeren van de taken die in dit overzicht worden weer gegeven. Gebruikers die deze aanpak willen proberen, moeten het [Criteo-overzicht raadplegen met behulp van een component ODBC-verbindings](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) onderwerp.

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Beschrijving van de Criteo-gegevensset
De Criteo-gegevens zijn een klik op een voor Spellings-dataset die 370 GB aan gzip gecomprimeerde TSV-bestanden (~ 1,3 TB niet-gecomprimeerd) is, met meer dan 4.300.000.000 records. Het wordt opgehaald van 24 dagen aan geklikte gegevens die beschikbaar zijn gesteld door [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Voor het gemak van gegevens wetenschappers zijn de gegevens die voor ons zijn beschikbaar om te experimenteren, uitgepakt.

Elke record in deze gegevensset bevat 40 kolommen:

* de eerste kolom is een label kolom die aangeeft of een gebruiker op een **add** (waarde 1) klikt of niet op een (waarde 0)
* de volgende 13 kolommen zijn numeriek en
* laatste 26 zijn categorische kolommen

De kolommen zijn geanonimiseerd en gebruiken een reeks genummerde namen: Kol1 (voor de kolom Label) naar Col40 (voor de laatste categorische kolom).

Hier volgt een fragment van de eerste 20 kolommen van twee waarnemingen (rijen) van deze gegevensset:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Er ontbreken waarden in de kolommen numeriek en categorische in deze gegevensset. Een eenvoudige methode voor het afhandelen van de ontbrekende waarden wordt beschreven. Aanvullende details van de gegevens worden onderzocht wanneer ze worden opgeslagen in Hive-tabellen.

**Definitie:** aantal aan */uit (plaats):* deze waarde is het percentage klikken in de gegevens. In deze Criteo-gegevensset bevindt de afdeling zich ongeveer 3,3% of 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voor beelden van voorspellings taken
Er zijn twee voor beelden van voorspellings problemen opgelost in deze walkthrough:

1. **Binaire classificatie** : Hiermee wordt voor speld of een gebruiker op een add:

   * Klasse 0: Nee klikken
   * Klasse 1: Klik op
2. **Regressie** : voor spelt de kans op een AD-klik van de gebruikers functies.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Een HDInsight Hadoop-cluster voor data Science instellen
> [!NOTE]
> Deze stap is doorgaans een **beheer** taak.

Stel uw Azure data Science-omgeving in voor het bouwen van predictive analytics oplossingen met HDInsight-clusters in drie stappen:

1. [Een opslag account maken](../../storage/common/storage-account-create.md): dit opslag account wordt gebruikt om gegevens op te slaan in Azure Blob Storage. De gegevens die in HDInsight-clusters worden gebruikt, worden hier opgeslagen.
2. [Azure HDInsight Hadoop clusters voor data Science aanpassen](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): met deze stap maakt u een Azure HDInsight Hadoop cluster met 64-bits Anaconda python 2,7 geïnstalleerd op alle knoop punten. Er zijn twee belang rijke stappen (beschreven in dit onderwerp) om te volt ooien bij het aanpassen van het HDInsight-cluster.

   * Koppel het opslag account dat u in stap 1 hebt gemaakt met uw HDInsight-cluster wanneer het wordt gemaakt. Dit opslag account wordt gebruikt voor toegang tot gegevens die in het cluster kunnen worden verwerkt.
   * Externe toegang tot het hoofd knooppunt van het cluster inschakelen nadat dit is gemaakt. Onthoud de externe toegangs referenties die u hier opgeeft (anders dan de referenties die zijn opgegeven tijdens het maken van het cluster): Voer de volgende procedures uit.
3. [Een Azure machine learning studio-werk ruimte (klassieke) maken](../classic/create-workspace.md): deze Azure machine learning-werk ruimte wordt gebruikt voor het bouwen van machine learning modellen na een eerste gegevens exploratie en-bemonstering op het HDInsight-cluster.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Gegevens uit een open bare bron ophalen en gebruiken
U kunt de [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -gegevensset openen door op de koppeling te klikken, de gebruiks voorwaarden te accepteren en een naam op te geven. Hier wordt een moment opname weer gegeven:

![Criteo-voor waarden accepteren](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klik op **door gaan om te downloaden** voor meer informatie over de gegevensset en de beschik baarheid ervan.

De gegevens bevinden zich in een [Azure Blob-opslag](../../storage/blobs/storage-quickstart-blobs-dotnet.md) locatie: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . De wasb verwijst naar de locatie van Azure Blob Storage.

1. De gegevens in deze Azure Blob-opslag bestaan uit drie submappen van ongecomprimeerde gegevens.

   1. De submap *RAW/Count/* bevat de eerste 21 dagen aan gegevens-van dag \_ 00 tot dag \_ 20
   2. De submap *RAW/Train/* bestaat uit één dag van de gegevens, dag \_ 21
   3. De submap *RAW/test/* bestaat uit twee dagen aan gegevens, dag \_ 22 en dag \_ 23
2. De onbewerkte gzip-gegevens zijn ook beschikbaar in de hoofdmap *RAW/* als day_NN. gz, waarbij nn van 00 tot 23 loopt.

Een alternatieve benadering voor het openen, verkennen en model leren van deze gegevens waarvoor geen lokale down loads nodig zijn, wordt verderop in dit overzicht uitgelegd wanneer u Hive-tabellen maakt.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Meld u aan bij het cluster hoofd knooppunt
Als u zich wilt aanmelden bij de hoofd knooppunt van het cluster, gebruikt u de [Azure Portal](https://ms.portal.azure.com) om het cluster te vinden. Klik op het pictogram HDInsight Elephant aan de linkerkant en dubbel klik vervolgens op de naam van uw cluster. Ga naar het tabblad **configuratie** , dubbel klik op het pictogram verbinding maken aan de onderkant van de pagina en voer uw referenties voor externe toegang in wanneer u hierom wordt gevraagd, waarbij u naar de hoofd knooppunt van het cluster gaat.

Hier ziet u een typische eerste keer dat u zich aanmeldt voor de cluster hoofd knooppunt:

![Aanmelden bij cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Aan de linkerkant is de ' Hadoop-opdracht regel ', het paard van het verkennen van de gegevens. Let op twee nuttige Url's: "Hadoop-garen status" en "Hadoop-naam knooppunt". De URL van de garen status toont de voortgang van de taak en de naam knooppunt-URL geeft details over de cluster configuratie.

U bent nu klaar om te beginnen met het eerste deel van het scenario: gegevens verkennen met behulp van Hive en gegevens ophalen die gereed zijn voor Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Hive-data base en-tabellen maken
Als u Hive-tabellen voor onze Criteo-gegevensset wilt maken, opent u de * *_Hadoop-opdracht regel_* _ op het bureau blad van het hoofd knooppunt en voert u de Hive-map in door de opdracht in te voeren

```console
cd %hive_home%\bin
```

> [!NOTE]
> Alle Hive-opdrachten uitvoeren in deze walkthrough van de Hive bin/directory-prompt. Dit zorgt ervoor dat er automatisch problemen met paden optreden. U kunt de termen ' hive directory prompt ', ' hive bin/directory prompt ' en ' Hadoop-opdracht regel ' door elkaar gebruiken.
>
> [!NOTE]
> Als u een Hive-query wilt uitvoeren, kan een van de volgende opdrachten altijd worden gebruikt:
>
> `cd %hive_home%\bin`
> `hive`

Nadat de Hive-functie REPL wordt weer gegeven met het teken ' hive > ', knipt en plakt u de query om deze uit te voeren.

Met de volgende code wordt een data base ' Criteo ' gemaakt en worden vervolgens vier tabellen gegenereerd:

_ een *tabel voor het genereren van aantallen* die zijn gebouwd op dagen dag \_ 00 tot en met dag \_ 20,
* een *tabel voor gebruik als de Train-gegevensset die* is gebouwd op dag \_ 21, en
* twee *tabellen die moeten worden gebruikt als de test gegevens sets die* respectievelijk op dag \_ 22 en 23 zijn gebouwd \_ .

Splits de gegevensset van de test in twee verschillende tabellen, omdat een van de dagen een feestdag is. Het doel is om te bepalen of het model verschillen kan detecteren tussen een feestdag en een vrije dag vanaf het door lopen van de klik.

De [voorbeeld script&#95;-hive&#95;&#95;criteo&#95;database&#95;en&#95;Tables maken. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) wordt hier weer gegeven voor het gemak:

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

Al deze tabellen zijn extern, zodat u naar de Azure Blob Storage-locaties (wasb) kunt verwijzen.

**Er zijn twee manieren om een Hive-query uit te voeren:**

* **Met behulp van de Hive-opdracht regel** : de eerste is het uitgeven van de opdracht ' hive ' en het kopiëren en plakken van een query op de Hive-opdracht regel van de component repl:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Nu kunt u op de REPL-opdracht regel de query knippen en plakken uitvoeren.
* **Query's opslaan in een bestand en de opdracht uitvoeren** : de tweede is om de query's op te slaan in een. HQL-bestand (voor [beeld&#95;Hive&#95;&#95;Criteo&#95;data base te maken&#95;en&#95;Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) en voer de volgende opdracht uit om de query uit te voeren:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Data Base en tabel maken bevestigen
Bevestig vervolgens het maken van de data base met de volgende opdracht uit de Hive bin/directory prompt:

```console
hive -e "show databases;"
```

Dit geeft:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Hiermee wordt het maken van de nieuwe Data Base, ' Criteo ' bevestigd.

Als u wilt zien welke tabellen zijn gemaakt, geeft u de opdracht hier uit vanaf de Hive bin/directory prompt:

```output
hive -e "show tables in criteo;"
```

Vervolgens ziet u de volgende uitvoer:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Component gegevens verkennen in
U bent nu klaar om een aantal elementaire gegevens te verkennen in Hive. U begint met het tellen van het aantal voor beelden in de tabellen voor het trainen en testen van gegevens.

### <a name="number-of-train-examples"></a>Aantal voor beelden van treinen
De inhoud van de [voor beeld-&#95;hive&#95;aantal&#95;train&#95;tabel&#95;voor beelden. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) worden hier weer gegeven:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

Dit resulteert in het volgende:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

U kunt ook een van de volgende opdrachten op de Hive bin/directory-prompt geven:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Aantal test voorbeelden in de twee test gegevens sets
Tel nu het aantal voor beelden in de twee test gegevens sets. De inhoud van de [voor beeld-&#95;hive&#95;aantal&#95;criteo&#95;test&#95;dag&#95;22&#95;tabel&#95;voor beelden. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) zijn:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

Dit resulteert in het volgende:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

Op de gebruikelijke manier kunt u het script ook aanroepen vanuit de Hive bin/directory-prompt door de opdracht te geven:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Ten slotte bekijkt u het aantal test voorbeelden in de test-gegevensset op basis van de dag \_ 23.

De opdracht die u hiervoor moet volgen, is vergelijkbaar met de voor beeld die wordt weer gegeven (Raadpleeg de [voorbeeld&#95;hive&#95;aantal&#95;criteo&#95;test&#95;dag&#95;23&#95;voor beelden. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Dit geeft:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Label distributie in de Train-gegevensset
De label distributie in de Train-gegevensset is van belang. Als u dit wilt zien, geeft u de inhoud van de [voorbeeld&#95;-hive&#95;criteo&#95;label&#95;distributie&#95;Table. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Dit geeft de label distributie als resultaat:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

Het percentage positieve labels ligt ongeveer 3,3% (consistent met de oorspronkelijke gegevensset).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distributies van sommige numerieke variabelen in de Train-gegevensset
U kunt de functie ' histogram numeriek ' van de Hive gebruiken \_ om erachter te komen hoe de numerieke variabelen eruitzien. Hier vindt u de inhoud van de [voorbeeld&#95;-hive&#95;criteo&#95;histogram&#95;numeriek. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Dit resulteert in het volgende:

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Met de ZIJDELINGSe weer gave-combi natie in Hive kunt u een SQL-achtige uitvoer maken in plaats van de gebruikelijke lijst. In deze tabel komt de eerste kolom overeen met het bin Center en de tweede naar de frequentie van de opslag locatie.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentielen van sommige numerieke variabelen in de Train-gegevensset benaderen
Het is ook belang rijke percentielen voor het berekenen van de geschatte waarden. Het systeem eigen percentiel van de component \_ bevat dit voor ons. De inhoud van de voor [beeld-&#95;hive&#95;criteo&#95;geschatte&#95;percentielen. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) zijn:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

Dit resulteert in het volgende:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

De verdeling van percentielen is nauw verwant aan de histogram distributie van een wille keurige numerieke variabele.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Zoek het aantal unieke waarden voor sommige categorische-kolommen in de Train-gegevensset
Als u doorgaat met het verkennen van gegevens, kunt u voor sommige categorische-kolommen het aantal unieke waarden berekenen. Als u dit wilt doen, geeft u de inhoud van de [voorbeeld&#95;hive&#95;criteo&#95;unieke&#95;waarden&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

Dit resulteert in het volgende:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 heeft 19M unieke waarden! Het gebruik van Naïve-technieken als ' One-Hot encoding ' voor het coderen van dergelijke hoog-dimensionale Categorische-variabelen is niet haalbaar. Met name een krachtige, robuuste techniek met de naam [leren met aantallen](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) voor het efficiënt aanpakken van dit probleem wordt uitgelegd en gedemonstreerd.

Bekijk ten slotte ook het aantal unieke waarden voor andere categorische-kolommen. De inhoud van de voor [beeld-&#95;hive&#95;criteo&#95;unieke&#95;waarden&#95;meerdere&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) zijn:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

Dit resulteert in het volgende:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Let erop dat, behalve voor Col20, alle andere kolommen veel unieke waarden hebben.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Aantal combi Naties van Categorische-variabelen in de Train-gegevensset

De Count-verdelingen van paren van Categorische-variabelen zijn ook van belang. Dit kan worden bepaald met behulp van de code in de [voorbeeld&#95;hive&#95;criteo&#95;gekoppelde&#95;categorische&#95;counts. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Volg de rang orde van de aantallen op basis van het voorval en Bekijk in dit geval de top 15. Dit geeft ons:

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Voor beeld van de gegevens sets voor Azure Machine Learning
Door de gegevens sets te verkennen en te laten zien hoe u dit type onderzoek kunt doen voor alle variabelen (inclusief combi Naties), zet u een voor beeld van de gegevenssets, zodat modellen in Azure Machine Learning kunnen worden gebouwd. De focus van het probleem is: als er een aantal voorbeeld kenmerken (functie waarden van col2-Col40) wordt gegeven, voors pellen als Kol1 een 0 (geen klik) of een 1 (klik) is.

Als u de gegevens sets van de trein en test op 1% van de oorspronkelijke grootte wilt bemonsteren, gebruikt u de functie systeem eigen RAND () van de component. Het volgende script, [voorbeeld&#95;-hive&#95;criteo&#95;downsamplen&#95;train&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) doet dit voor de Train-gegevensset:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

Dit resulteert in het volgende:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

Het [voorbeeld script&#95;hive&#95;criteo&#95;down sampling&#95;test&#95;dag&#95;22&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) voor test gegevens, dag \_ 22:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

Dit resulteert in het volgende:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Ten slotte wordt het script [voorbeeld&#95;hive&#95;criteo&#95;down sampling&#95;test&#95;dag&#95;23&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) voor test gegevens, dag \_ 23:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

Dit resulteert in het volgende:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

U bent nu klaar om onze voor beeld-en test gegevens sets te gebruiken voor het bouwen van modellen in Azure Machine Learning.

Er is een laatste belang rijk onderdeel voordat u verdergaat op Azure Machine Learning, wat betrekking heeft op de tabel Count. In de volgende Subsectie wordt de tabel Count besproken.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> Een korte discussie over de tabel aantal
Zoals u hebt gezien, hebben verschillende categorische-variabelen een hoge dimensionaliteit. In de walkthrough wordt een krachtige techniek genoemd [met de naam learning met aantallen](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) om deze variabelen op een efficiënte en robuuste manier te coderen. Meer informatie over deze techniek vindt u in de koppeling.

>[!NOTE]
>In dit scenario is de focus gericht op het gebruik van Count-tabellen voor het produceren van compacte representatieve categorische-functies. Dit is niet de enige manier om categorische-functies te coderen. voor meer informatie over andere technieken kunnen geïnteresseerde gebruikers de [hashing](https://en.wikipedia.org/wiki/Feature_hashing)met [één Hot-encoding](https://en.wikipedia.org/wiki/One-hot) en functie controleren.
>

Gebruik de gegevens in de map RAW/Count om tellings tabellen te maken op basis van de hoeveelheid gegevens. In de sectie model lering worden gebruikers weer gegeven hoe u deze aantal tabellen kunt bouwen voor categorische-functies, of om een vooraf gemaakte telling tabel te gebruiken voor hun onderzoek. In de volgende gevallen wordt bij het gebruik van ' vooraf gemaakte tellingen tabellen ' verwezen naar de aantal tabellen die zijn verschaft. Gedetailleerde instructies voor toegang tot deze tabellen vindt u in de volgende sectie.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Een model bouwen met Azure Machine Learning
Het bouw proces voor modellen in Azure Machine Learning volgt deze stappen:

1. [De gegevens uit Hive-tabellen ophalen in Azure Machine Learning](#step1)
2. [Het experiment maken: de gegevens opschonen en maken van een functie met aantal tabellen](#step2)
3. [Het model bouwen, trainen en beoordelen](#step3)
4. [Het model evalueren](#step4)
5. [Het model publiceren als een webservice](#step5)

Nu bent u klaar om modellen te bouwen in Azure Machine Learning Studio. De gegevens van een voor beeld worden opgeslagen als Hive-tabellen in het cluster. Gebruik de module Azure Machine Learning **gegevens importeren** om deze gegevens te lezen. De referenties voor toegang tot het opslag account van dit cluster zijn op de volgende manier beschikbaar.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> Stap 1: gegevens uit Hive-tabellen ophalen in Azure Machine Learning met behulp van de module gegevens importeren en deze selecteren voor een machine learning experiment
Begin met het selecteren van een leeg experiment met **+ nieuwe**  ->  **experimenten**  ->  **Blank Experiment**. Zoek vervolgens **in het zoekvak** linksboven op ' gegevens importeren '. Sleep de module **gegevens importeren** naar het canvas op het experiment (het middelste gedeelte van het scherm) om de module voor gegevens toegang te gebruiken.

Dit is de manier waarop de **import gegevens** eruitzien tijdens het ophalen van gegevens uit de Hive-tabel:

![Gegevens importeren gegevens ophalen](./media/hive-criteo-walkthrough/i3zRaoj.png)

Voor de module **gegevens importeren** zijn de waarden van de para meters die in de afbeelding worden opgegeven, alleen voor beelden van de sorteer waarden die u moet opgeven. Hier volgen enkele algemene richt lijnen voor het invullen van de para meters die zijn ingesteld voor de module **gegevens importeren** .

1. Kies Hive-query voor de **gegevens bron**
2. In het **query vak Hive-data base** is dit een eenvoudige SELECT * van <de naam van uw \_ Data Base \_ . de \_ tabel \_ naam>-is voldoende.
3. **URI van Hcatalog-server** : als uw cluster ' ABC ' is, is dit simpelweg: https://ABC.azurehdinsight.net
4. **Hadoop-gebruikers accountnaam** : de gebruikers naam die is gekozen op het moment van het cluster. (Niet de gebruikers naam voor externe toegang.)
5. **Hadoop-gebruikers account wacht woord** : het wacht woord voor de gebruikers naam die is gekozen op het moment dat het cluster wordt ingesteld. (Niet het wacht woord voor externe toegang!)
6. **Locatie van uitvoer gegevens** : Kies Azure
7. **Azure Storage account naam** : het opslag account dat aan het cluster is gekoppeld
8. **Azure Storage account sleutel** : de sleutel van het opslag account dat aan het cluster is gekoppeld.
9. **Azure-container naam** : als de cluster naam "ABC" is, is dit gewoon "ABC", meestal.

Zodra de gegevens van de **import bewerking** zijn voltooid (u ziet het groene streepje in de module), slaat u deze gegevens op als een gegevensset (met een naam van uw keuze). Dit ziet er als volgt uit:

![Gegevens importeren gegevens opslaan](./media/hive-criteo-walkthrough/oxM73Np.png)

Klik met de rechter muisknop op de uitvoer poort van de module **import data** . Hiermee wordt de optie **Opslaan als gegevensset** en een optie voor **visualiseren** onthuld. Als u op de knop **visualiseren** klikt, worden 100 rijen van de gegevens weer gegeven, samen met een rechter paneel dat nuttig is voor een aantal samenvattings statistieken. Als u gegevens wilt opslaan, selecteert u **Opslaan als gegevensset** en volgt u de instructies.

Als u de opgeslagen gegevensset voor gebruik in een machine learning experiment wilt selecteren, zoekt u de gegevens sets op in het **zoekvak** dat wordt weer gegeven in de volgende afbeelding. Typ vervolgens de naam die u voor de gegevensset hebt opgegeven, gedeeltelijk om deze te openen en sleep de gegevensset naar het hoofd paneel. Als u het hoofd paneel neerzet, wordt dit geselecteerd voor gebruik in machine learning model lering.

![Gegevensset naar het hoofd paneel slepen](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Doe dit voor zowel de trein als de test gegevens sets. Vergeet ook niet de naam van de data base en de tabel namen te gebruiken die u voor dit doel hebt opgegeven. De waarden die in de afbeelding worden gebruikt, zijn alleen bedoeld voor afbeeldings doeleinden. * *
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> Stap 2: een experiment maken in Azure Machine Learning om te voors pellen klikken/geen klikken
Onze Azure Machine Learning Studio (klassiek) experiment ziet er als volgt uit:

![Machine Learning experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Bekijk nu de belangrijkste onderdelen van dit experiment. Sleep onze opgeslagen trein-en test gegevens sets eerst op het canvas van het experiment.

#### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen
De module **clean Missing Data** bevat de naam die wordt voorgesteld: er worden ontbrekende gegevens opgeschoond op manieren die door de gebruiker kunnen worden opgegeven. Bekijk deze module om dit te bekijken:

![Ontbrekende gegevens opschonen](./media/hive-criteo-walkthrough/0ycXod6.png)

Hier kunt u alle ontbrekende waarden vervangen door 0. Er zijn ook andere opties, die kunnen worden weer gegeven door te kijken naar de vervolg keuzelijsten in de module.

#### <a name="feature-engineering-on-the-data"></a>Functie techniek voor de gegevens
Er kunnen miljoenen unieke waarden zijn voor sommige categorische-functies van grote gegevens sets. Het gebruik van Naïve-methoden, zoals een hot-code ring voor het vertegenwoordigen van dergelijke verlaagde categorische-functies, is volledig niet haalbaar. In dit scenario ziet u hoe u met behulp van ingebouwde Azure Machine Learning modules de functie aantal kunt gebruiken om compacte voors tellingen van deze high-dimensiond Categorische-variabelen te genereren. Het eind resultaat is een kleinere model grootte, snellere opleidings tijden en prestatie gegevens die vergelijkbaar zijn met het gebruik van andere technieken.

##### <a name="building-counting-transforms"></a>Trans formaties voor het tellen van aantallen
Als u functies wilt maken, gebruikt u de module **Build Counting Transform** die beschikbaar is in azure machine learning. De module ziet er als volgt uit:

![De module eigenschappen van het tellen van de trans ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 formatie bouwen ![](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Voer in het vak **aantal kolommen** de kolommen in waarvoor u de aantallen wilt uitvoeren. Normaal gesp roken zijn dit (zoals vermeld) hoog-dimensionale categorische-kolommen. Houd er rekening mee dat de Criteo-gegevensset 26 categorische-kolommen heeft: van Col15 tot Col40. Hier telt het aantal op alle objecten en geven ze hun indices (van 15 tot 40 gescheiden door komma's zoals weer gegeven).
>

Als u de module in de MapReduce-modus (geschikt voor grote gegevens sets) wilt gebruiken, moet u toegang hebben tot een HDInsight Hadoop-cluster (de optie die wordt gebruikt voor het verkennen van functies kan ook voor dit doel worden gebruikt) en de bijbehorende referenties. In de voor gaande cijfers ziet u hoe de ingevulde waarden eruitzien (Vervang de waarden die zijn opgegeven voor de afbeelding met die van toepassing op uw eigen gebruiks voorbeeld).

![Module parameters](./media/hive-criteo-walkthrough/05IqySf.png)

In de voor gaande afbeelding ziet u hoe u de locatie van de invoer-BLOB opgeeft. Op deze locatie zijn de gegevens gereserveerd voor het maken van aantal tabellen.

Als deze module is voltooid, slaat u de trans formatie voor later op door met de rechter muisknop op de module te klikken en de optie **Opslaan als Transform** te selecteren:

![Optie Opslaan als trans formatie](./media/hive-criteo-walkthrough/IcVgvHR.png)

In onze experiment-architectuur die hierboven wordt weer gegeven, komt de gegevensset ' ytransform2 ' precies overeen met een trans formatie van het aantal opgeslagen aantallen. Voor de rest van dit experiment wordt ervan uitgegaan dat de lezer een Transform-module voor het **tellen** van gegevens heeft gebruikt voor het genereren van aantallen en vervolgens die aantallen kan gebruiken voor het genereren van aantal functies voor de gegevens sets voor de trein en test.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Opgeven welke onderdelen van aantal moeten worden opgenomen als onderdeel van de gegevens sets voor de trein en test
Zodra een aantal trans formatie is voltooid, kan de gebruiker kiezen welke functies er moeten worden opgenomen in de Train-en test gegevens sets met behulp van de module **aantal tabel parameters wijzigen** . Voor de volledigheid wordt deze module hier weer gegeven. Maar in het belang van eenvoud gebruiken we deze niet in het experiment.

![Para meters voor aantal tabel wijzigen](./media/hive-criteo-walkthrough/PfCHkVg.png)

In dit geval, zoals kan worden weer gegeven, moeten de conflicteert worden gebruikt en wordt de back-upkolom genegeerd. U kunt ook para meters instellen, zoals de drempel waarde voor garbagecollection, hoeveel pseudo-eerdere voor beelden u wilt toevoegen voor vloeiende en of u een Laplacian-ruis wilt gebruiken of niet. Dit zijn allemaal geavanceerde functies en er wordt opgemerkt dat de standaard waarden een goed uitgangs punt zijn voor gebruikers die geen ervaring hebben met dit type functie generatie.

##### <a name="data-transformation-before-generating-the-count-features"></a>Gegevens transformatie voordat de functies Count worden gegenereerd
Nu is de focus gericht op een belang rijk punt over het transformeren van onze Train-en test gegevens voordat het aantal functies daad werkelijk wordt gegenereerd. Er worden twee **R-script modules uitgevoerd** die worden gebruikt voordat de Count-trans formatie op onze gegevens wordt toegepast.

![R-script modules uitvoeren](./media/hive-criteo-walkthrough/aF59wbc.png)

Dit is het eerste R-script:

![Eerste R-script](./media/hive-criteo-walkthrough/3hkIoMx.png)

Met dit R-script worden de namen van de kolommen in ' Kol1 ' gewijzigd in ' Col40 '. Dit komt doordat de Count-trans formatie namen van deze indeling verwacht.

Het tweede R-script houdt de verdeling over van positieve en negatieve klassen (respectievelijk klassen 1 en 0) door lagere steek proeven voor de negatieve klasse. In het R-script ziet u hoe u dit doet:

![Tweede R-script](./media/hive-criteo-walkthrough/91wvcwN.png)

In dit eenvoudige R-script wordt de ' POS \_ NEG- \_ verhouding ' gebruikt om de hoeveelheid saldo tussen de positieve en negatieve klassen in te stellen. Dit is belang rijk om te doen omdat het verbeteren van de klasse niet normaal gesp roken prestatie voordelen biedt voor classificatie problemen waarbij de klasse-distributie wordt schuingetrokken (in dit geval is er 3,3% positieve klasse en 96,7% negatieve klasse).

##### <a name="applying-the-count-transformation-on-our-data"></a>Het aantal trans formatie op de gegevens Toep assen
Ten slotte kunt u de module **trans formatie Toep assen** gebruiken om het aantal transformaties op onze trein-en test gegevens sets toe te passen. Deze module neemt de trans formatie van het opgeslagen aantal als één invoer en de gegevens sets voor de trein of test als de andere invoer en retourneert gegevens met de functies aantal. Dit wordt hier weer gegeven:

![Transformatie module Toep assen](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Een uittreksel van de functies van het aantal
Het is een goed doel om te zien wat de aantal functies in ons geval zien. Hier volgt een fragment van dit:

![Aantal functies](./media/hive-criteo-walkthrough/FO1nNfw.png)

Dit fragment toont aan dat voor de kolommen die worden geteld op, de aantallen en logboek conflicteert naast alle relevante backoffs worden weer gegeven.

U bent nu klaar om een Azure Machine Learning model te bouwen met behulp van deze getransformeerde gegevens sets. In de volgende sectie ziet u hoe dit kan worden gedaan.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> Stap 3: het model bouwen, trainen en beoordelen

#### <a name="choice-of-learner"></a>Keuze van de kenniser
Eerst moet u een leerer kiezen. Gebruik een geboostte beslissings structuur met twee klassen als onze kenniser. Dit zijn de standaard opties voor deze kenniser:

![Two-Class versterkte para meters voor de beslissings structuur](./media/hive-criteo-walkthrough/bH3ST2z.png)

Kies voor het experiment de standaard waarden. De standaard waarden zijn nuttig en zijn een goede manier om snelle basis lijnen voor prestaties te krijgen. U kunt de prestaties verbeteren door para meters te verruimen als u een basis lijn hebt gekozen.

#### <a name="train-the-model"></a>Het model trainen
Voor training kunt u gewoon een **Train model** -module aanroepen. De twee invoer waarden zijn de Two-Class versterkte beslissings structuur leren en onze Train-gegevensset. Dit wordt hier weer gegeven:

![Train model-module](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Het model beoordelen
Zodra u een getraind model hebt, bent u klaar om de test gegevensset te beoordelen en de prestaties te evalueren. Doe dit met behulp van de module **score model** die in de volgende afbeelding wordt weer gegeven, samen met de module **Evaluate model** :

![De module Score Model (Scoremodel)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> Stap 4: het model evalueren
Ten slotte moet u de model prestaties analyseren. Normaal gesp roken is een goede meting voor problemen met twee klasse (binaire)-classificatie de AUC. Als u deze curve wilt visualiseren, koppelt u de module **score model** aan een module **Evaluate model** . Als u op **visualiseren** in de module **Evaluate model** klikt, wordt er een afbeelding weer gegeven zoals in het volgende voor beeld:

![Het BDT-model van de module evalueren](./media/hive-criteo-walkthrough/0Tl0cdg.png)

In binaire classificatie problemen (of twee klassen) is een goede meting van de nauw keurigheid van voor spellingen de Opper vlakte onder curve (AUC). In de volgende sectie vindt u de resultaten met behulp van dit model in onze test-gegevensset. Klik met de rechter muisknop op de uitvoer poort van de module **Evaluate model** en klik vervolgens op **visualiseren**.

![Visualiseren-model module evalueren](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> Stap 5: het model publiceren als een webservice
De mogelijkheid om een Azure Machine Learning model te publiceren als webservices met een minimum van Fuss is een waardevol onderdeel om het algemeen beschikbaar te maken. Als dat is gebeurd, kan iedereen aanroepen naar de webservice met invoer gegevens waarvoor ze voor spellingen nodig hebben. de webservice gebruikt het model om deze voor spellingen te retour neren.

Sla eerst ons getrainde model op als een getraind model object door met de rechter muisknop op de module **Train model** te klikken en de optie **Opslaan als opgeleid model** te gebruiken.

Maak vervolgens een invoer-en uitvoer poort voor onze webservice:

* een invoer poort haalt gegevens op hetzelfde formulier op als de gegevens waarvoor u voor spellingen nodig hebt voor
* een uitvoer poort retourneert de gescoorde labels en de bijbehorende kansen.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecteer een paar rijen met gegevens voor de invoer poort
Het is handig om een module voor het **Toep assen van SQL-trans formatie** te gebruiken om slechts 10 rijen te selecteren die als invoer poort gegevens dienen. Selecteer alleen deze gegevens rijen voor onze invoer poort met behulp van de SQL-query die hier wordt weer gegeven:

![Invoer poort gegevens](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webservice
U bent nu klaar om een klein experiment uit te voeren dat kan worden gebruikt voor het publiceren van onze webservice.

#### <a name="generate-input-data-for-webservice"></a>Invoer gegevens voor de webservice genereren
Als een zeroth-stap, omdat de Count-tabel groot is, neemt u enkele regels test gegevens en genereert u uitvoer gegevens van deze met aantal functies. Deze uitvoer kan fungeren als de invoer gegevens indeling voor onze webservice, zoals hier wordt weer gegeven:

![BDT-invoer gegevens maken](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Gebruik de uitvoer van de module **Count Featurizer** voor de indeling van de invoer gegevens. Zodra dit experiment is uitgevoerd, slaat u de uitvoer van de module **Count Featurizer** op als een gegevensset. Deze gegevensset wordt gebruikt voor de invoer gegevens in de webservice.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Score experiment voor het publiceren van webservice
Eerst is de essentiële structuur een module van een **score model** waarmee ons getrainde model object en enkele regels met invoer gegevens die in de vorige stappen zijn gegenereerd met behulp van de module **Count Featurizer** worden geaccepteerd. Gebruik ' select columns in Dataset ' om de gescoorde labels en de Score kansen te projecteren.

![Kolommen in gegevensset selecteren](./media/hive-criteo-walkthrough/kRHrIbe.png)

U ziet hoe de module **select columns in dataset** kan worden gebruikt voor het filteren van gegevens uit een gegevensset. De inhoud wordt hier weer gegeven:

![Filteren met de module select columns in DataSet](./media/hive-criteo-walkthrough/oVUJC9K.png)

Als u de blauwe invoer-en uitvoer poorten wilt ophalen, klikt u op **webservice voorbereiden** onder aan de rechter kant. Als u dit experiment uitvoert, kunnen we ook de webservice publiceren: Klik op het pictogram **PUBLISH web service** in de rechter benedenhoek, zoals hier wordt weer gegeven:

![Webservice publiceren](./media/hive-criteo-walkthrough/WO0nens.png)

Zodra de webservice is gepubliceerd, wordt u omgeleid naar een pagina die er zo uitziet:

![Dashboard van de webservice](./media/hive-criteo-walkthrough/YKzxAA5.png)

Let op de twee koppelingen voor webservices aan de linkerkant:

* De **aanvraag/antwoord** service (of rr's) is bedoeld voor enkele voor spellingen en is in deze workshop gebruikt.
* De **batch EXECUTION** service (BES) wordt gebruikt voor batch voorspellingen en vereist dat de invoer gegevens die worden gebruikt voor het maken van voor spellingen zich bevinden in Azure Blob Storage.

Als u op de koppelings **aanvraag/antwoord** klikt, gaat u naar een pagina die de code voor de pre-blik in C#, python en R bevat. Deze code kan gemakkelijk worden gebruikt voor het maken van aanroepen naar de webservice. De API-sleutel op deze pagina moet worden gebruikt voor verificatie.

Het is handig om deze python-code te kopiëren naar een nieuwe cel in de IPython-notebook.

Hier volgt een segment van de python-code met de juiste API-sleutel.

![Python-code](./media/hive-criteo-walkthrough/f8N4L4g.png)

De standaard-API-sleutel is vervangen door de API-sleutel van onze webservice. Als u in deze cel in een IPython-notebook op **uitvoeren** klikt, wordt het volgende antwoord opgeleverd:

![IPython-antwoord](./media/hive-criteo-walkthrough/KSxmia2.png)

Voor de twee test voorbeelden die worden gevraagd in het schema van het python-script, krijgt u terug-antwoorden in de vorm ' gescoorde labels, gescoorde kansen '. In dit geval zijn de standaard waarden gekozen. de vooraf ingecheckte code levert (0 voor alle numerieke kolommen en de teken reeks "waarde" voor alle kolommen van categorische).

In deze walkthrough ziet u hoe u met Azure Machine Learning een grootschalige gegevensset kunt verwerken. U bent begonnen met een terabyte aan gegevens, een Voorspellings model gemaakt en deze als een webservice in de Cloud geïmplementeerd.