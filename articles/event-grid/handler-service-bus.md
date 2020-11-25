---
title: Service Bus-wacht rijen en-onderwerpen als gebeurtenis-handlers voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Service Bus-wacht rijen en-onderwerpen kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024211"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus-wacht rijen en-onderwerpen als gebeurtenis-handlers voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure service bus** is een hiervan. 

U kunt een service wachtrij of onderwerp als een handler voor gebeurtenissen uit Event Grid gebruiken. 

## <a name="service-bus-queues"></a>Service Bus-wachtrijen
U kunt gebeurtenissen in Event Grid rechtstreeks naar Service Bus wacht rijen routeren voor gebruik in buffer-of opdracht & controle scenario's in bedrijfs toepassingen.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement **Service Bus wachtrij** als eindpunt type en klik vervolgens op **Selecteer een eind punt** om een service bus wachtrij te kiezen.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI gebruiken om een Service Bus-wachtrij-handler toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus-onderwerpen

U kunt gebeurtenissen in Event Grid rechtstreeks door sturen naar Service Bus-onderwerpen om Azure-systeem gebeurtenissen te verwerken met Service Bus-onderwerpen, of voor opdracht & bericht scenario's voor het beheren van berichten.

Selecteer in de Azure Portal tijdens het maken van een gebeurtenis abonnement **Service Bus onderwerp** als eindpunt type en klik vervolgens op **selecteren en eind punt** om een service bus onderwerp te kiezen.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI gebruiken om een Service Bus-onderwerpassistent toe te voegen

Voor Azure CLI wordt het volgende voor beeld geabonneerd en wordt een event grid-onderwerp verbonden met een Service Bus wachtrij:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Wanneer een gebeurtenis naar een Service Bus wachtrij of onderwerp wordt verzonden als een brokerd bericht, `messageid` is het brokered-bericht een interne systeem-id.

De interne systeem-ID voor het bericht wordt gehandhaafd over de herlevering van de gebeurtenis, zodat u dubbele leveringen kunt voor komen door **Duplicaten detectie** in te scha kelen op de service bus-entiteit. Het is raadzaam om de duur van de duplicaten detectie in te scha kelen op de Service Bus entiteit voor de TTL (time-to-Live) van de gebeurtenis of de maximale nieuwe duur, afhankelijk van wat langer is.

## <a name="rest-examples-for-put"></a>REST-voor beelden (voor PUT)

### <a name="service-bus-queue"></a>Service Bus wachtrij

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus wachtrij-levering met beheerde identiteit

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus-onderwerp

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus onderwerp: levering met beheerde identiteit

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
