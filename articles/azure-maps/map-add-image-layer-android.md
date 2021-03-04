---
title: Een afbeelding slaag toevoegen aan een Android-kaart | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van installatie kopieën aan een kaart. Lees hoe u de Azure Maps Android SDK gebruikt om afbeeldings lagen en overlay-afbeeldingen op vaste sets coördinaten aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054557"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Een afbeelding slaag toevoegen aan een kaart (Android SDK)

Dit artikel laat u zien hoe u een afbeelding kunt bedekken naar een vaste set coördinaten. Hier volgen enkele voor beelden van verschillende afbeeldings typen die kunnen worden overlapt met kaarten:

* Afbeeldingen die zijn vastgelegd vanuit Drones
* Floorplans bouwen
* Historische of andere gespecialiseerde kaart afbeeldingen
* Blauw drukken van taak sites

> [!TIP]
> Een afbeelding slaag is een eenvoudige manier om een afbeelding op een kaart te bedekken. Grote installatie kopieën kunnen veel geheugen gebruiken en prestatie problemen veroorzaken. In dit geval kunt u overwegen om uw afbeelding in tegels te verdelen en ze in de kaart te laden als een laag voor tegels.

## <a name="add-an-image-layer"></a>Een afbeeldingslaag toevoegen

De volgende code bedekken een afbeelding van een [kaart van Newark, New Jersey, van 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) op de kaart. Deze afbeelding wordt toegevoegd aan de `drawable` map van het project. Er wordt een afbeelding slaag gemaakt door de afbeelding en coördinaten in te stellen voor de vier hoeken in de notatie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Het is vaak wenselijk om afbeeldings lagen onder de laag toe te voegen `label` .

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

U kunt ook een URL naar een afbeelding die wordt gehost op online opgeven. Als uw scenario echter toestaat, voegt u de afbeelding toe aan de `drawable` map projecten, waardoor deze sneller wordt geladen omdat de installatie kopie lokaal beschikbaar is en niet hoeft te worden gedownload.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

In de volgende scherm afbeelding ziet u een overzicht van Newark, New Jersey, van 1922 overlay met behulp van een afbeelding slaag.

![Kaart van Newark, New Jersey, van 1922 overlay met behulp van een afbeelding slaag](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Een KML-bestand importeren als een wegdek bedekking

In dit voor beeld wordt gedemonstreerd hoe u KML-oppervlak bedekkings gegevens kunt toevoegen als een afbeelding slaag op de kaart. KML-bedekkingen bieden Noord-, Zuid-, Oost-en West-coördinaten en een rotatie linksom. Maar de afbeelding slaag verwacht coördinaten voor elke hoek van de afbeelding. De KML-bedekking in dit voor beeld is voor de Chartres-Cathedral en is bron van [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

De code maakt gebruik van de statische `getCoordinatesFromEdges` methode van de- `ImageLayer` klasse. Met deze methode worden de vier hoeken van de afbeelding berekend met behulp van de Noord-, Zuid-, Oost-, West-en rotatie gegevens van de KML-vlak bedekking.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

In de volgende scherm afbeelding ziet u een kaart met een KML-wegdek dekking die wordt weer gegeven met behulp van een afbeelding slaag.

![Kaarten met een KML-wegdek bedekking met behulp van een afbeelding slaag](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Gebruik de `getPixels` `getPositions` methoden en van de klasse afbeelding slaag om te converteren tussen geografische coördinaten van de gepositioneerde afbeelding slaag en de pixel coördinaten van de lokale afbeelding.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende artikel voor meer informatie over de manieren waarop u beelden kunt bedekken op een kaart.

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](how-to-add-tile-layer-android-map.md)