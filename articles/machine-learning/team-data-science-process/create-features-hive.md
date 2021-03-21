---
title: 'Functies maken voor gegevens in een Azure HDInsight Hadoop cluster: team data Science process'
description: Voor beelden van Hive-query's waarmee functies worden gegenereerd in gegevens die zijn opgeslagen in een Azure HDInsight Hadoop cluster.
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
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020582"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query's
In dit document wordt beschreven hoe u functies maakt voor gegevens die zijn opgeslagen in een Azure HDInsight Hadoop cluster met behulp van Hive-query's. Deze Hive-query's maken gebruik van Inge sloten Hive User-Defined functions (UDFs), de scripts waarvoor wordt voorzien.

De bewerkingen die nodig zijn om functies te maken, kunnen geheugen intensief zijn. De prestaties van Hive-query's worden kritieker in dergelijke gevallen en kunnen worden verbeterd door bepaalde para meters af te stemmen. De afstemming van deze para meters wordt beschreven in de laatste sectie.

Voor beelden van de query's die worden gepresenteerd, zijn specifiek voor de [NYC-gegevens](https://chriswhong.com/open-data/foil_nyc_taxi/) scenario's in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's hebben al een gegevens schema opgegeven en zijn gereed om te worden verzonden om te worden uitgevoerd. In de laatste sectie zijn para meters die gebruikers kunnen afstemmen, zodat de prestaties van Hive-query's kunnen worden verbeterd.

Deze taak is een stap in het [team data Science process (TDSP)](./index.yml).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure-opslag account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Er is een aangepast Hadoop-cluster ingericht met de HDInsight-service.  Zie [Azure HDInsight Hadoop clusters aanpassen voor geavanceerde analyse](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)als u instructies nodig hebt.
* De gegevens zijn geüpload naar Hive-tabellen in Azure HDInsight Hadoop clusters. Als dat niet het geval is, volgt u [gegevens maken en laden in Hive-tabellen](move-hive-tables.md) om eerst gegevens naar Hive-tabellen te uploaden.
* Externe toegang tot het cluster is ingeschakeld. Zie [toegang tot het hoofd knooppunt van het Hadoop-cluster](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)als u instructies nodig hebt.

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Onderdelen genereren
In deze sectie worden verschillende voor beelden van de manieren waarop functies kunnen worden gegenereerd met hive-query's beschreven. Zodra u extra functies hebt gegenereerd, kunt u deze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en de primaire sleutel, die vervolgens kunnen worden gekoppeld aan de oorspronkelijke tabel. Dit zijn de voor beelden die worden weer gegeven:

1. [Genereren op basis van frequentie onderdelen](#hive-frequencyfeature)
2. [Risico's van Categorische-variabelen in binaire classificatie](#hive-riskfeature)
3. [Het veld datetime extra heren](#hive-datefeatures)
4. [Functies uit het tekst veld extra heren](#hive-textfeatures)
5. [De afstand tussen GPS-coördinaten berekenen](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Genereren op basis van frequentie onderdelen
Het is vaak handig om de frequenties van de niveaus van een variabele categorische te berekenen of de frequentie van bepaalde combi Naties van niveaus uit meerdere Categorische-variabelen. Gebruikers kunnen het volgende script gebruiken om deze frequenties te berekenen:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Risico's van Categorische-variabelen in binaire classificatie
In binaire classificatie moeten niet-numerieke Categorische variabelen worden geconverteerd naar numerieke functies wanneer de modellen die worden gebruikt alleen numerieke functies hebben. Deze conversie wordt uitgevoerd door elk niet-numeriek niveau te vervangen door een numeriek risico. In deze sectie vindt u enkele algemene Hive-query's waarmee de risico waarden (log conflicteert) van een categorische-variabele worden berekend.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

In dit voor beeld `smooth_param1` worden variabelen en `smooth_param2` ingesteld op het vloeiend maken van de risico waarden die worden berekend op basis van de gegevens. Risico's hebben een bereik tussen-inf en inf. Een risico > 0 geeft aan dat de kans dat het doel gelijk is aan 1 groter is dan 0,5.

Nadat de risico tabel is berekend, kunnen gebruikers risico waarden toewijzen aan een tabel door deze te koppelen aan de risico tabel. In de vorige sectie is de Hive-query toegevoegd.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Functies extra heren uit datum/tijd-velden
Hive wordt geleverd met een set UDFs voor het verwerken van datetime-velden. In Hive is de standaard notatie voor datum/tijd ' JJJJ-MM-DD 00:00:00 ' (' 1970-01-01 12:21:32 ' bijvoorbeeld). In deze sectie vindt u voor beelden van het extra heren van de dag van een maand, de maand uit een datum veld, en andere voor beelden die een datetime-teken reeks omzetten in een andere indeling dan de standaard indeling naar een datetime-teken reeks in de standaard indeling.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Deze Hive-query gaat ervan uit dat de *\<datetime field>* in de standaard notatie voor datum/tijd is.

Als een datum veld niet de standaard indeling heeft, moet u eerst het datum/tijd-veld converteren naar een UNIX-tijds tempel en de UNIX-tijds tempel vervolgens converteren naar een datum/tijd-teken reeks in de standaard indeling. Wanneer de datum/tijd de standaard indeling heeft, kunnen gebruikers de Inge sloten datetime-Udf's Toep assen om functies te extra heren.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

In deze query geldt dat als het een *\<datetime field>* patroon heeft dat lijkt op *\<pattern of the datetime field>* *03/26/2015 12:04:39* `'MM/dd/yyyy HH:mm:ss'` . Gebruikers kunnen de app testen

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

De *hivesampletable* in deze query is standaard vooraf geïnstalleerd op alle Azure HDInsight Hadoop clusters wanneer de clusters zijn ingericht.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Functies uit tekst velden extra heren
Als de Hive-tabel een tekst veld bevat met een teken reeks met woorden die worden gescheiden door spaties, haalt de volgende query de lengte van de teken reeks en het aantal woorden in de teken reeks op.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>De afstanden tussen sets van GPS-coördinaten berekenen
De query die in deze sectie wordt gegeven, kan rechtstreeks worden toegepast op de NYC-gegevens van de taxi-reis. Het doel van deze query is om te laten zien hoe een Inge sloten wiskundige functie in Hive moet worden toegepast om functies te genereren.

De velden die in deze query worden gebruikt, zijn de GPS-coördinaten van pickup-en dropoff-locaties, de naam van de *ophaal \_ lengte*, *afhalen \_ breedte* graad, *dropoff- \_ lengte graad* en *dropoff \_ breedte graad*. De query's die de directe afstand berekenen tussen de coördinaten voor ophalen en dropoff zijn:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

De wiskundige vergelijkingen waarmee de afstand tussen twee GPS-coördinaten worden berekend, vindt u op de site met <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Gebeweegde type scripts</a> , ontworpen door Peter Lapisu. In deze Java script is de functie `toRad()` gewoon *lat_or_lon* pi/180, waarmee graden wordt geconverteerd naar radialen. *Lat_or_lon* is hier de breedte graad of lengte graad. Component bevat de functie niet `atan2` , maar biedt de functie `atan` . de `atan2` functie wordt geïmplementeerd door de `atan` functie in de bovenstaande Hive-query met behulp van de definitie in <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Werkruimte maken](./media/create-features-hive/atan2new.png)

Een volledige lijst met Inge sloten Hive-Udf's vindt u in de sectie **ingebouwde functies** op de <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki Apache Hive</a>.  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Geavanceerde onderwerpen: Hive-para meters afstemmen om de query snelheid te verbeteren
De standaard parameter instellingen van het Hive-cluster zijn mogelijk niet geschikt voor de Hive-query's en de gegevens die door de query's worden verwerkt. In deze sectie worden enkele para meters beschreven die gebruikers kunnen afstemmen om de prestaties van Hive-query's te verbeteren. Gebruikers moeten de para meters voor het afstemmen van query's toevoegen vóór de query's voor het verwerken van gegevens.

1. **Java-heapruimte**: voor query's waarbij grote gegevens sets worden samengevoegd of waarmee lange **records worden verwerkt, is een** van de meest voorkomende fouten opgetreden. Deze fout kan worden vermeden door het instellen van de para meters *MapReduce. map. java. kiest* en *MapReduce. Task. io.* . Hier volgt een voorbeeld:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Met deze para meter wordt er 4 GB geheugen toegewezen aan de opslag ruimte voor Java-heaps, en wordt het sorteren efficiënter door meer geheugen toe te wijzen. Het is een goed idee om met deze toewijzingen af te spelen als er sprake is van fout fouten met betrekking tot de heap-ruimte.

1. **Grootte van DFS-blok**: met deze para meter wordt de kleinste eenheid van gegevens ingesteld die het bestands systeem opslaat. Als de grootte van het DFS-blok bijvoorbeeld 128 MB is, worden gegevens met een grootte van minder dan en Maxi maal 128 MB opgeslagen in één blok. Gegevens die groter zijn dan 128 MB, worden toegewezen aan extra blokken. 
2. Het kiezen van een kleine blok grootte leidt tot grote overhead in Hadoop omdat het naam knooppunt veel meer aanvragen moet verwerken om het relevante blok dat bij het bestand hoort te vinden. Een aanbevolen instelling voor het omgaan met gigabytes (of grotere) gegevens is:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. De **samenvoegings bewerking in de Hive wordt geoptimaliseerd**: Hoewel samen voegingen in het Framework van de kaart of het raam werk normaal gesp roken plaatsvinden in de reductie fase, kunnen enorm winsten worden gerealiseerd door het plannen van samen voegingen in de kaart fase (ook wel ' mapjoins ' genoemd). Stel deze optie in:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Opgeven van het aantal mappers naar Hive**: Hoewel Hadoop de gebruiker in staat stelt het aantal verminderers in te stellen, wordt het aantal mappers doorgaans niet ingesteld door de gebruiker. Een slag waarmee u rekening moet houden met een zekere mate van controle op dit nummer is het kiezen van de Hadoop-variabelen *mapred. min. split. size* en *mapred. max. split. size* als de grootte van elke kaart taak wordt bepaald door:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Normaal gesp roken de standaard waarde:
    
   - *mapred. min. split. grootte* is 0, dat van
   - *mapred. max. split. grootte* is **lang. Max** en die van 
   - *DFS. Block. grootte* is 64 MB.

     Zoals we kunnen zien, op basis van de gegevens grootte, met behulp van deze para meters, kunnen we het aantal gebruikte mappers afstemmen.

4. Hier volgen enkele andere **Geavanceerde opties** voor het optimaliseren van Hive-prestaties. Met deze opties kunt u het toegewezen geheugen instellen om taken te koppelen en te verminderen. Dit kan nuttig zijn bij het verfijnen van de prestaties. Houd er rekening mee dat *MapReduce. Reduc. MB* niet groter mag zijn dan de grootte van het fysieke geheugen van elk worker-knoop punt in het Hadoop-cluster.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```