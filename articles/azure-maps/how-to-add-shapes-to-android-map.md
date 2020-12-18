---
title: Een polygoon laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van veelhoeken of cirkels aan kaarten. Lees hoe u de Azure Maps Android-SDK gebruikt om geometrische shapes aan te passen en deze eenvoudig bij te werken en te onderhouden.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679483"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Een polygoon laag toevoegen aan de kaart (Android SDK)

In dit artikel leest u hoe u de gebieden van `Polygon` en `MultiPolygon` functie geometrie op de kaart kunt weer geven met behulp van een polygoon laag.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="use-a-polygon-layer"></a>Een polygoon laag gebruiken

Wanneer een polygoon laag is verbonden met een gegevens bron en op de kaart is geladen, wordt het gebied met en-functies weer gegeven `Polygon` `MultiPolygon` . Als u een veelhoek wilt maken, voegt u deze toe aan een gegevens bron en rendert u deze met een polygoon laag met behulp van de- `PolygonLayer` klasse.

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

De volgende scherm afbeelding toont de bovenstaande code waarmee het gebied van een veelhoek wordt weer gegeven met behulp van een polygoon laag.

![Veelhoek met het opgevulde gebied weer gegeven](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Een veelhoek en een lijn-laag samen gebruiken

Een line-laag wordt gebruikt om het overzicht van veelhoeken weer te geven. In het volgende code voorbeeld wordt een veelhoek weer gegeven zoals in het vorige voor beeld, maar wordt nu een line-laag toegevoegd. Deze laag is een tweede laag die is verbonden met de gegevens bron.  

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

In de volgende scherm afbeelding ziet u de bovenstaande code waarmee een veelhoek wordt weer gegeven met een contour met een lijn laag.

![Veelhoek met het opvullings gebied en de weer gegeven contour](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Als u een veelhoek met een laag wilt belichten, sluit u alle ringen in veelhoeken zodanig dat elke matrix met punten hetzelfde begin-en eind punt heeft. Als dat niet het geval is, wordt het laatste punt van de veelhoek niet met het eerste punt verbonden met de laag.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)
