---
title: Akka-streams gebruiken voor Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het verbinden van Akka-streams met een Azure Event Hub.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 92ab927189329493696c70b61ffc7f11cad22a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369570"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Akka Streams gebruiken met Event Hubs voor Apache Kafka

In deze zelf studie leert u hoe u Akka-streams verbindt via de Event Hubs ondersteuning voor Apache Kafka zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Akka-streams uitvoeren 
> * Akka streams Consumer uitvoeren

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u beschikken over de volgende vereisten:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.8+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs naam ruimte is vereist voor het verzenden of ontvangen van een Event Hubs service. Zie [een event hub maken](event-hubs-create.md) voor gedetailleerde informatie. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een Event Hubs connection string hebt, kloont u de Azure Event Hubs voor de Kafka-opslag plaats en navigeert u naar de `akka` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Akka-streams uitvoeren

Gebruik het meegeleverde Akka streams producer-voor beeld om berichten naar de Event Hubs-service te verzenden.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="producer-applicationconf"></a>Producer Application. conf

Werk de `bootstrap.servers` `sasl.jaas.config` waarden en in `producer/src/main/resources/application.conf` om de producent om te leiden naar het event hubs Kafka-eind punt met de juiste authenticatie.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-producer-from-the-command-line"></a>Producer uitvoeren vanaf de opdracht regel

Als u de producent wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of Genereer u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

De producent begint met het verzenden van gebeurtenissen naar de Event Hub in het onderwerp `test` en drukt de gebeurtenissen af op stdout.

## <a name="run-akka-streams-consumer"></a>Akka streams Consumer uitvoeren

Gebruik het voor beeld van de meegeleverde gebruiker om berichten te ontvangen van de Event Hub.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="consumer-applicationconf"></a>Consumer Application. conf

Werk de `bootstrap.servers` en `sasl.jaas.config` waarden in `consumer/src/main/resources/application.conf` bij om de consument om te leiden naar het event hubs Kafka-eind punt met de juiste authenticatie.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Consument uitvoeren vanaf de opdracht regel

Als u de gebruiker wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of genereert u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Als de Event Hub gebeurtenissen heeft (bijvoorbeeld als uw producent ook wordt uitgevoerd), begint de Consumer gebeurtenissen vanuit het onderwerp te ontvangen `test` . 

Bekijk de [Akka-hand leiding voor Kafka-stromen](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) voor meer gedetailleerde informatie over Akka-streams.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs voor Kafka:  

- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Kafka integreren met een Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)