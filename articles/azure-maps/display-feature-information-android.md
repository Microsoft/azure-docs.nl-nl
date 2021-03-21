---
title: Functie-informatie weer geven in Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het weer geven van gegevens wanneer gebruikers met kaart functies werken. Gebruik de Azure Maps Android-SDK om pop-upberichten en andere typen berichten weer te geven.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b9926d5d6a70d959c0baacd9602341bb69abe924
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097241"
---
# <a name="display-feature-information"></a>Functie-informatie weergeven

Ruimtelijke gegevens worden vaak weer gegeven met behulp van punten, lijnen en veelhoeken. Aan deze gegevens zijn vaak meta gegevens gekoppeld. Een punt kan bijvoorbeeld de locatie van een restaurant vertegenwoordigen en meta gegevens over dat restaurant kunnen de naam, het adres en het type levens middelen zijn. Deze meta gegevens kunnen worden toegevoegd als eigenschappen van een geojson `Feature` . Met de volgende code wordt een eenvoudige punt functie gemaakt met een `title` eigenschap die de waarde ' Hallo wereld! ' bevat.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Zie de documentatie [een gegevens bron maken](create-data-source-android-sdk.md) voor manieren om gegevens te maken en toe te voegen aan de kaart.

Wanneer een gebruiker met een functie op de kaart communiceert, kunnen gebeurtenissen worden gebruikt om te reageren op deze acties. Een veelvoorkomend scenario is het weer geven van een bericht dat is gemaakt van de meta gegevens eigenschappen van een functie waarmee de gebruiker heeft gecommuniceerd. De `OnFeatureClick` gebeurtenis is de belangrijkste gebeurtenis die wordt gebruikt om te detecteren wanneer de gebruiker een functie op de kaart heeft getikt. Er is ook een `OnLongFeatureClick` gebeurtenis. Wanneer de `OnFeatureClick` gebeurtenis aan de kaart wordt toegevoegd, kan deze worden beperkt tot één laag door de id van een laag door te geven om deze te beperken tot. Als er geen laag-ID wordt door gegeven, tikt u op een functie op de kaart, ongeacht in welke laag deze zich bevindt, wordt deze gebeurtenis geactiveerd. Met de volgende code wordt een symbool laag gemaakt om punt gegevens op de kaart weer te geven, waarna een gebeurtenis wordt toegevoegd `OnFeatureClick` en beperkt tot deze laag.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Een pop-upbericht weer geven

Een pop-upbericht is een van de eenvoudigste manieren om informatie weer te geven voor de gebruiker en is beschikbaar in alle versies van Android. Het biedt geen ondersteuning voor elk type gebruikers invoer en wordt alleen gedurende korte tijd weer gegeven. Als u de gebruiker snel op de hoogte wilt stellen van wat ze hebben getikt, is het mogelijk dat een pop-upbericht een goede optie is. De volgende code laat zien hoe een pop-upbericht kan worden gebruikt met de `OnFeatureClick` gebeurtenis.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animatie van een functie die wordt getikt en een pop-upbericht wordt weer gegeven](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Naast pop-upberichten zijn er nog vele andere manieren om de meta gegevens eigenschappen van een functie weer te geven, zoals:

- [Snackbar-widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` Geef licht gewicht feedback over een bewerking. Er wordt een kort bericht aan de onderkant van het scherm weer gegeven, op mobiele apparaten en linksonder op grotere apparatuur. `Snackbars` worden weer gegeven boven alle andere elementen op het scherm en er kan maar één tegelijk worden weer gegeven.
- [Dialoog vensters](https://developer.android.com/guide/topics/ui/dialogs) : een dialoog venster is een klein venster waarin de gebruiker wordt gevraagd een beslissing te nemen of aanvullende informatie in te voeren. Een dialoog venster vult het scherm niet en wordt normaal gesp roken gebruikt voor modale gebeurtenissen waarvoor gebruikers een actie moeten ondernemen voordat ze kunnen door gaan.
- Een [fragment](https://developer.android.com/guide/components/fragments) toevoegen aan de huidige activiteit.
- Navigeer naar een andere activiteit of weer gave.

## <a name="display-a-popup"></a>Een pop-upvenster weer geven

De Azure Maps Android SDK biedt een `Popup` klasse waarmee u eenvoudig ui-aantekeningen elementen kunt maken die zijn gekoppeld aan een positie op de kaart. Voor pop-ups moet u in een weer gave met een relatieve indeling door geven in de `content` optie van de pop-up. Hier volgt een eenvoudige indelings voorbeeld waarin donkere tekst boven op een achtergrond wordt weer gegeven.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Ervan uitgaande dat de bovenstaande indeling wordt opgeslagen in een bestand met de naam `popup_text.xml` in de `res -> layout` map van een app, wordt met de volgende code een pop-upvenster gemaakt en toegevoegd aan de kaart. Wanneer er op een functie wordt geklikt, `title` wordt de eigenschap weer gegeven met behulp van de `popup_text.xml` lay-out, met het onderste midden van de lay-out, verankerd op de opgegeven positie op de kaart.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()
})
```

::: zone-end

In de volgende scherm opname ziet u pop-ups die worden weer gegeven wanneer onderdelen worden geklikt en naar de opgegeven locatie op de kaart worden geankerd wanneer deze worden verplaatst.

![Animatie van een pop-upvenster dat wordt weer gegeven en de kaart is verplaatst met het pop-upvenster naar een positie op de kaart](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Volgende stappen

Meer gegevens toevoegen aan uw kaart:

> [!div class="nextstepaction"]
> [Reageren op toewijzings gebeurtenissen](android-map-events.md)

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
