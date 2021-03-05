---
title: Overzicht van transactie verwerking in Azure Service Bus
description: In dit artikel vindt u een overzicht van transactie verwerking en de functie verzenden via in Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2848f41d5557584b0f1a197b548a00a4aef1564
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183740"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Overzicht van de verwerking van Service Bus transacties

In dit artikel worden de transactie mogelijkheden van Microsoft Azure Service Bus beschreven. Veel van de discussies worden geïllustreerd door de [AMQP-trans acties met Service Bus voor beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Dit artikel is beperkt tot een overzicht van transactie verwerking en de functie *verzenden via* in service bus, terwijl het voor beeld van atomische trans acties breder en complexer is.

> [!NOTE]
> De laag basis van Service Bus biedt geen ondersteuning voor trans acties. De Standard-en Premium-lagen ondersteunen trans acties. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

## <a name="transactions-in-service-bus"></a>Trans acties in Service Bus

Met een *trans actie* worden twee of meer bewerkingen gegroepeerd in een *uitvoerings bereik*. Een dergelijke trans actie moet er dus voor zorgen dat alle bewerkingen die tot een bepaalde groep bewerkingen behoren, slagen of mislukken. In dit opzicht fungeren als één eenheid, die vaak *atomisch* wordt genoemd.

Service Bus is een transactionele bericht Broker en zorgt voor transactionele integriteit voor alle interne bewerkingen voor de berichten archieven. Alle overdrachten van berichten binnen Service Bus, zoals het verplaatsen van berichten naar een [wachtrij met onbestelbare](service-bus-dead-letter-queues.md) berichten of het [automatisch door sturen](service-bus-auto-forwarding.md) van bericht tussen entiteiten, zijn transactioneel. Als Service Bus een bericht accepteert, is het dus al opgeslagen en voorzien van een label met een Volg nummer. Vanaf dat punt worden alle bericht overdrachten binnen Service Bus gecoördineerde bewerkingen tussen entiteiten. Dit leidt niet tot verlies (de bron slaagt en het doel mislukt) of naar duplicatie (bron mislukt en doel is geslaagd) van het bericht.

Service Bus biedt ondersteuning voor het groeperen van bewerkingen voor één berichtentiteit (wachtrij, onderwerp of abonnement) binnen het bereik van een transactie. U kunt bijvoorbeeld verschillende berichten verzenden naar één wachtrij vanuit een transactie bereik en de berichten worden alleen doorgevoerd in het logboek van de wachtrij wanneer de trans actie is voltooid.

## <a name="operations-within-a-transaction-scope"></a>Bewerkingen binnen een transactie bereik

De bewerkingen die kunnen worden uitgevoerd binnen een transactie bereik zijn als volgt:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: `Complete` , `CompleteAsync` , `Abandon` , `AbandonAsync` , `Deadletter` , `DeadletterAsync` , `Defer` , `DeferAsync` , `RenewLock` , `RenewLockAsync` 

Ontvangst bewerkingen worden niet opgenomen, omdat ervan wordt uitgegaan dat de toepassing berichten ophaalt via de modus [ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) , in een bepaalde receive-lus of met een [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) -call back, en vervolgens een transactie bereik opent om het bericht te verwerken.

De toestand van het bericht (volledig, afwijzen, onbestelbare letter, defer) wordt vervolgens weer gegeven binnen het bereik van en is afhankelijk van het totale resultaat van de trans actie.

## <a name="transfers-and-send-via"></a>Overdrachten en verzenden via

Om transactionele overdracht van gegevens van een wachtrij of onderwerp naar een processor in te scha kelen en vervolgens naar een andere wachtrij of een ander onderwerp, ondersteunt Service Bus *overdrachten*. Bij een overdrachts bewerking verzendt een afzender eerst een bericht naar een *overdrachts wachtrij of onderwerp*. de overdrachts wachtrij of het onderwerp verplaatst het bericht onmiddellijk naar de beoogde doel wachtrij of het onderwerp met behulp van dezelfde robuuste overdrachts implementatie waarbij de functie voor door sturen gebruikmaakt van. Het bericht wordt nooit doorgevoerd in het logboek van de overdrachts wachtrij of het onderwerp op een manier dat het zichtbaar wordt voor de overdrachts wachtrij of de consumenten van het onderwerp.

De kracht van deze transactionele mogelijkheden wordt duidelijk wanneer de overdrachts wachtrij of het onderwerp zelf de bron is van de invoer berichten van de afzender. Met andere woorden, Service Bus kunt het bericht overzetten naar de doel wachtrij of het onderwerp ' via ' de overdrachts wachtrij of het onderwerp, terwijl u een volledige (of defered ' of onbestelbare) bewerking uitvoert op het invoer bericht, allemaal in één atomische bewerking. 

Als u van een onderwerp wilt ontvangen en vervolgens naar een wachtrij of onderwerp in dezelfde trans actie wilt verzenden, moet de entiteit overdracht een onderwerp zijn. In dit scenario start u het transactie bereik in het onderwerp, ontvangt u van het abonnement met in het transactie bereik en verzendt u dit via het onderwerp overzetten naar een wachtrij of onderwerp-bestemming. 

### <a name="see-it-in-code"></a>In code weer geven

Als u dergelijke overdrachten wilt instellen, maakt u een bericht verzender die de doel wachtrij verricht via de overdrachts wachtrij. U hebt ook een ontvanger die berichten uit dezelfde wachtrij ophaalt. Bijvoorbeeld:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Een eenvoudige trans actie gebruikt vervolgens deze elementen, zoals in het volgende voor beeld. Als u het volledige voor beeld wilt raadplegen, raadpleegt u de [bron code op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Time-out
Een trans actie verkeert na 2 minuten. De trans actie-timer wordt gestart wanneer de eerste bewerking in de trans actie wordt gestart. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Service Bus wachtrijen:

* [Service Bus-wachtrijen gebruiken](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus entiteiten koppelen met autoforwarding](service-bus-auto-forwarding.md)
* [Voor beeld van door sturen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomische trans acties met Service Bus-voor beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Vergeleken met Azure queues en Service Bus-wacht rijen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


