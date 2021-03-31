---
title: CloudEvents v 1.0-schema met Azure Event Grid
description: Hierin wordt beschreven hoe u het CloudEvents v 1.0-schema gebruikt voor gebeurtenissen in Azure Event Grid. De service ondersteunt gebeurtenissen in de JSON-implementatie van Cloud gebeurtenissen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324175"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>CloudEvents v 1.0-schema met Azure Event Grid

Naast het [standaard schema](event-schema.md)van de gebeurtenis, ondersteunt Azure Event grid systeem eigen gebeurtenissen in de [JSON-implementatie van CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [http-protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenis gegevens.

CloudEvents vereenvoudigt interoperabiliteit door een gemeen schappelijk gebeurtenis schema te bieden voor het publiceren en gebruiken van Cloud gebeurtenissen. Dit schema biedt uniforme hulp middelen, standaard methoden voor route ring & het afhandelen van gebeurtenissen en universele manieren om het buitenste gebeurtenis schema te deserialiseren. Met een gemeen schappelijk schema kunt u gemakkelijker werk op verschillende platforms integreren.

CloudEvents wordt gebouwd door verschillende deel [nemers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder micro soft, via de [systeem eigen Cloud Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1,0.

In dit artikel wordt het CloudEvents-schema met Event Grid beschreven.

## <a name="sample-event-using-cloudevents-schema"></a>Voorbeeld gebeurtenis met behulp van CloudEvents-schema

Hier volgt een voor beeld van een Azure Blob Storage-gebeurtenis in de indeling CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[Hier](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)vindt u een gedetailleerde beschrijving van de beschik bare velden, hun typen en definities in CloudEvents v 1.0.

De waarden van headers voor gebeurtenissen die in het CloudEvents-schema en het Event Grid schema worden bezorgd, zijn hetzelfde, behalve voor `content-type` . Voor het CloudEvents-schema is die header waarde `"content-type":"application/cloudevents+json; charset=utf-8"` . Voor Event Grid schema is die header waarde `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>Event Grid voor CloudEvents

U kunt Event Grid gebruiken voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. U kunt CloudEvents gebruiken voor systeem gebeurtenissen, zoals Blob Storage gebeurtenissen en IoT Hub gebeurtenissen, en aangepaste gebeurtenissen. Het kan ook deze gebeurtenissen op de achtergrond transformeren.


| Invoer schema       | Uitvoer schema
|--------------------|---------------------
| CloudEvents-indeling | CloudEvents-indeling
| Event Grid indeling  | CloudEvents-indeling
| Event Grid indeling  | Event Grid indeling

Voor alle gebeurtenis schema's moet Event Grid worden gevalideerd bij het publiceren naar een event grid-onderwerp en bij het maken van een gebeurtenis abonnement. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [CloudEvents v 1.0-schema gebruiken met Event grid](cloudevents-schema.md).  
