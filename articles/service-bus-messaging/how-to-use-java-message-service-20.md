---
title: De API voor Java-berichten Service 2,0 met Azure Service Bus Premium gebruiken
description: De Java-berichten service (JMS) gebruiken met Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698396"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>De API voor Java-berichten Service 2,0 met Azure Service Bus Premium gebruiken

In dit artikel wordt uitgelegd hoe u de **JMS 2,0-API (populaire Java Message Service)** gebruikt om te communiceren met Azure service bus over het protocol Advanced Message queueing Protocol (AMQP 1,0).

> [!NOTE]
> Ondersteuning voor de JMS-API (Java Message Service) 2,0 is alleen beschikbaar in de **laag Azure service bus Premium**.
>

## <a name="pre-requisites"></a>Vereisten

### <a name="get-started-with-service-bus"></a>Aan de slag met Service Bus

In deze hand leiding wordt ervan uitgegaan dat u al een Service Bus naam ruimte hebt. Als dat niet het geval is, kunt u [de naam ruimte en wachtrij maken](service-bus-create-namespace-portal.md) met behulp van de [Azure Portal](https://portal.azure.com). 

Zie [aan de slag met Service Bus wachtrijen via de Azure Portal](service-bus-quickstart-portal.md)voor meer informatie over het maken van service bus naam ruimten en wacht rijen.

### <a name="set-up-a-java-development-environment"></a>Een Java-ontwikkel omgeving instellen

Voor het ontwikkelen van Java-toepassingen moet u de juiste ontwikkel omgeving instellen: 
   * De JDK (Java Development Kit) of de JRE (Java Runtime Environment) is geïnstalleerd.
   * De JDK of JRE wordt toegevoegd aan het build-pad en de relevante systeem variabelen.
   * Er wordt een Java IDE geïnstalleerd om gebruik te maken van de JDK of JRE. Bijvoorbeeld: eclips of IntelliJ.

Gebruik [deze hand leiding](/azure/developer/java/fundamentals/)voor meer informatie over het voorbereiden van uw ontwikkel omgeving voor Java op Azure.

## <a name="what-jms-features-are-supported"></a>Welke JMS-functies worden ondersteund?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>De client bibliotheek van de Java-berichten service (JMS) downloaden

Als u gebruik wilt maken van alle functies die beschikbaar zijn op Azure Service Bus Premium-laag, moet de onderstaande bibliotheek worden toegevoegd aan het build-pad van het project.

[Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Als u de [Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) aan het build-pad wilt toevoegen, gebruikt u het hulp programma voor het beheer van de voor keuren voor uw project zoals [maven](https://maven.apache.org/) of [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Java-toepassingen coderen

Zodra de afhankelijkheden zijn geïmporteerd, kunnen de Java-toepassingen worden geschreven in een JMS provider neutraal.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Verbinding maken met Azure Service Bus met behulp van JMS

Als u verbinding wilt maken met Azure Service Bus met behulp van JMS-clients, hebt u de **Connection String** die beschikbaar is in het beleid voor gedeelde toegang in de [Azure Portal](https://portal.azure.com) onder **primaire verbindings reeks**.

1. Exemplaar maken van de `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Exemplaar `ServiceBusJmsConnectionFactory` met de juiste `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Gebruik de `ConnectionFactory` om een te maken `Connection` en vervolgens een `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    of a `JMSContext` (voor JMS 2,0-clients)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Hoewel een JMS-sessie en Service Bus-sessie ook volledig onafhankelijk van elkaar worden genoemd.
    >
    > In JMS 1,1 is Session een essentiële bouw steen van de API waarmee de MessageProducer, MessageConsumer en het bericht zelf kunnen worden gemaakt. Raadpleeg het [JMS API programming model](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) voor meer informatie.
    >
    > In Service Bus zijn [sessies](message-sessions.md) service-en client zijde construct om FIFO-verwerking op wacht rijen en abonnementen in te scha kelen.
    >

### <a name="write-the-jms-application"></a>De JMS-toepassing schrijven

Zodra de `Session` of `JMSContext` is geïnstantieerd, kan uw toepassing gebruikmaken van de vertrouwde JMS-api's om zowel beheer-als gegevens bewerkingen uit te voeren.

Raadpleeg de lijst met [ondersteunde JMS-functies](how-to-use-java-message-service-20.md#what-jms-features-are-supported) om te zien welke api's worden ondersteund.

Hieronder ziet u enkele voor beelden van code fragmenten om aan de slag te gaan met JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Berichten verzenden naar een wachtrij en onderwerp

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Berichten ontvangen van een wachtrij

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Berichten ontvangen van een gedeeld duurzaam abonnement op een onderwerp

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Samenvatting

In deze hand leiding wordt uitgelegd hoe Java-client toepassingen die gebruikmaken van Java Message Service (JMS) via AMQP 1,0 kunnen communiceren met Azure Service Bus.

U kunt ook Service Bus AMQP 1,0 gebruiken uit andere talen, waaronder .NET, C, python en PHP. Onderdelen die zijn gebouwd met behulp van deze verschillende talen kunnen berichten veilig en met volledige kwaliteit uitwisselen met behulp van de AMQP 1,0-ondersteuning in Service Bus.

## <a name="next-steps"></a>Volgende stappen

Bekijk de onderstaande koppelingen voor meer informatie over Azure Service Bus en informatie over JMS-entiteiten (Java Message Service). 
* [Service Bus-wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java-berichten service-entiteiten](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Ondersteuning voor AMQP 1,0 in Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0-hand leiding voor ontwikkel aars](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [API voor Java-berichten service (extern Oracle-document)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Meer informatie over hoe u migreert van ActiveMQ naar Service Bus](migrate-jms-activemq-to-servicebus.md)