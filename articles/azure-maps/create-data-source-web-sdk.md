---
title: Een gegevens bron voor een kaart maken in Microsoft Azure Maps
description: 'Meer informatie over het maken van een gegevens bron voor een kaart. Meer informatie over de gegevens bronnen die worden gebruikt door de Azure Maps Web-SDK: geojson-bronnen en vector tegels.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678860"
---
# <a name="create-a-data-source"></a>Een gegevensbron maken

De Azure Maps Web-SDK slaat gegevens op in gegevens bronnen. Het gebruik van gegevens bronnen optimaliseert de gegevens bewerkingen voor het uitvoeren van query's en rendering. Er zijn momenteel twee soorten gegevens bronnen:

* **GEOjson-bron**: Hiermee worden gegevens van onbewerkte locaties lokaal beheerd in geojson-indeling. Geschikt voor kleine tot middel grote gegevens sets (van honderden duizenden vormen).
* **Vector tegel bron**: laadt gegevens die zijn opgemaakt als vector tegels voor de huidige kaart weergave, op basis van het Maps-systeem. Ideaal voor grote tot enorme gegevens sets (miljoenen of miljarden vormen).

## <a name="geojson-data-source"></a>Geojson-gegevens bron

Met een geojson gebaseerde gegevens bron worden gegevens lokaal geladen en opgeslagen met behulp van de- `DataSource` klasse. Geojson-gegevens kunnen hand matig worden gemaakt of gemaakt met behulp van de Help-klassen in de naam ruimte [Atlas. data](/javascript/api/azure-maps-control/atlas.data) . De `DataSource` klasse biedt functies voor het importeren van lokale of externe GEOjson-bestanden. Externe geojson-bestanden moeten worden gehost op een eind punt waarvoor CORs is ingeschakeld. De `DataSource` klasse biedt functionaliteit voor cluster-punt gegevens. En kunnen gegevens eenvoudig worden toegevoegd, verwijderd en bijgewerkt met de- `DataSource` klasse. De volgende code laat zien hoe geojson-gegevens kunnen worden gemaakt in Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Zodra u deze hebt gemaakt, kunt u gegevens bronnen toevoegen aan de kaart via de `map.sources` eigenschap. Dit is een [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager). De volgende code laat zien hoe u een maakt en hoe u `DataSource` deze toevoegt aan de kaart.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

De volgende code toont de verschillende manieren waarop geojson-gegevens kunnen worden toegevoegd aan een `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> Stel dat u wilt dat alle gegevens in een worden overschreven `DataSource` . Als u aanroepen naar de `clear` then `add` -functies, kan de kaart twee keer opnieuw worden weer gegeven. Dit kan een beetje vertraging veroorzaken. Gebruik in plaats daarvan de `setShapes` functie, waarmee alle gegevens in de gegevens bron worden verwijderd en vervangen en slechts één weer gave van de kaart wordt geactiveerd.

## <a name="vector-tile-source"></a>Bron van vector tegel

Een vector tegel bron beschrijft hoe een vector tegel laag kan worden geopend. Gebruik de [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) -klasse om een vector tegel bron te instantiëren. Vector tegel lagen zijn vergelijkbaar met de tegel lagen, maar ze zijn niet hetzelfde. Een tegel laag is een raster afbeelding. Vector tegel lagen zijn een gecomprimeerd bestand in de **PBF** -indeling. Dit gecomprimeerde bestand bevat gegevens van een vector kaart en een of meer lagen. Het bestand kan worden gerenderd en opgemaakt op de client, op basis van de stijl van elke laag. De gegevens in een vector tegel bevatten geografische functies in de vorm van punten, lijnen en veelhoeken. Er zijn verschillende voor delen van het gebruik van vector tegel lagen in plaats van raster tegel lagen:

* Een bestands grootte van een vector tegel is doorgaans veel kleiner dan een gelijkwaardige raster tegel. Als zodanig wordt er minder band breedte gebruikt. Dit betekent een lagere latentie, een snellere kaart en een betere gebruikers ervaring.
* Omdat vector tegels worden weer gegeven op de client, worden ze aangepast aan de resolutie van het apparaat waarop ze worden weer gegeven. Als gevolg hiervan worden de gerenderde kaarten meer duidelijk gedefinieerd, met kristalheldere labels.
* Voor het wijzigen van de stijl van de gegevens in de vector kaarten hoeven de gegevens niet opnieuw te worden gedownload omdat de nieuwe stijl op de client kan worden toegepast. Als u daarentegen de stijl van een raster tegel laag wijzigt, moet meestal het laden van tegels van de server worden geladen en vervolgens de nieuwe stijl wordt toegepast.
* Omdat de gegevens in een vector vorm worden geleverd, is er minder server-side verwerking vereist om de gegevens voor te bereiden. Als gevolg hiervan kunnen de nieuwere gegevens sneller beschikbaar worden gemaakt.

Azure Maps voldoet aan de [tegel specificatie Mapbox vector](https://github.com/mapbox/vector-tile-spec), een open standaard. Azure Maps biedt de volgende vector tegel Services als onderdeel van het platform:

- Details van [](/rest/api/maps/renderv2/getmaptilepreview)de  |  [gegevens indeling](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) voor wegtegels in de documentatie
- Details van [documentatie](/rest/api/maps/traffic/gettrafficincidenttile)voor verkeers incidenten  |  [gegevens indeling](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Details van [](/rest/api/maps/traffic/gettrafficflowtile)de  |  [gegevens indeling](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) van de verkeers stroom
- Met Azure Maps Creator (preview) kunnen aangepaste vector tegels worden gemaakt en geopend via de [weer gave tegel ophalen v2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Wanneer u gebruikmaakt van vector-of raster afbeeldings tegels van de Azure Maps render-service met de Web-SDK, kunt u vervangen door `atlas.microsoft.com` de tijdelijke aanduiding `{azMapsDomain}` . Deze tijdelijke aanduiding wordt vervangen door het domein dat wordt gebruikt door de kaart en zal ook automatisch dezelfde verificatie gegevens toevoegen. Dit vereenvoudigt de verificatie met de render-service aanzienlijk wanneer u Azure Active Directory-verificatie gebruikt.

Als u gegevens uit een vector tegel bron op de kaart wilt weer geven, verbindt u de bron met een van de gegevens weergave lagen. Alle lagen die gebruikmaken van een vector bron moeten een `sourceLayer` waarde in de opties opgeven. Met de volgende code wordt de Azure Maps traffic flow vector-tegel service als een vector tegel bron geladen en vervolgens weer gegeven op een kaart met behulp van een laag. Deze vector tegel bron heeft één set gegevens in de bron laag met de naam ' verkeers stroom '. De regel gegevens in deze gegevensset hebben een eigenschap `traffic_level` met de naam die wordt gebruikt in deze code om de kleur te selecteren en de grootte van regels te schalen.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijn-laag voor vector tegel" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de laag van de pen <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>Vector tegel lijn</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Een gegevens bron verbinden met een laag

De gegevens worden weer gegeven op de kaart met behulp van lagen renderen. Naar één gegevens bron kan worden verwezen door een of meer rendering-lagen. Voor de volgende weergave lagen is een gegevens bron vereist:

* [Bubble Layer](map-add-bubble-layer.md) : geeft punt gegevens weer als geschaalde cirkels op de kaart.
* [Symbol-laag](map-add-pin.md) : geeft punt gegevens weer als pictogrammen of tekst.
* [Heatmap voor hitte](map-add-heat-map-layer.md) : geeft punt gegevens weer als een hitte heatmap.
* [Line Layer](map-add-shape.md) : een lijn weer geven en het overzicht van veelhoeken weer geven. 
* [Veelhoek-laag](map-add-shape.md) : Hiermee wordt het gebied van een veelhoek gevuld met een effen kleur of een patroon voor een afbeelding.

De volgende code laat zien hoe u een gegevens bron maakt, hoe u deze toevoegt aan de kaart en hoe u deze verbindt met een Bubble Layer. En importeer vervolgens geojson Point-gegevens vanaf een externe locatie naar de gegevens bron. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Er zijn extra rendering lagen die geen verbinding maken met deze gegevens bronnen, maar ze kunnen de gegevens rechtstreeks laden voor rendering. 

* [Afbeelding slaag](map-add-image-layer.md) : bedekking één afbeelding boven op de kaart en koppelt de hoeken aan een set opgegeven coördinaten.
* [Tegel laag](map-add-tile-layer.md) : er wordt een raster tegel laag boven op de kaart opgelegd.

## <a name="one-data-source-with-multiple-layers"></a>Eén gegevens bron met meerdere lagen

Meerdere lagen kunnen worden verbonden met één gegevens bron. Er zijn veel verschillende scenario's waarin deze optie handig is. Denk bijvoorbeeld na over het scenario waarin een gebruiker een veelhoek tekent. Het veelhoek gebied moet worden weer gegeven en gevuld wanneer de gebruiker punten toevoegt aan de kaart. Als u een lijn met stijl hebt toegevoegd om het overzicht van de veelhoek te maken, kunt u de randen van de veelhoek gemakkelijker zien, omdat de gebruiker tekent. Voor een gemakkelijke bewerking van een afzonderlijke positie in de veelhoek, kunnen we boven elke positie een koppeling toevoegen, zoals een pincode of een markering.

![Toewijzing met meerdere lagen die gegevens uit een enkele gegevens bron weer geven](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

In de meeste toewijzings platforms hebt u een veelhoek object, een lijn object en een pincode voor elke positie in de veelhoek nodig. Als de veelhoek is gewijzigd, moet u de regel en pincodes hand matig bijwerken. Dit kan snel complexer worden.

Met Azure Maps hoeft u alleen maar één veelhoek in een gegevens bron te hebben, zoals in de onderstaande code wordt weer gegeven.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> Wanneer u met behulp van de functie lagen aan de kaart toevoegt `map.layers.add` , kan de id of het exemplaar van een bestaande laag worden door gegeven als een tweede para meter. Hiermee wordt aangegeven dat de toewijzing van de nieuwe laag onder de bestaande laag moet worden ingevoegd. Naast het door geven van een laag-ID, ondersteunt deze methode ook de volgende waarden.
>
> * `"labels"` -Hiermee wordt de nieuwe laag onder de kaart label lagen ingevoegd.
> * `"transit"` -Voegt de nieuwe laag onder de lagen kaart en transit toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Bron](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een heatmap toevoegen](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)