---
title: Een tegel laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van een tegel laag aan een kaart. Bekijk een voor beeld waarin de Azure Maps Android-SDK wordt gebruikt om een weers radar-overlay toe te voegen aan een kaart.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608974"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Een laag voor een tegel toevoegen aan een kaart (Android SDK)

In dit artikel wordt beschreven hoe u een tegel laag op een kaart kunt weer geven met behulp van de Azure Maps Android SDK. Met tegel lagen kunt u afbeeldingen boven op Azure Maps basis kaart tegels plaatsen. Meer informatie over Azure Maps tegel systeem vindt u in de documentatie over het [Zoom niveau en het tegel raster](zoom-levels-and-tile-grid.md) .

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven en opgeslagen, zoals elke andere installatie kopie op een server, met behulp van een naamgevings Conventie die de laag van de tegel begrijpt. Het is ook mogelijk dat deze installatie kopieën worden weer gegeven met een dynamische service die de afbeeldingen bijna in real time genereert. Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door Azure Maps klasse TileLayer:

* X, Y, zoom notatie: gebaseerd op het zoom niveau, x is de kolom en Y de rijpositie van de tegel in het tegel raster.
* Quadkey notatie: combi natie x, y en zoom informatie in een enkele teken reeks waarde die een unieke id voor een tegel is.
* Omsluitende Box-coördinaten kunnen worden gebruikt voor het opgeven van een afbeelding in de indeling `{west},{south},{east},{north}` , die meestal wordt gebruikt door de [Services voor webtoewijzing (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Een TileLayer is een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag. Door vector gegevens als een tegel laag te renderen, hoeft het kaart besturings element alleen de tegels te laden. Dit kan veel kleiner zijn dan de vector gegevens die ze vertegenwoordigen. Deze techniek wordt gebruikt door veel die miljoenen rijen met gegevens op de kaart moeten weer geven.

De tegel-URL die wordt door gegeven aan een tegel laag moet een HTTP/HTTPS-URL zijn naar een TileJSON-resource of een tegel-URL-sjabloon die gebruikmaakt van de volgende para meters:

* `{x}` -X-positie van de tegel. Ook nodig `{y}` en `{z}` .
* `{y}` -Y-positie van de tegel. Ook nodig `{x}` en `{z}` .
* `{z}` -Zoom niveau van de tegel. Ook nodig `{x}` en `{y}` .
* `{quadkey}` -Tegel quadkey-id gebaseerd op de naam Conventie voor Bing Maps-tegel systeem.
* `{bbox-epsg-3857}` -Een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het EPSG 3857 Spatial Reference System.
* `{subdomain}` -Een tijdelijke aanduiding voor de waarden van het subdomein als de subdomeinwaarde is opgegeven.
* `azmapsdomain.invalid` -Een tijdelijke aanduiding voor het uitlijnen van het domein en de verificatie van Tegel aanvragen met dezelfde waarden die worden gebruikt door de kaart. Gebruik deze methode wanneer u een tegel service aanroept die wordt gehost door Azure Maps.

## <a name="prerequisites"></a>Vereisten

Om het proces in dit artikel te volt ooien, moet u [Azure Maps ANDROID SDK](how-to-use-android-map-control-library.md) installeren om een kaart te laden.

## <a name="add-a-tile-layer-to-the-map"></a>Een tegel laag aan de kaart toevoegen

Dit voor beeld laat zien hoe u een tegel laag maakt die verwijst naar een set tegels. In dit voor beeld wordt het tegel systeem x, y, Zoom gebruikt. De bron van deze laag voor tegels is het [OpenSeaMap-project](https://openseamap.org/index.php), dat prosourced zeemijl-grafieken bevat. Bij het weer geven van Tegel lagen is het wenselijk om de labels van steden op de kaart duidelijk zichtbaar te maken. Dit gedrag kan worden bereikt door de laag van de tegel onder de kaart label lagen in te voegen.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Op de volgende scherm afbeelding ziet u de bovenstaande code met een tegel laag met zeemijl over een kaart met een donkere grijs waarde.

![Android-kaart met weer gave van laag van Tegel](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Een OGC-webtoewijzings service (WMS) toevoegen

Een WMTS (web mapping service) is een Open Geospatial Consortium (OGC) standaard voor het leveren van installatie kopieën van kaart gegevens. Er zijn veel geopende gegevens sets beschikbaar in deze indeling die u kunt gebruiken met Azure Maps. Dit type service kan worden gebruikt met een laag als de service de `EPSG:3857` coördinaten referentie systeem (CRS) ondersteunt. Wanneer u een WMS-service gebruikt, stelt u de para meters voor breedte en hoogte in op dezelfde waarde die wordt ondersteund door de service. u kunt deze waarde ook instellen in de `tileSize` optie. Stel in de geformatteerde URL de `BBOX` para meter van de service in met de `{bbox-epsg-3857}` tijdelijke aanduiding.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

In de volgende scherm afbeelding ziet u de bovenstaande code voor het bedekken van een webtoewijzings service van geologische gegevens van de [USGS-kaart (U.S. geologische enquêtes)](https://mrdata.usgs.gov/) , onder de labels.

![Android-kaart waarin WMS-tegel laag wordt weer gegeven](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Een OGC-tegel service voor webtoewijzing toevoegen (WMTS)

Een Webmapping-service (WMTS) is een Open Geospatial Consortium-standaard (OGC) voor het leveren van Overzichten op basis van tegels. Er zijn veel geopende gegevens sets beschikbaar in deze indeling die u kunt gebruiken met Azure Maps. Dit type service kan worden gebruikt met een laag als de service het- `EPSG:3857` of `GoogleMapsCompatible` coördinaten referentie systeem (CRS) ondersteunt. Wanneer u een WMTS-service gebruikt, stelt u de para meters voor breedte en hoogte in op dezelfde waarde die door de service wordt ondersteund. Zorg er ook voor dat u dezelfde waarde in de `tileSize` optie instelt. Vervang in de geformatteerde URL de volgende tijdelijke aanduidingen dienovereenkomstig:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

In de volgende scherm afbeelding ziet u de bovenstaande code voor het bedekken van een overzichts service voor webtoewijzing van installatie kopie van de [nationale kaart van de Amerikaanse geologische enquête (USGS)](https://viewer.nationalmap.gov/services/) boven op een kaart, onder de wegen en labels.

![Android-kaart die WMTS-tegel laag weergeeft](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende artikel voor meer informatie over de manieren waarop u beelden kunt bedekken op een kaart.

> [!div class="nextstepaction"]
> [Afbeeldingslaag](map-add-image-layer-android.md)
