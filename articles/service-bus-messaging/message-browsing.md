---
title: Azure Service Bus-bericht bladeren
description: Met Service Bus berichten bladeren en bekijken kunnen een Azure Service Bus-client alle berichten in een wachtrij of abonnement opsommen.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553638"
---
# <a name="message-browsing"></a>Berichten doorzoeken

Door een bericht te bladeren of weer te geven, kan een Service Bus-client alle berichten in een wachtrij of een abonnement opsommen voor diagnose-en fout opsporing.

De bewerking Peek op basis van een wachtrij retourneert alle berichten in de wachtrij, niet alleen de items die beschikbaar zijn voor direct ophalen met `Receive()` of de `OnMessage()` lus. De `State` eigenschap van elk bericht vertelt u of het bericht actief is (dat kan worden ontvangen), [uitgesteld](message-deferral.md)of [gepland](message-sequencing.md). De bewerking voor het weer geven van een abonnement retourneert alle berichten behalve geplande berichten in het logboek voor abonnements berichten. 

Verbruikte en verlopen berichten worden opgeschoond door een asynchrone garbagecollection-bewerking uit te voeren. Deze stap is mogelijk niet onmiddellijk na het verlopen van berichten. Daarom `Peek` kunnen berichten die al verlopen zijn, worden geretourneerd. Deze berichten worden verwijderd of onbestelbaar wanneer een receive-bewerking de volgende keer wordt aangeroepen voor de wachtrij of het abonnement. Houd rekening met dit gedrag bij het herstellen van uitgestelde berichten uit de wachtrij. Een verlopen bericht is niet langer in aanmerking komen voor regel matige ophaal acties, zelfs wanneer dit wordt geretourneerd door Peek. Als u deze berichten retourneert, is het ontwerp als Peek een diagnostisch hulp programma dat de huidige status van het logboek weergeeft.

PEEK retourneert ook berichten die zijn vergrendeld en die momenteel worden verwerkt door andere ontvangers. Omdat Peek echter een niet-verbonden moment opname retourneert, kan de vergrendelings status van een bericht niet worden waargenomen op getoonde berichten. De eigenschappen [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) en [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) genereren een [InvalidOperationException](/dotnet/api/system.invalidoperationexception) wanneer de toepassing deze probeert te lezen.

## <a name="peek-apis"></a>Api's bekijken

De methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) en [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) bestaan in .net-en Java-client bibliotheken en op receiver-objecten: **MessageReceiver**, **MessageSession**. Peek werkt op wacht rijen, abonnementen en hun respectieve wacht rijen voor onbestelbare berichten.

Wanneer herhaaldelijk wordt aangeroepen **, worden alle** berichten in de wachtrij of het abonnements logboek genummerd, in volg orde van het laagste beschik bare Volg nummer tot het hoogste niveau. Het is de volg orde waarin berichten in wachtrij worden gezet, niet in de volg orde waarin berichten uiteindelijk kunnen worden opgehaald.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) haalt meerdere berichten op en retourneert deze als een opsomming. Als er geen berichten beschikbaar zijn, is het opsommings object leeg, niet null.

U kunt ook een overbelasting van de methode gebruiken met een [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) waarop u wilt starten. Vervolgens roept u de methode-overload zonder para meters aan om verdere inventarisatie uit te voeren. **PeekBatch** werkt gelijk aan, maar haalt een aantal berichten tegelijk op.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
