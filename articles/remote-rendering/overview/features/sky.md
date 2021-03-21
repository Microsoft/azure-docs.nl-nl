---
title: Luchtreflecties
description: Hierin wordt beschreven hoe u omgevings kaarten instelt voor lucht reflecties
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594177"
---
# <a name="sky-reflections"></a>Luchtreflecties

In azure rendering op afstand wordt een hemel patroon gebruikt om objecten realistisch te lichten. Voor uitgebreide Reality-toepassingen moet dit patroon eruitzien als uw echte omgeving, om ervoor te zorgen dat objecten overtuigend worden weer gegeven. In dit artikel wordt beschreven hoe u het patroon van de lucht wijzigt.

> [!NOTE]
> Het lucht patroon wordt ook wel een *omgevings kaart* genoemd. Deze voor waarden worden door elkaar gebruikt.

## <a name="object-lighting"></a>Object verlichting

Azure remote rendering maakt gebruik van *fysiek gebaseerde rendering* (PBR) voor realistische belichtings berekeningen. Hoewel u [licht bronnen](lights.md) kunt toevoegen aan uw scène, heeft het gebruik van een goede lucht patroon de grootste impact.

In de onderstaande afbeeldingen ziet u de resultaten van de verlichting van verschillende Opper vlakken alleen met een lucht patroon:

| Ruw  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Niet-Metal  | ![Dielectric, grofheid = 0](media/dielectric-0.png)   | ![Dielectric, ruwheid = 0,25](media/dielectric-0.25.png)  | ![Dielectric, ruwheid = 0.5](media/dielectric-0.5.png)  | ![Dielectric, ruw = 0,75](media/dielectric-0.75.png)  | ![Dielectric, ruwheid = 1](media/dielectric-1.png)  |
| Metal      | ![Metaal, ruw = 0](media/metallic-0.png)  | ![Metaal, ruw = 0,25](media/metallic-0.25.png)    | ![Metaal, ruw = 0.5](media/metallic-0.5.png)    | ![Metaal, ruw = 0,75](media/metallic-0.75.png)    | ![Metaal, ruw = 1](media/metallic-1.png)    |

Zie het hoofd stuk [materialen](../../concepts/materials.md) voor meer informatie over het belichtings model.

> [!IMPORTANT]
> Azure remote rendering maakt alleen gebruik van het lucht patroon voor belichtings modellen. De lucht wordt niet weer gegeven als achtergrond, omdat uitgebreide Reality-toepassingen al een correcte achtergrond hebben: de echte wereld.

## <a name="changing-the-sky-texture"></a>Het hemel patroon wijzigen

Als u de omgevings kaart wilt wijzigen, hoeft u alleen [een structuur te laden](../../concepts/textures.md) en de sessie te wijzigen `SkyReflectionSettings` :

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

Houd er rekening mee dat de `LoadTextureFromSasAsync` Variant wordt gebruikt, omdat een ingebouwd bitmappatroon wordt geladen. Gebruik de variant in het geval van het laden vanuit [gekoppelde Blob-opslag](../../how-tos/create-an-account.md#link-storage-accounts) `LoadTextureAsync` .

## <a name="sky-texture-types"></a>Lucht patroon typen

U kunt *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* -en *2D-structuren* gebruiken als omgevings kaarten.

Alle bitmappatronen moeten een [ondersteunde structuur indeling](../../concepts/textures.md#supported-texture-formats)hebben. U hoeft geen mipmaps op te geven voor het lucht patroon.

### <a name="cube-environment-maps"></a>Kubus omgevings kaarten

Ter referentie is dit een niet-verpakte cubemap:

![Een onverpakte cubemap](media/Cubemap-example.png)

Gebruiken `RenderingSession.Connection.LoadTextureAsync` /  `LoadTextureFromSasAsync` met `TextureType.CubeMap` voor het laden van cubemap-structuren.

### <a name="sphere-environment-maps"></a>Sphere-omgevings kaarten

Wanneer u een 2D-bitmappatroon als een omgevings kaart gebruikt, moet de afbeelding zich in een [bolvormige coördinaat ruimte](https://en.wikipedia.org/wiki/Spherical_coordinate_system)bevinden.

![Een lucht afbeelding in bolvormige coördinaten](media/spheremap-example.png)

Gebruik `RenderingSession.Connection.LoadTextureAsync` met `TextureType.Texture2D` om bolvormige-omgevings kaarten te laden.

## <a name="built-in-environment-maps"></a>Ingebouwde omgevings kaarten

Externe rendering van Azure biedt een aantal ingebouwde omgevings kaarten die altijd beschikbaar zijn. Alle ingebouwde omgevings kaarten zijn cubemaps.

|Id                         | Beschrijving                                              | Zien                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Diverse Stripe-lampen, heldere basis verlichting    | ![Skybox gebruikt voor het licht van een object](media/autoshop.png)
|builtin://BoilerRoom               | Lichte instelling voor licht binnenshuis, meerdere venster verlichting      | ![BoilerRoom Skybox gebruikt voor het licht van een object](media/boiler-room.png)
|builtin://ColorfulStudio           | Afwijkende kleur verlichting in de instelling gemiddelde licht binnenshuis  | ![ColorfulStudio Skybox gebruikt voor het licht van een object](media/colorful-studio.png)
|builtin://Hangar                   | Gemiddeld helder omgevings zaal lampje                     | ![SmallHangar Skybox gebruikt voor het licht van een object](media/hangar.png)
|builtin://IndustrialPipeAndValve   | De instelling Dim binnen en licht-donker contrast              | ![IndustrialPipeAndValve Skybox gebruikt voor het licht van een object](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Dagrijlicht-omgevings lampje, helder venster dun     | ![Lebombo Skybox gebruikt voor het licht van een object](media/lebombo.png)
|builtin://SataraNight              | Donkergrijze lucht en grond met een groot aantal omringende lichten   | ![SataraNight Skybox gebruikt voor het licht van een object](media/satara-night.png)
|builtin://SunnyVondelpark          | Helder zonlicht en schaduw contrast                      | ![SunnyVondelpark Skybox gebruikt voor het licht van een object](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Scherp licht wissen met een lichte verlichting            | ![Syferfontein Skybox gebruikt voor het licht van een object](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Zon en scha duwen die gemiddeld variëren                         | ![TearsOfSteelBridge Skybox gebruikt voor het licht van een object](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Zons ondergangs lampje bijna Dusk                    | ![VeniceSunset Skybox gebruikt voor het licht van een object](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Helder, lush-groen en wit licht tonen, lichter gekleurd vlak | ![WhippleCreekRegionalPark Skybox gebruikt voor het licht van een object](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Daytime met helder omgevings licht                 | ![WinterRiver Skybox gebruikt voor het licht van een object](media/winter-river.png)
|builtin://DefaultSky               | Hetzelfde als TearsOfSteelBridge                               | ![DefaultSky Skybox gebruikt voor het licht van een object](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. SkyReflectionSettings eigenschap](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection:: SkyReflectionSettings ()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>Volgende stappen

* [Lampen](../../overview/features/lights.md)
* [Materialen](../../concepts/materials.md)
* [Patronen](../../concepts/textures.md)
