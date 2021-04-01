---
title: Apache Kafka ontwikkelaars handleiding voor Event Hubs
description: Dit artikel bevat koppelingen naar artikelen waarin wordt beschreven hoe u uw Kafka-toepassingen integreert met Azure Event Hubs.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90061730"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs
Dit artikel bevat koppelingen naar artikelen waarin wordt beschreven hoe u uw Apache Kafka-toepassingen integreert met Azure Event Hubs. 

## <a name="overview"></a>Overzicht
Event Hubs biedt een Kafka-eind punt dat door uw bestaande op Kafka gebaseerde toepassingen kan worden gebruikt als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs werkt met veel van uw bestaande Kafka-toepassingen. Zie [Event hubs voor Apache Kafka voor](event-hubs-for-kafka-ecosystem-overview.md) meer informatie.

## <a name="quickstarts"></a>Snelstartgidsen
U vindt Quick starts in GitHub en in deze inhoudsset waarmee u snel op Event Hubs kunt komen voor Kafka.

### <a name="quickstarts-in-github"></a>Quick starts in GitHub
Raadpleeg de volgende Quick starts in de **Azure-Event-hubs-for-Kafka** -opslag plaats: 

| Client taal/-Framework | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en maakt met behulp van een voorbeeld producent en een consument die is geschreven in C# met behulp van .NET Core 2,0.</p><p>Dit voor beeld is gebaseerd op [confluente Apache Kafka .net-client](https://github.com/confluentinc/confluent-kafka-dotnet), gewijzigd voor gebruik met Event hubs voor Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en er verbinding mee kunt maken met behulp van een voorbeeld producent en een consument die is geschreven in Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en er verbinding mee kunt maken met behulp van een voorbeeld producent en een consument die is geschreven in het</p><p>In dit voor beeld wordt de [node-rdkafka-](https://github.com/Blizzard/node-rdkafka) bibliotheek gebruikt. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en maakt met behulp van een voorbeeld producent en een consument die in python is geschreven.</p><p>Dit voor beeld is gebaseerd op [confluente Apache Kafka python-client](https://github.com/confluentinc/confluent-kafka-python), gewijzigd voor gebruik met Event hubs voor Kafka.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en maakt met behulp van een voor beeld van een producent en een consument die in go is geschreven.</p><p>Dit voor beeld is gebaseerd op [confluente Apache Kafka Golang-client](https://github.com/confluentinc/confluent-kafka-go), gewijzigd voor gebruik met Event hubs voor Kafka.</p>| 
| [Sarama Kafka go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en maakt met behulp van een voorbeeld producent en een consument die in go is geschreven met behulp van de [Sarama Kafka-client](https://github.com/Shopify/sarama) bibliotheek. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | In deze Quick start ziet u hoe u een Event Hubs Kafka-eind punt maakt en maakt met behulp van de CLI die is gebundeld met de Apache Kafka-distributie.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat is een niet-JVM opdracht regel verbruiker en producent op basis van librdkafka, populair vanwege de snelheid en de geringe footprint. Deze Snelstartgids bevat een voor beeld van een configuratie en enkele eenvoudige voor beelden van kafkacat-opdrachten. | 
 
### <a name="quickstarts-in-docs"></a>Quick starts in DOCS
Bekijk de Quick Start: [gegevensstreaming met Event hubs met behulp van het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md) in deze inhoudsset, dat stapsgewijze instructies bevat voor het streamen van gegevens in Event hubs. U leert hoe u uw producenten kunt gebruiken en consumenten met Event Hubs, kunnen praten met slechts een configuratiewijziging in uw toepassingen. 


## <a name="tutorials"></a>Zelfstudies 

### <a name="tutorials-in-github"></a>Zelf studies in GitHub
Zie de volgende zelf studies op GitHub:

| Zelfstudie | Beschrijving | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Deze zelf studie laat zien hoe u Akka-streams verbindt met Kafka ingeschakeld Event Hubs zonder uw protocol-clients of uw eigen clusters te wijzigen. Er zijn twee afzonderlijke zelf studies die gebruikmaken van **Java** -en **scala** -programmeer talen. | 
| [Verbinding maken](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Dit document helpt u bij het integreren van Kafka Connect met Azure Event Hubs en het implementeren van basis-FileStreamSource en FileStreamSink-connectors. Hoewel deze connectors niet bestemd zijn voor productie gebruik, demonstreren ze een end-to-end Kafka Connect-scenario waarbij Azure Event Hubs maskers als een Kafka-Broker.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | In dit document wordt stapsgewijs uitgelegd hoe u Filebeat en Event Hubs integreert via de Kafka-uitvoer van Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | In deze zelf studie wordt uitgelegd hoe u verbinding kunt maken met Apache flink met Kafka Event Hubs zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | In dit document wordt uitgelegd hoe u gepasseerde en Event Hubs kunt integreren met behulp `out_kafka` van de uitvoer-invoeg toepassing. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | In deze zelf studie leert u hoe u gebeurtenissen tussen consumenten en producenten uitwisselt met behulp van verschillende protocollen. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Deze zelf studie begeleidt u bij het integreren van Logstash met Kafka-ingeschakeld Event Hubs met Logstash Kafka-invoeg toepassingen voor invoer/uitvoer. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | In deze zelf studie ziet u hoe een Event Hub-en Kafka-MirrorMaker een bestaande Kafka-pijp lijn kan integreren in azure door de Kafka-invoer stroom te spie gelen in de Event Hubs-service. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | In deze zelf studie ziet u hoe u Apache NiFi verbindt met een Event Hubs naam ruimte. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | In Quick starts ziet u hoe u een Event Hubs Kafka-eind punt maakt en er verbinding mee kunt maken met behulp van een voorbeeld producent en een consument die is geschreven in go en Java-programmeer talen. |
| [Schema register confluente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | In deze zelf studie leert u de integratie van schema register en Event Hubs voor Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | In deze zelf studie ziet u hoe u uw Spark-toepassing verbindt met een Event Hub zonder uw protocol-clients te wijzigen of uw eigen Kafka-clusters uit te voeren. | 

### <a name="tutorials-in-docs"></a>Zelf studies in DOCS
Zie ook de zelf studie: [proces Apache Kafka voor Event hubs gebeurtenissen met Stream Analytics](event-hubs-kafka-stream-analytics.md) in deze inhoudsset, die laat zien hoe u gegevens kunt streamen naar Event hubs en deze kunt verwerken met Azure stream Analytics.

## <a name="how-to-guides"></a>Instructiegidsen
Raadpleeg de volgende hand leidingen in onze documentatie:

| Artikel | Beschrijving | 
| ------- | ----------- | 
| [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md) | Laat zien hoe u een Kafka-Broker kunt spie gelen in een Event Hub met behulp van Kafka MirrorMaker. |
| [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md) | Helpt u bij het aansluiten van uw Spark-toepassing op Event Hubs voor realtime-streaming. |
| [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md) | Hier ziet u hoe u Apache flink verbindt met een Event Hub zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. |
| [Apache Kafka verbinding maken met een Event Hub (preview-versie)](event-hubs-kafka-connect-tutorial.md) | Begeleidt u bij het integreren van Kafka Connect met een Event Hub en het implementeren van basis-FileStreamSource en FileStreamSink-connectors. |
| [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md) | Hierin wordt beschreven hoe u Akka-streams verbindt met een Event Hub zonder uw protocol-clients of uw eigen clusters te wijzigen. |
| [De Spring boot starter gebruiken voor Apache Kafka met Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | In dit voor beeld ziet u hoe u een op Java gebaseerde lente-Cloud stroom binder kunt configureren voor gebruik met Apache Kafka met Azure Event Hubs. |

## <a name="next-steps"></a>Volgende stappen
Bekijk de voor beelden in de mappen GitHub opslag plaats [Azure-Event-hubs-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) onder Snelstartgids en zelf studies.

Zie ook de volgende artikelen:

- [Gids voor het oplossen van problemen met Apache Kafka voor Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Veelgestelde vragen-Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka migratie handleiding voor Event Hubs](apache-kafka-migration-guide.md)



