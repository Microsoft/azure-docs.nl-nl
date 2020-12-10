---
title: Gegevens opslag optimaliseren voor Apache Spark-Azure HDInsight
description: Meer informatie over het optimaliseren van gegevens opslag voor gebruik met Apache Spark in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ad09cdc2c0054c9d9a58e6bfa00252862f1e8c0f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028220"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Optimalisatie van gegevens opslag voor Apache Spark

In dit artikel worden strategieën beschreven voor het optimaliseren van gegevens opslag voor efficiënte Apache Spark uitvoering van taken op Azure HDInsight.

## <a name="overview"></a>Overzicht

Spark biedt ondersteuning voor veel indelingen, zoals csv, json, xml, parquet, orc en avro. Spark kan worden uitgebreid om ondersteuning te bieden voor veel meer indelingen met externe gegevensbronnen. Raadpleeg [Apache Spark-pakketten](https://spark-packages.org) voor meer informatie.

De beste indeling voor prestaties is parquet met *snappy-compressie*. Dit is de standaardinstelling in Spark 2.x. Met parquet worden gegevens opgeslagen in de kolomindeling. Parquet is bovendien zeer geoptimaliseerd in Spark.

## <a name="choose-data-abstraction"></a>Gegevens abstractie kiezen

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

## <a name="select-default-storage"></a>Standaard opslag selecteren

Wanneer u een nieuw Spark-cluster maakt, kunt u Azure Blob Storage of Azure Data Lake Storage als de standaard opslag van uw cluster selecteren. Beide opties bieden u het voor deel van lange termijn opslag voor tijdelijke clusters. Uw gegevens worden dus niet automatisch verwijderd wanneer u uw cluster verwijdert. U kunt een tijdelijk cluster opnieuw maken en toch toegang krijgen tot uw gegevens.

| Winkeltype | Bestandssysteem | Snelheid | Wijk | Gebruiksscenario's |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Ja | Tijdelijk cluster |
| Azure Blob Storage (beveiligd) | **wasbs:**//URL/ | **Standard** | Ja | Tijdelijk cluster |
| Azure Data Lake Storage Gen 2| **abfs:**//URL/ | **Gereed** | Ja | Tijdelijk cluster |
| Azure Data Lake Storage Gen 1| **ADL:**//URL/ | **Gereed** | Ja | Tijdelijk cluster |
| Lokale HDFS | **hdfs:**//URL/ | **Snelste** | Nee | Interactive 24/7-cluster |

Zie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](../hdinsight-hadoop-compare-storage-options.md)voor een volledige beschrijving van opslag opties.

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt systeemeigen mechanismen voor caching die kunnen worden gebruikt via verschillende methoden, zoals `.persist()`, `.cache()` en `CACHE TABLE`. Deze systeem eigen cache is effectief met kleine gegevens sets en in ETL-pijp lijnen waar u tussenliggende resultaten in de cache moet opslaan. Systeemeigen Spark-caching werkt momenteel echter niet goed met partitionering, omdat gepartitioneerde gegevens niet behouden blijven in een in cache geplaatste tabel. Een meer generieke en betrouw bare cache techniek is het opslaan van de *opslag laag in de cache*.

* Systeem eigen Spark-caching (niet aanbevolen)
    * Geschikt voor kleine gegevens sets.
    * Werkt niet voor partitionering, wat in toekomstige Spark-releases kan veranderen.

* Caching op opslag niveau (aanbevolen)
    * Kan worden geïmplementeerd op HDInsight met behulp van de [i/o-cache](apache-spark-improve-performance-iocache.md) functie.
    * Maakt gebruik van in-Memory en SSD-caching.

* Lokale HDFS (aanbevolen)
    * `hdfs://mycluster` programmapad.
    * Maakt gebruik van SSD-caching.
    * Gegevens in de cache gaan verloren wanneer u het cluster verwijdert, waardoor een cache opnieuw moet worden opgebouwd.

## <a name="optimize-data-serialization"></a>Gegevensserialisatie optimaliseren

Spark-taken worden gedistribueerd, daarom is de juiste gegevensserialisatie belangrijk voor de beste prestaties.  Er zijn twee opties voor serialisatie voor Spark:

* Java-serialisatie is de standaardinstelling.
* `Kryo` serialisatie is een nieuwere indeling en kan leiden tot snellere en meer compacte serialisatie dan Java.  `Kryo` Hiervoor moet u de klassen in het programma registreren en wordt niet alle serialiseerbare typen ondersteund.

## <a name="use-bucketing"></a>Bucketing gebruiken

Bucket is vergelijkbaar met het partitioneren van gegevens. Elke Bucket kan echter een set kolom waarden bevatten in plaats van slechts één. Deze methode werkt goed voor het partitioneren van grote aantallen (in miljoenen of meer) waarden, zoals product-id's. Een bucket wordt bepaald door de bucketsleutel van de rij te hashen. Tabellen die in buckets zijn geplaatst, bieden unieke optimalisaties, omdat ze metagegevens bevatten over de bucketing of sortering.

Enkele geavanceerde functies voor bucketing zijn:

* Queryoptimalisatie op basis van bucketing van metagegevens.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde joins.

U kunt partitionering en bucketing gelijktijdig gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
