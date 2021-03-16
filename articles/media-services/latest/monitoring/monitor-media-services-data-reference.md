---
title: Media Services gegevens referentie bewaken
description: Belang rijk referentie materiaal dat nodig is wanneer u Media Services bewaakt
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/11/2021
ms.openlocfilehash: 461c998aa85d70d69cb267fdbeabd7eabcfb5854
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471546"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services gegevens referentie bewaken

In dit artikel worden de gegevens behandeld die nuttig zijn voor het bewaken van Media Services. Raadpleeg voor meer informatie over alle platform metrieken die worden ondersteund in Azure Monitor [ondersteunde metrische gegevens met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

## <a name="media-services-metrics"></a>Media Services metrische gegevens

Metrische gegevens worden op gezette tijden verzameld, ongeacht of de waarde wordt gewijzigd. Ze zijn handig voor waarschuwingen omdat ze regel matig kunnen worden bemonsterd en een waarschuwing snel kan worden geactiveerd met relatief eenvoudige logica.

Media Services biedt ondersteuning voor het bewaken van metrische gegevens voor de volgende resources:

* Account
* Streaming-eindpunt

### <a name="account"></a>Account

U kunt de metrische gegevens van het volgende account bewaken.

|Naam van metrische gegevens|Weergavenaam|Beschrijving|
|---|---|---|
|AssetCount|Aantal assets|Assets in uw account.|
|AssetQuota|Activa quotum|Activa quota in uw account.|
|AssetQuotaUsedPercentage|Percentage gebruikt voor het activa quotum|Het percentage van het activa quotum dat al wordt gebruikt.|
|ContentKeyPolicyCount|Aantal beleids regels voor inhouds sleutels|Beleids regels voor inhouds sleutels in uw account.|
|ContentKeyPolicyQuota|Quotum voor inhouds sleutel beleid|Quota voor beleids regels voor inhouds sleutels in uw account.|
|ContentKeyPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor inhouds sleutel beleid|Het percentage van het quotum voor het inhouds sleutel beleid wordt al gebruikt.|
|StreamingPolicyCount|Aantal stroomsgewijze beleids regels|Stroomsgewijze beleids regels in uw account.|
|StreamingPolicyQuota|Quota voor streaming-beleid|Het quotum voor het streamen van beleid in uw account.|
|StreamingPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor het streaming-beleid|Het percentage van het quotum voor het streaming-beleid wordt al gebruikt.|

U moet ook [rekening quota's en limieten](../limits-quotas-constraints.md)bekijken.

### <a name="streaming-endpoint"></a>Streaming-eindpunt

De volgende Media Services gegevens [stromen voor streaming-eind punten](/rest/api/media/streamingendpoints) worden ondersteund:

|Naam van metrische gegevens|Weergavenaam|Beschrijving|
|---|---|---|
|Aanvragen|Aanvragen|Geeft het totale aantal HTTP-aanvragen dat door het streaming-eind punt wordt geleverd.|
|Uitgaand verkeer|Uitgaand verkeer|Totaal aantal uitgaande bytes per minuut per streaming-eind punt.|
|SuccessE2ELatency|Geslaagde end-to-end-latentie|Tijds duur vanaf het moment waarop het streaming-eind punt de aanvraag bij het verzenden van de laatste byte van het antwoord heeft ontvangen.|
|CPU-gebruik| | CPU-gebruik voor Premium streaming-eind punten. Deze gegevens zijn niet beschikbaar voor standaard streaming-eind punten. |
|Uitgangs band breedte | | Uitgangs band breedte in bits per seconde.|

## <a name="metric-dimensions"></a>Metrische dimensies

Zie [multidimensionale metrische](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)gegevens voor meer informatie over de metrieke dimensies.

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Resourcelogboeken

## <a name="media-services-diagnostic-logs"></a>Diagnostische logboeken Media Services

Diagnostische logboeken bieden uitgebreide en frequente gegevens over de werking van een Azure-resource. Zie [logboek gegevens van uw Azure-resources verzamelen en gebruiken](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)voor meer informatie.

Media Services ondersteunt de volgende Diagnostische logboeken:

* Levering van sleutels

### <a name="key-delivery"></a>Levering van sleutels

|Naam|Beschrijving|
|---|---|
|Aanvraag voor key delivery service|Logboeken die de informatie over de sleutel leverings service aanvragen weer geven. Zie [schema's](monitor-media-services-data-reference.md)voor meer informatie.|

## <a name="schemas"></a>Schema 's

Zie [ondersteunde services, schema's en categorieën voor Diagnostische logboeken van Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md)voor een gedetailleerde beschrijving van het schema voor Diagnostische logboeken op het hoogste niveau.

## <a name="key-delivery-log-schema-properties"></a>Eigenschappen van schema voor sleutel leverings logboek

Deze eigenschappen zijn specifiek voor het schema van het sleutel leverings logboek.

|Naam|Beschrijving|
|---|---|
|keyId|De ID van de aangevraagde sleutel.|
|keyType|Dit kan een van de volgende waarden zijn: ' Clear ' (geen versleuteling), ' FairPlay ', ' PlayReady ' of ' Widevine '.|
|policyName|De Azure Resource Manager naam van het beleid.|
|Type|Het token type.|
|statusMessage|Het status bericht.|

### <a name="example"></a>Voorbeeld

Eigenschappen van het schema voor de sleutel afleverings aanvragen.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
