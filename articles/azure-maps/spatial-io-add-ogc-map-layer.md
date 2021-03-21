---
title: Een Open Geospatial Consortium-kaartLaag (OGC) toevoegen | Microsoft Azure kaarten
description: Meer informatie over het bedekken van een OGC-kaartLaag op de kaart en het gebruik van de verschillende opties in de klasse OgcMapLayer.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891662"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Een kaartLaag toevoegen vanuit de Open Geospatial Consortium (OGC)

De `atlas.layer.OgcMapLayer` klasse kan installatie kopieën van web map Services (WMS) en WMTS (Web Maps tile Services) op de kaart overlay. WMS is een standaard protocol dat door OGC is ontwikkeld voor het leveren van geoverwijzings installatie kopieën via internet. Afbeelding georefererend is de processen voor het koppelen van een afbeelding aan een geografische locatie. WMTS is ook een standaard protocol dat is ontwikkeld door OGC. Het is ontworpen voor het leveren van vooraf gerenderde en geoverwijzinge kaart tegels.

De volgende secties bevatten een overzicht van de functies van de web map-service die worden ondersteund door de- `OgcMapLayer` klasse.

**Web map-service (WMS)**

- Ondersteunde versies: `1.0.0` , `1.1.0` , `1.1.1` en `1.3.0`
- De service moet het `EPSG:3857` projectie systeem ondersteunen of reprojecties verwerken.
- GetFeatureInfo vereist dat de service `EPSG:4326` herprojecties ondersteunt of verwerkt. 
- Ondersteunde bewerkingen:

    | Bewerking | Beschrijving |
    | :-- | :-- |
    | GetCapabilities | Hiermee worden meta gegevens van de service met de ondersteunde mogelijkheden opgehaald |
    | GetMap | Hiermee wordt een kaart afbeelding voor een opgegeven regio opgehaald |
    | GetFeatureInfo | Haalt `feature_info` , die onderliggende gegevens bevat over de functie |

**Websitemap-tegel service (WMTS)**

- Ondersteunde versies: `1.0.0`
- Tegels moeten een vier kant zijn, dat wil zeggen `TileWidth == TileHeight` .
- CRS ondersteund: `EPSG:3857` of `GoogleMapsCompatible` 
- De TileMatrix-id moet een geheel getal zijn dat overeenkomt met een zoom niveau op de kaart. Op een Azure-kaart is het zoom niveau een waarde tussen `"0"` en `"22"` . Dit wordt dus wel `"0"` ondersteund, maar wordt `"00"` niet ondersteund.
- Ondersteunde bewerkingen:

    | Bewerking | Beschrijving |
    | :-- | :-- |
    | GetCapabilities | Hiermee worden de ondersteunde bewerkingen en functies opgehaald |
    | GetTile | Hiermee wordt de afbeelding voor een bepaalde tegel opgehaald |

## <a name="overlay-an-ogc-map-layer"></a>Een OGC-kaartLaag bedekken

De `url` kan de basis-URL voor de service zijn of een volledige URL met de query voor het ophalen van de mogelijkheden van de service. Afhankelijk van de gegevens die u opgeeft, kan de WFS-client verschillende standaard-URL-indelingen proberen om te bepalen hoe de service voor het eerst wordt geopend.

De volgende code laat zien hoe u een OGC-kaartLaag kunt bedekken op de kaart.

<br/>

<iframe height='700' scrolling='no' title='Voor beeld van OGC-kaartLaag' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk het <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>voor beeld</a> Azure Maps van de OGC-kaartLaag van de pen <a href='https://codepen.io/azuremaps'>@azuremaps</a> op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opties voor OGC-kaartLaag

In het onderstaande voor beeld ziet u de verschillende opties voor de OGC-kaartLaag. U kunt in de rechter bovenhoek op de knop code-pen klikken om de code-pen te bewerken.

<br/>

<iframe height='700' scrolling='no' title='Opties voor OGC-kaartLaag' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de OGC voor de <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>kaart laag</a> Azure Maps van de pen <a href='https://codepen.io/azuremaps'>@azuremaps</a> op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web map service Explorer

Met het volgende hulp programma worden afbeeldingen van de web map Services (WMS) en WMTS (web map tile Services) als lagen bedekt. U kunt selecteren welke lagen in de service worden weer gegeven op de kaart. U kunt ook de bijbehorende legenda's voor deze lagen weer geven.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web map service Explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web map service Explorer</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt ook de kaart instellingen voor het gebruik van een proxy service opgeven. Met de proxy service kunt u bronnen laden die worden gehost op domeinen waarvoor CORS niet is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Raadpleeg de volgende artikelen, met code voorbeelden die u aan uw kaarten kunt toevoegen:

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kernbewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)