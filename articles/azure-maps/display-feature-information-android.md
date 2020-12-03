---
title: Functie-informatie weer geven in de Azure Maps Android-SDK | Microsoft Azure kaarten
description: Meer informatie over het weer geven van gegevens wanneer gebruikers met kaart functies werken. Gebruik de Azure Maps Android-SDK om pop-upberichten en andere typen berichten weer te geven.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: fabb4cd1e555a7a67a53bf2f5a99d93c87df436c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532802"
---
# <a name="display-feature-information"></a>Functie-informatie weergeven

Ruimtelijke gegevens worden vaak weer gegeven met behulp van punten, lijnen en veelhoeken. Aan deze gegevens zijn vaak meta gegevens gekoppeld. Een punt kan bijvoorbeeld de locatie van een Store vertegenwoordigen en meta gegevens over dat restaurant kunnen de naam, het adres en het type levens middelen zijn. Deze meta gegevens kunnen worden toegevoegd als eigenschappen van deze functies met behulp van een `JsonObject` . Met de volgende code wordt een eenvoudige punt functie gemaakt met een `title` eigenschap die de waarde ' Hallo wereld! ' bevat.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Wanneer een gebruiker met een functie op de kaart communiceert, kunnen gebeurtenissen worden gebruikt om te reageren op deze acties. Een veelvoorkomend scenario is het weer geven van een bericht dat is gemaakt van de meta gegevens eigenschappen van een functie waarmee de gebruiker heeft gecommuniceerd. De `OnFeatureClick` gebeurtenis is de belangrijkste gebeurtenis die wordt gebruikt om te detecteren wanneer de gebruiker een functie op de kaart heeft getikt. Er is ook een `OnLongFeatureClick` gebeurtenis. Wanneer de `OnFeatureClick` gebeurtenis aan de kaart wordt toegevoegd, kan deze worden beperkt tot één laag door de id van een laag door te geven om deze te beperken tot. Als er geen laag-ID wordt door gegeven, tikt u op een functie op de kaart, ongeacht in welke laag deze zich bevindt, wordt deze gebeurtenis geactiveerd. Met de volgende code wordt een symbool laag gemaakt om punt gegevens op de kaart weer te geven, waarna een gebeurtenis wordt toegevoegd `OnFeatureClick` en beperkt tot deze laag.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
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
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

![Animatie van een functie die wordt getikt en een pop-upbericht wordt weer gegeven](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Naast pop-upberichten zijn er nog vele andere manieren om de meta gegevens eigenschappen van een functie weer te geven, zoals:

- [Snakbar](https://developer.android.com/training/snackbar/showing.html) -snackbars bieden licht gewicht feedback over een bewerking. Er wordt een kort bericht aan de onderkant van het scherm weer gegeven, op mobiele apparaten en linksonder op grotere apparatuur. Snackbars worden boven alle andere elementen op het scherm weer gegeven, maar er kan slechts één tegelijk worden weer gegeven.
- [Dialoog vensters](https://developer.android.com/guide/topics/ui/dialogs) : een dialoog venster is een klein venster waarin de gebruiker wordt gevraagd een beslissing te nemen of aanvullende informatie in te voeren. Een dialoog venster vult het scherm niet en wordt normaal gesp roken gebruikt voor modale gebeurtenissen waarvoor gebruikers een actie moeten ondernemen voordat ze kunnen door gaan.
- Een [fragment](https://developer.android.com/guide/components/fragments) toevoegen aan de huidige activiteit.
- Navigeer naar een andere activiteit of weer gave.

## <a name="next-steps"></a>Volgende stappen

Meer gegevens toevoegen aan uw kaart:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Vormen toevoegen aan een Android-kaart](how-to-add-shapes-to-android-map.md)
