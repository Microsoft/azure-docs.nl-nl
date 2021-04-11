---
title: Een laag voor een tegel aan een kaart toevoegen | Microsoft Azure kaarten
description: Meer informatie over het opleggen van installatie kopieën op kaarten. Bekijk een voor beeld waarin de Web-SDK van Azure Maps wordt gebruikt voor het toevoegen van een tegel laag met een weers radar-overlay aan een kaart.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608099"
---
# <a name="add-a-tile-layer-to-a-map"></a>Een titellaag toevoegen aan een kaart

In dit artikel wordt beschreven hoe u een tegel laag kunt bedekken op de kaart. Met tegel lagen kunt u afbeeldingen boven op Azure Maps basis kaart tegels plaatsen. Zie [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md)voor meer informatie over Azure Maps tegel systeem.

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven of dynamisch worden weer gegeven. Vooraf gerenderde installatie kopieën worden op dezelfde manier opgeslagen als andere installatie kopieën op een server met behulp van een naamgevings Conventie die de laag van de tegel begrijpt. Dynamisch gerenderde afbeeldingen gebruiken een service om de afbeeldingen in realtime te laden. Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door Azure Maps klasse [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) :

* X, Y, zoom notatie-X is de kolom, Y is de rijpositie van de tegel in het tegel raster en de zoom notatie een waarde op basis van het zoom niveau.
* Quadkey notatie: Hiermee wordt de x-, y-en zoom informatie gecombineerd tot één teken reeks waarde. Deze teken reeks waarde wordt een unieke id voor één tegel.
* Begrenzingsvak: Geef een afbeelding op in de coördinaten notatie van het begrenzingsvak `{west},{south},{east},{north}` . Deze indeling wordt vaak gebruikt door de [Services voor webtoewijzing (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Een [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) is een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag. Door vector gegevens als een tegel laag te renderen, hoeft u alleen de tegels te laden die kleiner zijn dan de vector gegevens die ze vertegenwoordigen. Deze techniek wordt vaak gebruikt om miljoenen rijen met gegevens op de kaart weer te geven.

De tegel-URL die wordt door gegeven aan een tegel laag moet een HTTP-of HTTPS-URL zijn naar een TileJSON-resource of een tegel-URL-sjabloon die gebruikmaakt van de volgende para meters:

* `{x}` -X-positie van de tegel. Ook nodig `{y}` en `{z}` .
* `{y}` -Y-positie van de tegel. Ook nodig `{x}` en `{z}` .
* `{z}` -Zoom niveau van de tegel. Ook nodig `{x}` en `{y}` .
* `{quadkey}` -Tegel quadkey-id gebaseerd op de naam Conventie voor Bing Maps-tegel systeem.
* `{bbox-epsg-3857}` -Een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het EPSG 3857 Spatial Reference System.
* `{subdomain}` -Een tijdelijke aanduiding voor de waarden van het subdomein, indien opgegeven, `subdomain` wordt toegevoegd.
* `{azMapsDomain}` -Een tijdelijke aanduiding voor het uitlijnen van het domein en de verificatie van Tegel aanvragen met dezelfde waarden die worden gebruikt door de kaart.

## <a name="add-a-tile-layer"></a>Een titellaag toevoegen

 Dit voor beeld laat zien hoe u een tegel laag maakt die verwijst naar een set tegels. In dit voor beeld wordt gebruikgemaakt van het systeem x, y en zoomen. de bron van deze laag voor tegels is het [OpenSeaMap-project](https://openseamap.org/index.php), dat prosourced zeemijl-grafieken bevat. Bij het weer geven van radar gegevens zien gebruikers in het ideale geval de labels van steden duidelijk wanneer ze door de kaart navigeren. Dit gedrag kan worden geïmplementeerd door de laag van de tegel onder de laag in te voegen `labels` .

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Tegel laag met X, Y en Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>laag met de tegels van de pen met X, Y en Z</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Een OGC-webtoewijzings service (WMS) toevoegen

Een WMTS (web mapping service) is een Open Geospatial Consortium (OGC) standaard voor het leveren van installatie kopieën van kaart gegevens. Er zijn veel geopende gegevens sets beschikbaar in deze indeling die u kunt gebruiken met Azure Maps. Dit type service kan worden gebruikt met een laag als de service de `EPSG:3857` coördinaten referentie systeem (CRS) ondersteunt. Wanneer u een WMS-service gebruikt, stelt u de para meters voor breedte en hoogte in op dezelfde waarde die wordt ondersteund door de service. u kunt deze waarde ook instellen in de `tileSize` optie. Stel in de geformatteerde URL de `BBOX` para meter van de service in met de `{bbox-epsg-3857}` tijdelijke aanduiding.

In de volgende scherm afbeelding ziet u de bovenstaande code voor het bedekken van een webtoewijzings service van geologische gegevens van de [USGS-kaart (U.S. geologische enquêtes)](https://mrdata.usgs.gov/) , onder de labels.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="WMS-tegel laag" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/BapjZqr'>tegel laag</a> van de pen WMS door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Een OGC-tegel service voor webtoewijzing toevoegen (WMTS)

Een Webmapping-service (WMTS) is een Open Geospatial Consortium-standaard (OGC) voor het leveren van Overzichten op basis van tegels. Er zijn veel geopende gegevens sets beschikbaar in deze indeling die u kunt gebruiken met Azure Maps. Dit type service kan worden gebruikt met een laag als de service het- `EPSG:3857` of `GoogleMapsCompatible` coördinaten referentie systeem (CRS) ondersteunt. Wanneer u een WMTS-service gebruikt, stelt u de para meters voor breedte en hoogte in op dezelfde waarde die door de service wordt ondersteund. Zorg er ook voor dat u dezelfde waarde in de `tileSize` optie instelt. Vervang in de geformatteerde URL de volgende tijdelijke aanduidingen dienovereenkomstig:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

In de volgende scherm afbeelding ziet u de bovenstaande code voor het bedekken van een overzichts service voor webtoewijzing van installatie kopie van de [nationale kaart van de Amerikaanse geologische enquête (USGS)](https://viewer.nationalmap.gov/services/) boven op een kaart, onder de wegen en labels.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMTS-tegel laag" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/BapjZVY'>tegel laag</a> van de pen-WMTS door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Een laag voor een tegel aanpassen

De klasse van de tegel laag heeft veel stijl opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor de laag van de tegel' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de laag opties voor pen- <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>tegels</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een afbeeldingslaag toevoegen](./map-add-image-layer.md)
