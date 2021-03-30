---
title: 'Quickstart: Streamen van gegevens met Azure Event Hubs met het Kafka-Protocol'
description: "Quickstart: Dit artikel bevat informatie over het streamen naar Azure Event Hubs met behulp van het Kafka-protocol en API's."
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 2020534a3984453bcd6eff7ad0f5c02d9e7a29ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368346"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Quickstart: Streamen naar Event Hubs met behulp van API's en het Kafka-protocol
Deze snelstart laat zien hoe u kunt streamen naar Event Hubs zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. U leert hoe u uw producenten kunt gebruiken en consumenten met Event Hubs, kunnen praten met slechts een configuratiewijziging in uw toepassingen. 

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door.
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een Event Hubs-naamruimte van het type **Standaard** maakt, wordt het Kafka-eindpunt voor de naamruimte automatisch ingeschakeld. U kunt gebeurtenissen vanaf uw toepassingen, waarbij gebruikgemaakt wordt van het Kafka-protocol, naar Event Hubs van het type Standaard streamen. Volg de stapsgewijze instructies in [Een Event Hub maken met behulp van Azure Portal](event-hubs-create.md) om een Event Hubs-naamruimte van het type **Standaard** te maken. 

> [!NOTE]
> Voor Kafka zijn alleen Event Hubs van het type **Standaard** en **Toegewezen** beschikbaar. Het type **Basis** biedt geen ondersteuning voor Kafka op Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Berichten verzenden en ontvangen met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Werk de configuratiegegevens voor de producent in `src/main/resources/producer.config` als volgt bij:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
    > [!IMPORTANT]
    > Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    U vindt de broncode voor de voorbeeld-handlerklasse CustomAuthenticateCallbackHandler in GitHub [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Voer de producentcode uit en stream gebeurtenissen naar Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Werk de configuratiegegevens voor de consument in `src/main/resources/consumer.config` als volgt bij:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    > [!IMPORTANT]
    > Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    U vindt de broncode voor de voorbeeld-handlerklasse CustomAuthenticateCallbackHandler in GitHub [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    U vindt alle OAuth-voorbeelden voor Event Hubs voor Kafka [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Voer de consumentcode en verwerk gebeurtenissen vanuit Event Hubs met behulp van uw Kafka-clients:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Als uw Event Hubs Kafka-cluster gebeurtenissen heeft, moet u deze nu ontvangen van de consument.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Zie [Ontwikkelaarshandleiding van Apache Kafka voor Azure Event Hubs](apache-kafka-developer-guide.md) voor meer informatie.