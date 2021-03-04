---
title: Een laag met een polygoon effect toevoegen aan een Android-kaart | Microsoft Azure kaarten
description: Een Layer extrusie-laag toevoegen aan de Microsoft Azure Mapss Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054635"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Een laag met een polygoon effect toevoegen aan de kaart (Android SDK)

In dit artikel leest u hoe u de laag voor het 3D-effect kunt gebruiken om gebieden en functie-geometrie weer te geven `Polygon` `MultiPolygon` als geëxtrudeerde vormen.

## <a name="use-a-polygon-extrusion-layer"></a>Een laag met een polygoon effect gebruiken

Verbind de laag met de polygoon extrusie met een gegevens bron. Vervolgens wordt deze op de kaart geladen. De laag met het polygoon reliëf geeft de gebieden van een `Polygon` en- `MultiPolygon` functies weer als geëxtrudeerde vormen. De `height` `base` Eigenschappen en van de Layer extrusie-laag definiëren de basis afstand van de grond en hoogte van de geëxtrudeerde vorm in **meters**. De volgende code laat zien hoe u een veelhoek maakt, voegt deze toe aan een gegevens bron en geeft deze weer met behulp van de klasse van de polygoon laag.

> [!Note]
> De `base` waarde die is gedefinieerd in de Layer extrusie laag moet kleiner zijn dan of gelijk zijn aan die van de `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

In de volgende scherm afbeelding ziet u de bovenstaande code rending een veelhoek die verticaal is uitgerekt met behulp van een Layer extrusie-laag.

![Verticaal gerekte kaarten met veelhoeken met een veelhoek extrusie-laag](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Gegevensgestuurde veelhoeken toevoegen

Een choropleth-kaart kan worden gerenderd met behulp van de Layer extrusie-laag. Stel de `height` `fillColor` Eigenschappen en van de laag extrusie in op de meting van de statistische variabele in `Polygon` de `MultiPolygon` functies en geometrie. Het volgende code voorbeeld toont een geëxtrudeerde choropleth toewijzing van de Verenigde Staten op basis van de meting van de populatie densiteit per status.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

In de volgende scherm afbeelding ziet u een choropleth-kaart van de Amerikaanse Staten die verticaal zijn gekleurd en uitgerekt als geëxtrudeerde veelhoeken op basis van de populatie dichtheid.

![Een choropleth-kaart van Amerikaanse provincies, gekleurd en uitgerekt verticaal als geëxtrudeerde veelhoeken op basis van de populatie dichtheid](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
