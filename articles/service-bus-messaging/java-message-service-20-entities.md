---
title: Azure Service Bus Messa ging-service-entiteiten voor Java-berichten
description: Dit artikel bevat een overzicht van Azure Service Bus Messa ging-entiteiten die toegankelijk zijn via de API voor Java-berichten service.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652578"
---
# <a name="java-message-service-jms-20-entities"></a>JMS-entiteiten (Java Message Service) 2,0

Client toepassingen die verbinding maken met Azure Service Bus Premium en de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kunnen de onderstaande entiteiten gebruiken.

## <a name="queues"></a>Wachtrijen

Wacht rijen in JMS zijn semantisch vergelijkbaar met de traditionele [service bus-wacht rijen](service-bus-queues-topics-subscriptions.md#queues).

Als u een wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Onderwerpen

De onderwerpen in JMS zijn semantisch vergelijkbaar met de traditionele [Service Bus-onderwerpen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Als u een onderwerp wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Tijdelijke wacht rijen

Als voor een client toepassing een tijdelijke entiteit is vereist die voor de levens duur van de toepassing bestaat, kan deze gebruikmaken van tijdelijke wacht rijen. Deze entiteiten worden gebruikt in het patroon voor [aanvraag/antwoord](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Als u een tijdelijke wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tijdelijke onderwerpen

Net als tijdelijke wacht rijen bestaan er tijdelijke onderwerpen om publiceren/abonneren mogelijk te maken via een tijdelijke entiteit die bestaat voor de levens duur van de toepassing.

Als u een tijdelijk onderwerp wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS-abonnementen (Java Message Service)

Hoewel dit op semantische wijze lijkt op de [abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (die zich in een onderwerp bevinden en de semantiek van publiceren/abonneren inschakelen), introduceert de Java-bericht service specificatie de concepten van **gedeelde**, niet- **gedeelde**, * * duurzame en **niet-duurzame** kenmerken voor een bepaald abonnement.

> [!NOTE]
> De onderstaande abonnementen zijn beschikbaar in Azure Service Bus Premium-laag voor client toepassingen die verbinding maken met Azure Service Bus met behulp van de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Alleen duurzame abonnementen kunnen worden gemaakt met behulp van de Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Gedeelde duurzame abonnementen

Er wordt een gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing altijd actief verbruikt van het abonnement.

Elke toepassing die is geverifieerd om te ontvangen van Service Bus kan worden ontvangen van het gedeelde duurzame abonnement.

Als u een gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden voor de `JMSContext` klasse-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Het gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Niet-gedeelde duurzame abonnementen

Net als bij een gedeeld duurzaam abonnement wordt een niet-gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing actief gebruikmaakt van het abonnement.

Omdat dit echter een niet-gedeeld abonnement is, kan alleen de toepassing die het abonnement heeft gemaakt hiervan worden ontvangen.

Als u een niet-gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden van `JMSContext` klasse- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Gedeelde niet-duurzame abonnementen

Een gedeeld niet-duurzaam abonnement wordt gebruikt wanneer meerdere client toepassingen berichten van een enkel abonnement moeten ontvangen en verwerken, alleen tot ze er actief op worden verbruikt/ontvangen.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve gebruikers zijn.

Als u een gedeeld niet-duurzaam abonnement wilt maken, maakt u een `JmsConsumer` zoals wordt weer gegeven in de onderstaande methoden van de `JMSContext` klasse-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Het gedeelde niet-duurzame abonnement blijft bestaan tot er actieve consumers worden ontvangen.

### <a name="unshared-non-durable-subscriptions"></a>Niet-verdeelde niet-duurzame abonnementen

Er wordt een niet-verwisselbaar niet-duurzaam abonnement gebruikt wanneer de client toepassing een bericht van een abonnement moet ontvangen en verwerken, alleen tot het systeem actief is. Er kan slechts één consument bestaan in dit abonnement, dat wil zeggen, de client die het abonnement heeft gemaakt.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve consument op is.

Als u een niet-duurzaam niet-gedeeld abonnement wilt maken, maakt u een `JMSConsumer` zoals wordt weer gegeven in de onderstaande methoden van de `JMSContext` klasse-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-duurzame niet-duurzaam abonnement blijft bestaan totdat er een actieve Consumer is die er een ontvangt.

### <a name="message-selectors"></a>Bericht kiezer

Net zoals **filters en acties** bestaan voor gewone service bus abonnementen, bestaan er **bericht selectieers** voor JMS-abonnementen.

Bericht selectie vakjes kunnen worden ingesteld voor elk van de JMS-abonnementen en bestaan als filter voorwaarde voor de eigenschappen van de bericht header. Alleen berichten met header-eigenschappen die overeenkomen met de bericht kiezer-expressie worden geleverd. Een waarde van null of een lege teken reeks geeft aan dat er geen bericht kiezer is voor het JMS-abonnement/de gebruiker.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Aanvullende concepten voor JMS (Java Message Service) 2,0-abonnementen

### <a name="client-scoping"></a>Bereik van client

Abonnementen, zoals opgegeven in de JMS-API (Java Message Service) 2,0, kunnen al dan niet worden *beperkt tot specifieke client toepassingen/s* (geïdentificeerd met de juiste `clientId` ).

Zodra het abonnement is bereikt, is het **alleen toegankelijk** vanuit client toepassingen die dezelfde client-id hebben. 

Pogingen om toegang te krijgen tot een abonnement dat is gericht op een specifieke client-ID (bijvoorbeeld clientId1) van een toepassing die een andere client-ID heeft (bijvoorbeeld clientId2), zal leiden tot het maken van een ander abonnement dat is afgestemd op de andere client-ID (clientId2).

> [!NOTE]
> De client-ID kan null of leeg zijn, maar moet overeenkomen met de client-ID die is ingesteld op de client toepassing JMS. Vanuit het Azure Service Bus perspectief hebben een null-client-ID en een lege client-id hetzelfde gedrag.
>
> Als de client-ID is ingesteld op null of leeg, is deze alleen toegankelijk voor client toepassingen waarvan de client-ID ook is ingesteld op null of leeg.
>

### <a name="shareability"></a>Moeten delen

Met **gedeelde** abonnementen kunnen meerdere client/Consumer (dat wil zeggen, JMSConsumer-objecten) berichten van deze aanvragen ontvangen.

>[!NOTE]
> Gedeelde abonnementen die een specifieke client-ID bereiken, kunnen nog steeds worden gebruikt door meerdere client-consumers (JMSConsumer-objecten), maar alle client toepassingen moeten dezelfde client-ID hebben.
>
 

Bij niet- **gedeelde** abonnementen is slechts één client/Consumer (dat wil zeggen, JMSConsumer-object) toegestaan om berichten van deze te ontvangen. Als een `JMSConsumer` is gemaakt in een niet-gedeeld abonnement terwijl er al een actief `JMSConsumer` naar berichten wordt geluisterd, wordt er een `JMSException` gegenereerd.


### <a name="durability"></a>Duurzaamheid

**Duurzame** abonnementen worden bewaard en blijven berichten ontvangen van het onderwerp, ongeacht of een toepassing ( `JMSConsumer` ) berichten van de app gebruikt.

**Niet-duurzame** abonnementen worden niet persistent gemaakt en verzamelen berichten van het onderwerp, zolang er door een toepassing ( `JMSConsumer` ) berichten worden verbruikt. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representatie van abonnementen op client bereik

Omdat de JMS-abonnementen (client scoped) naast de bestaande [abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)moeten bestaan, de manier waarop de client-abonnementen (JMS) worden weer gegeven, volgt u de onderstaande indeling.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (voor duurzame abonnementen)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (voor niet-duurzame abonnementen)

Hier **$** is het scheidings teken.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voor beelden van het gebruik van Service Bus Messa ging:

* [Overzicht van Service Bus berichten](service-bus-messaging-overview.md)
* [De API voor Java-berichten Service 2,0 met Azure Service Bus Premium gebruiken](how-to-use-java-message-service-20.md)



