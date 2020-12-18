---
title: Een kaart stijl instellen in Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over twee manieren om de stijl van een kaart in te stellen. Zie How to use the Azure Maps Android SDK in het indelings bestand of de klasse activity om de stijl aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1cce355c8ffbcd4704bd32b0e4d1739c77c2b623
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678462"
---
# <a name="set-map-style-android-sdk"></a>Kaart stijl instellen (Android SDK)

In dit artikel ziet u twee manieren om kaart stijlen in te stellen met behulp van de Azure Maps Android SDK. Azure Maps heeft zes verschillende kaarten stijlen waaruit u kunt kiezen. Zie [ondersteunde kaart stijlen in azure Maps](supported-map-styles.md)voor meer informatie over ondersteunde kaart stijlen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken.

## <a name="set-map-style-in-the-layout"></a>Kaart stijl instellen in de lay-out

U kunt een kaart stijl instellen in het indelings bestand voor uw activiteiten klasse bij het toevoegen van het kaart besturings element. Met de volgende code worden de middelste locatie, het zoom niveau en de kaart stijl ingesteld.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

Op de volgende scherm afbeelding ziet u de bovenstaande code met de stijl voor de donkere grijs waarden.

![Stijl van donkere weg kaarten voor kaart met grijs waarden](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Kaart stijl instellen in code

De kaart stijl kan in code worden ingesteld met behulp `setStyle` van de methode van de kaart. Met de volgende code wordt de middelste locatie en het zoom niveau ingesteld met behulp van de kaarten `setCamera` methode en de kaart stijl op `SATELLITE_ROAD_LABELS` .

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

Op de volgende scherm afbeelding ziet u de bovenstaande code die een kaart met de stijl satelliet weglabels weergeeft.

![Kaart met stijl voor uitvoerige labels van satelliet](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>De kaart camera instellen

De kaart camera bepaalt welk deel van de kaart wordt weer gegeven in de kaart. De camera kan worden geprogrammeerd in de indeling van code. Wanneer u deze in code instelt, zijn er twee hoofd methoden voor het instellen van de positie van de kaart. centreren en zoomen gebruiken of in een selectie kader door geven. De volgende code laat zien hoe u alle optionele camera opties kunt instellen wanneer u `center` en gebruikt `zoom` .

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Vaak is het wenselijk de kaart te richten op een reeks gegevens. Een selectie kader kan worden berekend op basis van functies met behulp van de- `MapMath.fromData` methode en kan worden door gegeven aan de `bounds` optie van de kaart camera. Bij het instellen van een kaart weergave op basis van een selectie kader is het vaak handig om een `padding` waarde op te geven voor de pixel grootte van punten die worden weer gegeven als bellen of symbolen. De volgende code laat zien hoe u alle optionele camera opties instelt wanneer u een selectie kader gebruikt om de positie van de camera in te stellen.

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Houd er rekening mee dat de hoogte-breedte verhouding van een selectie vakje mogelijk niet gelijk is aan de hoogte-breedte verhouding van de kaart, omdat de kaart vaak het volledige begrenzingsvak van het vak weergeeft, maar meestal alleen nauw keurig verticaal of horizon taal is.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)
