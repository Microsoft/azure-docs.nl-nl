---
title: Azure Media Services inhoud publiceren met .NET | Microsoft Docs
description: Meer informatie over het maken van een Locator die wordt gebruikt voor het bouwen van een streaming-URL. Code voorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015791"
---
# <a name="publish-media-services-content-using-net"></a>Media Services inhoud publiceren met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Overzicht
U kunt een adaptieve bitsnelheid-MP4 streamen die is ingesteld door een OnDemand streaming-Locator te maken en een streaming-URL te bouwen. De [code ring van een Asset](media-services-encode-asset.md) -onderwerp laat zien hoe u kunt coderen in een adaptieve bitsnelheid MP4-set. 

> [!NOTE]
> Als uw inhoud is versleuteld, moet u het beleid voor de levering van assets configureren (zoals beschreven in [Dit](media-services-dotnet-configure-asset-delivery-policy.md) onderwerp) voordat u een Locator maakt. 
> 
> 

U kunt ook een OnDemand streaming-Locator gebruiken om Url's te bouwen die verwijzen naar MP4-bestanden die progressief kunnen worden gedownload.  

In dit onderwerp wordt beschreven hoe u een OnDemand streaming-Locator maakt om uw asset te publiceren en een vloeiende, MPEG-DASH en HLS streaming-Url's te bouwen. Ook wordt weer gegeven hoe u progressieve down load-Url's kunt bouwen. 

## <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand streaming-Locator maken
Als u de OnDemand streaming-Locator wilt maken en Url's wilt ophalen, moet u de volgende handelingen uitvoeren:

1. Als de inhoud is versleuteld, definieert u een toegangs beleid.
2. Maak een OnDemand streaming-Locator.
3. Als u van plan bent om te streamen, haalt u het streaming-manifest bestand (. ISM) op in de Asset. 
   
   Als u van plan bent om geleidelijk te downloaden, kunt u de namen van MP4-bestanden in de Asset ophalen.  
4. Bouw Url's naar het manifest bestand of de MP4-bestanden. 


>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen/toegangs machtigingen gebruikt. Bijvoorbeeld beleids regels voor Locators die zijn bedoeld om gedurende een lange periode te blijven (niet-upload beleid). Raadpleeg [dit](media-services-dotnet-manage-entities.md#limit-access-policies) onderwerp voor meer informatie.

### <a name="use-media-services-net-sdk"></a>Media Services .NET SDK gebruiken
Streaming-Url's bouwen 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

De uitvoer:

- URL naar manifest voor client streaming met Smooth Streaming-Protocol: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- URL naar manifest voor client streaming met HLS-Protocol: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- URL naar manifest voor client streaming met MPEG DASH-Protocol: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> U kunt ook uw inhoud streamen via een TLS-verbinding. Als u deze aanpak wilt uitvoeren, moet u ervoor zorgen dat uw streaming-Url's beginnen met HTTPS. AMS biedt momenteel geen ondersteuning voor TLS met aangepaste domeinen.
> 
> 

Url's voor progressieve down loads maken 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
De uitvoer:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK-extensies gebruiken
Met de volgende code worden .NET SDK-extensie methoden aangeroepen waarmee een Locator wordt gemaakt en de Smooth Streaming, HLS en MPEG-DASH-Url's voor adaptieve streaming worden gegenereerd.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
* [Assets downloaden](media-services-deliver-asset-download.md)
* [Leverings beleid voor assets configureren](media-services-dotnet-configure-asset-delivery-policy.md)

