---
title: Reageren op Azure Policy status wijzigings gebeurtenissen
description: Gebruik Azure Event Grid om u te abonneren op app-beleids gebeurtenissen, waardoor toepassingen op status wijzigingen kunnen reageren zonder gecompliceerde code te hoeven gebruiken.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735073"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reageren op Azure Policy status wijzigings gebeurtenissen

Met Azure Policy gebeurtenissen kunnen toepassingen reageren op status wijzigingen. Deze integratie wordt uitgevoerd zonder dat er complexe code of dure en inefficiënte polling services nodig zijn. In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](../../../event-grid/index.yml) naar abonnees zoals [Azure functions](../../../azure-functions/index.yml), [Azure Logic apps](../../../logic-apps/index.yml)of zelfs naar uw eigen aangepaste HTTP-listener.
Kritiek, u betaalt alleen voor wat u gebruikt.

Azure Policy gebeurtenissen worden verzonden naar de Azure Event Grid, waarmee betrouw bare leverings Services voor uw toepassingen kunnen worden uitgevoerd via een uitgebreid beleid voor opnieuw proberen en levering van onbestelbare berichten. Zie [Event grid aflevering van berichten en probeer het opnieuw](../../../event-grid/delivery-and-retry.md).

Het veelvoorkomende Azure Policy-gebeurtenis scenario is het bijhouden van de nalevings status van een bron tijdens het evalueren van het beleid. Een architectuur op basis van gebeurtenissen is een efficiënte manier om te reageren op deze wijzigingen in plaats van de nalevings status van resources te scannen volgens een vast schema.

> [!NOTE]
> Azure Policy status wijzigings gebeurtenissen worden naar Event Grid verzonden nadat een [evaluatie trigger](../how-to/get-compliance-data.md#evaluation-triggers) de resource-evaluatie heeft voltooid.

Zie [status wijzigings gebeurtenissen voor het routerings beleid in Event grid met Azure cli](../tutorials/route-state-change-events.md) voor een volledige zelf studie.

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Event Grid-model van bronnen en handlers" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Beschik bare Azure Policy gebeurtenissen

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Azure Policy gebeurtenis abonnementen kunnen drie typen gebeurtenissen bevatten:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Micro soft. PolicyInsights. PolicyStateCreated | Deze gebeurtenis treedt op wanneer een nalevings status van een beleid wordt gemaakt. |
| Micro soft. PolicyInsights. PolicyStateChanged | Deze gebeurtenis treedt op wanneer de status van een beleids naleving wordt gewijzigd. |
| Micro soft. PolicyInsights. PolicyStateDeleted | Deze gebeurtenis treedt op wanneer de nalevings status van een beleid wordt verwijderd. |

## <a name="event-schema"></a>Gebeurtenisschema

Azure Policy gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Azure Policy gebeurtenis identificeren wanneer de `eventType` eigenschap begint met ' micro soft. PolicyInsights '.
Meer informatie over het gebruik van Event Grid gebeurtenis eigenschappen wordt beschreven in  
[Event grid-gebeurtenis schema](../../../event-grid/event-schema.md).

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `topic` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | De volledig gekwalificeerde ID van de resource waarvan de compatibiliteits status is gewijzigd, met inbegrip van de resource naam en het resource type. Maakt gebruik van de notatie, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Azure Policy gebeurtenis gegevens. |
| `data.timestamp` | tekenreeks | De tijd (in UTC) die de resource heeft gescand door Azure Policy. Voor het best Ellen van gebeurtenissen gebruikt u deze eigenschap in plaats van het hoogste niveau `eventTime` of de `time` Eigenschappen. |
| `data.policyAssignmentId` | tekenreeks | De resource-ID van de beleids toewijzing. |
| `data.policyDefinitionId` | tekenreeks | De resource-ID van de beleids definitie. |
| `data.policyDefinitionReferenceId` | tekenreeks | De referentie-ID voor de beleids definitie in de initiatief definitie, als de beleids toewijzing voor een initiatief is. Kan leeg zijn. |
| `data.complianceState` | tekenreeks | De compatibiliteits status van de resource met betrekking tot de beleids toewijzing. |
| `data.subscriptionId` | tekenreeks | De abonnements-ID van de resource. |
| `data.complianceReasonCode` | tekenreeks | De reden code voor naleving. Kan leeg zijn. |
| `eventType` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `eventTime` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `dataVersion` | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| `metadataVersion` | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Hier volgt een voor beeld van een beleids status wijzigings gebeurtenis:

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

Zie [Azure Policy Events schema](../../../event-grid/event-schema-policy.md)voor meer informatie.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Azure Policy gebeurtenissen verwerken, moeten de volgende aanbevolen procedures volgen:

> [!div class="checklist"]
> - Meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, dus stel dat er geen gebeurtenissen van een bepaalde bron zijn. In plaats daarvan raadpleegt u het onderwerp van het bericht om ervoor te zorgen dat de beleids toewijzing, de beleids definitie en de bron van de status wijzigings gebeurtenis voor zijn.
> - Controleer de `eventType` en ga er niet van uit dat alle gebeurtenissen die u ontvangt, het verwachte type zijn.
> - Gebruiken `data.timestamp` om de volg orde van de gebeurtenissen in azure policy in plaats van het hoogste niveau of de eigenschappen te bepalen `eventTime` `time` .
> - Gebruik het onderwerpveld om toegang te krijgen tot de resource die een beleids status wijziging heeft.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure Policy status wijzigings gebeurtenissen een try:

- [Status wijzigings gebeurtenissen van het routerings beleid naar Event Grid met Azure CLI](../tutorials/route-state-change-events.md)
- [Azure Policy schema Details voor Event Grid](../../../event-grid/event-schema-policy.md)
- [Over Event Grid](../../../event-grid/overview.md)