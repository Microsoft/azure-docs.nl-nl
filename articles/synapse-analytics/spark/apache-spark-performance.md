---
title: Spark-taken voor prestaties optimaliseren
description: Dit artikel bevat een inleiding tot Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96450929"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Apache Spark-taken optimaliseren in Azure Synapse Analytics

Leer hoe u een [Apache Spark](https://spark.apache.org/)-clusterconfiguratie kunt optimaliseren voor uw specifieke workload.  De meest voorkomende uitdaging is geheugenbelasting vanwege onjuiste configuraties (met name uitvoeringen met verkeerde indeling), langlopende bewerkingen en taken die resulteren in Cartesische bewerkingen. U kunt taken versnellen met de juiste caching en door [gegevensverschil](#optimize-joins-and-shuffles) toe te staan. Bewaak en controleer uitvoeringen van Spark-taken die lang lopen en veel resources verbruiken, voor de beste prestaties.

In de volgende secties worden veelvoorkomende optimalisaties en aanbevelingen voor Spark-taken beschreven.

## <a name="choose-the-data-abstraction"></a>De gegevensabstractie kiezen

Eerdere Spark-versies gebruiken RDD’s voor gegevensabstractie. In Spark 1.3 en 1.6 zijn respectievelijk DataFrames en DataSets geïntroduceerd. Bekijk de volgende relatieve voordelen:

* **DataFrames**
  * Beste keuze in de meeste situaties.
  * Biedt queryoptimalisatie via Catalyst.
  * Codegeneratie in de volledige fase.
  * Directe geheugentoegang.
  * Lage GC-overhead (garbagecollection).
  * Niet zo ontwikkelaarsvriendelijk als DataSets, omdat er geen controle van compilatietijd of programmering van domeinobjecten beschikbaar is.
* **DataSets**
  * Goed voor complexe ETL-pijplijnen, waarbij de impact op de prestaties acceptabel is.
  * Niet goed voor aggregaties waarbij de impact op de prestaties aanzienlijk kan zijn.
  * Biedt queryoptimalisatie via Catalyst.
  * Ontwikkelaarsvriendelijk vanwege de beschikbare programmering van domeinobjecten en controle van compilatietijd.
  * Voegt overhead voor serialisatie/deserialisatie toe.
  * Hoge GC-overhead.
  * Verbreekt codegeneratie in de volledige fase.
* **RDD’s**
  * U hoeft geen RDD's te gebruiken, tenzij u een nieuwe aangepaste RDD wilt maken.
  * Geen queryoptimalisatie via Catalyst.
  * Geen codegeneratie in de volledige fase.
  * Hoge GC-overhead.
  * Moeten gebruikmaken van verouderde API’s voor Spark 1.x.

## <a name="use-optimal-data-format"></a>Optimale gegevensindeling gebruiken

Spark biedt ondersteuning voor veel indelingen, zoals csv, json, xml, parquet, orc en avro. Spark kan worden uitgebreid om ondersteuning te bieden voor veel meer indelingen met externe gegevensbronnen. Raadpleeg [Apache Spark-pakketten](https://spark-packages.org) voor meer informatie.

De beste indeling voor prestaties is parquet met *snappy-compressie*. Dit is de standaardinstelling in Spark 2.x. Met parquet worden gegevens opgeslagen in de kolomindeling. Parquet is bovendien zeer geoptimaliseerd in Spark. Daarnaast kan de *snappy-compressie* resulteren in grotere bestanden dan bijvoorbeeld gzip-compressie. Vanwege de splitsbare aard van deze bestanden, kunnen ze sneller worden gedecomprimeerd.

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt systeemeigen mechanismen voor caching die kunnen worden gebruikt via verschillende methoden, zoals `.persist()`, `.cache()` en `CACHE TABLE`. Systeemeigen caching is efficiënt bij kleine gegevenssets en in ETL-pijplijnen waarbij u tussendoor resultaten moet opslaan in de cache. Systeemeigen Spark-caching werkt momenteel echter niet goed met partitionering, omdat gepartitioneerde gegevens niet behouden blijven in een in cache geplaatste tabel.

## <a name="use-memory-efficiently"></a>Geheugen efficiënt gebruiken

Spark werkt door gegevens in het geheugen te plaatsen. Het beheren van geheugenresources is daarom een belangrijk aspect van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u kunt toepassen om het geheugen van uw cluster efficiënt te gebruiken.

* Gebruik eerder kleinere gegevenspartities en houd in uw partitiestrategie rekening met gegevensgrootten, -typen, en -distributie.
* Overweeg de nieuwere efficiëntere [Kryo-gegevensserialisatie](https://github.com/EsotericSoftware/kryo), in plaats van de standaardserialisatie van Java.
* Bewaak Spark-configuratie-instellingen en verfijn ze.

Ter referentie worden de Spark-geheugenstructuur en enkele belangrijke geheugenparameters voor de uitvoerder weergegeven in de volgende afbeelding.

### <a name="spark-memory-considerations"></a>Overwegingen voor Spark-geheugen

Apache Spark in Azure Synapse maakt gebruik van YARN [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Met YARN wordt de maximale hoeveelheid geheugen beheerd die wordt gebruikt voor alle containers op elk Spark-knooppunt.  In het volgende diagram ziet u de belangrijkste objecten en hun relaties.

![Geheugenbeheer met YARN Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Probeer het volgende om berichten over onvoldoende geheugen af te handelen:

* Bekijk beheer-shuffles in DAG. Verminder door te verminderen aan de toewijzingszijde, brongegevens vooraf te partitioneren (of in buckets te plaatsen), enkele shuffles te maximaliseren, en de hoeveelheid verzonden gegevens te verminderen.
* Gebruik eerder `ReduceByKey` - met de bijbehorende vaste geheugenlimiet - in plaats van `GroupByKey`, wat aggregaties, vensterbewerking en andere functies biedt, maar waarvoor een niet-gebonden geheugenlimiet geldt.
* Gebruik eerder `TreeReduce`, wat meer werk uitvoert met de uitvoerders of partities, in plaats van `Reduce`, wat al het werk uitvoert in het stuurprogramma.
* Gebruik DataFrames in plaats van RDD-objecten in lagere niveaus.
* Maak ComplexTypes dat acties inkapselt, zoals ‘Top N’, verschillende aggregaties of vensterbewerking.

## <a name="optimize-data-serialization"></a>Gegevensserialisatie optimaliseren

Spark-taken worden gedistribueerd, daarom is de juiste gegevensserialisatie belangrijk voor de beste prestaties.  Er zijn twee opties voor serialisatie voor Spark:

* Java-serialisatie is de standaardinstelling.
* Kryo-serialisatie is een nieuwere indeling en kan leiden tot snellere en compactere serialisatie dan Java.  Voor Kryo is vereist dat u de klassen in uw programma registreert, en er wordt nog geen ondersteuning geboden voor alle typen die kunnen worden geserialiseerd.

## <a name="use-bucketing"></a>Bucketing gebruiken

Bucketing is vergelijkbaar met het partitioneren van gegevens, maar elke bucket kan een set kolomwaarden bevatten, in plaats van slechts één. Bucketing werkt goed voor het partitioneren van grote aantallen waarden (miljoenen of meer), zoals product-id’s. Een bucket wordt bepaald door de bucketsleutel van de rij te hashen. Tabellen die in buckets zijn geplaatst, bieden unieke optimalisaties, omdat ze metagegevens bevatten over de bucketing of sortering.

Enkele geavanceerde functies voor bucketing zijn:

* Queryoptimalisatie op basis van bucketing van metagegevens.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde joins.

U kunt partitionering en bucketing gelijktijdig gebruiken.

## <a name="optimize-joins-and-shuffles"></a>Joins en shuffles optimaliseren

Als er sprake is van langzame taken voor een join of shuffle, wordt dit waarschijnlijk veroorzaakt door *gegevensverschil*, wat asymmetrie is in uw taakgegevens. Een toewijzingstaak kan bijvoorbeeld 20 seconden duren, maar het duurt uren om een taak uit te voeren waarvan de gegevens zijn gekoppeld (join) of in een willekeurige volgorde geplaatst (shuffle). Als u gegevensverschil wilt oplossen, moet u salt toevoegen aan de hele sleutel, of een *geïsoleerde salt* gebruiken voor slechts enkele sleutelsubsets. Als u een geïsoleerde salt gebruikt, moet u verder filteren om uw sleutelsubset waaraan salt is toegevoegd, te isoleren in toewijzings-joins. Een andere optie is om eerst een bucketkolom en een samenvoeging vooraf te introduceren in buckets.

Een andere factor die langzame joins veroorzaakt, kan het jointype zijn. Spark maakt standaard gebruik van het jointype `SortMerge`. Dit jointype is het meest geschikt voor grote gegevenssets, maar zorgt wel voor hoge rekenkosten, omdat eerst de linker- en rechterzijden van gegevens moeten worden geordend voordat u ze samenvoegt.

Een `Broadcast`-join is het meest geschikt voor kleinere gegevenssets, of waarbij één zijde van de join veel kleiner is dan de andere zijde. Met dit jointype wordt één zijde naar alle uitvoerders uitgezonden, waardoor meer geheugen voor uitzendingen in het algemeen is vereist.

U kunt het jointype in uw configuratie wijzigen door `spark.sql.autoBroadcastJoinThreshold` in te stellen, of u kunt een join-hint instellen met behulp van de DataFrame API’s (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Als u tabellen in buckets gebruikt, hebt u een derde jointype, de `Merge`-join. Met een correct vooraf gepartitioneerde en vooraf gesorteerde gegevensset wordt de dure sorteerfase in een `SortMerge`-join overgeslagen.

De volgorde van joins doet ertoe, met name bij meer complexe query’s. Begin met de meeste selectieve joins. Verplaats ook joins die het aantal rijen na aggregaties vergroten, indien mogelijk.

Als u parallelle uitvoering voor Cartesian-joins wilt beheren, kunt u geneste structuren en vensterbewerking toevoegen, en misschien een of meer stappen overslaan in uw Spark-taak.

### <a name="select-the-correct-executor-size"></a>Selecteer de juiste grootte voor de uitvoerder

Houd bij het bepalen van de configuratie van de uitvoerder rekening met Java GC-overhead (garbagecollection).

* Factoren om de grootte van de uitvoerder te verkleinen:
  * Verklein de heap-grootte naar minder dan 32 GB om de GC-overhead < 10% te houden.
  * Verklein het aantal kernen om de GC-overhead < 10% te houden.

* Factoren om de grootte van de uitvoerder te vergroten:
  * Verminder de communicatie-overhead tussen uitvoerders.
  * Verminder het aantal openstaande verbindingen tussen uitvoerders (N2) in grotere clusters (> 100 uitvoerders).
  * Vergroot de heap-grootte zodat deze geschikt is voor geheugenintensieve taken.
  * Optioneel: Verminder de geheugen-overhead per uitvoerder.
  * Optioneel: Vergroot het gebruik en de gelijktijdigheid door de CPU te overbelasten.

Als algemene vuistregel bij het selecteren van de grootte van de uitvoerder:

* Begin met 30 GB per uitvoerder, en distribueer beschikbare machinekernen.
* Verhoog het aantal uitvoerkernen voor grotere clusters (> 100 uitvoerders).
* Wijzig de grootte op basis van zowel uitvoeringen van de proefversie als op de voorgaande factoren, zoals GC-overhead.

Bij het uitvoeren van gelijktijdige query's moet u rekening houden met het volgende:

* Begin met 30 GB per uitvoerder en alle machinekernen.
* Maak meerdere parallelle Spark-toepassingen door de CPU te overbelasten (circa 30% latentieverbetering).
* Verdeel query's over parallelle toepassingen.
* Wijzig de grootte op basis van zowel uitvoeringen van de proefversie als op de voorgaande factoren, zoals GC-overhead.

Bewaak de queryprestaties voor uitschieters, of andere prestatieproblemen, door te kijken naar de tijdlijnweergave, SQL-grafiek, taakstatistieken, enzovoort. Soms zijn een of meer uitvoerders langzamer dan de rest, en duurt het veel langer om taken uit te voeren. Dit gebeurt vaak in grotere clusters (> 30 knooppunten). In dit geval verdeelt u het werk over een groter aantal taken, zodat de planner de langzame taken kan compenseren. 

Zorg er bijvoorbeeld voor dat u twee keer zoveel taken hebt als het aantal uitvoerkernen in de toepassing. U kunt ook speculatieve uitvoering van taken inschakelen met `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Taakuitvoering optimaliseren

* Sla gegevens zo nodig in de cache op, bijvoorbeeld als u ze twee keer gebruikt.
* Zend variabelen uit naar alle uitvoerders. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere zoekacties.
* Gebruik de threadpool in het stuurprogramma, wat resulteert in een snellere bewerking voor veel taken.

Het belangrijkste aspect voor Spark 2.x-queryprestaties is de Tungsten-engine, die afhankelijk is van codegeneratie in de volledige fase. In sommige gevallen is codegeneratie in de volledige fase mogelijk uitgeschakeld. 

Als u bijvoorbeeld een niet-veranderlijk type (`string`) in de aggregatie-expressie gebruikt, wordt `SortAggregate` weergegeven in plaats van `HashAggregate`. Probeer bijvoorbeeld voor betere prestaties het volgende uit, en schakel vervolgens codegeneratie in:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

- [Apache Spark afstemmen](https://spark.apache.org/docs/2.4.5/tuning.html)
- [Uw Apache Spark-taken effectief afstemmen zodat ze werken](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo-serialisatie](https://github.com/EsotericSoftware/kryo)
