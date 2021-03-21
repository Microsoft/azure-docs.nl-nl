---
title: Patronen
description: Structuur resource werk stroom
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594414"
---
# <a name="textures"></a>Patronen

Bitmappatronen zijn een onveranderbare [gedeelde bron](../concepts/lifetime.md). Bitmappatronen kunnen worden geladen vanuit [Blob Storage](../how-tos/conversion/blob-storage.md) en worden toegepast op modellen, zoals wordt geïllustreerd in [zelf studie: de omgeving en het materiaal wijzigen](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). De meeste patronen zullen echter deel uitmaken van een [geconverteerd model](../how-tos/conversion/model-conversion.md), waar ze naar worden verwezen door het bijbehorende [materiaal](materials.md).

## <a name="texture-types"></a>Typen bitmappatronen

Verschillende typen bitmappatronen hebben verschillende use cases:

* **2D-bitmappatronen** worden voornamelijk gebruikt in [materialen](materials.md).
* **Cubemaps** kunnen worden gebruikt voor de [lucht](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Ondersteunde Texture-indelingen

Alle bitmappatronen die aan ARR zijn gegeven, moeten in [DDS-indeling](https://en.wikipedia.org/wiki/DirectDraw_Surface)zijn. Bij voor keur met mipmaps en texture compression.

## <a name="loading-textures"></a>Bitmappatronen laden

Wanneer u een bitmappatroon laadt, moet u het verwachte type opgeven. Als het type niet overeenkomt, mislukt het laden van de structuur.
Als u een bitmappatroon met dezelfde URI twee keer laadt, wordt hetzelfde object bitmappatroon geretourneerd, omdat het een [gedeelde bron](../concepts/lifetime.md)is.

Net als bij het laden van modellen zijn er twee varianten van het adresseren van een texture-asset in de bron-Blob-opslag:

* Het bitmappatroon kan rechtstreeks worden aangepakt door de Blob Storage-para meters, [als de Blob-opslag is gekoppeld aan het account](../how-tos/create-an-account.md#link-storage-accounts). Relevante laad functie in dit geval is een `LoadTextureAsync` para meter `LoadTextureOptions` .
* De Asset Texture kan worden aangepakt door de SAS-URI. Relevante laad functie is `LoadTextureFromSasAsync` met para meter `LoadTextureFromSasOptions` . Gebruik deze variant ook bij het laden van [ingebouwde structuren](../overview/features/sky.md#built-in-environment-maps).

De volgende voorbeeld code laat zien hoe u een structuur kunt laden:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Houd er rekening mee dat in het geval van het gebruik van de SAS-variant alleen de functie/para meter voor het laden overeenkomen.

Afhankelijk van waarvoor het bitmappatroon moet worden gebruikt, zijn er mogelijk beperkingen voor het type bitmappatroon en de inhoud. De grof toewijzing van een [PBR-materiaal](../overview/features/pbr-materials.md) moet bijvoorbeeld grijs waarden zijn.

## <a name="api-documentation"></a>API-documentatie

* [C#-structuur klasse](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Klasse C++ Texture](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Volgende stappen

* [Materialen](materials.md)
* [Heel](../overview/features/sky.md)