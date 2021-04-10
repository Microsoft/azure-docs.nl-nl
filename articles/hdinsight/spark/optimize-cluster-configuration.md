---
title: Apache Spark cluster configuratie optimaliseren-Azure HDInsight
description: Meer informatie over het configureren van uw Apache Spark-cluster om de door Voer in azure HDInsight te maximaliseren.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944741"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Optimalisatie van cluster configuratie voor Apache Spark

In dit artikel wordt beschreven hoe u de configuratie van uw Apache Spark-cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Afhankelijk van de werk belasting van het Spark-cluster kunt u bepalen dat een niet-standaard Spark-configuratie zou leiden tot een meer geoptimaliseerde Spark-taak uitvoering.  Voer Bench Mark-tests uit met voorbeeld werkbelastingen om niet-standaard cluster configuraties te valideren.

Hier volgen enkele algemene para meters die u kunt aanpassen:

|Parameter |Beschrijving |
|---|---|
|--num-uitvoerende modules|Hiermee stelt u het juiste aantal uitvoerender in.|
|--uitvoerder-kernen|Hiermee stelt u het aantal kernen in voor elke uitvoerder. Normaal gesp roken moet u middelste uitvoerings modules hebben, aangezien andere processen een deel van het beschik bare geheugen verbruiken.|
|--uitvoerbaar geheugen|Hiermee stelt u de grootte van het geheugen voor elke uitvoerder, waarmee de heapgrootte op GARENs wordt beheerd. Zorg dat er voldoende geheugen beschikbaar is voor de overhead van de uitvoering.|

## <a name="select-the-correct-executor-size"></a>Selecteer de juiste grootte voor de uitvoerder

Houd bij het bepalen van de configuratie van de uitvoerder rekening met Java GC-overhead (garbagecollection).

* Factoren om de grootte van de uitvoerder te verkleinen:
    1. Verklein de heap-grootte naar minder dan 32 GB om de GC-overhead < 10% te houden.
    2. Verklein het aantal kernen om de GC-overhead < 10% te houden.

* Factoren om de grootte van de uitvoerder te vergroten:
    1. Verminder de communicatie-overhead tussen uitvoerders.
    2. Verminder het aantal openstaande verbindingen tussen uitvoerders (N2) in grotere clusters (> 100 uitvoerders).
    3. Vergroot de heap-grootte zodat deze geschikt is voor geheugenintensieve taken.
    4. Optioneel: Verminder de geheugen-overhead per uitvoerder.
    5. Optioneel: gebruik en gelijktijdigheid verhogen door de CPU te verlengen.

Als algemene regel wordt bij het selecteren van de grootte van de uitvoerder:

1. Begin met 30 GB per uitvoerder, en distribueer beschikbare machinekernen.
2. Verhoog het aantal uitvoerkernen voor grotere clusters (> 100 uitvoerders).
3. Wijzig de grootte op basis van zowel uitvoeringen van de proefversie als op de voorgaande factoren, zoals GC-overhead.

Bij het uitvoeren van gelijktijdige query's moet u rekening houden met het volgende:

1. Begin met 30 GB per uitvoerder en alle machinekernen.
2. Maak meerdere parallelle Spark-toepassingen door de CPU te overbelasten (circa 30% latentieverbetering).
3. Verdeel query's over parallelle toepassingen.
4. Wijzig de grootte op basis van zowel uitvoeringen van de proefversie als op de voorgaande factoren, zoals GC-overhead.

Zie [Apache Spark Settings-Spark-uitvoerende modules](apache-spark-settings.md#configuring-spark-executors)voor meer informatie over het gebruik van Ambari voor het configureren van uitvoerender.

Bewaak de query prestaties voor uitbijters of andere prestatie problemen door te kijken naar de tijdlijn weergave. Ook SQL-grafiek, taak statistieken, enzovoort. Zie [Debug Apache Spark jobs die worden uitgevoerd op Azure HDInsight](apache-spark-job-debugging.md)voor informatie over het opsporen van fouten in Spark-taken met garens en de Spark-geschiedenis server. Voor tips over het gebruik van een garen tijdlijn Server raadpleegt u [toegang Apache Hadoop toepassings logboeken van garens](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Soms zijn een of meer uitvoerders langzamer dan de rest, en duurt het veel langer om taken uit te voeren. Deze traagheid gebeurt vaak op grotere clusters (> 30 knoop punten). In dit geval verdeelt u het werk over een groter aantal taken, zodat de planner de langzame taken kan compenseren. Zorg er bijvoorbeeld voor dat u twee keer zoveel taken hebt als het aantal uitvoerkernen in de toepassing. U kunt ook speculatieve uitvoering van taken inschakelen met `conf: spark.speculation = true`.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
