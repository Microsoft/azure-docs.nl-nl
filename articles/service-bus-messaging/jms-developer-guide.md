---
title: Ontwikkelaars handleiding voor Azure Service Bus JMS 2,0
description: De JMS 2,0-API (Java Message Service) gebruiken om te communiceren met Azure Service Bus
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 6c535b12906b6d9385029896dc5d0caf85d3399a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654172"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Ontwikkelaars handleiding voor Azure Service Bus JMS 2,0

Deze hand leiding bevat gedetailleerde informatie die u kan helpen bij de communicatie met Azure Service Bus met behulp van de JMS-API (Java Message Service) 2,0.

Als Java-ontwikkelaar, als u geen ervaring hebt met Azure Service Bus, kunt u de onderstaande artikelen lezen.

| Aan de slag | Concepten |
|----------------|-------|
| <ul> <li> [Wat is Azure Service Bus](service-bus-messaging-overview.md) </li> <li> [Wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus-Premium-laag](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>JMS-programmeer model (Java Message Service)

Het API-programmeer model voor Java Message Service is zoals hieronder weer gegeven- 

> [!NOTE]
>
>**Azure service bus Premium-laag** ondersteunt jms 1,1 en JMS 2,0.
> <br> <br>
> **Azure service bus-Standard-** laag ondersteunt beperkte jms 1,1-functionaliteit. Raadpleeg deze [documentatie](service-bus-java-how-to-use-jms-api-amqp.md)voor meer informatie.
>

# <a name="jms-20-programming-model"></a>[JMS 2,0-programmeer model](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png"alt-text="Diagram met het JMS 2,0-programmeer model."border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1,1-programmeer model](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png"alt-text="Diagram met het JMS 1,1-programmeer model."border="false":::

---

## <a name="jms---building-blocks"></a>JMS-bouw stenen

De onderstaande bouw stenen zijn beschikbaar om te communiceren met de JMS-toepassing.

> [!NOTE]
> De onderstaande hand leiding is aangepast met de [Oracle Java EE 6-zelf studie voor Java Message Service (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> Raadpleeg deze zelf studie voor meer informatie over de Java-bericht service (JMS).
>

### <a name="connection-factory"></a>Connection Factory
Het Connection Factory-object wordt door de client gebruikt om verbinding te maken met de JMS-provider. De Connection Factory encapsulateert een reeks verbindings configuratie parameters die door de beheerder zijn gedefinieerd.

Elke Connection Factory is een instantie van `ConnectionFactory` `QueueConnectionFactory` of `TopicConnectionFactory` interface.

Om het verbinden met Azure Service Bus te vereenvoudigen, worden deze interfaces geïmplementeerd via `ServiceBusJmsConnectionFactory` `ServiceBusJmsQueueConnectionFactory` en `ServiceBusJmsTopicConnectionFactory` respectievelijk. De Connection Factory kan worden geïnstantieerd met de onderstaande para meters: 
   * Verbindings reeks: de connection string voor de naam ruimte van de Azure Service Bus Premium-laag.
   * ServiceBusJmsConnectionFactorySettings van eigenschappen verzameling met
      * connectionIdleTimeoutMS-time-out voor niet-actieve verbindingen in milliseconden.
      * traceFrames: Boole-vlag voor het verzamelen van AMQP-tracerings frames voor fout opsporing.
      * *andere configuratie parameters*

U kunt de fabriek als volgt maken. De connection string is een vereiste para meter, maar de aanvullende eigenschappen zijn optioneel.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Java-toepassingen die gebruikmaken van de JMS 2,0-API, moeten alleen verbinding maken met Azure Service Bus met behulp van de connection string. Verificatie voor JMS-clients wordt momenteel alleen ondersteund met behulp van de verbindings reeks.
>
> Back-upverificatie van Azure Active Directory (AAD) wordt momenteel niet ondersteund.
>

### <a name="jms-destination"></a>JMS-doel

Een doel is het object dat door een client wordt gebruikt om het doel van de gegenereerde berichten op te geven en de bron van de berichten die het gebruikt.

Doelen worden toegewezen aan entiteiten in Azure Service Bus-wacht rijen (in Point-to-Point scenario's) en onderwerpen (in publicatie-sub scenario's).

### <a name="connections"></a>Verbindingen

Een verbinding heeft een virtuele verbinding ingekapseld met een JMS-provider. Met Azure Service Bus vertegenwoordigt dit een stateful verbinding tussen de toepassing en Azure Service Bus over AMQP.

Er wordt een verbinding gemaakt op basis van de Connection Factory, zoals hieronder wordt weer gegeven.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessies

Een sessie is een context met één thread voor het produceren en gebruiken van berichten. Het kan worden gebruikt voor het maken van berichten, producenten van berichten en consumenten, maar biedt ook een transactionele context om het groeperen van verzenden en ontvangen van een atomische werk eenheid toe te staan.

Een sessie kan worden gemaakt op basis van het verbindings object, zoals hieronder wordt weer gegeven.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Sessie modi

Een sessie kan worden gemaakt met een van de onderstaande modi.

| Sessie modi | Gedrag |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | De sessie bevestigt automatisch de ontvangst van een bericht wanneer de sessie is geretourneerd door een aanroep om te ontvangen of wanneer de Message listener de sessie heeft aangeroepen om het bericht te verwerken.|
|**Session.CLIENT_ACKNOWLEDGE** |De client bevestigt een geconsumeerd bericht door de bevestigings methode van het bericht aan te roepen.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Deze bevestigings modus geeft de sessie aan vertraagd de bezorging van berichten te bevestigen.| 
|**Session.SESSION_TRANSACTED**|Deze waarde kan worden door gegeven als het argument voor de methode createSession (int sessionMode) op het verbindings object om op te geven dat de sessie een lokale trans actie moet gebruiken.| 

Wanneer de sessie modus niet is opgegeven, wordt de **Session.AUTO_ACKNOWLEDGE** standaard geselecteerd.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext is gedefinieerd als onderdeel van de JMS 2,0-specificatie.
>

JMSContext combineert de functionaliteit van de verbinding en het sessie object. Het kan worden gemaakt op basis van het Connection Factory-object.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext modi

Net als bij het object **Session** kan de JMSContext worden gemaakt met dezelfde bevestigings modi als vermeld in de [sessie modus](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Wanneer de modus niet is opgegeven, wordt de **JMSContext.AUTO_ACKNOWLEDGE** standaard geselecteerd.

### <a name="jms-message-producers"></a>JMS-bericht producenten

Een bericht producent is een object dat is gemaakt met behulp van een JMSContext of een sessie en wordt gebruikt voor het verzenden van berichten naar een bestemming.

Het kan worden gemaakt als zelfstandig object: 

```java
JMSProducer producer = context.createProducer();
```

of tijdens runtime gemaakt wanneer een bericht moet worden verzonden.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS-bericht gebruikers

Een bericht verbruiker is een object dat wordt gemaakt door een JMSContext of een sessie en wordt gebruikt voor het ontvangen van berichten die naar een bestemming worden verzonden. Deze kan worden gemaakt zoals hieronder wordt weer gegeven:

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>De methode synchrone ontvangen via receive ()

De consument van het bericht biedt een synchrone manier om via de methode berichten van de bestemming te ontvangen `receive()` .

Als er geen argumenten/time-out is opgegeven of als er een time-out van ' 0 ' is opgegeven, blokkeert de Consumer voor onbepaalde tijd tenzij het bericht binnenkomt, of de verbinding wordt verbroken (afhankelijk van wat er eerder is).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Wanneer een niet-nul positief argument wordt gegeven, wordt de Consumer geblokkeerd totdat de timer verloopt.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Asynchrone ontvangst met JMS-bericht listeners

Een Message listener is een object dat wordt gebruikt voor asynchrone verwerking van berichten op een bestemming. Hiermee wordt de- `MessageListener` Interface geïmplementeerd die de `onMessage` methode bevat waarin de specifieke bedrijfs logica moet worden gebruikt.

Een Message listener-object moet worden geïnstantieerd en geregistreerd bij een specifieke bericht verbruiker met behulp van de `setMessageListener` methode.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Gebruiken van onderwerpen

[JMS-bericht gebruikers](#jms-message-consumers) worden gemaakt op basis van een [bestemming](#jms-destination) die een wachtrij of onderwerp kan zijn.

Consumenten in wacht rijen zijn eenvoudigweg objecten aan de client zijde die in de context van de sessie (en verbinding) tussen de client toepassing en Azure Service Bus worden geplaatst.

Consumenten op onderwerpen hebben echter twee onderdelen: 
   * Een object aan de client zijde dat zich in de context van de sessie (of JMSContext) **bevindt** , en
   * Een **abonnement** dat een entiteit op Azure service bus is.

De abonnementen worden [hier](java-message-service-20-entities.md#java-message-service-jms-subscriptions) beschreven en kunnen een van de onderstaande zijn: 
   * Gedeelde duurzame abonnementen
   * Gedeelde niet-duurzame abonnementen
   * Niet-gedeelde duurzame abonnementen
   * Niet-verdeelde niet-duurzame abonnementen

### <a name="jms-queue-browsers"></a>JMS-wachtrij browsers

De JMS-API biedt een `QueueBrowser` object waarmee de toepassing door de berichten in de wachtrij kan bladeren en de header waarden voor elk bericht kan weer geven.

Een wachtrij browser kan worden gemaakt met behulp van de JMSContext zoals hieronder.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API biedt geen API om te bladeren in een onderwerp.
>
> Dit komt doordat het onderwerp zelf de berichten niet opslaat. Zodra het bericht naar het onderwerp wordt verzonden, wordt het doorgestuurd naar de juiste abonnementen.
>

### <a name="jms-message-selectors"></a>Bericht selectie JMS

Bericht selectie vakjes kunnen worden gebruikt door ontvangende toepassingen om de ontvangen berichten te filteren. Met bericht selectieers offloadt de ontvangende toepassing het werk van het filteren van berichten naar de JMS-provider (in dit geval Azure Service Bus) in plaats van deze verantwoordelijkheid zelf te maken.

Selecters kunnen worden gebruikt bij het maken van een van de volgende gebruikers: 
   * Gedeeld duurzaam abonnement
   * Niet-gedeeld duurzaam abonnement
   * Gedeeld niet-duurzaam abonnement
   * Niet-gedeeld niet-duurzaam abonnement
   * Wachtrij browser


## <a name="summary"></a>Samenvatting

In deze ontwikkelaars handleiding wordt uitgelegd hoe Java-client toepassingen die gebruikmaken van Java Message Service (JMS) verbinding kunnen maken met Azure Service Bus.

## <a name="next-steps"></a>Volgende stappen

Bekijk de onderstaande koppelingen voor meer informatie over Azure Service Bus en informatie over JMS-entiteiten (Java Message Service). 
* [Service Bus-wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java-berichten service-entiteiten](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Ondersteuning voor AMQP 1,0 in Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0-hand leiding voor ontwikkel aars](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [API voor Java-berichten service (extern Oracle-document)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Meer informatie over hoe u migreert van ActiveMQ naar Service Bus](migrate-jms-activemq-to-servicebus.md)
