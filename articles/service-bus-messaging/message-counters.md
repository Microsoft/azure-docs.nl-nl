---
title: Azure Service Bus-aantal berichten
description: Het aantal berichten in wacht rijen en abonnementen ophalen met behulp van Azure Resource Manager en de Azure Service Bus NamespaceManager-Api's.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341265"
---
# <a name="message-counters"></a>Berichtentellers

U kunt het aantal berichten dat in wacht rijen en abonnementen wordt bewaard, ophalen met behulp van Azure Resource Manager en de Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's in de .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met Power shell kunt u het aantal als volgt verkrijgen:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Details van aantal berichten

Het aantal actieve berichten is handig om te bepalen of een wachtrij een achterstand opstelt waarvoor meer resources moeten worden verwerkt dan wat momenteel is geïmplementeerd. De volgende meter Details zijn beschikbaar in de [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) -klasse:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): berichten in de wachtrij of het abonnement die de actieve status hebben en gereed zijn voor levering.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): berichten in de wachtrij voor onbestelbare letters.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): berichten in de geplande status.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): berichten die niet zijn overgedragen naar een andere wachtrij of een ander onderwerp en die zijn verplaatst naar de wachtrij voor onbestelbare overdrachten.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): berichten moeten worden overgedragen naar een andere wachtrij of een ander onderwerp.

Als een toepassing resources wil schalen op basis van de lengte van de wachtrij, moet dit doen met een gemeten snelheid. De verwerving van de bericht items is een dure bewerking in de Message Broker en voert deze regel matig rechtstreeks uit en heeft nadelige gevolgen voor de prestaties van de entiteit.

> [!NOTE]
> De berichten die worden verzonden naar een Service Bus onderwerp worden doorgestuurd naar abonnementen voor dat onderwerp. Het aantal actieve berichten in het onderwerp zelf is dus 0, omdat deze berichten zijn doorgestuurd naar het abonnement. Haal het aantal berichten op bij het abonnement en controleer of het groter is dan 0. Hoewel er berichten bij het abonnement worden weer geven, worden ze daad werkelijk opgeslagen in een opslag die eigendom is van het onderwerp. 

Als u de abonnementen bekijkt, zou het aantal berichten niet gelijk zijn aan nul (wat Maxi maal 323MB aan ruimte voor deze hele entiteit bevat).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
