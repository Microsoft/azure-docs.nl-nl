---
title: Service Bus wacht rijen voor onbestelbare berichten | Microsoft Docs
description: Beschrijft wacht rijen voor onbestelbare berichten in Azure Service Bus. Service Bus-wacht rijen en-onderwerp-abonnementen bieden een secundaire subwachtrij, een zogeheten wachtrij met onbestelbare berichten.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630095"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Overzicht van Service Bus wacht rijen voor onbestelbare berichten

Azure Service Bus-wacht rijen en-onderwerp-abonnementen bieden een secundaire subwachtrij, een zogeheten *wachtrij met onbestelbare berichten* (DLQ). De wachtrij voor onbestelbare berichten hoeft niet expliciet te worden gemaakt en kan niet worden verwijderd of anderszins onafhankelijk van de hoofd entiteit worden beheerd.

In dit artikel worden wacht rijen voor onbestelbare berichten in Service Bus beschreven. Veel van de discussies worden geïllustreerd door het voor [beeld van onbestelbare wacht rijen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) op github.
 
## <a name="the-dead-letter-queue"></a>De wachtrij met onbestelbare berichten

Het doel van de wachtrij voor onbestelbare berichten is het vasthouden van een bericht dat niet aan een ontvanger kan worden geleverd of berichten die niet kunnen worden verwerkt. Berichten kunnen vervolgens worden verwijderd uit de DLQ en geïnspecteerd. Een toepassing kan, met behulp van een operator, problemen oplossen en het bericht opnieuw verzenden, het feit registreren dat er een fout is opgetreden en corrigerende actie ondernemen. 

Vanuit een API en protocol perspectief is de DLQ grotendeels vergelijkbaar met een andere wachtrij, behalve dat berichten alleen kunnen worden verzonden via de bewerking voor onbestelbare brieven van de bovenliggende entiteit. Daarnaast wordt de time-to-Live niet in acht genomen en kunt u niet onbestelbare berichten van een DLQ afschrijven. De wachtrij voor onbestelbare berichten biedt volledige ondersteuning voor Peek-Lock-levering en transactionele bewerkingen.

De DLQ kan niet automatisch worden opgeruimd. Berichten blijven aanwezig in de DLQ totdat u ze expliciet ophaalt uit de DLQ en de aanroep [complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) aanroept op het onbestelbare bericht.

## <a name="dlq-message-count"></a>Aantal DLQ-berichten
Het is niet mogelijk om het aantal berichten in de wachtrij voor onbestelbare meldingen op onderwerpniveau te verkrijgen. Dat komt doordat berichten niet op onderwerpniveau zijn, tenzij Service Bus een interne fout genereert. Wanneer een afzender een bericht naar een onderwerp verzendt, wordt het bericht in de milliseconden doorgestuurd naar abonnementen voor het onderwerp en bevindt deze zich niet meer op het niveau van het onderwerp. U kunt dus berichten weer geven in de DLQ die zijn gekoppeld aan het abonnement voor het onderwerp. In het volgende voor beeld toont **Service Bus Explorer** dat er 62 berichten momenteel aanwezig zijn in de DLQ voor het abonnement test1. 

![Aantal DLQ-berichten](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

U kunt ook het aantal DLQ-berichten ophalen met behulp van de Azure CLI-opdracht: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Berichten verplaatsen naar de DLQ

Er zijn verschillende activiteiten in Service Bus die ertoe leiden dat berichten worden gepusht naar de DLQ vanuit de berichten engine zelf. Een toepassing kan ook expliciet berichten verplaatsen naar de DLQ. 

Wanneer het bericht door de Broker wordt verplaatst, worden er twee eigenschappen aan het bericht toegevoegd, omdat de Broker de interne versie van de [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) -methode aanroept in het bericht: `DeadLetterReason` en `DeadLetterErrorDescription` .

Toepassingen kunnen hun eigen codes definiëren voor de `DeadLetterReason` eigenschap, maar het systeem stelt de volgende waarden in.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |Het quotum voor de grootte voor deze stream is overschreden. |
|TTLExpiredException |Het bericht is verlopen en naar de wachtrij voor onbestelbare berichten verplaatst. Zie de sectie [overschrijden over TimeToLive](#exceeding-timetolive) voor meer informatie. |
|Sessie-ID is null. |Door sessie ingeschakelde entiteit staat geen berichten toe waarvan de sessie-id null is. |
|MaxTransferHopCountExceeded | Het maximum aantal toegestane hops tijdens het door sturen tussen wacht rijen. De waarde is ingesteld op 4. |
| MaxDeliveryCountExceededExceptionMessage | Het bericht kan niet worden gebruikt na maximum aantal bezorgings pogingen. Zie de sectie [overschrijden over MaxDeliveryCount](#exceeding-maxdeliverycount) voor meer informatie. |

## <a name="exceeding-maxdeliverycount"></a>Meer dan MaxDeliveryCount

Wacht rijen en abonnementen hebben respectievelijk een eigenschap [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) en [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) . de standaard waarde is 10. Wanneer een bericht is bezorgd onder een vergren deling ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), maar is wel expliciet is afgebroken of de vergren deling is verlopen, wordt het bericht [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) verhoogd. Wanneer [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) groter is dan [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), wordt het bericht verplaatst naar de DLQ, waarbij de `MaxDeliveryCountExceeded` reden code wordt opgegeven.

Dit gedrag kan niet worden uitgeschakeld, maar u kunt [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) instellen op een groot aantal.

## <a name="exceeding-timetolive"></a>Meer dan TimeToLive

Wanneer de eigenschap [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) of [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) is ingesteld op **True** (de standaard waarde is **False**), worden alle verlopen berichten verplaatst naar de DLQ en worden de  `TTLExpiredException` reden code opgegeven.

Verlopen berichten worden alleen opgeschoond en verplaatst naar de DLQ wanneer er ten minste één actieve ontvanger wordt opgehaald uit de hoofd wachtrij of het abonnement, en [uitgestelde berichten](./message-deferral.md) worden ook niet leeg gemaakt en verplaatst naar de wachtrij voor onbestelbare brieven nadat deze zijn verlopen. Dit gedrag is zo ontworpen.

## <a name="errors-while-processing-subscription-rules"></a>Fouten bij het verwerken van abonnements regels

Wanneer de eigenschap [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) is ingeschakeld voor een abonnement, worden eventuele fouten die optreden tijdens het uitvoeren van de SQL-filter regel van een abonnement, samen met het foutieve bericht vastgelegd in de DLQ. Gebruik deze optie niet in een productie omgeving waarin niet alle bericht typen abonnees hebben.

## <a name="application-level-dead-lettering"></a>Onbestelbare berichten op toepassings niveau

Naast de door het systeem aangelegde functies voor onbestelbare berichten kunnen toepassingen de DLQ gebruiken voor het expliciet afwijzen van niet-aanvaard bare gegevens. Ze kunnen berichten bevatten die niet op de juiste manier kunnen worden verwerkt vanwege een soort systeem probleem, berichten die ongeldige nettoladingen bevatten of berichten die authenticatie mislukken wanneer een beveiligings schema op bericht niveau wordt gebruikt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Onbestelbare berichten in ForwardTo-of SendVia-scenario's

Berichten worden in de volgende gevallen verzonden naar de wachtrij voor onbestelbare overdrachten:

- Een bericht wordt door gegeven door meer dan vier wacht rijen of onderwerpen die [samen worden samengevoegd](service-bus-auto-forwarding.md).
- De doel wachtrij of het onderwerp is uitgeschakeld of verwijderd.
- De doel wachtrij of het onderwerp overschrijdt de maximale entiteits grootte.

Als u deze berichten met een onbestelbare bericht wilt ophalen, kunt u een ontvanger maken met de methode [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) .

## <a name="example"></a>Voorbeeld

Met het volgende code fragment wordt een ontvanger van een bericht gemaakt. In de receive-lus voor de hoofd wachtrij wordt met de code het bericht met de status [Receive (time span. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)opgehaald, waarmee de Broker wordt gevraagd om onmiddellijk een bericht te retour neren dat direct beschikbaar is, of om te retour neren zonder resultaat. Als de code een bericht ontvangt, wordt deze onmiddellijk afgebroken, waardoor de wordt verhoogd  `DeliveryCount` . Zodra het systeem het bericht naar de DLQ verplaatst, is de hoofd wachtrij leeg en wordt de lus afgesloten, omdat [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) **Null** retourneert.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Pad naar de wachtrij met onbestelbare berichten
U kunt de wachtrij met onbestelbare berichten openen met behulp van de volgende syntaxis:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Als u de .NET SDK gebruikt, kunt u het pad naar de wachtrij met onbestelbare berichten ophalen met behulp van de methode SubscriptionClient. FormatDeadLetterPath (). Deze methode neemt de onderwerpnaam/abonnements naam en achtervoegsels met **/$DeadLetterQueue**.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Service Bus wachtrijen:

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Vergeleken met Azure queues en Service Bus-wacht rijen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

