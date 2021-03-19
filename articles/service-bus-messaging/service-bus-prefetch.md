---
title: Azure Service Bus prefetch-berichten | Microsoft Docs
description: Verbeter de prestaties door Azure Service Bus berichten vooraf op te halen. Berichten zijn direct beschikbaar voor lokaal ophalen voordat ze door de toepassing worden aangevraagd.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341044"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus-berichten prefetch

Als *prefetch* is ingeschakeld in een van de officiële service bus-clients, verkrijgt de ontvanger op een flexibele wijze meer berichten, tot aan de limiet van [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) , in het geval dat de toepassing voor het eerst wordt gevraagd.

Een enkele eerste [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) -of [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) -aanroep verwerft daarom een bericht voor onmiddellijk verbruik dat wordt geretourneerd als beschikbaar. De client verwerft vervolgens nieuwe berichten op de achtergrond om de prefetch-buffer te vullen.

## <a name="enable-prefetch"></a>Prefetch inschakelen

Met .NET schakelt u de functie prefetch in door de eigenschap [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) van een **MessageReceiver**, **QueueClient** of **SubscriptionClient** in te stellen op een getal dat groter is dan nul. Als u de waarde instelt op nul, wordt prefetch uitgeschakeld.

U kunt deze instelling eenvoudig toevoegen aan de receive-zijde van de instellingen voor [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) of [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) samples om het effect in die contexten te bekijken.

Hoewel berichten beschikbaar zijn in de prefetch-buffer, worden **alle daaropvolgende** / **ReceiveAsync** -aanroepen onmiddellijk vanuit de buffer uitgevoerd en wordt de buffer op de achtergrond aangevuld, omdat er ruimte beschikbaar is. Als er geen berichten beschikbaar zijn voor levering, wordt de buffer door de receive-bewerking leeg gemaakt en vervolgens gewacht of geblokkeerd, zoals verwacht.

Prefetch werkt ook op dezelfde manier met de [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) -en [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) -api's.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Als het sneller is, waarom wordt prefetch niet de standaard optie?

Prefetch versnelt de berichten stroom door een bericht direct beschikbaar te stellen voor lokaal ophalen wanneer en voordat de toepassing er een vraagt. Deze door Voer is het resultaat van een trans actie die de auteur van de toepassing expliciet moet maken:

Met de [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -ontvangst modus zijn alle berichten die zijn verkregen in de prefetch-buffer niet meer beschikbaar in de wachtrij en zich alleen in de buffer van de prefetch in het geheugen bevinden, totdat ze worden ontvangen in de toepassing via de OnMessageAsync-api's **Receive** / **ReceiveAsync** of **OnMessage** /  . Als de toepassing wordt beëindigd voordat de berichten in de toepassing worden ontvangen, worden deze berichten permanent verloren gegaan.

In de [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) -ontvangst modus worden berichten die in de prefetch-buffer worden opgehaald, in de buffer opgehaald in een vergrendelde status en hebben de time-out voor de vergren deling. Als de prefetch-buffer groot is en de verwerking zo lang duurt dat bericht vergrendelingen verlopen terwijl het aanwezig is in de prefetch-buffer, of zelfs terwijl de toepassing het bericht verwerkt, zijn er mogelijk verwarrende gebeurtenissen die de toepassing kan verwerken.

De toepassing kan een bericht verkrijgen met een verlopen of binnenkort verlooptde vergren deling. Als dit het geval is, kan de toepassing het bericht verwerken, maar deze kan niet worden voltooid omdat de vergren deling is verlopen. De toepassing kan de eigenschap [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (die onderhevig is aan klok verschil tussen de Broker en de klok van de lokale computer) controleren. Als de bericht vergrendeling is verlopen, moet de toepassing het bericht negeren. Er moet geen API-aanroep worden uitgevoerd op of met het bericht. Als het bericht niet is verlopen, maar de verloopt binnenkort, kan de vergren deling worden vernieuwd en uitgebreid door een andere standaard vergrendelings periode door het aanroepen van een [bericht. RenewLock ()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Als de vergren deling op de achtergrond verloopt in de prefetch-buffer, wordt het bericht beschouwd als ingetrokken en wordt het opnieuw beschikbaar gemaakt voor ophalen uit de wachtrij. Dat kan ertoe leiden dat het kan worden opgehaald in de prefetch-buffer; aan het einde geplaatst. Als de prefetch-buffer normaal gesp roken niet kan worden gebruikt tijdens het verlopen van het bericht, worden de berichten herhaaldelijk vooraf opgehaald, maar worden ze nooit in een bruikbare status (geldig vergrendeld) bezorgd en worden ze uiteindelijk naar de wachtrij voor onbestelbare meldingen verplaatst zodra het maximum aantal bezorgingen wordt overschreden.

Als u een hoge mate van betrouw baarheid nodig hebt voor bericht verwerking en de verwerking veel werk en tijd in beslag neemt, is het raadzaam de prefetch-functie op een conservatieve of helemaal niet te gebruiken.

Als u een hoge door Voer en bericht verwerking nodig hebt, is de prefetch een aanzienlijke doorvoer voordelen.

Het maximum aantal prefetch en de vergrendelings duur die zijn geconfigureerd voor de wachtrij of het abonnement moeten evenwichtig worden verdeeld, zodat de time-out van de vergren deling ten minste de cumulatieve verwachte verwerkings tijd overschrijdt voor de maximale grootte van de prefetch-buffer, plus één bericht. Tegelijkertijd moet de time-out van de vergren deling niet zo lang zijn dat berichten de maximale [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) kunnen overschrijden wanneer ze per ongeluk zijn verwijderd, waardoor de vergren deling moet verlopen voordat ze opnieuw worden geleverd.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
