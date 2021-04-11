---
title: Streaming-locators in Azure Media Services
description: In dit artikel wordt uitgelegd wat streaming-locators zijn en hoe deze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: cd7dd5b5be7af5e4c65ae88a138fff627105003a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281917"
---
# <a name="streaming-locators"></a>Streaming-locators

Om video's in de uitvoerasset beschikbaar te maken voor clients om af te spelen, moet u een [streaming-locator](/rest/api/media/streaminglocators) maken en streaming-URL's maken. Als u een URL wilt samenstellen, moet u de hostnaam van het streaming-eindpunt en het pad van de streaming-locator samenvoegen. Zie [Een streaming-locator ophalen](stream-files-tutorial-with-api.md#get-a-streaming-locator) voor een voorbeeld met .NET.

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-Locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt uitgevoerd en de laatste keer totdat deze is verwijderd, tenzij u de optionele begin-en eind tijden configureert. 

Wanneer u een **streaming-Locator** maakt, moet u een **assetnaam** en een naam voor het **streaming-beleid** opgeven. Zie de volgende onderwerpen voor meer informatie:

* [Assets](assets-concept.md)
* [Streaming-beleid](stream-streaming-policy-concept.md)
* [Beleid voor inhouds sleutels](drm-content-key-policy-concept.md)

U kunt ook de start-en eind tijd opgeven op uw streaming-Locator, zodat uw gebruiker de inhoud niet kan afspelen tussen deze tijden (bijvoorbeeld tussen 5/1/2019 en 5/5/2019).  

## <a name="considerations"></a>Overwegingen

* **Streaming-Locators** kunnen niet worden bijgewerkt. 
* Eigenschappen van **streaming-Locators** van het type datetime zijn altijd in UTC-indeling.
* U dient een beperkt aantal beleids regels te ontwerpen voor uw media service-account en deze opnieuw te gebruiken voor uw streaming-Locators wanneer dezelfde opties nodig zijn. Zie [quota's en limieten](limits-quotas-constraints-reference.md)voor meer informatie.

## <a name="create-streaming-locators"></a>Streaming-Locators maken  

### <a name="not-encrypted"></a>Niet versleuteld

Als u uw bestand in-the-Clear (niet-versleuteld) wilt streamen, stelt u het vooraf gedefinieerde beleid voor Clear streaming in: naar ' Predefined_ClearStreamingOnly ' (in .NET kunt u de Enum PredefinedStreamingPolicy. ClearStreamingOnly gebruiken).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Versleuteld 

Als u uw inhoud wilt versleutelen met de CENC-versleuteling, stelt u uw beleid in op Predefined_MultiDrmCencStreaming. De Widevine-versleuteling wordt toegepast op een streep stroom en PlayReady om vloeiend te maken. De sleutel wordt geleverd aan een Play-client op basis van de geconfigureerde DRM-licenties.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Als u de HLS-stroom ook wilt versleutelen met CBCS (FairPlay), gebruikt u Predefined_MultiDrmStreaming.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Filters koppelen aan streaming-Locators

Zie [filters: koppelen aan stream Locators](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filteren, order, entiteiten voor het streamen van een pagina

Zie [filteren, ordenen, pagineren van Media Services entiteiten](filter-order-page-entitites-how-to.md).

## <a name="list-streaming-locators-by-asset-name"></a>Streaming-Locators vermelden op naam van activum

Als u stroomsgewijze Locators wilt ophalen op basis van de gekoppelde Asset-naam, gebruikt u de volgende bewerkingen:

|Taal|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ AMS activa List-streaming-Locators](/cli/azure/ams/asset#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Zie ook

* [Assets](assets-concept.md)
* [Streaming-beleid](stream-streaming-policy-concept.md)
* [Beleid voor inhouds sleutels](drm-content-key-policy-concept.md)
* [Zelf studie: Video's uploaden, coderen en streamen met behulp van .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Volgende stappen

[Een streaming-Locator maken en Url's bouwen](create-streaming-locator-build-url.md)
