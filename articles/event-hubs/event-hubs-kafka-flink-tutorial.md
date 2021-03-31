---
title: Apache flink gebruiken voor Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het verbinden van Apache flink met een Azure-Event Hub
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369502"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Flink gebruiken met Azure Event Hubs voor Apache Kafka
In deze zelf studie leert u hoe u Apache flink verbindt met een Event Hub zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. Zie [Event hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)voor meer informatie over de ondersteuning van Event hubs voor het Apache Kafka Consumer-protocol.


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Flink-producer uitvoeren 
> * Flink-consument uitvoeren

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u beschikken over de volgende vereisten:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs naam ruimte is vereist voor het verzenden of ontvangen van een Event Hubs service. Zie [een event hub maken](event-hubs-create.md) voor instructies voor het maken van een naam ruimte en een event hub. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u de Event Hubs connection string hebt, kloont u de Azure Event Hubs voor de Kafka-opslag plaats en navigeert u naar de `flink` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink-producer uitvoeren

Gebruik het meegeleverde flink producer-voor beeld om berichten te verzenden naar de Event Hubs-service.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="producerconfig"></a>producer.config

Werk de `bootstrap.servers` `sasl.jaas.config` waarden en in `producer/src/main/resources/producer.config` om de producent om te leiden naar het event hubs Kafka-eind punt met de juiste authenticatie.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Producer uitvoeren vanaf de opdracht regel

Als u de producent wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of Genereer u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

De producent begint nu met het verzenden van gebeurtenissen naar de Event Hub in het onderwerp `test` en het afdrukken van de gebeurtenissen naar stdout.

## <a name="run-flink-consumer"></a>Flink-consument uitvoeren

Gebruik het voor beeld van de meegeleverde gebruiker om berichten te ontvangen van de Event Hub. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="consumerconfig"></a>consumer.config

Werk de `bootstrap.servers` en `sasl.jaas.config` waarden in `consumer/src/main/resources/consumer.config` bij om de consument om te leiden naar het event hubs Kafka-eind punt met de juiste authenticatie.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Consument uitvoeren vanaf de opdracht regel

Als u de gebruiker wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of genereert u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Als de Event Hub gebeurtenissen heeft (bijvoorbeeld als uw producent ook wordt uitgevoerd), begint de Consumer nu met het ontvangen van gebeurtenissen van het onderwerp `test` .

Bekijk [de Kafka-connector gids van flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) voor meer gedetailleerde informatie over het verbinden van flink met Kafka.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs voor Kafka:  

- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Kafka integreren met een Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)