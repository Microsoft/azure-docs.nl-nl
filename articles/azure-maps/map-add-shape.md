---
title: Een polygoon laag toevoegen aan een kaart | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van veelhoeken of cirkels aan kaarten. Bekijk hoe u de Azure Maps Web-SDK kunt gebruiken om geometrische shapes aan te passen en deze gemakkelijk bij te werken en te onderhouden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890994"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Een polygoon laag toevoegen aan de kaart

In dit artikel leest u hoe u de gebieden van `Polygon` en `MultiPolygon` functie geometrie op de kaart kunt weer geven met behulp van een polygoon laag. De websdk van Azure Maps biedt ook ondersteuning voor het maken van cirkel geometrie zoals gedefinieerd in het [uitgebreide GEOjson-schema](extend-geojson.md#circle). Deze cirkels worden omgezet in veelhoeken wanneer ze op de kaart worden weer gegeven. Alle functie-geometrieën kunnen eenvoudig worden bijgewerkt wanneer deze met de [Atlas wordt verpakt. ](/javascript/api/azure-maps-control/atlas.shape) Klasse van vorm.

## <a name="use-a-polygon-layer"></a>Een polygoon laag gebruiken 

Wanneer een polygoon laag is verbonden met een gegevens bron en op de kaart is geladen, wordt het gebied met en-functies weer gegeven `Polygon` `MultiPolygon` . Als u een veelhoek wilt maken, voegt u deze toe aan een gegevens bron en rendert u deze met een polygoon laag met behulp van de [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) -klasse.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Hieronder ziet u het volledige en actieve voor beeld van de bovenstaande code.

<br/>

<iframe height='500' scrolling='no' title='Een veelhoek aan een kaart toevoegen ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>een veelhoek aan een kaart toevoegen </a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Een veelhoek en een lijn-laag samen gebruiken

Een line-laag wordt gebruikt om het overzicht van veelhoeken weer te geven. In het volgende code voorbeeld wordt een veelhoek weer gegeven zoals in het vorige voor beeld, maar wordt nu een line-laag toegevoegd. Deze laag is een tweede laag die is verbonden met de gegevens bron.  

<br/>

<iframe height='500' scrolling='no' title='Veelhoek en lijn-laag om veelhoek toe te voegen' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>veelhoek en de lijn-laag om veelhoek toe te voegen</a> met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Een veelhoek met een patroon vullen

Naast het vullen van een veelhoek met een kleur, kunt u een afbeeldings patroon gebruiken om de veelhoek op te vullen. Laad een afbeeldings patroon in de Maps-afbeelding sprite-resources en verwijs vervolgens naar deze installatie kopie met de `fillPattern` eigenschap van de polygoon laag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opvul patroon veelhoek" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>opvul patroon</a> voor de pen-veelhoek door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> De Azure Maps Web-SDK biedt verschillende aanpas bare afbeeldings sjablonen die u als opvul patroon kunt gebruiken. Zie het document [Image-sjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="customize-a-polygon-layer"></a>Een polygoon laag aanpassen

De polygoon laag heeft slechts enkele opmaak opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Een cirkel toevoegen aan de kaart

Azure Maps gebruikt een uitgebreide versie van het geojson-schema dat een definitie voor cirkels bevat, zoals [hier](extend-geojson.md#circle)wordt vermeld. Een cirkel wordt weer gegeven op de kaart door een functie te maken `Point` . Dit `Point` heeft een `subType` eigenschap met de waarde `"Circle"` en een `radius` eigenschap met een getal dat de RADIUS in meters vertegenwoordigt. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Met de Azure Maps Web-SDK worden deze `Point` functies geconverteerd naar `Polygon` functies. Vervolgens worden deze functies op de kaart weer gegeven met behulp van veelhoek-en lijn lagen, zoals in het volgende code voorbeeld wordt weer gegeven.

<br/>

<iframe height='500' scrolling='no' title='Een cirkel aan een kaart toevoegen' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>een cirkel aan een kaart toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Een geometrie gemakkelijk te updaten maken

Een `Shape` klasse verpakt een [geometrie](/javascript/api/azure-maps-control/atlas.data.geometry) of [functie](/javascript/api/azure-maps-control/atlas.data.feature) en maakt het eenvoudig om deze functies bij te werken en te onderhouden. Als u een vorm variabele wilt instantiëren, geeft u een geometrie of een set eigenschappen door aan de vorm-constructor.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

In het volgende code voorbeeld ziet u hoe u een geojson-object in cirkel met een shape-klasse kunt inpakken. Als de waarde van de RADIUS verandert in de vorm, wordt de cirkel automatisch weer gegeven op de kaart.

<br/>

<iframe height='500' scrolling='no' title='Eigenschappen van shape bijwerken' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de eigenschappen van de <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>shape pen bijwerken</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Polygoon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw Maps:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

Aanvullende bronnen:

> [!div class="nextstepaction"]
> [Uitbrei ding voor geojson-specificatie Azure Maps](extend-geojson.md#circle)