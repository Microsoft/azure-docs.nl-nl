---
title: Naslaginformatie Media Services bewakingsgegevens
description: Belangrijk referentiemateriaal dat nodig is bij het bewaken van Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: 3fd7b8013ec67d718f308ccd1b72a6f90012e02e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873051"
---
# <a name="monitoring-media-services-data-reference"></a>Naslaginformatie Media Services bewakingsgegevens

In dit artikel worden de gegevens beschreven die nuttig zijn voor het controleren Media Services. Voor meer informatie over alle platformmetrieken die worden ondersteund in Azure Monitor, bekijkt u [Ondersteunde metrische gegevens met Azure Monitor.](../../../azure-monitor/essentials/metrics-supported.md)

## <a name="metrics"></a>Metrische gegevens

Metrische gegevens worden regelmatig verzameld, ongeacht of de waarde verandert. Ze zijn handig voor waarschuwingen, omdat er vaak steekproefs kunnen worden genomen en een waarschuwing snel kan worden gemaakt met relatief eenvoudige logica.


Media Services ondersteunt het bewaken van metrische gegevens voor de volgende resources:

|Type metrische gegevens | Resourceprovider/type naamruimte<br/> en koppeling naar afzonderlijke metrische gegevens |
|-------|-----|
| Media Services algemeen | [Algemeen](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Livegebeurtenissen | [Microsoft.Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Streaming-eindpunten | [Microsoft.Media/mediaservices/streamingEndpoints,](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints)die relevant zijn voor de [streaming-eindpunten REST API](/rest/api/media/streamingendpoints). 


U moet ook [accountquota en -limieten bekijken.](../limits-quotas-constraints-reference.md)


## <a name="metric-dimensions"></a>Metrische dimensies

Zie Multidimensionale metrische gegevens voor meer informatie over wat [metrische dimensies zijn.](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

Media Services heeft de volgende metrische dimensies.  Ze zijn zelfplanlijk op basis van de metrische gegevens die ze ondersteunen.  Zie de [bovenstaande koppelingen voor](#metrics) metrische gegevens voor meer informatie.   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- TrackName 

## <a name="resource-logs"></a>Resourcelogboeken

Resourcelogboeken bieden uitgebreide en regelmatige gegevens over de werking van een Azure-resource. Zie Logboekgegevens van uw [Azure-resources verzamelen en](../../../azure-monitor/essentials/platform-logs-overview.md)gebruiken voor meer informatie.

Media Services ondersteunt de volgende resourcelogboeken: [Microsoft.Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Schema 's

Zie Ondersteunde services, schema's en categorieën voor Diagnostische logboeken van Azure voor een gedetailleerde beschrijving van het schema voor diagnostische [logboeken op het hoogste niveau.](../../../azure-monitor/essentials/resource-logs-schema.md)

## <a name="key-delivery-log-schema-properties"></a>Schema-eigenschappen voor sleutelleveringslogboek

Deze eigenschappen zijn specifiek voor het schema van het sleutelleveringslogboek.

|Naam|Beschrijving|
|---|---|
|keyId|De id van de aangevraagde sleutel.|
|Keytype|Dit kan een van de volgende waarden zijn: 'Clear' (geen versleuteling), 'FairPlay', 'PlayReady' of 'Widevine'.|
|policyName|De Azure Resource Manager naam van het beleid.|
|tokenType|Het tokentype.|
|statusMessage|Het statusbericht.|

### <a name="example"></a>Voorbeeld

Eigenschappen van het schema voor sleutelleveringsaanvragen.

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
