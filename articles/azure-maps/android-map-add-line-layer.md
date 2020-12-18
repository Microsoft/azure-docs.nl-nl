---
title: Een line-laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van lijnen aan Maps. Zie voor beelden die gebruikmaken van de Azure Maps Android-SDK om lijn lagen toe te voegen aan Maps en om lijnen met symbolen en kleur overgangen aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681584"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Een laagmap toevoegen aan de kaart (Android SDK)

Een line-laag kan worden gebruikt om te renderen `LineString` en te `MultiLineString` functies als paden of routes op de kaart. Een line-laag kan ook worden gebruikt om het overzicht van `Polygon` en functies weer te geven `MultiPolygon` . Een gegevens bron is verbonden met een laag om deze te voorzien van gegevens die moeten worden weer gegeven.

> [!TIP]
> Met lijn lagen worden standaard de coördinaten van veelhoeken en lijnen in een gegevens bron weer gegeven. Als u de laag zo wilt beperken dat alleen Lines Tring Geometry-functies worden weer gegeven, stelt `filter` u de optie van de laag in op `eq(geometryType(), "LineString")` . Als u ook multi line string-functies wilt toevoegen, stelt u de `filter` optie van de laag in op `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="add-a-line-layer"></a>Een lijnlaag toevoegen

De volgende code laat zien hoe u een regel kunt maken. Voeg de lijn toe aan een gegevens bron en geef deze vervolgens weer met behulp van de klasse in een laag `LineLayer` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Op de volgende scherm afbeelding ziet u de bovenstaande code waarmee een regel in een laag wordt weer gegeven.

![Toewijzen aan een regel die wordt weer gegeven met een regel-laag](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Lijn stijl van gegevens station

Met de volgende code worden twee regel functies gemaakt en wordt een snelheids grens waarde als eigenschap aan elke regel toegevoegd. Een line-laag maakt gebruik van een expressie voor de stijl van een gegevens station de regels op basis van de snelheids limiet. Omdat de lijn gegevens langs de weg volgen, wordt met de code hieronder de laag onder de label laag toegevoegd zodat de labels van de weg nog steeds duidelijk kunnen worden gelezen.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

De volgende scherm afbeelding toont de bovenstaande code om twee regels in een laag weer te geven met hun kleur die wordt opgehaald uit een op Data gebaseerde stijl expressie op basis van een eigenschap in de lijn functies.

![Kaart met gegevens stations met stijl lijnen die worden weer gegeven in een lijn-laag](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>Symbolen toevoegen langs een regel

In dit voor beeld ziet u hoe u pijl pictogrammen kunt toevoegen langs een regel op de kaart. Wanneer u een symbool-laag gebruikt, stelt `symbolPlacement` u de optie in op `SymbolPlacement.LINE` . Met deze optie worden de symbolen op de regel weer gegeven en de pictogrammen geroteerd (0 graden = rechts).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

Voor dit voor beeld is de volgende afbeelding geladen in de map drawable van de app.

| ![Afbeelding van het pictogram met paarse pijl](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

In de onderstaande scherm afbeelding ziet u de bovenstaande code waarmee een lijn met pijl pictogrammen wordt weer gegeven.

![Toewijzen aan gegevens stations met stijl lijnen met pijlen die worden weer gegeven in een lijn-laag](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
