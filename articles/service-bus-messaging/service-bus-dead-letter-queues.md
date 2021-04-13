---
title: Service Bus wacht rijen voor onbestelbare berichten | Microsoft Docs
description: Beschrijft wacht rijen voor onbestelbare berichten in Azure Service Bus. Service Bus-wacht rijen en-onderwerp-abonnementen bieden een secundaire subwachtrij, een zogeheten wachtrij met onbestelbare berichten.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6459c8edd03427357810c1ad30161e87c18e059c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304321"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Overzicht van Service Bus wacht rijen voor onbestelbare berichten

Azure Service Bus-wacht rijen en-onderwerp-abonnementen bieden een secundaire subwachtrij, een zogeheten *wachtrij met onbestelbare berichten* (DLQ). De wachtrij met onbestelbare berichten hoeft niet expliciet te worden gemaakt en kan niet worden verwijderd of beheerd, onafhankelijk van de hoofd entiteit.

In dit artikel worden wacht rijen voor onbestelbare berichten in Service Bus beschreven. Veel van de discussies worden geïllustreerd door het voor [beeld van onbestelbare wacht rijen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) op github.
 
## <a name="the-dead-letter-queue"></a>De wachtrij met onbestelbare berichten

Het doel van de wachtrij voor onbestelbare berichten is het vasthouden van een bericht dat niet aan een ontvanger kan worden geleverd of berichten die niet kunnen worden verwerkt. Berichten kunnen vervolgens worden verwijderd uit de DLQ en geïnspecteerd. Een toepassing kan, met behulp van een operator, problemen oplossen en het bericht opnieuw verzenden, het feit registreren dat er een fout is opgetreden en corrigerende actie ondernemen. 

Vanuit een API en protocol perspectief is de DLQ grotendeels vergelijkbaar met een andere wachtrij, behalve dat berichten alleen kunnen worden verzonden via de bewerking voor onbestelbare brieven van de bovenliggende entiteit. Daarnaast wordt de time-to-Live niet in acht genomen en kunt u niet onbestelbare berichten van een DLQ afschrijven. De wachtrij voor onbestelbare berichten biedt volledige ondersteuning voor Peek-Lock-levering en transactionele bewerkingen.

De DLQ kan niet automatisch worden opgeruimd. Berichten blijven in de DLQ totdat u ze expliciet ophaalt uit de DLQ en het onbestelbare bericht voltooit.


## <a name="dlq-message-count"></a>Aantal DLQ-berichten
Het is niet mogelijk om het aantal berichten in de wachtrij voor onbestelbare meldingen op onderwerpniveau te verkrijgen. Dat komt doordat berichten niet op onderwerpniveau zijn, tenzij Service Bus een interne fout genereert. Wanneer een afzender een bericht naar een onderwerp verzendt, wordt het bericht in de milliseconden doorgestuurd naar abonnementen voor het onderwerp en bevindt deze zich niet meer op het niveau van het onderwerp. U kunt dus berichten weer geven in de DLQ die zijn gekoppeld aan het abonnement voor het onderwerp. In het volgende voor beeld toont **Service Bus Explorer** dat er 62 berichten momenteel aanwezig zijn in de DLQ voor het abonnement test1. 

![Aantal DLQ-berichten](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

U kunt ook het aantal DLQ-berichten ophalen met behulp van de Azure CLI-opdracht: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Berichten verplaatsen naar de DLQ
Er zijn verschillende activiteiten in Service Bus die ertoe leiden dat berichten worden gepusht naar de DLQ vanuit de berichten engine zelf. Een toepassing kan ook expliciet berichten verplaatsen naar de DLQ. De volgende twee eigenschappen (een onbestelbare reden en een onbestelbare beschrijving) worden toegevoegd aan berichten die niet worden geberichtd. Toepassingen kunnen hun eigen codes definiëren voor de eigenschap onbestelbare reden, maar het systeem stelt de volgende waarden in.

| Onbestelbare reden | Fout beschrijving voor onbestelbare berichten |
| --- | --- |
|HeaderSizeExceeded |Het quotum voor de grootte voor deze stream is overschreden. |
|TTLExpiredException |Het bericht is verlopen en naar de wachtrij voor onbestelbare berichten verplaatst. Zie de sectie [time to Live](#time-to-live) voor meer informatie. |
|Sessie-ID is null. |Door sessie ingeschakelde entiteit staat geen berichten toe waarvan de sessie-id null is. |
|MaxTransferHopCountExceeded | Het maximum aantal toegestane hops tijdens het door sturen tussen wacht rijen. De waarde is ingesteld op 4. |
| MaxDeliveryCountExceededExceptionMessage | Bericht kan niet worden gebruikt na maximum aantal bezorgings pogingen. Zie de sectie [maximum aantal leveringen](#maximum-delivery-count) voor meer informatie. |

## <a name="maximum-delivery-count"></a>Maximum aantal bezorgingen
Er is een limiet voor het aantal pogingen om berichten te leveren voor Service Bus wachtrijen en abonnementen. De standaardwaarde is 10. Wanneer een bericht wordt geleverd met een kortere vergren deling, maar expliciet is afgebroken of de vergren deling is verlopen, wordt het aantal bezorgingen in het bericht verhoogd. Wanneer het aantal leveringen de limiet overschrijdt, wordt het bericht verplaatst naar de DLQ. De reden van de onbestelbare berichten voor het bericht in DLQ is ingesteld op: MaxDeliveryCountExceeded. Dit gedrag kan niet worden uitgeschakeld, maar u kunt het maximum aantal bezorgingen instellen op een groot getal.

## <a name="time-to-live"></a>Time To Live
Wanneer u onbestelbare berichten inschakelt voor wacht rijen of abonnementen, worden alle verlopende meldingen naar de DLQ verplaatst. De reden code voor onbestelbare berichten is ingesteld op: TTLExpiredException.

Verlopen berichten worden alleen opgeschoond en verplaatst naar de DLQ wanneer er ten minste één actieve ontvanger wordt opgehaald uit de hoofd wachtrij of het-abonnement. De uitgestelde berichten worden ook niet leeg gemaakt en verplaatst naar de wachtrij met onbestelbare meldingen nadat deze zijn verlopen. Dit gedrag is standaard.

## <a name="errors-while-processing-subscription-rules"></a>Fouten bij het verwerken van abonnements regels
Als u onbestelbare berichten inschakelt voor de uitzonde ringen op filter evaluatie, worden eventuele fouten die optreden tijdens het uitvoeren van de SQL-filter regel van een abonnement, samen met het foutieve bericht in de DLQ vastgelegd. Gebruik deze optie niet in een productie omgeving waarin niet alle bericht typen abonnees hebben.

## <a name="application-level-dead-lettering"></a>Onbestelbare berichten op toepassings niveau
Naast de door het systeem aangelegde functies voor onbestelbare berichten kunnen toepassingen de DLQ gebruiken voor het expliciet afwijzen van niet-aanvaard bare gegevens. Ze kunnen berichten bevatten die niet op de juiste manier kunnen worden verwerkt vanwege een soort systeem probleem, berichten die ongeldige nettoladingen bevatten of berichten die authenticatie mislukken wanneer een beveiligings schema op bericht niveau wordt gebruikt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Onbestelbare berichten in ForwardTo-of SendVia-scenario's
Berichten worden in de volgende gevallen verzonden naar de wachtrij voor onbestelbare overdrachten:

- Een bericht wordt door gegeven door meer dan vier wacht rijen of onderwerpen die [samen worden samengevoegd](service-bus-auto-forwarding.md).
- De doel wachtrij of het onderwerp is uitgeschakeld of verwijderd.
- De doel wachtrij of het onderwerp overschrijdt de maximale entiteits grootte.

## <a name="path-to-the-dead-letter-queue"></a>Pad naar de wachtrij met onbestelbare berichten
U kunt de wachtrij met onbestelbare berichten openen met behulp van de volgende syntaxis:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Service Bus wachtrijen:

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Vergeleken met Azure queues en Service Bus-wacht rijen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

