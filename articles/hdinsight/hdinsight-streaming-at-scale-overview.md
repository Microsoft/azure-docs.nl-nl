---
title: Streaming op schaal in azure HDInsight
description: Gegevens stromen gebruiken met schaal bare Apache-clusters in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 2b2dfe9da55548f2648f847a9d7c2cb3478e6bad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931713"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Schaalbaar streamen in HDInsight

Realtime-big data oplossingen worden toegepast op gegevens die zich in beweging bevindt. Normaal gesp roken zijn deze gegevens het meest waardevol op het moment van aankomst. Als de binnenkomende gegevens stroom groter wordt dan op dat moment kan worden verwerkt, moet u mogelijk resources beperken. Een HDInsight-cluster kan ook omhoog worden geschaald om te voldoen aan uw streaming-oplossing door knoop punten op aanvraag toe te voegen.

In een streaming-toepassing worden er in een of meer gegevens bronnen gebeurtenissen gegenereerd (soms in miljoenen per seconde) die snel moeten worden opgenomen zonder dat er bruikbare informatie hoeft te worden verwijderd. De binnenkomende gebeurtenissen worden verwerkt met behulp van de *stroom buffering*, ook wel *Event Queuing* genoemd, door een service zoals [Apache Kafka](kafka/apache-kafka-introduction.md) of [Event hubs](https://azure.microsoft.com/services/event-hubs/). Nadat u de gebeurtenissen hebt verzameld, kunt u de gegevens analyseren met behulp van een real-time analyse systeem binnen de laag voor *stroom verwerking* , zoals [Apache Storm](storm/apache-storm-overview.md) of [Apache Spark streaming](spark/apache-spark-streaming-overview.md). De verwerkte gegevens kunnen worden opgeslagen in lange termijn opslag systemen, zoals [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), en in realtime worden weer gegeven op een Business Intelligence dash board, zoals [Power bi](https://powerbi.microsoft.com), tableau of een aangepaste webpagina.

![Azure HDInsight-streaming-patronen](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka biedt een berichten wachtrij service met hoge door Voer en lage latentie en maakt nu deel uit van de Apache-suite van open source software (OSS). Kafka maakt gebruik van een berichten model voor publiceren en abonneren en slaat streams van gepartitioneerde gegevens veilig op in een gedistribueerd, gerepliceerd cluster. Kafka schaalt lineair als door Voer toeneemt.

Zie [inleiding Apache Kafka op HDInsight](kafka/apache-kafka-introduction.md)voor meer informatie.

## <a name="apache-storm"></a>Apache Storm

Apache Storm is een gedistribueerd, fout tolerant, open-source berekenings systeem dat is geoptimaliseerd voor het verwerken van gegevens stromen in realtime met Hadoop. De kern eenheid van gegevens voor een binnenkomende gebeurtenis is een tuple. Dit is een onveranderlijke set sleutel-waardeparen. Een niet-gebonden reeks van deze Tuples vormt een stroom, die afkomstig is van een Spout. De Spout verloopt een streaming-gegevens bron (zoals Kafka) en verzendt Tuples. Een storm-topologie is een reeks trans formaties voor deze stromen.

Zie [Wat is er Apache Storm in azure HDInsight?](storm/apache-storm-overview.md)voor meer informatie.

## <a name="spark-streaming"></a>Spark Streaming

Spark streaming is een uitbrei ding van Spark, waarmee u dezelfde code kunt gebruiken die u voor batch verwerking gebruikt. U kunt zowel batch-als interactieve query's combi neren in dezelfde toepassing. In tegens telling tot Storm biedt Spark streaming een status precies eenmaal de verwerkings semantiek. Bij gebruik in combi natie met de [Kafka direct-API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), die ervoor zorgt dat alle Kafka-gegevens exact één keer worden ontvangen door Spark-streaming, is het mogelijk om end-to-end precies één keer per garantie te zorgen. Een van de sterke punten van Spark streaming is de fout tolerante mogelijkheden. het herstellen van defecte knoop punten is snel mogelijk wanneer er meerdere knoop punten in het cluster worden gebruikt.

Zie [Wat is Apache Spark streaming?](./spark/apache-spark-streaming-overview.md)voor meer informatie.

## <a name="scaling-a-cluster"></a>Een cluster schalen

Hoewel u het aantal knoop punten in het cluster tijdens het maken kunt opgeven, wilt u het cluster wellicht verg Roten of verkleinen zodat het overeenkomt met de werk belasting. Alle HDInsight-clusters bieden u [de mogelijkheid om het aantal knoop punten in het cluster te wijzigen](hdinsight-administer-use-portal-linux.md#scale-clusters). Spark-clusters kunnen zonder gegevens verlies worden verwijderd, omdat alle gegevens worden opgeslagen in Azure Storage of Data Lake Storage.

Er zijn voor delen voor het loskoppelen van technologieën. Kafka is bijvoorbeeld een technologie voor gebeurtenis buffers, dus het is zeer IO-intensief en heeft geen veel verwerkings kracht nodig. In vergelijking zijn stream-processors, zoals Spark-streaming, reken intensief, waardoor krachtigere Vm's zijn vereist. Als deze technologieën zijn losgekoppeld van verschillende clusters, kunt u deze onafhankelijk van elkaar schalen terwijl u het beste gebruikmaakt van de Vm's.

### <a name="scale-the-stream-buffering-layer"></a>De laag van de stroom buffer schalen

De technologieën voor het bufferen van streams Event Hubs en Kafka beide partities gebruiken en consumenten lezen van deze partities. Het schalen van de invoer doorvoer vereist het schalen van het aantal partities en het toevoegen van partities zorgt voor een groeiende parallelle uitvoering. In Event Hubs kan het aantal partities niet worden gewijzigd na de implementatie, zodat het belang rijk is om te beginnen met de doel schaal. Met Kafka is het mogelijk om [partities toe te voegen](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), zelfs wanneer Kafka gegevens verwerkt. Kafka biedt een hulp programma waarmee u partities opnieuw kunt toewijzen  `kafka-reassign-partitions.sh` . HDInsight biedt een [hulp programma voor het herverdelen van partitie replica's](https://github.com/hdinsight/hdinsight-kafka-tools),  `rebalance_rackaware.py` . Dit hulp programma roept het `kafka-reassign-partitions.sh` hulp programma op een zodanige manier aan dat elke replica zich in een afzonderlijk fout domein en een update domein bevindt, waardoor Kafka rack bewust wordt en fout tolerantie wordt verhoogd.

### <a name="scale-the-stream-processing-layer"></a>De laag voor stream processing schalen

Zowel Apache Storm als Spark-streaming ondersteunen worker-knoop punten toe te voegen aan hun clusters, zelfs wanneer de gegevens worden verwerkt.

Als u wilt profiteren van nieuwe knoop punten die zijn toegevoegd door het schalen van stormen, moet u eventuele Storm-topologieën die zijn gestart voordat de cluster grootte werd verhoogd, opnieuw verdelen. Deze herverdeling kan worden uitgevoerd met behulp van de Storm-webgebruikersinterface of de CLI. Zie de [Apache Storm-documentatie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)voor meer informatie.

Apache Spark gebruikt drie belang rijke para meters voor het configureren van de omgeving, afhankelijk van de toepassings vereisten: `spark.executor.instances` , `spark.executor.cores` en `spark.executor.memory` . Een uitvoerder *is een proces dat wordt gestart* voor een Spark-toepassing. Een uitvoerder wordt uitgevoerd op het worker-knoop punt en is verantwoordelijk voor het uitvoeren van de taken van de toepassing. Het standaard aantal uitvoerende agents en de uitvoerings grootte voor elk cluster worden berekend op basis van het aantal worker-knoop punten en de grootte van het worker-knoop punt. Deze getallen worden opgeslagen in het `spark-defaults.conf` bestand op elk cluster hoofd knooppunt.

Deze drie para meters kunnen worden geconfigureerd op cluster niveau voor alle toepassingen die op het cluster worden uitgevoerd en kunnen ook voor elke afzonderlijke toepassing worden opgegeven. Zie [resources beheren voor Apache Spark-clusters](spark/apache-spark-resource-manager.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Apache Storm-topologie maken/bewaken in Azure HDInsight](storm/apache-storm-quickstart.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](storm/apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
* [Inleiding tot Apache Spark op HDInsight](spark/apache-spark-overview.md)
* [Met Apache Kafka in HDInsight beginnen](kafka/apache-kafka-get-started.md)