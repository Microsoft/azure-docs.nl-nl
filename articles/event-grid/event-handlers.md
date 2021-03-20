---
title: Gebeurtenis-handlers van Azure Event Grid
description: Hierin worden ondersteunde gebeurtenis-handlers voor Azure Event Grid beschreven. Azure Automation, functions, Event Hubs, Hybride verbindingen, Logic Apps, Service Bus, Queue Storage, webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86117012"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gebeurtenis-handlers in Azure Event Grid
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het verwerken van gebeurtenissen. U kunt ook een webhook gebruiken voor het verwerken van gebeurtenissen. De webhook hoeft niet te worden gehost in azure voor het verwerken van gebeurtenissen. Event Grid ondersteunt alleen HTTPS-webhook-eind punten.

## <a name="supported-event-handlers"></a>Ondersteunde gebeurtenis-handlers
Dit zijn de ondersteunde gebeurtenis-handlers: 

- [Webhooks](handler-webhooks.md). Azure Automation runbooks en Logic Apps worden ondersteund via webhooks. 
- [Azure functions](handler-functions.md)
- [Event hubs](handler-event-hubs.md)
- [Hybride verbindingen door geven](handler-relay-hybrid-connections.md)
- [Service Bus-wacht rijen en-onderwerpen](handler-service-bus.md)
- [Opslagwachtrijen](handler-storage-queues.md)

## <a name="next-steps"></a>Volgende stappen
- Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
