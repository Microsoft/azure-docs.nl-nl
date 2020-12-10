---
title: Azure Service Bus als Event Grid bron
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Service Bus gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34c6990c4e6e87304c457a5b2ca6459c404c8d9a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008109"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Service Bus gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Service Bus worden de volgende gebeurtenis typen meeverzonden:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Micro soft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Deze gebeurtenis treedt op wanneer er actieve berichten in een wachtrij of abonnement zijn en er geen ontvangers Luis teren. |
| Micro soft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Deze gebeurtenis treedt op wanneer er actieve berichten in een wachtrij met onbestelbare meldingen en geen actieve listeners zijn. |
| Micro soft. ServiceBus. ActiveMessagesAvailablePeriodicNotifications | Deze gebeurtenis wordt regel matig gegenereerd als er actieve berichten in een wachtrij of abonnement zijn, zelfs als er actieve listeners zijn voor die specifieke wachtrij of dit abonnement. |
| Micro soft. ServiceBus. DeadletterMessagesAvailablePeriodicNotifications | Deze gebeurtenis treedt regel matig op als er zich berichten in de Deadletter-entiteit van een wachtrij of abonnement bevinden, zelfs als er actieve listeners zijn voor de Deadletter-entiteit van die specifieke wachtrij of dit abonnement. | 

### <a name="example-event"></a>Voorbeeld gebeurtenis

#### <a name="active-messages-available-with-no-listeners"></a>Actieve berichten die beschikbaar zijn zonder listeners

In het volgende voor beeld ziet u het schema van actieve berichten zonder listeners:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Beschik bare Deadletter-berichten zonder listener

Het schema voor een wachtrij gebeurtenis met een onbestelbare berichten is vergelijkbaar:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Actieve berichten beschik bare periodieke meldingen

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Deadletter berichten beschik bare periodieke meldingen

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens van Blob-opslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| namespaceName | tekenreeks | De naam ruimte van de Service Bus waarin de resource zich bevindt. |
| requestUri | tekenreeks | De URI naar de specifieke wachtrij of het abonnement waarmee de gebeurtenis wordt verzonden. |
| entityType | tekenreeks | Het type Service Bus entiteit waarmee gebeurtenissen worden verzonden (wachtrij of abonnement). |
| queueName | tekenreeks | De wachtrij met actieve berichten als u zich abonneert op een wachtrij. Waarde Null als er onderwerpen/abonnementen worden gebruikt. |
| onderwerpnaam | tekenreeks | Het onderwerp waarvan het Service Bus-abonnement met actieve berichten deel uitmaakt. Waarde Null bij het gebruik van een wachtrij. |
| subscriptionName | tekenreeks | Het Service Bus abonnement met actieve berichten. Waarde Null bij het gebruik van een wachtrij. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar de functie app en de logische app. |
| [Azure Service Bus Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Overzicht van het integreren van Service Bus met Event Grid. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie het [overzicht van service bus voor Event grid integratie](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)voor meer informatie over het gebruik van Azure Event Grid met Service Bus.
* Probeer [Service Bus gebeurtenissen te ontvangen met functies of Logic apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
