---
title: Functie-informatie weer geven in Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het weer geven van gegevens wanneer gebruikers met kaart functies werken. Gebruik de Azure Maps Android-SDK om pop-upberichten en andere typen berichten weer te geven.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680013"
---
# <a name="display-feature-information"></a>Functie-informatie weergeven

Ruimtelijke gegevens worden vaak weer gegeven met behulp van punten, lijnen en veelhoeken. Aan deze gegevens zijn vaak meta gegevens gekoppeld. Een punt kan bijvoorbeeld de locatie van een restaurant vertegenwoordigen en meta gegevens over dat restaurant kunnen de naam, het adres en het type levens middelen zijn. Deze meta gegevens kunnen worden toegevoegd als eigenschappen van een geojson `Feature` . Met de volgende code wordt een eenvoudige punt functie gemaakt met een `title` eigenschap die de waarde ' Hallo wereld! ' bevat.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Zie de documentatie [een gegevens bron maken](create-data-source-android-sdk.md) voor manieren om gegevens te maken en toe te voegen aan de kaart.

Wanneer een gebruiker met een functie op de kaart communiceert, kunnen gebeurtenissen worden gebruikt om te reageren op deze acties. Een veelvoorkomend scenario is het weer geven van een bericht dat is gemaakt van de meta gegevens eigenschappen van een functie waarmee de gebruiker heeft gecommuniceerd. De `OnFeatureClick` gebeurtenis is de belangrijkste gebeurtenis die wordt gebruikt om te detecteren wanneer de gebruiker een functie op de kaart heeft getikt. Er is ook een `OnLongFeatureClick` gebeurtenis. Wanneer de `OnFeatureClick` gebeurtenis aan de kaart wordt toegevoegd, kan deze worden beperkt tot één laag door de id van een laag door te geven om deze te beperken tot. Als er geen laag-ID wordt door gegeven, tikt u op een functie op de kaart, ongeacht in welke laag deze zich bevindt, wordt deze gebeurtenis geactiveerd. Met de volgende code wordt een symbool laag gemaakt om punt gegevens op de kaart weer te geven, waarna een gebeurtenis wordt toegevoegd `OnFeatureClick` en beperkt tot deze laag.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Een pop-upbericht weer geven

Een pop-upbericht is een van de eenvoudigste manieren om informatie weer te geven voor de gebruiker en is beschikbaar in alle versies van Android. Het biedt geen ondersteuning voor elk type gebruikers invoer en wordt alleen gedurende korte tijd weer gegeven. Als u de gebruiker snel op de hoogte wilt stellen van wat ze hebben getikt, is het mogelijk dat een pop-upbericht een goede optie is. De volgende code laat zien hoe een pop-upbericht kan worden gebruikt met de `OnFeatureClick` gebeurtenis.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animatie van een functie die wordt getikt en een pop-upbericht wordt weer gegeven](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Naast pop-upberichten zijn er nog vele andere manieren om de meta gegevens eigenschappen van een functie weer te geven, zoals:

- [Snackbar-widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` Geef licht gewicht feedback over een bewerking. Er wordt een kort bericht aan de onderkant van het scherm weer gegeven, op mobiele apparaten en linksonder op grotere apparatuur. `Snackbars` worden weer gegeven boven alle andere elementen op het scherm en er kan maar één tegelijk worden weer gegeven.
- [Dialoog vensters](https://developer.android.com/guide/topics/ui/dialogs) : een dialoog venster is een klein venster waarin de gebruiker wordt gevraagd een beslissing te nemen of aanvullende informatie in te voeren. Een dialoog venster vult het scherm niet en wordt normaal gesp roken gebruikt voor modale gebeurtenissen waarvoor gebruikers een actie moeten ondernemen voordat ze kunnen door gaan.
- Een [fragment](https://developer.android.com/guide/components/fragments) toevoegen aan de huidige activiteit.
- Navigeer naar een andere activiteit of weer gave.

## <a name="next-steps"></a>Volgende stappen

Meer gegevens toevoegen aan uw kaart:

> [!div class="nextstepaction"]
> [Reageren op toewijzings gebeurtenissen](android-map-events.md)

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
