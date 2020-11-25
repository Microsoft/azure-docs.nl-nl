---
title: Event hub als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Event hubs kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96005623"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event hub als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een actie nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure Event hubs** is een hiervan. 

Gebruik **Event hubs** wanneer uw oplossing gebeurtenissen van Event grid sneller ontvangt dan de gebeurtenissen kan verwerken. Zodra de gebeurtenissen zich in een Event Hub bevinden, kan uw toepassing gebeurtenissen uit de Event Hub op basis van een eigen planning verwerken. U kunt uw gebeurtenis verwerking schalen om de binnenkomende gebeurtenissen te verwerken.

## <a name="tutorials"></a>Zelfstudies
Zie de volgende voorbeelden: 

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Event Hubs door sturen met Azure CLI](custom-event-to-eventhub.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een Event Hub voor verwerking door een toepassing. |
| [Resource Manager-sjabloon: een Event Grid aangepast onderwerp maken en gebeurtenissen naar een Event Hub verzenden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resource manager-sjabloon voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een Azure-Event Hubs verzonden. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST-voor beelden (voor PUT)


### <a name="event-hub"></a>Event Hub

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event hub-levering met beheerde identiteit

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
