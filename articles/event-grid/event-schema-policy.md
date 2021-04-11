---
title: Azure Policy als Event Grid bron
description: In dit artikel wordt beschreven hoe u Azure Policy als een Event Grid gebeurtenis bron gebruikt. Het bevat het schema en de koppelingen naar de zelf studie en de artikelen met procedures.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735064"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor [Azure Policy](../governance/policy/index.yml) gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](./event-schema.md)voor een inleiding tot gebeurtenis schema's. Het biedt ook een lijst met snel starten en zelf studies om Azure Policy als gebeurtenis bron te gebruiken.

## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Azure Policy worden de volgende gebeurtenis typen meeverzonden:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Micro soft. PolicyInsights. PolicyStateCreated | Deze gebeurtenis treedt op wanneer een nalevings status van een beleid wordt gemaakt. |
| Micro soft. PolicyInsights. PolicyStateChanged | Deze gebeurtenis treedt op wanneer de status van een beleids naleving wordt gewijzigd. |
| Micro soft. PolicyInsights. PolicyStateDeleted | Deze gebeurtenis treedt op wanneer de nalevings status van een beleid wordt verwijderd. |

## <a name="example-event"></a>Voorbeeld gebeurtenis

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)
In het volgende voor beeld ziet u het schema van een gebeurtenis gemaakt voor een beleids status: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Het schema voor een gewijzigde gebeurtenis voor een beleids status is vergelijkbaar: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

In het volgende voor beeld ziet u het schema van een gebeurtenis gemaakt voor een beleids status: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Het schema voor een gewijzigde gebeurtenis voor een beleids status is vergelijkbaar: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Gebeurtenis eigenschappen

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `topic` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | De volledig gekwalificeerde ID van de resource waarvan de compatibiliteits status is gewijzigd, met inbegrip van de resource naam en het resource type. Maakt gebruik van de notatie, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `eventTime` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Azure Policy gebeurtenis gegevens. |
| `dataVersion` | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| `metadataVersion` | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `source` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | De volledig gekwalificeerde ID van de resource waarvan de compatibiliteits status is gewijzigd, met inbegrip van de resource naam en het resource type. Maakt gebruik van de notatie, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `time` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Azure Policy gebeurtenis gegevens. |
| `specversion` | tekenreeks | CloudEvents-schema specificatie versie. |

---

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `timestamp` | tekenreeks | De tijd (in UTC) die de resource heeft gescand door Azure Policy. Voor het best Ellen van gebeurtenissen gebruikt u deze eigenschap in plaats van het hoogste niveau `eventTime` of de `time` Eigenschappen. |
| `policyAssignmentId` | tekenreeks | De resource-ID van de beleids toewijzing. |
| `policyDefinitionId` | tekenreeks | De resource-ID van de beleids definitie. |
| `policyDefinitionReferenceId` | tekenreeks | De referentie-ID voor de beleids definitie in de initiatief definitie, als de beleids toewijzing voor een initiatief is. Kan leeg zijn. |
| `complianceState` | tekenreeks | De compatibiliteits status van de resource met betrekking tot de beleids toewijzing. |
| `subscriptionId` | tekenreeks | De abonnements-ID van de resource. |
| `complianceReasonCode` | tekenreeks | De reden code voor naleving. Kan leeg zijn. |

## <a name="next-steps"></a>Volgende stappen

- Zie voor een beschrijving van de route ring Azure Policy status wijzigings gebeurtenissen [Event grid gebruiken voor wijzigings meldingen van de beleids status](../governance/policy/tutorials/route-state-change-events.md).
- Zie [reageren op Azure Policy gebeurtenissen met Event grid](../governance/policy/concepts/event-overview.md)voor een overzicht van het integreren van Azure Policy met Event grid.
- Zie [Wat is Event Grid?](./overview.md) voor een inleiding tot Azure Event Grid.
- Zie [Event grid Subscription schema](./subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.