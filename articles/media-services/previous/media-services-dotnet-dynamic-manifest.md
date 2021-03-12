---
title: Filters maken met Azure Media Services .NET SDK
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services .NET SDK maakt dynamische manifesten om deze selectief streaming te verzorgen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin
ms.custom: devx-track-csharp
ms.openlocfilehash: bd5435f7a2969c486042c9447a0fffbb745229f9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014108"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Filters maken met Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Met ingang van 2,17 versie Media Services kunt u filters voor uw assets definiëren. Deze filters zijn regels aan de server zijde waarmee uw klanten kunnen kiezen voor het uitvoeren van dingen als: alleen een sectie van een video afspelen (in plaats van de volledige video afspelen), of alleen een subset van audio-en video weergaven opgeven die het apparaat van de klant kan verwerken (in plaats van alle vertoningen die aan de Asset zijn gekoppeld). Dit filter van uw assets wordt bereikt via de **dynamische manifesten** die worden gemaakt op de aanvraag van uw klant om een video te streamen op basis van opgegeven filter (s).

Zie [overzicht van dynamische manifesten](media-services-dynamic-manifest-overview.md)voor meer informatie over filters en dynamische manifesten.

In dit artikel wordt beschreven hoe u Media Services .NET SDK gebruikt om filters te maken, bij te werken en te verwijderen. 

Opmerking Als u een filter bijwerkt, kan het Maxi maal twee minuten duren voordat het streaming-eind punt de regels vernieuwt. Als de inhoud is geleverd met dit filter (en in cache is opgeslagen in proxy's en CDN-caches), kan het bijwerken van dit filter leiden tot fouten in de speler. De cache altijd wissen na het bijwerken van het filter. Als deze optie niet mogelijk is, kunt u overwegen een ander filter te gebruiken. 

## <a name="types-used-to-create-filters"></a>Typen die worden gebruikt om filters te maken
De volgende typen worden gebruikt bij het maken van filters: 

* **IStreamingFilter**.  Dit type is gebaseerd op het volgende REST API [filter](/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Dit type is gebaseerd op de volgende REST API [AssetFilter](/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Dit type is gebaseerd op de volgende REST API [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** en **IFilterTrackPropertyCondition**. Deze typen zijn gebaseerd op de volgende REST Api's [FilterTrackSelect en FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Globale filters maken/bijwerken/lezen/verwijderen
De volgende code laat zien hoe u .NET gebruikt voor het maken, bijwerken, lezen en verwijderen van Asset-filters.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Activa filters maken/bijwerken/lezen/verwijderen
De volgende code laat zien hoe u .NET gebruikt voor het maken, bijwerken, lezen en verwijderen van Asset-filters.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Streaming-Url's bouwen die filters gebruiken
Zie voor meer informatie over het publiceren en leveren van uw assets [overzicht inhoud aan klanten leveren](media-services-deliver-content-overview.md).

In de volgende voor beelden ziet u hoe u filters kunt toevoegen aan uw streaming-Url's.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van dynamische manifesten](media-services-dynamic-manifest-overview.md)
