---
title: Afbeeldingsbelichting
description: Beschrijving van de licht bron en eigenschappen
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206846"
---
# <a name="scene-lighting"></a>Afbeeldingsbelichting

Standaard worden de extern gerenderde objecten niet gebrand met een [lucht licht](sky.md). Voor de meeste toepassingen is dit al voldoende, maar u kunt verdere licht bronnen toevoegen aan de scène.

> [!IMPORTANT]
> Alleen [PBR-materialen](pbr-materials.md) worden beïnvloed door licht bronnen. [Kleuren materialen](color-materials.md) worden altijd volledig helder weer gegeven.

> [!NOTE]
> Giet scha duwen worden momenteel niet ondersteund. De externe rendering van Azure is geoptimaliseerd voor het renderen van enorme hoeveel heden geometrie, waarbij gebruik wordt gemaakt van meerdere Gpu's, indien nodig. Traditionele benaderingen voor het converteren van schaduw werken niet goed in dergelijke scenario's.

## <a name="common-light-component-properties"></a>Eigenschappen van algemene licht onderdelen

Alle lichte typen worden afgeleid van de abstracte basis klasse `LightComponent` en delen deze eigenschappen:

* **Kleur:** De kleur van het licht in [gamma-ruimte](https://en.wikipedia.org/wiki/SRGB). Alfa wordt genegeerd.

* **Intensiteit:** De helderheid van het licht. Voor punt-en spot lichten definieert de intensiteit ook hoe ver het licht schijnt.

## <a name="point-light"></a>Punt licht

In azure-rendering op afstand `PointLightComponent` kan het niet alleen licht vanaf één punt worden gegenereerd, maar ook van een kleine bol of een kleine buis om zachtere licht bronnen te simuleren.

### <a name="pointlightcomponent-properties"></a>PointLightComponent-eigenschappen

* **RADIUS:** De standaard RADIUS is nul, in het geval dat het lampje als een punt licht fungeert. Als de RADIUS groter is dan nul, fungeert deze als een bolvormige licht bron, waardoor het uiterlijk van reflecterende hooglichten wordt gewijzigd.

* **Lengte:** Als beide `Length` en `Radius` niet-nul zijn, fungeert het licht als een buis licht. Dit kan worden gebruikt om neon buizen te simuleren.

* **AttenuationCutoff:** Indien van links naar (0, 0) is de uitzwakking van het licht alleen afhankelijk van het `Intensity` . U kunt echter aangepaste min/max-afstanden opgeven waarvan de intensiteit van het licht lineair omlaag wordt geschaald tot 0. Deze functie kan worden gebruikt om een kleiner bereik van invloed op een bepaald licht af te dwingen.

* **ProjectedCubemap:** Als deze is ingesteld op een geldige [cubemap](../../concepts/textures.md), wordt het bitmappatroon geprojecteerd op de omringende geometrie van het licht. De kleur van de cubemap wordt gemoduleerd met de kleur van het licht.

## <a name="spot-light"></a>Spot licht

Het `SpotLightComponent` is vergelijkbaar met het `PointLightComponent` licht, maar het lampje is beperkt tot de vorm van een kegel. De afdruk stand van de kegel wordt gedefinieerd door de *negatieve z-as van de eigenaar* van de entiteit.

### <a name="spotlightcomponent-properties"></a>SpotLightComponent-eigenschappen

* **RADIUS:** Hetzelfde als voor de `PointLightComponent` .

* **SpotAngleDeg:** Dit interval definieert de binnenste en buitenste hoek van de kegel, gemeten in graden. Alles binnen de binnenste hoek is lichter met volledige helderheid. Er wordt een wegval toegepast op de buitenste hoek waarmee een Penumbra effect wordt gegenereerd.

* **FalloffExponent:** Definieert hoe scherp de wegval overgang tussen de binnenste en de buitenste kegel hoek is. Een hogere waarde resulteert in een scherpere overgang. De standaard waarde van 1,0 resulteert in een lineaire overgang.

* **AttenuationCutoff:** Hetzelfde als voor de `PointLightComponent` .

* **Projected2dTexture:** Als deze is ingesteld op een geldig [2D-patroon](../../concepts/textures.md), wordt de afbeelding geprojecteerd op de geometrie waar het lampje op staat. De kleur van het bitmappatroon wordt gemoduleerd met de kleur van het licht.

## <a name="directional-light"></a>Directioneel licht

Het `DirectionalLightComponent` simuleert een licht bron die oneindig ver weg is. Het licht schijnt in de richting van de *negatieve z-as van de entiteit eigenaar*. De positie van de entiteit wordt genegeerd.

Er zijn geen aanvullende eigenschappen.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Licht bronnen hebben veel invloed op de rendering van prestaties. Gebruik deze zorgvuldig en alleen als dit door de toepassing wordt vereist. Een statische globale belichtings voorwaarde, met inbegrip van een statische richtings onderdeel, kan worden behaald met een [aangepast lucht patroon](sky.md), zonder dat er extra kosten voor rendering zijn.

## <a name="api-documentation"></a>API-documentatie

* [C# LightComponentBase-klasse](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent-klasse](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent-klasse](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent-klasse](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase-klasse](/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent-klasse](/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent-klasse](/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent-klasse](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Volgende stappen

* [Materialen](../../concepts/materials.md)
* [Heel](sky.md)