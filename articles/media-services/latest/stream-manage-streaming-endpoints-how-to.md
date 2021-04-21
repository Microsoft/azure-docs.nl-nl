---
title: Streaming-eindpunten beheren
description: In dit artikel wordt beschreven hoe u streaming-eindpunten beheert met Azure Media Services v3.
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
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791729"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Streaming-eindpunten beheren met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer uw Media Services wordt gemaakt, wordt er een [standaardstreaming-eindpunt](stream-streaming-endpoint-concept.md) met de status Gestopt toegevoegd aan **uw** account.  Als u inhoud wilt streamen [](encode-dynamic-packaging-concept.md) en gebruik wilt maken van dynamische verpakking en dynamische versleuteling, [](drm-content-protection-concept.md)moet het streaming-eindpunt van waar u inhoud wilt streamen de status **Wordt** uitgevoerd hebben.

In dit artikel wordt beschreven hoe u de [startopdracht](/rest/api/media/streamingendpoints/start) uitvoert op uw streaming-eindpunt met behulp van verschillende technologieën. 
 
> [!NOTE]
> U wordt alleen gefactureerd wanneer uw streaming-eindpunt wordt uitgevoerd.
    
## <a name="prerequisites"></a>Vereisten

Bekijk: 

* [Media Services-concepts](concepts-overview.md)
* [Concept streaming-eindpunt](stream-streaming-endpoint-concept.md)
* [Dynamische verpakking](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>REST gebruiken

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Zie voor meer informatie: 

* De [start een StreamingEndpoint-referentiedocumentatie.](/rest/api/media/streamingendpoints/start)
* Het starten van een streaming-eindpunt is een asynchrone bewerking. 

    Zie Langlopende bewerkingen voor meer informatie over het bewaken [van langlopende bewerkingen.](media-services-apis-overview.md)
* Deze [Postman-verzameling](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) bevat voorbeelden van meerdere REST-bewerkingen, waaronder het starten van een streaming-eindpunt.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken 
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Ga naar uw Azure Media Services account.
1. Selecteer streaming-eindpunten **in het linkerdeelvenster.**
1. Selecteer het streaming-eindpunt dat u wilt starten en selecteer vervolgens **Starten.**

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Zie az [ams streaming-endpoint start voor meer informatie.](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart)

## <a name="use-sdks"></a>SDK's gebruiken

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Bekijk het volledige [Java-codevoorbeeld](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

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

Bekijk het volledige [.NET-codevoorbeeld.](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112)

---

## <a name="next-steps"></a>Volgende stappen

* [Media Services v3 OpenAPI Specification (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Streaming-eindpuntbewerkingen](/rest/api/media/streamingendpoints)
