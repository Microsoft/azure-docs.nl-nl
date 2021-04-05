---
title: Hoge Beschik baarheid met Apache Kafka-Azure HDInsight
description: Informatie over hoge beschikbaarheid garanderen met Apache Kafka in Azure HDInsight. Informatie over het opnieuw verdelen van partitiereplica's in Kafka zodat deze zich bevinden op verschillende foutdomeinen binnen de Azure-regio met HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e3f31bc1876a0c1f90ad4882ff782be25b4b6ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932760"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Hoge beschikbaarheid van uw gegevens met Apache Kafka in HDInsight

Meer informatie over het configureren van partitie replica's voor Apache Kafka-onderwerpen om te profiteren van de onderliggende configuratie van de hardwareconfiguratie. Deze configuratie waarborgt de beschikbaarheid van gegevens die zijn opgeslagen in Apache Kafka op HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Fout-en update domeinen met Apache Kafka

Een foutdomein is een logische groepering van de onderliggende hardware in een Azure-datacenter. Elk foutdomein deelt een algemene voedingsbron en netwerkswitch. De virtuele machines en beheerde schijven die de knooppunten in een HDInsight-cluster implementeren zijn verdeeld over deze foutdomeinen. Deze architectuur beperkt de potentiële impact van problemen met de fysieke hardware.

Elke Azure-regio heeft een bepaald aantal foutdomeinen. Zie de [Beschikbaarheidssets](../../virtual-machines/availability.md#availability-sets)-documentatie voor een lijst met domeinen en het aantal foutdomeinen die ze bevatten.

> [!IMPORTANT]  
> Kafka is niet bekend met foutdomeinen. Wanneer u een onderwerp in Kafka maakt, is het daarom mogelijk dat alle partitiereplica's in hetzelfde foutdomein worden opgeslagen. Als oplossing voor dit probleem bevat HDInsight het [Kafka partition rebalance tool](https://github.com/hdinsight/hdinsight-kafka-tools) (hulpprogramma voor het opnieuw indelen van Kafka-partities).

## <a name="when-to-rebalance-partition-replicas"></a>Wanneer partitiereplica 's opnieuw moeten worden ingedeeld

Om de hoogst mogelijke beschikbaarheid van uw Kafka-gegevens te waarborgen, moet u de partitiereplica's voor uw onderwerp op de volgende tijden opnieuw indelen:

* wanneer een nieuw onderwerp of partitie wordt gemaakt

* wanneer u een cluster opschaalt

## <a name="replication-factor"></a>Replicatiefactor

> [!IMPORTANT]  
> Wij raden het gebruik aan van een Azure-regio die drie foutdomeinen bevat en van een replicatiefactor van 3.

Als u een regio met slechts twee foutdomeinen moet gebruiken, gebruik dan een replicatiefactor van 4 om de replica's gelijkmatig te verdelen over de twee foutdomeinen.

Zie het document [beginnen met Apache Kafka in HDInsight](apache-kafka-get-started.md) voor een voor beeld van het maken van onderwerpen en het instellen van de replicatie factor.

## <a name="how-to-rebalance-partition-replicas"></a>Hoe partitiereplica 's opnieuw moeten worden ingedeeld

Gebruik het [Apache Kafka hulp programma](https://github.com/hdinsight/hdinsight-kafka-tools) voor het opnieuw verdelen van partities om geselecteerde onderwerpen te herverdelen. Dit hulpprogramma moet vanaf een SSH-sessie naar het hoofdknooppunt van het Kafka-cluster worden uitgevoerd.

Zie het document [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over verbinding maken met HDInsight met behulp van SSH.

## <a name="next-steps"></a>Volgende stappen

* [Schaal baarheid van Apache Kafka op HDInsight](apache-kafka-scalability.md)
* [Spie gelen met Apache Kafka op HDInsight](apache-kafka-mirroring.md)