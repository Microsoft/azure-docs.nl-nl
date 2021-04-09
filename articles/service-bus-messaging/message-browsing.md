---
title: Azure Service Bus-bericht bladeren
description: Met Service Bus berichten bladeren en bekijken kunnen een Azure Service Bus-client alle berichten in een wachtrij of abonnement opsommen.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090304"
---
# <a name="message-browsing"></a>Berichten doorzoeken

Door een bericht te bladeren of weer te geven, kan een Service Bus-client alle berichten in een wachtrij of een abonnement opsommen voor diagnose-en fout opsporing.

Met de Peek-bewerking voor een **wachtrij** worden alle berichten in de wachtrij geretourneerd, niet alleen de items die beschikbaar zijn voor direct ophalen. De bewerking voor het weer geven van een **abonnement** retourneert alle berichten behalve geplande berichten in het logboek voor abonnements berichten. 

Verbruikte en verlopen berichten worden opgeschoond door een asynchrone garbagecollection-bewerking uit te voeren. Deze stap is mogelijk niet onmiddellijk na het verlopen van berichten. Daarom is het mogelijk dat een Peek-bewerking berichten retourneert die al zijn verlopen. Deze berichten worden verwijderd of onbestelbaar wanneer een receive-bewerking de volgende keer wordt aangeroepen voor de wachtrij of het abonnement. Houd rekening met dit gedrag bij het herstellen van uitgestelde berichten uit de wachtrij. Een verlopen bericht is niet langer in aanmerking komen voor regel matige ophaal acties, zelfs wanneer dit wordt geretourneerd door Peek. Als u deze berichten retourneert, is het ontwerp als Peek een diagnostisch hulp programma dat de huidige status van het logboek weergeeft.

PEEK retourneert ook berichten die zijn vergrendeld en die momenteel worden verwerkt door andere ontvangers. Omdat Peek echter een niet-verbonden moment opname retourneert, kan de vergrendelings status van een bericht niet worden waargenomen op getoonde berichten.

## <a name="peek-apis"></a>Api's bekijken
## <a name="azuremessagingservicebus"></a>[Azure. Messa ging. ServiceBus](#tab/dotnet)
De methoden [PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) en [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) bestaan op receiver-objecten: `ServiceBusReceiver` , `ServiceBusSessionReceiver` . Peek werkt op wacht rijen, abonnementen en hun respectieve wacht rijen voor onbestelbare berichten.

Wanneer herhaaldelijk wordt aangeroepen, worden `PeekMessageAsync` alle berichten in de wachtrij of het abonnements logboek gesorteerd op volg orde van het laagste beschik bare Volg nummer tot het hoogste niveau. Het is de volg orde waarin berichten in wachtrij worden gezet, niet in de volg orde waarin berichten uiteindelijk kunnen worden opgehaald.
PeekMessagesAsync haalt meerdere berichten op en retourneert deze als een opsomming. Als er geen berichten beschikbaar zijn, is het opsommings object leeg, niet null.

U kunt ook de para meter [fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) vullen met een SequenceNumber waarop moet worden gestart. Vervolgens roept u de methode opnieuw aan zonder de para meter op te geven die u wilt inventariseren. `PeekMessagesAsync` werkt gelijk aan functies, maar haalt een aantal berichten tegelijk op.


## <a name="microsoftazureservicebus"></a>[Micro soft. Azure. ServiceBus](#tab/dotnetold)
De methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) en [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) bestaan op receiver-objecten: `MessageReceiver` , `MessageSession` . Peek werkt op wacht rijen, abonnementen en hun respectieve wacht rijen voor onbestelbare berichten.

Wanneer herhaaldelijk wordt aangeroepen, worden `Peek` alle berichten in de wachtrij of het abonnements logboek gesorteerd op volg orde van het laagste beschik bare Volg nummer tot het hoogste niveau. Het is de volg orde waarin berichten in wachtrij worden gezet, niet in de volg orde waarin berichten uiteindelijk kunnen worden opgehaald.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) haalt meerdere berichten op en retourneert deze als een opsomming. Als er geen berichten beschikbaar zijn, is het opsommings object leeg, niet null.

U kunt ook een overbelasting van de methode gebruiken met een [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) waarop u wilt starten. Vervolgens roept u de methode-overload zonder para meters aan om verdere inventarisatie uit te voeren. **PeekBatch** werkt gelijk aan, maar haalt een aantal berichten tegelijk op.


---

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
