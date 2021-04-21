---
title: Service Bus wachtrijen voor niet-| Microsoft Docs
description: Beschrijft wachtrijen met in wachtrijen Azure Service Bus. Service Bus wachtrijen en onderwerpabonnementen bieden een secundaire subqueue, ook wel een wachtrij voor inlopende berichten genoemd.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812368"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Overzicht van Service Bus wachtrijen voor niet-lees berichten

Azure Service Bus wachtrijen en onderwerpabonnementen bieden een secundaire subqueue, ook wel een *wachtrij voor inlopende* letters (DLQ) genoemd. De wachtrij voor in wachtrijen geplaatste berichten hoeft niet expliciet te worden gemaakt en kan niet worden verwijderd of beheerd, onafhankelijk van de hoofdentiteit.

In dit artikel worden wachtrijen met inlopende berichten Service Bus. Een groot deel van de discussie wordt geïllustreerd door het [voorbeeld van wachtrijen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) met wachtrijen voor in wachtrijen in Een wachtrij voor een impasse op GitHub.
 
## <a name="the-dead-letter-queue"></a>De wachtrij voor in wachtrij voor in wachtrij geplaatste berichten

Het doel van de wachtrij voor niet-bezorgde berichten is om berichten te houden die niet aan een ontvanger kunnen worden geleverd of berichten die niet kunnen worden verwerkt. Berichten kunnen vervolgens worden verwijderd uit de DLQ en worden geïnspecteerd. Een toepassing kan, met behulp van een operator, problemen corrigeren en het bericht opnieuw indienen, het feit melden dat er een fout is opgetreden en corrigerende actie ondernemen. 

Vanuit het perspectief van een API en protocol is de DLQ voornamelijk vergelijkbaar met elke andere wachtrij, behalve dat berichten alleen kunnen worden verzonden via de dead-letter-bewerking van de bovenliggende entiteit. Bovendien wordt time-to-live niet waargenomen en kunt u een bericht van een DLQ niet in een dead-letter schrijven. De wachtrij voor niet-bezorgde berichten biedt volledige ondersteuning voor levering van peek-lock en transactionele bewerkingen.

Er is geen automatische opschoning van de DLQ. Berichten blijven in de DLQ totdat u ze expliciet uit de DLQ op haalt en het bericht over de inlopende berichten voltooit.


## <a name="dlq-message-count"></a>Aantal DLQ-berichten
Het is niet mogelijk om het aantal berichten in de wachtrij voor berichten in de wachtrij voor berichten te verkrijgen op onderwerpniveau. Dat komt doordat berichten niet op het niveau van het onderwerp zitten, tenzij Service Bus een interne fout veroorzaakt. Wanneer een afzender in plaats daarvan een bericht naar een onderwerp verzendt, wordt het bericht binnen milliseconden doorgestuurd naar abonnementen voor het onderwerp en bevindt het zich dus niet meer op onderwerpniveau. U kunt dus berichten zien in de DLQ die is gekoppeld aan het abonnement voor het onderwerp. In het volgende voorbeeld **Service Bus Explorer** dat de DLQ 62 berichten bevat voor het abonnement test1. 

![Aantal DLQ-berichten](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

U kunt ook het aantal DLQ-berichten krijgen met behulp van de Azure CLI-opdracht: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Berichten verplaatsen naar de DLQ
Er zijn verschillende activiteiten in Service Bus ervoor zorgen dat berichten naar de DLQ worden pushen vanuit de berichten-engine zelf. Een toepassing kan ook expliciet berichten naar de DLQ verplaatsen. De volgende twee eigenschappen (dead-letter reason en dead-letter description) worden toegevoegd aan berichten met een dead-letter. Toepassingen kunnen hun eigen codes definiëren voor de eigenschap dead-letter reason, maar het systeem stelt de volgende waarden in.

| Reden voor inlopende letter | Beschrijving van fout in dead-letter |
| --- | --- |
|HeaderSizeExceeded |Het quotum voor de grootte voor deze stream is overschreden. |
|TTLExpiredException |Het bericht is verlopen en naar de wachtrij voor onbestelbare berichten verplaatst. Zie de [sectie Time to Live](#time-to-live) voor meer informatie. |
|Sessie-id is null. |Door sessie ingeschakelde entiteit staat geen berichten toe waarvan de sessie-id null is. |
|MaxTransferHopCountExceeded | Het maximum aantal toegestane hops bij het doorsturen tussen wachtrijen. De waarde is ingesteld op 4. |
| MaxDeliveryCountExceededExceptionMessage | Het bericht kan niet worden gebruikt na maximale bezorgingspogingen. Zie de [sectie Maximum aantal leveringen](#maximum-delivery-count) voor meer informatie. |

## <a name="maximum-delivery-count"></a>Maximum aantal leveringen
Er geldt een limiet voor het aantal pogingen om berichten te leveren Service Bus wachtrijen en abonnementen. De standaardwaarde is 10. Wanneer een bericht is afgeleverd onder een peek-lock, maar expliciet is verlaten of de vergrendeling is verlopen, wordt het aantal leveringen voor het bericht verhoogd. Wanneer het aantal leveringen de limiet overschrijdt, wordt het bericht verplaatst naar de DLQ. De reden van de onbewerkte letter voor het bericht in DLQ is ingesteld op: MaxDeliveryCountExceeded. Dit gedrag kan niet worden uitgeschakeld, maar u kunt het maximum aantal leveringen instellen op een groot aantal.

## <a name="time-to-live"></a>Time To Live
Wanneer u in wachtrijen of abonnementen dead-lettering inschakelen, worden alle verlopen berichten verplaatst naar de DLQ. De redencode voor de impasse is ingesteld op: TTLExpiredException.

Verlopen berichten worden alleen verwijderd en verplaatst naar de DLQ wanneer er ten minste één actieve ontvanger is die uit de hoofdwachtrij of het hoofdabonnement haalt. De uitgestelde berichten worden ook niet verwijderd en na het verlopen naar de wachtrij voor niet-leesberichten verplaatst. Dit gedrag is standaard.

## <a name="errors-while-processing-subscription-rules"></a>Fouten tijdens het verwerken van abonnementsregels
Als u dead-lettering inschakelen voor filterevaluatie-uitzonderingen, worden eventuele fouten die optreden tijdens het uitvoeren van de SQL-filterregel van een abonnement vastgelegd in de DLQ, samen met het beledigende bericht. Gebruik deze optie niet in een productieomgeving waarin niet alle berichttypen abonnees hebben.

## <a name="application-level-dead-lettering"></a>Dead lettering op toepassingsniveau
Naast de systeemfuncties voor dead-lettering kunnen toepassingen de DLQ gebruiken om onacceptabele berichten expliciet af te wijzen. Ze kunnen berichten bevatten die niet goed kunnen worden verwerkt vanwege een soort systeemprobleem, berichten die verkeerd vorm geven nettoladingen bevatten of berichten die niet kunnen worden verificatie wanneer een beveiligingsschema op berichtniveau wordt gebruikt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering in ForwardTo- of SendVia-scenario's
Berichten worden onder de volgende voorwaarden verzonden naar de wachtrij voor in wachtrij voor niet-voltooide overdrachten:

- Een bericht doorstaat meer dan vier wachtrijen of onderwerpen die [aan elkaar zijn vastgeketend.](service-bus-auto-forwarding.md)
- De doelwachtrij of het doelonderwerp is uitgeschakeld of verwijderd.
- De doelwachtrij of het doelonderwerp overschrijdt de maximale entiteitsgrootte.

## <a name="path-to-the-dead-letter-queue"></a>Pad naar de wachtrij voor inlopende berichten
U kunt toegang krijgen tot de wachtrij voor in wachtrijen met de volgende syntaxis:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Volgende stappen
Zie [Enable dead lettering for a queue or subscription](enable-dead-letter.md) (Inschakelen van in wachtrijen of abonnementen) voor meer informatie over verschillende manieren om de instelling voor het verlopen van berichten te configureren voor het instellen van inlopende berichten. 
