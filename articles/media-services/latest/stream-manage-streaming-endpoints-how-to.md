---
title: Streaming-eindpunten beheren
description: In dit artikel wordt beschreven hoe u streaming-eind punten beheert met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 5ad7ef447e1b8e5ac141e4feb65f1b8361b2a646
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281934"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Streaming-eind punten beheren met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer uw Media Services-account is gemaakt, wordt er een **standaard** [streaming-eind punt](stream-streaming-endpoint-concept.md) aan uw account toegevoegd met de status **gestopt** . Als u de inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](encode-dynamic-packaging-concept.md) en [dynamische versleuteling](drm-content-protection-concept.md), moet het streaming-eind punt van waar u inhoud wilt streamen, de status **wordt uitgevoerd** hebben.

In dit artikel wordt beschreven hoe u de [Start](/rest/api/media/streamingendpoints/start) -opdracht uitvoert op het streaming-eind punt met behulp van verschillende technologieën. 
 
> [!NOTE]
> Er worden alleen kosten in rekening gebracht wanneer het streaming-eind punt wordt uitgevoerd.
    
## <a name="prerequisites"></a>Vereisten

Bekijk: 

* [Media Services-concepts](concepts-overview.md)
* [Concept van streaming-eind punt](stream-streaming-endpoint-concept.md)
* [Dynamische verpakking](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>REST gebruiken

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Zie voor meer informatie: 

* De documentatie voor [een StreamingEndpoint](/rest/api/media/streamingendpoints/start) -referentie starten.
* Het starten van een streaming-eind punt is een asynchrone bewerking. 

    Zie [langlopende bewerkingen](media-services-apis-overview.md)voor meer informatie over het controleren van langlopende bewerkingen.
* Deze [postman-verzameling](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) bevat voor beelden van meerdere rest-bewerkingen, met inbegrip van het starten van een streaming-eind punt.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken 
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Ga naar uw Azure Media Services-account.
1. Selecteer in het linkerdeel venster  **streaming-eind punten**.
1. Selecteer het streaming-eind punt dat u wilt starten en selecteer vervolgens **starten**.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Zie [AZ AMS streaming-endpoint start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start)voor meer informatie.

## <a name="use-sdks"></a>SDK's gebruiken

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Bekijk het volledige voor [beeld van Java-code](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Bekijk het volledige voor [beeld van .net-code](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Volgende stappen

* [Media Services v3 OpenAPI-specificatie (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Streaming-eindpunt bewerkingen](/rest/api/media/streamingendpoints)
