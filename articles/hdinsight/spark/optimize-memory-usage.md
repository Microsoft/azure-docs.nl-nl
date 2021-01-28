---
title: Het geheugen gebruik optimaliseren in Apache Spark-Azure HDInsight
description: Meer informatie over het optimaliseren van geheugen gebruik in Apache Spark in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d54cf4e9025dfd75d9029dec534fc5dc5dd990a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944174"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optimalisatie van geheugen gebruik voor Apache Spark

In dit artikel wordt beschreven hoe u het geheugen beheer van uw Apache Spark cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Spark werkt door gegevens in het geheugen te plaatsen. Het beheren van geheugen bronnen is dus een belang rijk aspect van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u kunt toepassen om het geheugen van uw cluster efficiënt te gebruiken.

* Gebruik eerder kleinere gegevenspartities en houd in uw partitiestrategie rekening met gegevensgrootten, -typen, en -distributie.
* Overweeg de nieuwere, efficiënter [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , in plaats van de standaard-Java-serialisatie.
* Geniet van de voor keur aan GARENs, omdat deze `spark-submit` door batch worden gescheiden.
* Bewaak Spark-configuratie-instellingen en verfijn ze.

Ter referentie worden de Spark-geheugenstructuur en enkele belangrijke geheugenparameters voor de uitvoerder weergegeven in de volgende afbeelding.

## <a name="spark-memory-considerations"></a>Overwegingen voor Spark-geheugen

Als u Apache Hadoop GARENs gebruikt, beheert garen het geheugen dat door alle containers op elk Spark-knoop punt wordt gebruikt.  In het volgende diagram ziet u de belangrijkste objecten en hun relaties.

![Geheugenbeheer met YARN Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Probeer het volgende om berichten over onvoldoende geheugen af te handelen:

* Bekijk beheer-shuffles in DAG. Verlaag met rebuising aan de kaart zijde, pre-Partition (of buckets) bron gegevens, Maximaliseer enkele wille keurige volg orde en verminder de hoeveelheid verzonden gegevens.
* Gebruik eerder `ReduceByKey` - met de bijbehorende vaste geheugenlimiet - in plaats van `GroupByKey`, wat aggregaties, vensterbewerking en andere functies biedt, maar waarvoor een niet-gebonden geheugenlimiet geldt.
* Gebruik eerder `TreeReduce`, wat meer werk uitvoert met de uitvoerders of partities, in plaats van `Reduce`, wat al het werk uitvoert in het stuurprogramma.
* Gebruik DataFrames in plaats van RDD-objecten op lagere niveaus.
* Maak ComplexTypes dat acties inkapselt, zoals ‘Top N’, verschillende aggregaties of vensterbewerking.

Zie [OutOfMemoryError-uitzonde ringen voor Apache Spark in azure HDInsight](apache-spark-troubleshoot-outofmemory.md)voor meer probleemoplossings stappen.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
