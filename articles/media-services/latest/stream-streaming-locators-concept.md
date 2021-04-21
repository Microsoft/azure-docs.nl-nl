---
title: Streaming-locators in Azure Media Services
description: In dit artikel wordt beschreven wat streaming-locators zijn en hoe deze worden gebruikt door Azure Media Services.
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
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773275"
---
# <a name="streaming-locators"></a>Streaming-locators

Om video's in de uitvoerasset beschikbaar te maken voor clients om af te spelen, moet u een [streaming-locator](/rest/api/media/streaminglocators) maken en streaming-URL's maken. Als u een URL wilt samenstellen, moet u de hostnaam van het streaming-eindpunt en het pad van de streaming-locator samenvoegen. Zie [Een streaming-locator ophalen](stream-files-tutorial-with-api.md#get-a-streaming-locator) voor een voorbeeld met .NET.

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gedaan en duurt totdat deze wordt verwijderd, tenzij u de optionele begin- en eindtijden configureert. 

Wanneer u een **streaming-locator maakt,** moet u een **assetnaam** en een **naam voor het streamingbeleid** opgeven. Zie de volgende onderwerpen voor meer informatie:

* [Assets](assets-concept.md)
* [Beleid voor streaming](stream-streaming-policy-concept.md)
* [Beleid voor inhoudssleutels](drm-content-key-policy-concept.md)

U kunt ook de begin- en eindtijd op uw streaming-locator opgeven, zodat uw gebruiker de inhoud alleen tussen deze tijden kan afspelen (bijvoorbeeld tussen 1-5-2019 tot 5-5-2019).  

## <a name="considerations"></a>Overwegingen

* **Streaming-locators** zijn niet updatable. 
* Eigenschappen van **streaming-locators** van het datum/tijd-type hebben altijd de UTC-indeling.
* U moet een beperkte set beleidsregels ontwerpen voor uw Media Service-account en deze opnieuw gebruiken voor uw streaming-locators wanneer dezelfde opties nodig zijn. Zie Quota en limieten [voor meer informatie.](limits-quotas-constraints-reference.md)

## <a name="create-streaming-locators"></a>Streaming-locators maken  

### <a name="not-encrypted"></a>Niet versleuteld

Als u uw bestand in-the-clear (niet-versleuteld) wilt streamen, stelt u het vooraf gedefinieerde duidelijke streamingbeleid in op 'Predefined_ClearStreamingOnly' (in .NET kunt u de enum PredefinedStreamingPolicy.ClearStreamingOnly gebruiken).

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

Als u uw inhoud wilt versleutelen met de CENC-versleuteling, stelt u uw beleid in op 'Predefined_MultiDrmCencStreaming'. De Widevine-versleuteling wordt toegepast op een DASH-stream en PlayReady op Smooth. De sleutel wordt geleverd aan een afspeelclient op basis van de geconfigureerde DRM-licenties.

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

Als u uw HLS-stream ook wilt versleutelen met CBCS (FairPlay), gebruikt u 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Filters koppelen aan streaming-locators

Zie [Filters: koppelen aan streaming-locators.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>Filteren, orde, paginastreaminglocatorentiteiten

Zie [Filteren, orden, pagineren van Media Services entiteiten.](filter-order-page-entitites-how-to.md)

## <a name="list-streaming-locators-by-asset-name"></a>Lijst met streaming-locators op assetnaam

Gebruik de volgende bewerkingen om streaming-locators op te halen op basis van de bijbehorende assetnaam:

|Taal|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Zie ook

* [Assets](assets-concept.md)
* [Beleid voor streaming](stream-streaming-policy-concept.md)
* [Beleid voor inhoudssleutels](drm-content-key-policy-concept.md)
* [Zelfstudie: video's uploaden, coderen en streamen met .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Volgende stappen

[Een streaming-locator maken en URL's bouwen](create-streaming-locator-build-url.md)
