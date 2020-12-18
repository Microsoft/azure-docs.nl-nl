---
title: Een symbool laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van een markering aan een kaart. Bekijk een voor beeld waarin de Azure Maps Android SDK wordt gebruikt om een Symbol-laag toe te voegen die op punten gebaseerde gegevens uit een gegevens bron bevat.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679352"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Een Symbol-laag toevoegen (Android SDK)

Dit artikel laat u zien hoe u punt gegevens van een gegevens bron kunt weer geven als een symbool laag op een kaart met behulp van de Azure Maps Android SDK. Symbool lagen geven punten weer als een afbeelding en tekst op de kaart.

> [!TIP]
> Met symbool lagen worden standaard de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag wilt beperken zodat deze alleen functies van punt geometrie weergeeft, stelt `filter` u de optie van de laag in op `eq(geometryType(), "Point")` . Als u ook multi point-functies wilt toevoegen, stelt u de `filter` optie van de laag in op `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Voordat u een Symbol-laag aan de kaart kunt toevoegen, moet u een aantal stappen uitvoeren. Maak eerst een gegevens bron en voeg deze toe aan de kaart. Maak een symbool laag. Geef vervolgens de gegevens bron door aan de symbool laag om de gegevens op te halen uit de gegevens bron. Voeg ten slotte gegevens toe aan de gegevens bron, zodat er iets wordt weer gegeven.

De volgende code laat zien wat er aan de kaart moet worden toegevoegd nadat deze is geladen. In dit voor beeld wordt één punt op de kaart weer gegeven met behulp van een symbool laag.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Er zijn drie verschillende typen punt gegevens die aan de kaart kunnen worden toegevoegd:

- Geometrie van geojson-punt: dit object bevat alleen een coördinaat van een punt en niets anders. De `Point.fromLngLat` statische methode kan worden gebruikt om deze objecten eenvoudig te maken.
- Geojson multi point Geometry: dit object bevat de coördinaten van meerdere punten en niets anders. Geef een matrix met punten door `MultiPoint` aan de klasse om deze objecten te maken.
- Geojson-functie: dit object bestaat uit een geojson-geometrie en een set eigenschappen die meta gegevens bevatten die aan de geometrie zijn gekoppeld.

Zie het document [een gegevens bron maken](create-data-source-android-sdk.md) voor het maken en toevoegen van gegevens aan de kaart voor meer informatie.

In het volgende code voorbeeld wordt een geometrie voor een geojson-punt gemaakt en door gegeven aan de geojson-functie en een `title` waarde toegevoegd aan de eigenschappen. De `title` eigenschap wordt weer gegeven als tekst boven het symbool pictogram op de kaart.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

De volgende scherm afbeelding toont de bovenstaande code rending een punt functie met behulp van een pictogram en een tekst label met een symbool laag.

![Kaart met een punt dat wordt weer gegeven met een Symbol-laag met een pictogram en een tekst label voor een punt functie](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Standaard optimaliseert symbool lagen de rendering van symbolen door symbolen te verbergen die elkaar overlappen. Wanneer u inzoomt, worden de verborgen symbolen zichtbaar. Als u deze functie wilt uitschakelen en alle symbolen altijd wilt weer geven, `iconAllowOverlap` stelt `textAllowOverlap` u de opties en in op `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Een aangepast pictogram toevoegen aan een symbool-laag

Symbool lagen worden gerenderd met behulp van WebGL. Alle resources, zoals pictogram afbeeldingen, moeten worden geladen in de WebGL-context. In dit voor beeld ziet u hoe u een aangepast pictogram aan de kaart resources toevoegt. Dit pictogram wordt vervolgens gebruikt voor het weer geven van punt gegevens met een aangepast symbool op de kaart. `textField`Voor de eigenschap van de Symbol-laag moet een expressie worden opgegeven. In dit geval willen we de eigenschap Tempe ratuur weer geven. Omdat de Tempe ratuur een getal is, moet dit worden geconverteerd naar een teken reeks. Daarnaast willen we ' °F ' eraan toevoegen. Een expressie kan worden gebruikt om deze samen voeging uit te voeren. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

Voor dit voor beeld is de volgende afbeelding geladen in de map drawable van de app.

| ![Pictogram afbeelding van regen Showers](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

De volgende scherm afbeelding toont de bovenstaande code rending een punt functie met behulp van een aangepast pictogram en een opgemaakt tekst label met een symbool laag.

![Kaart met een punt dat wordt weer gegeven met behulp van een Symbol-laag met een aangepast pictogram en opgemaakt tekst label voor een punt functie](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Als u alleen tekst wilt weer geven met een symbool-laag, kunt u het pictogram verbergen door de `iconImage` eigenschap van de pictogram opties in te stellen op `"none"` .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)
