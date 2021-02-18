---
title: Schema's van Azure Media Services Diagnostische logboeken-Azure
description: In dit artikel worden de schema's van Azure Media Services Diagnostische logboeken gedemonstreerd.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8015f610dfd9404810a1fa19c951078ebb45ac4f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588498"
---
# <a name="diagnostic-logs-schemas"></a>Schema's van diagnostische logboeken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met [Azure monitor](../../azure-monitor/overview.md) kunt u metrische gegevens en Diagnostische logboeken bewaken die u helpen begrijpen hoe uw toepassingen worden uitgevoerd. U kunt Media Services Diagnostische logboeken bewaken en waarschuwingen en meldingen maken voor de verzamelde metrische gegevens en Logboeken. U kunt logboeken naar [Azure Storage](https://azure.microsoft.com/services/storage/)verzenden, ze streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/)en exporteren naar [log Analytics](https://azure.microsoft.com/services/log-analytics/)of services van derden gebruiken.

Zie [Azure monitor metrische](../../azure-monitor/data-platform.md) gegevens en [Azure monitor Diagnostische logboeken](../../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie.

In dit artikel worden Media Services schema's voor diagnostische Logboeken beschreven.

## <a name="top-level-diagnostic-logs-schema"></a>Schema voor Diagnostische logboeken op het hoogste niveau

Zie [ondersteunde services, schema's en categorieën voor Diagnostische logboeken van Azure](../../azure-monitor/essentials/resource-logs-schema.md)voor een gedetailleerde beschrijving van het schema voor Diagnostische logboeken op het hoogste niveau.

## <a name="key-delivery-log-schema"></a>Schema voor sleutel leverings logboek

### <a name="properties"></a>Eigenschappen

Deze eigenschappen zijn specifiek voor het schema van het sleutel leverings logboek.

|Naam|Beschrijving|
|---|---|
|keyId|De ID van de aangevraagde sleutel.|
|keyType|Dit kan een van de volgende waarden zijn: ' Clear ' (geen versleuteling), ' FairPlay ', ' PlayReady ' of ' Widevine '.|
|policyName|De Azure Resource Manager naam van het beleid.|
|Type|Het token type.|
|statusMessage|Het status bericht.|

### <a name="examples"></a>Voorbeelden

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

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

[Media Services metrische gegevens en Diagnostische logboeken bewaken](media-services-metrics-diagnostic-logs.md)
