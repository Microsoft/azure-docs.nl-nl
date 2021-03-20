---
title: Een polygoon laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van veelhoeken of cirkels aan kaarten. Lees hoe u de Azure Maps Android-SDK gebruikt om geometrische shapes aan te passen en deze eenvoudig bij te werken en te onderhouden.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 68d68424e71bcf60bf504ae174b84b9c361b8637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097258"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Een polygoon laag toevoegen aan de kaart (Android SDK)

In dit artikel leest u hoe u de gebieden van `Polygon` en `MultiPolygon` functie geometrie op de kaart kunt weer geven met behulp van een polygoon laag.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="use-a-polygon-layer"></a>Een polygoon laag gebruiken

Wanneer een polygoon laag is verbonden met een gegevens bron en op de kaart is geladen, wordt het gebied met en-functies weer gegeven `Polygon` `MultiPolygon` . Als u een veelhoek wilt maken, voegt u deze toe aan een gegevens bron en rendert u deze met een polygoon laag met behulp van de- `PolygonLayer` klasse.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

De volgende scherm afbeelding toont de bovenstaande code waarmee het gebied van een veelhoek wordt weer gegeven met behulp van een polygoon laag.

![Veelhoek met het opgevulde gebied weer gegeven](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Een veelhoek en een lijn-laag samen gebruiken

Een line-laag wordt gebruikt om het overzicht van veelhoeken weer te geven. In het volgende code voorbeeld wordt een veelhoek weer gegeven zoals in het vorige voor beeld, maar wordt nu een line-laag toegevoegd. Deze laag is een tweede laag die is verbonden met de gegevens bron.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

In de volgende scherm afbeelding ziet u de bovenstaande code waarmee een veelhoek wordt weer gegeven met een contour met een lijn laag.

![Veelhoek met het opvullings gebied en de weer gegeven contour](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Als u een veelhoek met een laag wilt belichten, sluit u alle ringen in veelhoeken zodanig dat elke matrix met punten hetzelfde begin-en eind punt heeft. Als dat niet het geval is, wordt het laatste punt van de veelhoek niet met het eerste punt verbonden met de laag.

## <a name="fill-a-polygon-with-a-pattern"></a>Een veelhoek met een patroon vullen

Naast het vullen van een veelhoek met een kleur, kunt u een afbeeldings patroon gebruiken om de veelhoek op te vullen. Laad een afbeeldings patroon in de Maps-afbeelding sprite-resources en verwijs vervolgens naar deze installatie kopie met de `fillPattern` optie van de polygoon laag.

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

Voor dit voor beeld is de volgende afbeelding geladen in de map drawable van de app.

| ![Afbeelding van het pictogram met paarse pijl](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Hier volgt een scherm opname van de bovenstaande code waarmee een veelhoek met een opvul patroon op de kaart wordt weer gegeven.

![Veelhoek met een opvul patroon dat wordt weer gegeven op de kaart](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een laag met een polygooneffect toevoegen](map-extruded-polygon-android.md)
