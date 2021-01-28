---
title: Gegevens verwerking optimaliseren voor Apache Spark-Azure HDInsight
description: Meer informatie over het kiezen van de meest efficiënte bewerkingen voor het verwerken van uw gegevens op Apache Spark met Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944712"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Optimalisatie van gegevens verwerking voor Apache Spark

In dit artikel wordt beschreven hoe u de configuratie van uw Apache Spark-cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Als u langzame taken hebt voor een samen voeging of een wille keurige volg orde, is de oorzaak waarschijnlijk van *gegevens scheef*. Gegevens scheefheid is asymmetrie in uw taak gegevens. Een kaart taak kan bijvoorbeeld 20 seconden duren. Maar het uitvoeren van een taak waarbij de gegevens worden toegevoegd of waarin de rang orde wordt geduurd. Als u gegevensverschil wilt oplossen, moet u salt toevoegen aan de hele sleutel, of een *geïsoleerde salt* gebruiken voor slechts enkele sleutelsubsets. Als u een geïsoleerde salt gebruikt, moet u verder filteren om uw sleutelsubset waaraan salt is toegevoegd, te isoleren in toewijzings-joins. Een andere optie is om eerst een bucketkolom en een samenvoeging vooraf te introduceren in buckets.

Een andere factor die langzame joins veroorzaakt, kan het jointype zijn. Spark maakt standaard gebruik van het jointype `SortMerge`. Dit type samen voeging is het meest geschikt voor grote gegevens sets. Maar is anders rekenbaar kostbaar omdat het eerst de linker-en rechter zijde van de gegevens moet sorteren voordat u ze samenvoegt.

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

## <a name="optimize-job-execution"></a>Taakuitvoering optimaliseren

* Sla gegevens zo nodig in de cache op, bijvoorbeeld als u ze twee keer gebruikt.
* Zend variabelen uit naar alle uitvoerders. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere zoekacties.
* Gebruik de threadpool in het stuurprogramma, wat resulteert in een snellere bewerking voor veel taken.

Bewaak uw actieve taken regel matig voor prestatie problemen. Als u meer inzicht wilt in bepaalde problemen, kunt u een van de volgende hulpprogram ma's voor prestatie profilering overwegen:

* [Intel PAL-hulp programma](https://github.com/intel-hadoop/PAT) bewaakt CPU, opslag en netwerk bandbreedte gebruik.
* De beheer profielen voor de [missie van Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark en de uitvoerder code.

Het belangrijkste aspect voor Spark 2.x-queryprestaties is de Tungsten-engine, die afhankelijk is van codegeneratie in de volledige fase. In sommige gevallen is codegeneratie in de volledige fase mogelijk uitgeschakeld. Als u bijvoorbeeld een niet-veranderlijk type (`string`) in de aggregatie-expressie gebruikt, wordt `SortAggregate` weergegeven in plaats van `HashAggregate`. Probeer bijvoorbeeld voor betere prestaties het volgende uit, en schakel vervolgens codegeneratie in:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
