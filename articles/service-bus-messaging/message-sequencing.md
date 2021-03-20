---
title: Azure Service Bus bericht sequentiëren en tijds tempels | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u sequentiëren en volg orde (met tijds tempels) van Azure Service Bus berichten kunt behouden.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fdb18802e576ad114fd3f783d5efd7bb826a5f94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341167"
---
# <a name="message-sequencing-and-timestamps"></a>Berichtvolgorde en -timestamps

Sequentiëren en tijds tempels zijn twee functies die altijd zijn ingeschakeld voor alle Service Bus entiteiten en het Opper vlak via de eigenschappen [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) en [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) van ontvangen of bekeken berichten.

Voor de gevallen waarin de absolute volg orde van berichten significant is en/of waarin een consument een betrouw bare unieke id voor berichten nodig heeft, stemt de Broker berichten met een lege ruimte en een groter Volg nummer relatief ten opzichte van de wachtrij of het onderwerp. Voor gepartitioneerde entiteiten wordt het Volg nummer uitgegeven ten opzichte van de partitie.

De waarde **SequenceNumber** is een uniek 64-bits geheel getal dat is toegewezen aan een bericht dat wordt geaccepteerd en opgeslagen door de Broker en fungeert als interne id. Voor gepartitioneerde entiteiten weerspiegelt de bovenste 16 bits de partitie-id. Reeks nummers worden gekanteld tot nul wanneer het 48/64-bits bereik wordt uitgeput.

Het Volg nummer kan worden vertrouwd als een unieke id omdat het wordt toegewezen door een centrale en neutrale instantie en niet door clients. Het bevat ook de werkelijke volg orde van aankomst en is nauw keuriger dan een tijds tempel als een order criterium, omdat tijds tempels mogelijk niet een hoge resolutie hebben bij extreme bericht snelheden en mogelijk zijn onderhevig aan een klok snelheid in situaties waarin het eigendom van de Broker tussen knoop punten verandert.

De absolute aankomst order is bijvoorbeeld van belang voor bedrijfs scenario's waarin een beperkt aantal aangeboden goederen wordt geleverd op basis van het eerste geleverde product, terwijl het voor het laatst wordt geleverd. de verkoop van concert tickets is een voor beeld.

De functie voor tijds tempels fungeert als een neutrale en betrouw bare instantie die de UTC-tijd van de aankomst van een bericht nauw keurig vastlegt, weer gegeven in de eigenschap **EnqueuedTimeUtc** . De waarde is handig als een bedrijfs scenario afhankelijk is van deadlines, zoals of een werk item is verzonden op een bepaalde datum vóór middernacht, maar de verwerking ver achter de achterstand van de wachtrij is.

## <a name="scheduled-messages"></a>Geplande berichten

U kunt berichten verzenden naar een wachtrij of onderwerp voor vertraagde verwerking, bijvoorbeeld om te plannen dat een taak op een bepaald moment beschikbaar komt voor verwerking door een systeem. Deze functie realiseert een betrouw bare, gedistribueerde op tijd gebaseerde planner.

Geplande berichten worden niet in de wachtrij realiseren tot de gedefinieerde tijd voor Intijd. De geplande berichten kunnen vóór die tijd worden geannuleerd. Met de annulering wordt het bericht verwijderd.

U kunt berichten plannen door de eigenschap [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) in te stellen wanneer u een bericht verzendt via het gewone verzend traject of expliciet met de [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) -API. De laatste retourneert onmiddellijk de **SequenceNumber** van het geplande bericht, dat u later kunt gebruiken om het geplande bericht zo nodig te annuleren. Geplande berichten en hun Volg nummers kunnen ook worden gedetecteerd met behulp van [bericht bladeren](message-browsing.md).

De **SequenceNumber** voor een gepland bericht is alleen geldig wanneer het bericht deze status heeft. Wanneer het bericht wordt overgezet naar de actieve status, wordt het bericht toegevoegd aan de wachtrij, alsof het is in de huidige chat opgenomen, waarbij een nieuwe **SequenceNumber** is toegewezen.

Omdat de functie verankerd is voor afzonderlijke berichten en berichten slechts eenmaal in de wachtrij kunnen worden geplaatst, ondersteunt Service Bus geen terugkerende schema's voor berichten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)