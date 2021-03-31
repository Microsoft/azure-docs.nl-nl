---
title: 'Een model bouwen en implementeren in een SQL Server VM: team data Science process'
description: Bouw en implementeer een machine learning model met behulp van SQL Server op een Azure VM met een openbaar beschik bare gegevensset.
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
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96007406"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Het proces van de team data Science in actie: met behulp van SQL Server
In deze zelf studie doorloopt u het proces van het bouwen en implementeren van een machine learning model met behulp van SQL Server en een openbaar beschik bare gegevensset, de NYC-gegevensset voor de [taxi-trips](https://www.andresmh.com/nyctaxitrips/) . De procedure volgt een standaard werk stroom voor data technologie: de gegevens opnemen en verkennen, functies van de engineer om leren te vergemakkelijken, en vervolgens een model bouwen en implementeren.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beschrijving van gegevensset voor NYCe taxi trips
De NYC-gegevens over de taxi zijn ongeveer 20 GB aan gecomprimeerde CSV-bestanden (~ 48 GB niet-gecomprimeerd), bestaande uit meer dan 173.000.000 afzonderlijke reizen en de voor elke reis betaalde tarieven. Elke reis record bevat de locatie en tijd van de ophaling en de dropoff, geanonimiseerd Hack (rijbewijs) en Medallion (unieke id van de taxi). De gegevens omvatten alle reizen in het jaar 2013 en worden in de volgende twee gegevens sets voor elke maand vermeld:

1. Het CSV-bestand ' trip_data ' bevat details over reizen, zoals het aantal reizigers, de ophaal-en dropoff punten, de duur van de reis en de lengte van de reis. Hier volgen enkele voorbeeld records:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. De trip_fare CSV bevat details over het tarief dat voor elke reis is betaald, zoals het betalings type, het tarief bedrag, de toeslag en belastingen, fooien en het aantal te betalen bedragen. Hier volgen enkele voorbeeld records:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

De unieke sleutel voor deelname aan reis \_ gegevens en reis \_ tarief bestaat uit de velden: Medallion, Hack- \_ licentie en \_ datum/tijd van ophalen.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voor beelden van voorspellings taken
We zullen drie Voorspellings problemen formuleren op basis van de *Tip- \_ hoeveelheid*, namelijk:

* Binaire classificatie: voor spelt of er voor een reis een tip is betaald, dat wil zeggen *een \_ fooiwaarde* van meer dan $0 is een positief voor beeld, terwijl een *tip- \_ bedrag* van $0 een negatief voor beeld is.
* Classificatie met verschillende klassen: om het bereik van fooien voor de reis te voors pellen. We delen het *fooien \_ bedrag* in vijf bakken of klassen:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Regressie taak: voor het voors pellen van de hoeveelheid fooien die voor een reis wordt betaald.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>De Azure data Science-omgeving instellen voor geavanceerde analyse
Zoals u kunt zien in de hand leiding voor het [plannen van uw omgeving](plan-your-environment.md) , zijn er verschillende opties voor het werken met de gegevensset NYCe taxi trips in Azure:

* Werk met de gegevens in azure blobs en vervolgens model in Azure Machine Learning
* De gegevens in een SQL Server-Data Base laden en vervolgens model in Azure Machine Learning

In deze zelf studie wordt gebruikgemaakt van de parallelle bulkimportbewerking van de gegevens tot een SQL Server, het verkennen van functies, het samen stellen van onderdelen en het bemonsteren van SQL Server Management Studio en het gebruik van een IPython-notebook. [Voorbeeld scripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) en [IPython-notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) worden gedeeld in github. Een voor beeld van een IPython-notebook om te werken met de gegevens in azure blobs is ook beschikbaar op dezelfde locatie.

Uw Azure data Science-omgeving instellen:

1. [Een opslagaccount maken](../../storage/common/storage-account-create.md)
2. [Een Azure Machine Learning-werk ruimte maken](../classic/create-workspace.md)
3. [Richt een Data Science virtual machine](../data-science-virtual-machine/overview.md)in dat een SQL Server en een IPython notebook server biedt.
   
   > [!NOTE]
   > De voorbeeld scripts en IPython-notebooks worden tijdens het installatie proces gedownload naar uw virtuele machine voor data technologie. Wanneer het script na de installatie van de virtuele machine is voltooid, zijn de voor beelden in de documenten bibliotheek van uw VM:  
   > 
   > * Voorbeeld scripts: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Voor beelden van IPython-notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   waar `<user_name>` de Windows-aanmeldings naam van uw virtuele machine is. We verwijzen naar de voorbeeld mappen als **voorbeeld scripts** en voor **beelden van IPython-notebooks**.
   > 
   > 

Dit scenario is gebaseerd op de grootte van de gegevensset, de locatie van de gegevens bron en de geselecteerde Azure-doel omgeving en is vergelijkbaar met [scenario \# 5: grote gegevensset in een lokale bestanden, doel SQL Server in azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>De gegevens ophalen uit de open bare bron
Als u de [NYC](https://www.andresmh.com/nyctaxitrips/) van een taxi wilt ophalen uit de open bare locatie, kunt u een van de methoden die worden beschreven in [gegevens verplaatsen van en naar Azure Blob Storage](move-azure-blob.md) gebruiken om de gegevens naar de nieuwe virtuele machine te kopiëren.

De gegevens kopiëren met behulp van AzCopy:

1. Meld u aan bij uw virtuele machine (VM)
2. Een nieuwe map maken in de gegevens schijf van de virtuele machine (Opmerking: gebruik niet de tijdelijke schijf die als een gegevens schijf wordt geleverd bij de virtuele machine).
3. Voer in een opdracht prompt venster de volgende Azcopy-opdracht regel uit en vervang <path_to_data_folder> met uw gegevensmap die is gemaakt in (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Wanneer de AzCopy is voltooid, moet in de map Data een totaal van 24 gecomprimeerde CSV-bestanden (12 voor reis \_ gegevens en 12 voor reis \_ tarief) worden uitgevoerd.
4. De gedownloade bestanden uitpakken. Noteer de map waarin de niet-gecomprimeerde bestanden zich bevinden. In deze map wordt het <pad \_ naar \_ gegevens \_ bestanden genoemd \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Gegevens bulksgewijs importeren in SQL Server Data Base
De prestaties van het laden/overbrengen van grote hoeveel heden gegevens naar een SQL Database en volgende query's kunnen worden verbeterd door *gepartitioneerde tabellen en weer gaven* te gebruiken. In deze sectie volgen we de instructies die worden beschreven in [parallelle bulksgewijze gegevens import met behulp van SQL-partitie tabellen](parallel-load-sql-partitioned-tables.md) om een nieuwe Data Base te maken en de gegevens parallel in gepartitioneerde tabellen te laden.

1. Start **SQL Server Management Studio** als u bent aangemeld bij uw VM.
2. Verbinding maken met behulp van Windows-verificatie.
   
    ![SSMS Connect][12]
3. Als u de SQL Server-verificatie modus nog niet hebt gewijzigd en een nieuwe SQL-aanmeldings gebruiker hebt gemaakt, opent u het script bestand met de naam **Change \_ auth. SQL** in de map **sample scripts** . Wijzig de standaard gebruikers naam en-wacht woord. Klik op **uitvoeren** in de werk balk om het script uit te voeren.
   
    ![Script uitvoeren][13]
4. Controleer en/of wijzig de SQL Server standaard database-en logboek mappen om ervoor te zorgen dat nieuwe data bases worden opgeslagen op een gegevens schijf. De SQL Server VM-installatie kopie die is geoptimaliseerd voor het laden van gegevens opslag is vooraf geconfigureerd met gegevens-en logboek schijven. Als uw virtuele machine geen gegevens schijf bevat en u tijdens het installatie proces van de VM nieuwe virtuele harde schijven hebt toegevoegd, wijzigt u de standaard mappen als volgt:
   
   * Klik met de rechter muisknop op de naam van de SQL Server in het linkerdeel venster en klik op **Eigenschappen**.
     
       ![SQL Server eigenschappen][14]
   * Selecteer **Data Base-instellingen** in de lijst **Selecteer een pagina** aan de linkerkant.
   * Controleer en/of wijzig de **standaard locaties van de data base** naar de locatie van de **gegevens schijf** van uw keuze. Deze locatie is waar nieuwe data bases zich bevinden als ze worden gemaakt met de standaard instellingen.
     
       ![SQL Database standaard instellingen][15]  
5. Als u een nieuwe data base en een set bestands groepen wilt maken voor de gepartitioneerde tabellen, opent u het voorbeeld script **Create \_ DB \_ default. SQL**. Met het script wordt een nieuwe data base gemaakt met de naam **TaxiNYC** en 12 bestands groepen in de standaard locatie van gegevens. Elke bestands groep bevat één maand aan reis \_ gegevens en gegevens over reis \_ ritbedrag. Wijzig de database naam, indien gewenst. Klik op **uitvoeren** om het script uit te voeren.
6. Maak vervolgens twee partitie tabellen, een voor de reis \_ gegevens en een andere voor het reis \_ tarief. Open het voorbeeld script **\_ gepartitioneerde \_ tabel maken. SQL.** dit kan:
   
   * Maak een partitie functie om de gegevens op maand te splitsen.
   * Maak een partitie schema om de gegevens van elke maand toe te wijzen aan een andere bestands groep.
   * Maak twee gepartitioneerde tabellen die zijn toegewezen aan het partitie schema: **nyctaxie \_ trip** bevat de reis \_ gegevens en de **nyctaxi- \_ ritbedrag** houdt rekening met de reis \_ ritbedrag-gegevens.
     
     Klik op **uitvoeren** om het script uit te voeren en de gepartitioneerde tabellen te maken.
7. In de map **voorbeeld scripts** zijn er twee voor beelden van Power shell-scripts voor het demonstreren van parallelle bulk invoer van gegevens aan SQL Server tabellen.
   
   * **BCP \_ parallel \_generic.ps1** is een algemeen script voor het parallel importeren van gegevens in een tabel. Wijzig dit script om de invoer-en doel variabelen in te stellen zoals aangegeven in de opmerkings regels in het script.
   * **BCP \_ parallel \_nyctaxi.ps1** is een vooraf geconfigureerde versie van het algemene script en kan worden gebruikt om beide tabellen te laden voor de gegevens van de NYC taxi-reizen.  
8. Klik met de rechter muisknop op de script naam **BCP \_ parallel \_nyctaxi.ps1** en klik op **bewerken** om deze te openen in Power shell. Controleer de vooraf ingestelde variabelen en wijzig deze op basis van de geselecteerde database naam, invoer gegevensbestand, doelmap voor het doel logboek en de paden naar de voorbeeld bestanden **nyctaxi_trip.xml** en **nyctaxi \_fare.xml** (in de map **voorbeeld scripts** ).
   
    ![Gegevens bulksgewijs importeren][16]
   
    U kunt ook de verificatie modus selecteren, standaard is Windows-verificatie. Klik op de groene pijl in de werk balk om uit te voeren. Met het script worden 24 bulk import bewerkingen parallel uitgevoerd, 12 voor elke gepartitioneerde tabel. U kunt de voortgang van het importeren van gegevens controleren door de SQL Server standaardmap data-map te openen, zoals hierboven is ingesteld.
9. Het Power shell-script rapporteert de begin-en eind tijd. Wanneer alle bulk import is voltooid, wordt de eind tijd gerapporteerd. Controleer de doelmap van het logboek om te controleren of de bulk invoer is geslaagd, dat wil zeggen dat er geen fouten worden gerapporteerd in de doelmap van het logboek.
10. Uw data base is nu klaar voor onderzoek, functie techniek en andere bewerkingen, zoals gewenst. Omdat de tabellen zijn gepartitioneerd op basis van het veld **\_ datum/tijd van ophalen** , hebben query's met de voor waarden voor de leverings **\_ datum** in de component **where** voor deel van het partitie schema.
11. Bekijk in **SQL Server Management Studio** de voorbeeld query's van het voorbeeld script **\_ . SQL**. Als u een van de voorbeeld query's wilt uitvoeren, markeert u de query regels en klikt u op in de werk balk op **uitvoeren** .
12. De NYC-gegevens over taxi reizen worden geladen in twee afzonderlijke tabellen. Voor het verbeteren van de koppelings bewerkingen wordt het ten zeerste aanbevolen om de tabellen te indexeren. In het voorbeeld script wordt een **\_ gepartitioneerde \_ index gemaakt. SQL** maakt gepartitioneerde indexen voor de samengestelde samenvoegings sleutel **Medallion, Hack \_ License en \_ datum/tijd van ophalen**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Het verkennen van gegevens en functies in SQL Server
In deze sectie wordt het verkennen van gegevens en het genereren van onderdelen uitgevoerd door SQL-query's rechtstreeks uit te voeren in het **SQL Server Management Studio** met behulp van de SQL Server-Data Base die u eerder hebt gemaakt. Een voorbeeld script met de naam **voorbeeld \_ query's. SQL** is te zien in de map **voorbeeld scripts** . Wijzig het script om de naam van de data base te wijzigen als deze afwijkt van de standaard waarde: **TaxiNYC**.

In deze oefening gaan we het volgende doen:

* Maak verbinding met **SQL Server Management Studio** met behulp van Windows-verificatie of met behulp van SQL-verificatie en de SQL-aanmeldings naam en het wacht woord.
* Verken gegevens distributies van een paar velden in verschillende tijd Vensters.
* Onderzoek de kwaliteit van de gegevens van de velden lengte graad en breedte graad.
* Genereer binaire en classificatie labels voor multi klassen op basis van het **fooien \_ bedrag**.
* Maak functies en bereken/vergelijk de retour waarden voor de reis.
* Voeg de twee tabellen samen en extraheer een wille keurig voor beeld die wordt gebruikt om modellen te maken.

Wanneer u klaar bent om door te gaan met de Azure Machine Learning, kunt u het volgende doen:  

1. Sla de laatste SQL-query op om de gegevens te extra heren en voor te bereiden en kopieer de query rechtstreeks naar een [import gegevens][import-data] module in azure machine learning, of
2. Behoud de bemonsterde en ontworpen gegevens die u wilt gebruiken voor het maken van modellen in een nieuwe database tabel en gebruik de nieuwe tabel in de module [gegevens importeren][import-data] in azure machine learning.

In deze sectie wordt de laatste query opgeslagen om de gegevens op te halen en voor te bereiden. De tweede methode wordt gedemonstreerd in het gedeelte [voor het verkennen van gegevens en functies in IPython notebook](#ipnb) .

Voor een snelle controle van het aantal rijen en kolommen in de tabellen dat eerder is gevuld met parallelle bulkimportbewerking,

- Rapport aantal rijen in tabel nyctaxi_trip zonder tabel scan: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Rapport aantal kolommen in tabel nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: reis distributie per Medallion
In dit voor beeld wordt de Medallion (taxi getallen) geïdentificeerd met meer dan 100 trips binnen een bepaalde periode. De query zou profiteren van de gepartitioneerde tabel toegang, omdat deze wordt voor bereid op het partitie schema van de **\_ datum van ophalen**. Bij het uitvoeren van query's op de volledige gegevensset wordt ook gebruikgemaakt van de gepartitioneerde tabel en/of index scan.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploratie: reis distributie door Medallion en hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Beoordeling van gegevens kwaliteit: Verifieer records met een onjuiste lengte graad en/of breedte graad
In dit voor beeld wordt onderzocht of een van de lengte-en/of breedte velden een ongeldige waarde bevat (radiale graden moet tussen-90 en 90) zijn of (0, 0) coördinaten hebben.

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Verkennen: gekanteld versus zonder gekantelde trips
In dit voor beeld wordt gezocht naar het aantal trips dat is gekanteld en niet is gekanteld binnen een bepaalde periode (of in de volledige gegevensset als dit het hele jaar bestrijkt). Deze verdeling weerspiegelt de binaire label distributie om later te worden gebruikt voor het model leren van een binaire classificatie.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Exploratie: punt/bereik distributie van fooien
In dit voor beeld wordt de verdeling van penpuntstijl-bereiken in een bepaalde periode (of in de volledige gegevensset voor het hele jaar) berekend. Deze verdeling van de label klassen wordt later gebruikt voor het model leren van een classificatie met multi klassen.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploratie: reis afstand berekenen en vergelijken
In dit voor beeld worden de lengte van de ophaling en de dropoff en de breedte graad van de SQL-locatie geconverteerd naar de geografische locatie verschil en wordt een wille keurig voor beeld geretourneerd van de resultaten voor de vergelijking. In het voor beeld worden de resultaten beperkt tot geldige coördinaten, alleen met behulp van de query voor gegevens kwaliteits beoordeling die eerder is besproken.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Functie techniek in SQL-Query's
De query's voor het genereren van labels en geografie conversie kunnen ook worden gebruikt voor het genereren van labels/functies door het onderdeel tellen te verwijderen. Aanvullende technische SQL-voor beelden voor functies vindt u in de sectie [gegevens verkennen en functie in IPython notebook](#ipnb) . Het is efficiënter om de query's voor het genereren van functies uit te voeren op de volledige gegevensset of een grote subset hiervan met behulp van SQL-query's die rechtstreeks worden uitgevoerd op het SQL Server Data Base-exemplaar. De query's kunnen worden uitgevoerd in **SQL Server Management Studio**, IPython notebook of een ontwikkel hulpprogramma of omgeving dat lokaal of op afstand toegang heeft tot de data base.

#### <a name="preparing-data-for-model-building"></a>Gegevens voorbereiden voor het maken van modellen
Met de volgende query wordt de **nyctaxi- \_ reis** -en **nyctaxi- \_ ritbedrag** tabellen toegevoegd, wordt een binaire classificatie label met een classificatie **\_ klasse** met meerdere klassen **gekanteld** en wordt een wille keurige steek proef van 1% geëxtraheerd uit de verzameling met volledige joins. Deze query kan worden gekopieerd en vervolgens rechtstreeks in de module [Azure machine learning Studio](https://studio.azureml.net) [gegevens importeren][import-data] worden geplakt voor directe gegevens opname vanuit het SQL Server Data Base-exemplaar in Azure. De query sluit records met onjuiste (0, 0) coördinaten toe.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Gegevens exploratie en functie techniek in IPython notebook
In deze sectie wordt het verkennen van gegevens en het genereren van onderdelen uitgevoerd met behulp van python-en SQL-query's voor de SQL Server Data Base die u eerder hebt gemaakt. Een voor beeld van een IPython-notebook met de naam **machine learning-data-Science-process-SQL-Story. ipynb** vindt u in de voor **beeld-IPython-notebooks** . Dit notitie blok is ook beschikbaar op [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Wanneer u met big data werkt, volgt u deze aanbevolen procedure:

* Lees in een klein voor beeld van de gegevens in een gegevens frame in het geheugen.
* Voer enkele visualisaties en onderzoek uit met behulp van de voorbeeld gegevens.
* Experimenteer met functie techniek met behulp van de voorbeeld gegevens.
* Gebruik python om SQL-Query's rechtstreeks uit te geven op de SQL Server-data base in de Azure-VM voor grotere gegevens ontwikkeling, gegevens manipulatie en functie techniek.
* Bepaal de steekproef grootte die moet worden gebruikt voor het bouwen van Azure Machine Learning-modellen.

Wanneer u klaar bent om door te gaan met Azure Machine Learning, kunt u het volgende doen:  

1. Sla de laatste SQL-query op om de gegevens te extra heren en voor te bereiden en kopieer de query rechtstreeks naar een [import gegevens][import-data] module in azure machine learning. Deze methode wordt gedemonstreerd in de sectie [buil ding modellen in azure machine learning](#mlmodel) .    
2. Behoud de voorbeeld gegevens die u wilt gebruiken voor het maken van modellen in een nieuwe database tabel en gebruik vervolgens de nieuwe tabel in de module [gegevens importeren][import-data] .

Hier volgen enkele voor beelden van gegevens ontwikkeling, gegevens visualisatie en feature. Zie voor meer voor beelden het voor beeld van een SQL IPython-notebook in de map **IPython notebooks** .

#### <a name="initialize-database-credentials"></a>Database referenties initialiseren
Initialiseer de instellingen voor de database verbinding in de volgende variabelen:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Database verbinding maken

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapport aantal rijen en kolommen in tabel nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totaal aantal rijen = 173179759  
* Totaal aantal kolommen = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Lees-in een klein gegevens voorbeeld van de SQL Server Data Base

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

De tijd voor het lezen van de voorbeeld tabel is 6,492000 seconden  
Het aantal opgehaalde rijen en kolommen = (84952, 21)

#### <a name="descriptive-statistics"></a>Beschrijvende statistieken
Nu bent u klaar om de voorbeeld gegevens te verkennen. We beginnen met het bekijken van de beschrijvende statistieken voor de volgende **veld (en \_** ):

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Visualisatie: Boxplot-voor beeld
Vervolgens kijken we naar het Boxplot-venster voor de reis afstand om de quantiles te visualiseren

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![#1 afzetten][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisatie: voor beeld van een distributie tekening

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![#2 afzetten][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisatie: balk-en lijn tekenen
In dit voor beeld nemen we de reis afstand op in vijf opslag locaties en visualiseren we de binning-resultaten.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

We kunnen de bovenstaande bin-verdeling in een staaf-of lijn diagram zoals hieronder zetten

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![#3 afzetten][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![#4 afzetten][4]

#### <a name="visualization-scatterplot-example"></a>Visualisatie: scatterplot-voor beeld
Er wordt een spreidings diagram weer gegeven tussen de **\_ duur van de reis tijd \_ in \_ seconden** en **reis \_ afstand** om te zien of er een correlatie is

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![#6 afzetten][6]

Op dezelfde manier kunnen we de relatie tussen **tarief \_ code** en **reis \_ afstand** controleren.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![#8 afzetten][8]

### <a name="sub-sampling-the-data-in-sql"></a>De gegevens in SQL Sub-Sampling
Wanneer u gegevens voorbereidt voor het maken van modellen in [Azure machine learning Studio](https://studio.azureml.net), kunt u ervoor kiezen **om de SQL-query rechtstreeks te gebruiken in de module gegevens importeren** of de ontworpen en voorbeeld gegevens in een nieuwe tabel te bewaren, die u in de module [gegevens importeren][import-data] kunt gebruiken met een eenvoudige **Select * van <de \_ nieuwe \_ tabel \_ naam>**.

In deze sectie maakt u een nieuwe tabel voor de bemonsterde en engineerde gegevens. Een voor beeld van een rechtstreekse SQL-query voor het maken van modellen is te vinden in de sectie [voor het verkennen van gegevens en functies in SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Maak een voorbeeld tabel en vul 1% van de gekoppelde tabellen in. Verwijder eerst de tabel als deze bestaat.
In deze sectie voegen we de **nyctaxi- \_ reis** -en **nyctaxi- \_ ritbedrag** van de tabellen toe, extraheert u een wille keurig voor beeld van 1% en bewaart u de voorbeeld gegevens in een nieuwe tabel naam **nyctaxi \_ een \_ percentage**:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Gegevens verkennen met behulp van SQL-Query's in IPython notebook
In deze sectie verkennen we gegevens distributies met behulp van de 1% voorbeeld gegevens die zijn opgeslagen in de nieuwe tabel die hierboven is gemaakt. Vergelijk bare exploratie kan worden uitgevoerd met behulp van de oorspronkelijke tabellen, eventueel met behulp van **TABLESAMPLE** om het voorbereidings voorbeeld te beperken of door de resultaten te beperken tot een bepaalde periode met de time-out voor het **\_ ophalen** van [SQL Server gegevens](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploratie: dagelijkse distributie van reizen

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploratie: reis distributie per Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Het genereren van functies met SQL-Query's in IPython notebook
In deze sectie genereren we rechtstreeks nieuwe labels en functies met behulp van SQL-query's, die worden uitgevoerd in de voorbeeld tabel van 1% die we in de vorige sectie hebben gemaakt.

#### <a name="label-generation-generate-class-labels"></a>Label genereren: klassen Labels genereren
In het volgende voor beeld genereren we twee sets labels die moeten worden gebruikt voor model lering:

1. Binaire klassen labels worden **gekanteld** (voor speld als er een tip wordt gegeven)
2. Klasse van de **Tip \_** van verschillende klassen (voor speling van de tip bin of het bereik)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Functie techniek: aantal functies voor categorische kolommen
In dit voor beeld wordt een categorische-veld naar een numeriek veld getransformeerd door elke categorie te vervangen door het aantal exemplaren in de gegevens.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Functie techniek: bin-functies voor numerieke kolommen
In dit voor beeld wordt een doorlopend numeriek veld getransformeerd naar een vooraf ingestelde categorie bereik, dat wil zeggen numeriek veld transformeren naar een categorische-veld.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Functie techniek: locatie functies extra heren van decimale breedte graad/lengte graad
In dit voor beeld wordt de decimale weer gave van een breedte-en/of lengte veld verdeeld in meerdere regio velden van verschillende granulariteit, zoals land/regio, plaats, steen, blok, enzovoort. De nieuwe geo-velden zijn niet toegewezen aan de werkelijke locaties. Zie voor meer informatie over het toewijzen van Geocode-locaties [Bing Maps rest-Services](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>De uiteindelijke vorm van de featurized-tabel controleren

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

U kunt nu door gaan met het model leren van het bouwen en model implementeren in [Azure machine learning](https://studio.azureml.net). De gegevens zijn gereed voor eventuele voorspelde problemen die eerder zijn vastgesteld, namelijk:

1. Binaire classificatie: om te voors pellen of er al dan niet een tip voor een reis is betaald.
2. Classificatie met verschillende klassen: om het bereik aan betaalde fooien te voors pellen volgens de eerder gedefinieerde klassen.
3. Regressie taak: voor het voors pellen van de hoeveelheid fooien die voor een reis wordt betaald.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellen bouwen in Azure Machine Learning
Meld u aan bij uw Azure Machine Learning-werk ruimte om de modellerings oefening te starten. Als u nog geen machine learning-werk ruimte hebt gemaakt, raadpleegt u [een Azure machine learning-werk ruimte maken](../classic/create-workspace.md).

1. Zie [Wat is er Azure machine learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) om aan de slag te gaan met Azure machine learning?
2. Meld u aan bij [Azure machine learning Studio](https://studio.azureml.net).
3. Op de start pagina van Studio vindt u een schat aan informatie, Video's, zelf studies, koppelingen naar de referentie modules en andere bronnen. Raadpleeg het [Azure machine learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie over Azure machine learning.

Een typisch Oefen experiment bestaat uit de volgende stappen:

1. Maak een **Nieuw** experiment.
2. De gegevens ophalen die moeten worden Azure Machine Learning.
3. De gegevens vooraf verwerken, transformeren en manipuleren als dat nodig is.
4. Zo nodig functies genereren.
5. Splits de gegevens in sets voor training/validatie/testen (of een afzonderlijke gegevens sets voor elk).
6. Selecteer een of meer machine learning algoritmen, afhankelijk van het probleem dat u wilt oplossen. Bijvoorbeeld binaire classificatie, classificatie met meer klassen, regressie.
7. Train een of meer modellen met behulp van de trainings gegevensset.
8. Een score voor de validatie gegevensset met behulp van de getrainde model (len).
9. Evalueer de model (len) om de relevante metrische gegevens voor het leer probleem te berekenen.
10. Stem de model (len) af en selecteer het beste model om te implementeren.

In deze oefening hebben we de gegevens in SQL Server al verkend en ontworpen, en besloten over de grootte van de steek proef tot opname in Azure Machine Learning. We hebben besloten om een of meer van de Voorspellings modellen te bouwen:

1. De gegevens van Azure Machine Learning ophalen met behulp van [de module gegevens][import-data] **invoer en-uitvoer** die beschikbaar is. Zie de pagina referentie gegevens module [importeren][import-data] voor meer informatie.
   
    ![Gegevens Azure Machine Learning importeren][17]
2. Selecteer **Azure SQL database** als **gegevens bron** in het deel venster **Eigenschappen** .
3. Voer de naam van de data base-DNS in het veld **database server naam** in. Formatteer `tcp:<your_virtual_machine_DNS_name>,1433`
4. Voer de **database naam** in het bijbehorende veld in.
5. Voer de **SQL-gebruikers naam** in de naam van de **Server gebruikers account** en het **wacht woord** in het **wacht woord van de server gebruikers account** in.
7. Plak in het tekst gebied **database query** bewerken de query waarmee de benodigde database velden worden geëxtraheerd (met inbegrip van berekende velden zoals de labels) en druk op voor beelden van de gegevens naar de gewenste steekproef grootte.

Een voor beeld van een experiment met binaire classificatie voor het lezen van gegevens rechtstreeks vanuit de SQL Server Data Base bevindt zich in de onderstaande afbeelding. Vergelijk bare experimenten kunnen worden gebouwd voor classificaties en regressie problemen.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> In de voor beelden van model gegevens extractie en bemonsterings query's in de vorige secties **zijn alle labels voor de drie model oefeningen opgenomen in de query**. Een belang rijke (vereiste) stap in elk van de modellerings oefeningen is het **uitsluiten** van de overbodige labels voor de andere twee problemen en eventuele andere **doel lekkages**. Als u bijvoorbeeld een binaire classificatie gebruikt, gebruikt u **het label en** sluit u de velden **Tip- \_ klasse**, **Tip- \_ hoeveelheid** en **totaal \_ bedrag** uit. Deze laatste zijn doelwit lekkages, omdat ze de fooi hebben betaald.
> 
> Als u onnodige kolommen en/of doel lekkages wilt uitsluiten, kunt u de module [kolommen selecteren in gegevensset][select-columns] of de [meta gegevens bewerken][edit-metadata]gebruiken. Zie [kolommen selecteren in gegevensset][select-columns] en referentie pagina's voor [meta gegevens bewerken][edit-metadata] voor meer informatie.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellen implementeren in Azure Machine Learning
Als uw model klaar is, kunt u het eenvoudig implementeren als een webservice rechtstreeks vanuit het experiment. Zie [een Azure machine learning-webservice implementeren](../classic/deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van Azure machine learning-webservices.

Als u een nieuwe webservice wilt implementeren, moet u het volgende doen:

1. Een score experiment maken.
2. Implementeer de webservice.

Als u een score experiment wilt maken op basis van een **voltooid** trainings experiment, klikt u op Score voor punten **maken** in de onderste actie balk.

![Azure-Score][18]

Azure Machine Learning probeert een score experiment te maken op basis van de onderdelen van het trainings experiment. Met name:

1. Sla het getrainde model op en verwijder de modules voor model trainingen.
2. Identificeer een logische **invoer poort** om het verwachte invoer gegevens schema weer te geven.
3. Identificeer een logische **uitvoer poort** om het verwachte uitvoer schema van de webservice weer te geven.

Wanneer het Score-experiment wordt gemaakt, controleert u dit en past u het naar wens aan. Een typische aanpassing is het vervangen van de invoer-gegevensset en/of de query met één die label velden uitsluit omdat deze labels niet beschikbaar zijn in het schema wanneer de service wordt aangeroepen. Het is ook een goed idee om de grootte van de invoer-gegevensset en/of de query te verkleinen naar enkele records, genoeg om het invoer schema aan te duiden. Voor de uitvoer poort is het gebruikelijk om alle invoer **velden uit te sluiten en alleen** de **gescoorde labels** op te nemen in de uitvoer met behulp van de module [select columns in dataset][select-columns] .

Een voor beeld van een score experiment vindt u in de afbeelding hieronder. Wanneer u klaar bent om te implementeren, klikt u op de knop **PUBLISH web service** in de onderste actie balk.

![Azure Machine Learning publiceren][11]

In deze walkthrough-zelf studie hebt u een Azure data Science-omgeving gemaakt met een grote open bare gegevensset die de gegevens verwervingt voor het model leren van de training en het implementeren van een Azure Machine Learning-webservice.

### <a name="license-information"></a>Licentie gegevens
Deze voorbeeld walkthrough en de bijbehorende scripts en IPython-Notebook (s) worden door micro soft gedeeld onder de MIT-licentie. Controleer het LICENSE.txt-bestand in de map van de voorbeeld code op GitHub voor meer informatie.

### <a name="references"></a>Referenties
•    [Download pagina voor Andrés Monroy NYCe taxi](https://www.andresmh.com/nyctaxitrips/)  
•    [De taxi-reis gegevens van NYC door Chris Whong te folie](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC van de taxi en limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) van de Commissie

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data