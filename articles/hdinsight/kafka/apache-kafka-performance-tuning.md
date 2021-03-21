---
title: Prestaties van Apache Kafka HDInsight-clusters optimaliseren
description: Hierin wordt een overzicht gegeven van de technieken voor het optimaliseren van Apache Kafka workloads in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935884"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Prestaties van Apache Kafka HDInsight-clusters optimaliseren

In dit artikel vindt u enkele suggesties voor het optimaliseren van de prestaties van uw Apache Kafka-workloads in HDInsight. De focus is het aanpassen van de configuratie van de producent en de Broker. Er zijn verschillende manieren om de prestaties te meten en de optimalisaties die u toepast, zijn afhankelijk van de behoeften van uw bedrijf.

## <a name="architecture-overview"></a>Overzicht van de architectuur

Kafka-onderwerpen worden gebruikt om records te organiseren. Records worden geproduceerd door producenten en worden gebruikt door consumenten. Producenten sturen records naar Kafka-Brokers, die de gegevens vervolgens opslaan. Elk werkrolknooppunt in uw HDInsight-cluster is een Kafka-broker.

Onderwerpen verdelen records over brokers. Wanneer records worden verbruikt, kunt u maximaal één consumer per partitie gebruiken voor parallelle verwerking van de gegevens.

Replicatie wordt gebruikt voor het dupliceren van partities tussen knoop punten. Dit beveiligt tegen storingen van knoop punten (Broker). Een enkele partitie onder de groep replica's wordt aangeduid als de partitie leider. Producentverkeer wordt doorgestuurd naar de leider van elk knooppunt, met behulp van de status die wordt beheerd door ZooKeeper.

## <a name="identify-your-scenario"></a>Uw scenario identificeren

Apache Kafka prestaties heeft twee belang rijke aspecten: door Voer en latentie. De door Voer is de maximum snelheid waarmee gegevens kunnen worden verwerkt. Een hogere door Voer is doorgaans beter. Latentie is de tijd die nodig is om gegevens op te slaan of op te halen. Een lagere latentie is doorgaans beter. Het vinden van de juiste balans tussen de door Voer, latentie en de kosten van de infra structuur van de toepassing kan lastig zijn. Uw prestatie vereisten zullen waarschijnlijk overeenkomen met een van de volgende drie veelvoorkomende situaties, op basis van het feit of u een hoge door Voer, een lage latentie of beide nodig hebt:

* Hoge door Voer, lage latentie. Voor dit scenario is een hoge door Voer en een lage latentie vereist (~ 100 milliseconden). Een voor beeld van dit type toepassing is bewaking van de service beschikbaarheid.
* Hoge door Voer, hoge latentie. Dit scenario vereist een hoge door Voer (~ 1,5 GBps), maar kan een hogere latentie (< 250 MS). Een voor beeld van dit type toepassing is telemetrie-gegevens opname voor bijna realtime processen zoals beveiligings-en inbraak detectie toepassingen.
* Lage door Voer, lage latentie. Voor dit scenario is een lage latentie vereist (< 10 MS) voor realtime verwerking, maar kan een lagere door voer worden toegestaan. Een voor beeld van dit type toepassing is online spelling-en grammatica controles.

## <a name="producer-configurations"></a>Producer-configuraties

In de volgende secties worden enkele van de belangrijkste configuratie-eigenschappen gemarkeerd om de prestaties van uw Kafka-producenten te optimaliseren. Zie [Apache Kafka documentatie over producer-configuraties](https://kafka.apache.org/documentation/#producerconfigs)voor een gedetailleerde uitleg van alle configuratie-eigenschappen.

### <a name="batch-size"></a>Batchgrootte

Apache Kafka producenten groeperen groepen van berichten (batches genoemd) die als een eenheid worden verzonden om te worden opgeslagen in één opslag partitie. Batch grootte betekent het aantal bytes dat aanwezig moet zijn voordat deze groep wordt verzonden. Het verhogen `batch.size` van de para meter kan de door Voer verhogen, omdat hiermee de verwerkings overhead van netwerk-en i/o-aanvragen wordt verminderd. Onder lichte belasting kan een verhoogde batch grootte de latentie van Kafka-verzen ding verg Roten omdat de producent wacht totdat een batch gereed is. Onder zware belasting is het raadzaam de Batch grootte te verg Roten om de door Voer en latentie te verbeteren.

### <a name="producer-required-acknowledgments"></a>Bevestigingen voor producer vereist

De door de producent vereiste `acks` configuratie bepaalt het aantal bevestigingen dat de partitie leider vereist voordat een schrijf aanvraag als voltooid wordt beschouwd. Deze instelling is van invloed op de betrouw baarheid van gegevens en er worden waarden van `0` , `1` of gebruikt `-1` . De waarde van `-1` geeft aan dat er een bevestiging moet worden ontvangen van alle replica's voordat de schrijf bewerking is voltooid. Instelling `acks = -1` biedt betere garanties tegen gegevens verlies, maar resulteert ook in hogere latentie en een lagere door voer. Als uw aanvraag voor toepassings vereisten een hogere door Voer heeft, probeert u het in te stellen `acks = 0` of `acks = 1` . Denk eraan dat het niet bevestigen van alle replica's de betrouw baarheid van gegevens kan verminderen.

### <a name="compression"></a>Compressie

Een Kafka-producer kan worden geconfigureerd om berichten te comprimeren voordat ze naar Brokers worden verzonden. De `compression.type` instelling geeft aan welke compressie-codec moet worden gebruikt. Ondersteunde compressie-codecs zijn ' gzip ', ' Snappy ' en ' LZ4 '. Compressie is nuttig en moet worden overwogen als er sprake is van een limiet voor de schijf capaciteit.

Een van de twee veelgebruikte compressie-codecs `gzip` en `snappy` `gzip` heeft een hogere compressie ratio, wat resulteert in een lager schijf gebruik bij de kosten van een hogere CPU-belasting. De `snappy` codec biedt minder compressie met minder CPU-overhead. U kunt bepalen welke codec moet worden gebruikt op basis van de limieten van de Broker-schijf of de CPU van de producent. `gzip` kan gegevens comprimeren met een snelheid van vijf keer hoger dan `snappy` .

Als u gegevens compressie gebruikt, wordt het aantal records dat op een schijf kan worden opgeslagen, verg root. Het kan ook CPU-overhead verhogen wanneer er niet overeenkomt tussen de compressie-indelingen die door de producent en de Broker worden gebruikt. Als de gegevens moeten worden gecomprimeerd voordat ze worden verzonden en gedecomprimeerd voordat ze worden verwerkt.

## <a name="broker-settings"></a>Broker-instellingen

In de volgende secties worden enkele van de belangrijkste instellingen gemarkeerd voor het optimaliseren van de prestaties van uw Kafka-brokers. Zie [Apache Kafka documentatie over producer-configuraties](https://kafka.apache.org/documentation/#producerconfigs)voor een gedetailleerde uitleg van alle Broker-instellingen.

### <a name="number-of-disks"></a>Aantal schijven

Opslag schijven hebben beperkte IOPS (invoer/uitvoer-bewerkingen per seconde) en bytes per seconde voor lezen/schrijven. Bij het maken van nieuwe partities slaat Kafka elke nieuwe partitie op de schijf met mini maal bestaande partities op om ze te verdelen over de beschik bare schijven. Ondanks de opslag strategie kan Kafka bij het verwerken van honderden partitie replica's op elke schijf eenvoudig de beschik bare schijf doorvoer intensiteit. Dit is een balans tussen de door Voer en de kosten. Als voor uw toepassing een hogere door Voer is vereist, maakt u een cluster met meer beheerde schijven per Broker. HDInsight biedt momenteel geen ondersteuning voor het toevoegen van beheerde schijven aan een actief cluster. Zie [opslag en schaal baarheid configureren voor Apache Kafka op HDInsight](apache-kafka-scalability.md)voor meer informatie over het configureren van het aantal Managed disks. Inzicht in de kosten implicaties voor het verg Roten van de opslag ruimte voor de knoop punten in uw cluster.

### <a name="number-of-topics-and-partitions"></a>Aantal onderwerpen en partities

Kafka-producenten schrijven naar onderwerpen. Kafka-gebruikers lezen van onderwerpen. Een onderwerp is gekoppeld aan een logboek, een gegevens structuur op schijf. Kafka voegt records van een producent (s) toe aan het einde van een onderwerps logboek. Een onderwerpen logboek bestaat uit veel partities die over meerdere bestanden zijn verdeeld. Deze bestanden zijn op zijn beurt verdeeld over meerdere Kafka-cluster knooppunten. Consumenten lezen van Kafka-onderwerpen in hun uitgebracht en kunnen hun positie (offset) in het onderwerps logboek kiezen.

Elke Kafka-partitie is een logboek bestand op het systeem en de producenten-threads kunnen tegelijkertijd naar meerdere logboeken schrijven. En omdat elke Consumer-thread berichten van de ene partitie leest, wordt het gebruik van meerdere partities ook parallel verwerkt.

Het verhogen van de dichtheid van de partitie (het aantal partities per Broker) voegt een overhead toe die betrekking heeft op meta gegevens bewerkingen en per partitie aanvraag/antwoord tussen de partitie leider en de volgers. Zelfs als er geen gegevens worden doorgelopen, worden met partitie replica's gegevens opgehaald uit leiders, wat resulteert in extra verwerking voor het verzenden en ontvangen van aanvragen via het netwerk.

Voor Apache Kafka clusters 1,1 en hoger in HDInsight raden wij u aan Maxi maal 1000 partities per Broker te hebben, inclusief replica's. Het verhogen van het aantal partities per Broker vermindert de door Voer en kan ook leiden tot Beschik baarheid van het onderwerp. Zie voor meer informatie over ondersteuning voor Kafka-partities [het officiële Apache Kafka blog bericht over de toename van het aantal ondersteunde partities in versie 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Zie [Apache Kafka: onderwerpen wijzigen](https://kafka.apache.org/documentation/#basic_ops_modify_topic)voor meer informatie over het wijzigen van onderwerpen.

### <a name="number-of-replicas"></a>Aantal replica's

Een hogere replicatie factor resulteert in extra aanvragen tussen de partitie leider en volgers. Als gevolg hiervan neemt een hogere replicatie factor meer schijf en CPU in beslag om extra aanvragen af te handelen, de schrijf latentie te verhogen en de door voer te verminderen.

U wordt aangeraden ten minste 3x replicatie te gebruiken voor Kafka in azure HDInsight. De meeste Azure-regio's hebben drie fout domeinen, maar in regio's met slechts twee fout domeinen moeten gebruikers 4x-replicatie gebruiken.

Zie voor meer informatie over replicatie [Apache Kafka: replicatie](https://kafka.apache.org/documentation/#replication) en [Apache Kafka: toename van de replicatie factor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Volgende stappen

* [Biljoenen gebeurtenissen per dag verwerken met Apache Kafka in Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Wat is Apache Kafka in HDInsight?](apache-kafka-introduction.md)
