---
title: 'Zelfstudie: Een Android-app migreren | Microsoft Azure Maps'
description: Zelfstudie over hoe u een Android-app kunt migreren van Google Maps naar Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7f3d32a782c653b3be8b3d6f6714bc9065a73518
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042892"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>Zelfstudie: Een Android-app migreren vanuit Google Maps

De Azure Maps Android SDK bevat een API-interface die vergelijkbaar is met de Web SDK. Als u met een van deze SDK's hebt ontwikkeld, zijn veel van dezelfde concepten, best practices en architecturen van toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een kaart laden
> * Een kaar lokaliseren
> * Markeringen polylijnen en polygonen toevoegen.
> * Een tegellaag als overlay gebruiken
> * Verkeergegevens weergeven

De Azure Maps Android SDK ondersteunt een minimale Android-versie van API 21: Android 5.0.0 (Lollipop).

Alle voorbeelden zijn in Java. U kunt echter Kotlin gebruiken bij de Azure Maps Android SDK.

Voor meer informatie over ontwikkelen met de Android SDK van Azure Maps raadpleegt u de [Instructiegidsen voor de Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Vereisten

1. Maak een Azure Maps-account door u aan te melden bij de [Azure-portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="load-a-map"></a>Een kaart laden

Het laden van een kaart in een Android-app met behulp van Google of Azure Maps bestaat uit vergelijkbare stappen. Wanneer u een van deze SDK's gebruikt, moet u het volgende doen:

* Haal een API of abonnementssleutel op voor toegang tot een van beide platforms.
* Voeg enige XML toe aan een activiteit om op te geven waar de kaart moet worden weergegeven en hoe deze moet worden ingedeeld.
* Overschrijf alle levenscyclusmethoden van de activiteit die de kaartweergave bevat met de overeenkomstige methoden in de kaartklasse. U moet vooral de volgende methoden onderschrijven:
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* Wacht totdat de kaart gereed is voordat u deze opent en programmeert.

### <a name="before-google-maps"></a>Voor: Google Maps

Als u een kaart wilt weergeven met behulp van de Google Maps SDK voor Android, kunt u de volgende stappen uitvoeren:

1. Zorg ervoor dat Google Play Services is geïnstalleerd.
2. Voeg een afhankelijkheid voor de Google Maps-service toe aan het bestand **gradle.build** van de module:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Voeg een Google Maps API-sleutel toe in de toepassingssectie van het bestand **google\_maps\_api.xml**:

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Voeg een kaartfragment toe aan de hoofdactiviteit:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. In het bestand **MainActivity.java** moet u de Google Maps SDK importeren. Stuur alle levenscyclusmethoden van de activiteit die de kaartweergave bevat door naar de overeenkomstige methoden in de kaartklasse. Haal een `MapView`-instantie op uit het kaartfragment met behulp van de methode `getMapAsync(OnMapReadyCallback)`. Met `MapView` worden het kaartsysteem en de weergave geïnitialiseerd. Bewerk het bestand **MainActivity.java** als volgt:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Wanneer u een toepassing uitvoert, wordt het kaartbesturingselement geladen zoals in de volgende afbeelding.

![Eenvoudige Google Maps-weergave](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

Als u een kaart wilt weergeven met behulp van de Azure Maps SDK voor Android, moet u de volgende stappen uitvoeren:

1. Open het bestand **build.gradle** op het hoogste niveau en voeg de volgende code toe aan de bloksectie **alle projecten**:

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Werk uw **app/build.gradle** bij en voeg de volgende code eraan toe:

    1. Zorg ervoor dat de **minSdkVersion** van uw project API 21 of hoger is.

    2. Voeg de volgende code toe aan de Android-sectie:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Werk uw afhankelijkhedenblok bij. Voeg een nieuwe implementatieafhankelijkheidsregel toe voor de nieuwste Azure Maps Android SDK:

        ```Java
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > U kunt het versienummer wijzigen in 0+ zodat uw code altijd naar de nieuwste versie verwijst.

    4. Ga op de werkbalk naar **Bestand** en klik vervolgens op **Project met Gradle-bestanden synchroniseren**.

3. Voeg een kaartfragment toe aan de hoofdactiviteit (resources pwd\> indeling \> activity\_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. In het bestand **MainActivity.java** moet u het volgende doen:

    * De Azure Maps SDK importeren
    * Uw Azure Maps-verificatiegegevens instellen
    * Het exemplaar van het kaartbesturingselement ophalen in de methode **onCreate**

     Stel de verificatiegegevens in de klasse `AzureMaps` in met behulp van de methoden `setSubscriptionKey` of `setAadProperties`. Met deze algemene update zorgt u ervoor dat uw verificatiegegevens worden toegevoegd aan elke weergave.

    Het kaartbesturingselement bevat eigen levenscyclusmethoden voor het beheren van de OpenGL-levenscyclus van Android. Deze methoden moeten rechtstreeks vanuit de opgenomen activiteit worden aangeroepen. U moet de volgende levenscyclusmethoden overschrijven in de activiteit die het kaartbesturingselement bevat om de levenscyclusmethoden van het kaartbesturingselement correct aan te roepen. Roep de desbetreffende kaartbeheermethode aan.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Bewerk het bestand **MainActivity.java** als volgt:

    ```Java
    package com.example.myapplication;
    
    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

Als u uw toepassing uitvoert, wordt het kaartbesturingselement geladen zoals in de volgende afbeelding.

![Eenvoudige Azure Maps-weergave](media/migrate-google-maps-android-app/simple-azure-maps.png)

Het Azure Maps-besturingselement ondersteunt verder uitzoomen en biedt een ruimere weergave van de wereld.

> [!TIP]
> Als u een Android-emulator op een Windows-computer gebruikt, wordt de kaart mogelijk niet weergegeven vanwege conflicten met OpenGL en door software versnelde weergave van afbeeldingen. Sommige mensen hebben het probleem als volgt opgelost. Open AVD Manager en selecteer het virtuele apparaat dat u wilt bewerken. Schuif omlaag in het deelvenster **Configuratie verifiëren**. Stel in de sectie **Geëmuleerde prestaties** de optie **Afbeeldingen** in op **Hardware**.

## <a name="localizing-the-map"></a>De kaart lokaliseren

Lokalisatie is belangrijk als uw doelgroep zich in meerdere landen/regio's bevindt of verschillende talen spreekt.

### <a name="before-google-maps"></a>Voor: Google Maps

Voeg de volgende code toe aan de methode `onCreate` om de taal van de kaart in te stellen. De code moet worden toegevoegd voordat u de contextweergave van de kaart instelt. Met de taalcode fr beperkt u de taal tot het Frans.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Hier volgt een voorbeeld van Google Maps waarbij de taal is ingesteld op fr.

![Lokalisatie van Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

Azure Maps biedt drie verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De eerste optie is om informatie over de taal en de regionale weergave door te geven aan de klasse `AzureMaps`. Met deze optie worden de statische methoden `setLanguage` en `setView` algemeen gebruikt. Dit betekent dat de standaardtaal en de regionale weergave worden ingesteld voor alle Azure Maps-besturingselementen die in uw app worden geladen. In dit voorbeeld wordt het Frans ingesteld met behulp van de taalcode fr-FR.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

De tweede optie is om de taal- en weergave-informatie door te geven aan de XML-code voor het kaartbesturingselement.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

De derde optie is om de taal en regionale kaartweergave te programmeren met behulp van de methode `setStyle` van de kaart. Met deze optie worden de taal en de regionale weergave bijgewerkt telkens wanneer de code wordt uitgevoerd.

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

Hier volgt een voorbeeld van Azure Maps waarbij de taal is ingesteld op fr-FR.

![Lokalisatie van Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Bekijk de volledige lijst met [ondersteunde talen](supported-languages.md).

## <a name="setting-the-map-view"></a>De kaartweergave instellen

Dynamische kaarten in zowel Azure Maps als Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties door de juiste methoden aan te roepen. Laten we ervoor zorgen dat er satellietluchtfoto's op de kaart worden weergegeven, dat de kaart wordt gecentreerd op een locatie met coördinaten en dat het zoomniveau wordt gewijzigd. In dit voorbeeld gebruiken we breedtegraad 35,0272, lengtegraad -111,0225 en zoomniveau 15.

### <a name="before-google-maps"></a>Voor: Google Maps

De camera van het Google Maps-kaartbesturingselement kan programmatisch worden verplaatst met behulp van de methode `moveCamera`. Met de methode `moveCamera` kunt u het middelpunt van de kaart en een zoomniveau opgeven. Met de methode `setMapType` wordt het weer te geven kaarttype gewijzigd.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Ingestelde weergave in Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> In Google Maps worden tegels van 256 pixels gebruikt, terwijl in Azure Maps een grotere tegel van 512 pixels wordt gebruikt. Hierdoor zijn er in Azure Maps minder netwerkaanvragen nodig dan in Google Maps om hetzelfde kaartgebied te laden. Als u Azure Maps gebruikt, krijgt u hetzelfde weergavegebied als een kaart in Google Maps door het zoomniveau dat in Google Maps wordt gebruikt met 1 te verlagen.

### <a name="after-azure-maps"></a>Na: Azure Maps

Zoals hierboven vermeld, kunt u hetzelfde weergavegebied in Azure Maps krijgen door het zoomniveau dat in Google Maps wordt gebruikt met 1 te verlagen. In dit geval gebruikt u zoomniveau 14.

De eerste kaartweergave kan worden ingesteld in XML-kenmerken in het kaartbesturingselement.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

De kaartweergave kan worden geprogrammeerd met behulp van de methoden `setCamera` en `setStyle` van de kaart.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Ingestelde weergave in Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Aanvullende bronnen:**

* [Ondersteunde kaartstijlen](supported-map-styles.md)

## <a name="adding-a-marker"></a>Een markering toevoegen

Puntgegevens worden vaak weergegeven met behulp van een afbeelding op de kaart. Deze afbeeldingen worden aangeduid als markeringen, punaises, spelden of symbolen. Met de volgende voorbeelden worden puntgegevens als markeringen op de kaart weergegeven met breedtegraad 51,5 en lengtegraad -0,2.

### <a name="before-google-maps"></a>Voor: Google Maps

In Google Maps worden markeringen toegevoegd met behulp van de methode `addMarker` van de kaart.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Markering in Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

In Azure Maps geeft u puntgegevens op de kaart weer door de gegevens eerst toe te voegen aan een gegevensbron. Vervolgens verbindt u de gegevensbron aan een symboollaag. Met de gegevensbron wordt het beheer van ruimtelijke gegevens in het kaartbesturingselement geoptimaliseerd. Met de symboollaag wordt aangegeven hoe puntgegevens moeten worden weergegeven als afbeelding of tekst.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Markering in Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

Er kunnen aangepaste afbeeldingen worden gebruikt om punten op een kaart weer te geven. Op de kaart in de onderstaande voorbeelden wordt een aangepaste afbeelding gebruikt om een punt op de kaart weer te geven. Het punt bevindt zich op breedtegraad 51,5 en lengtegraad -0,2. Met het anker wordt de positie van de markering verschoven, zodat de punt van het punaisepictogram de juiste positie op de kaart aangeeft.

![afbeelding van een gele punaise](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

In beide voorbeelden wordt de bovenstaande afbeelding toegevoegd aan de drawable-map van de appsresources.

### <a name="before-google-maps"></a>Voor: Google Maps

Met Google Maps kunnen aangepaste afbeeldingen worden gebruikt als markeringen. Laad aangepaste afbeeldingen met behulp van de optie `icon` van de markering. Gebruik de optie `anchor` om de punt van de afbeelding overeen te laten komen met de coördinaat. Het anker verhoudt zich tot de afmetingen van de afbeelding. In dit geval is het anker 0,2 eenheden breed en 1 eenheid hoog.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Aangepaste markering in Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

In de symboollagen van Azure Maps worden aangepaste afbeeldingen ondersteund, maar eerst moet de afbeelding worden geladen in de kaartresources en moet er een unieke id aan worden toegewezen. Vervolgens moet de symboollaag naar deze id verwijzen. Verschuif het symbool naar het juiste punt op de afbeelding met behulp van de optie `iconOffset`. De pictogramverschuiving is in pixels. De verschuiving is standaard relatief ten opzichte van de onderkant van de afbeelding, maar deze verschuivingswaarde kan worden aangepast met de optie `iconAnchor`. In dit voorbeeld wordt de optie `iconAnchor` ingesteld op `"center"`. Er wordt een pictogramverschuiving gebruikt om de afbeelding vijf pixels naar rechts te verplaatsen en 15 pixels omhoog om de punt van de punaiseafbeelding op de juiste plaats te krijgen.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Aangepaste markering in Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Een polylijn toevoegen

Polylijnen worden gebruikt om een lijn of pad op de kaart aan te geven. In de volgende voorbeelden ziet u hoe u een gestippelde polylijn op de kaart maakt.

### <a name="before-google-maps"></a>Voor: Google Maps

In Google Maps geeft u een polylijn weer met behulp van de klasse `PolylineOptions`. Voeg de polylijn toe aan de kaart met behulp van de methode `addPolyline`. Stel de streekkleur in met behulp van de optie `color`. Stel de streekbreedte in met behulp van de optie `width`. Voeg een streepjesmatrix voor de streek toe met behulp van de optie `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Polylijn in Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

In Azure Maps worden polylijnen `LineString`- of `MultiLineString`-objecten genoemd. Voeg deze objecten toe aan een gegevensbron en geef deze weer met behulp van een lijnlaag. Stel de streekbreedte in met behulp van de optie `strokeWidth`. Voeg een streepjesmatrix voor de streek toe met behulp van de optie `strokeDashArray`.

De pixeleenheden van de streekbreedte en de streepjesmatrix in de Azure Maps Web SDK zijn hetzelfde als in de Google Maps-service. In beide worden dezelfde waarden geaccepteerd en dezelfde resultaten geproduceerd.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Polylijn in Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Veelhoeken worden gebruikt om een gebied op de kaart weer te geven. In de volgende voorbeelden ziet u hoe u een veelhoek maakt. Deze veelhoek is een driehoek op basis van de middelste coördinaat van de kaart.

### <a name="before-google-maps"></a>Voor: Google Maps

In Google Maps geeft u een veelhoek weer met behulp van de klasse `PolygonOptions`. Voeg de veelhoek toe aan de kaart met behulp van de methode `addPolygon`. Stel de kleur van de opvulling en van de streken in met behulp van respectievelijk de opties `fillColor` en `strokeColor`. Stel de streekbreedte in met behulp van de optie `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Veelhoek in Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

Voeg in Azure Maps `Polygon`- en `MultiPolygon`-objecten toe aan een gegevensbron en geef deze met behulp van lagen weer op de kaart. Geef het gebied van een veelhoek weer in een polygoonlaag. Geef de omtrek van een veelhoek weer met behulp van een lijnlaag. Stel de kleur en breedte van de streek in met behulp van de opties `strokeColor` en `strokeWidth`.

De pixeleenheden van de streekbreedte en de streepjesmatrix in de Azure Maps Web SDK komen overeen met de corresponderende eenheden in Google Maps. In beide worden dezelfde waarden geaccepteerd en dezelfde resultaten geproduceerd.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

![Veelhoek in Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Een tegellaag boven de kaart weergeven

 Geef tegellagen weer boven laagafbeeldingen die zijn opgesplitst in kleinere afbeeldingstegels. Deze tegellagen volgen het tegelsysteem van de kaart. Deze benadering is een gebruikelijke manier om laagafbeeldingen of grote gegevenssets boven de kaart weer te geven. Tegellagen worden afbeeldingsoverlays genoemd in Google Maps.

In de volgende voorbeelden wordt een weerradartegellaag van Iowa Environment Mesonet van Iowa State University boven de kaart weergegeven. De tegels zijn 256 pixels groot.

### <a name="before-google-maps"></a>Voor: Google Maps

In Google Maps kan een tegellaag boven de kaart worden weergegeven. Gebruik de klasse `TileOverlayOptions`. Voeg de tegellaag toe aan de kaart met behulp van de methode `addTileLauer`. Als u de tegels semitransparant wilt maken, stelt u de optie `transparency` in op 0,2 ofwel 20 procent transparant.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Tegellaag in Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

Een tegellaag kan net zoals elke andere laag aan de kaart worden toegevoegd. Een opgemaakte URL met de tijdelijke aanduidingen `{x}`, `{y}` en `{z}` voor respectievelijk x, y en zoomen dient als toegangsinformatie tot de tegels voor de laag. Daarnaast ondersteunen tegellagen in Azure Maps de tijdelijke aanduidingen `{quadkey}`, `{bbox-epsg-3857}` en `{subdomain}`. Een tegellaag wordt semitransparant door een matheid van 0,8 te gebruiken. Matheid en transparantie lijken op elkaar, maar werken met omgekeerde waarden. U kunt tussen de opties wisselen door de waarde ervan van het getal 1 af te trekken.

> [!TIP]
> In Azure Maps is het handig om lagen onder andere lagen weer te geven, waaronder basiskaartlagen. Daarnaast is het vaak wenselijk om tegellagen onder de kaartlabels weer te geven, zodat de kaartlabels goed leesbaar zijn. Voor de methode `map.layers.add` is een tweede parameter nodig: de id van de laag waar de nieuwe laag onder moet worden geplaatst. Als u een tegellaag onder de kaartlabels wilt plaatsen, kunt u de volgende code gebruiken: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Tegellaag in Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Verkeer weergeven

Zowel Azure Maps als Google Maps bevatten opties voor het weergegeven van verkeersgegevens boven de kaart.

### <a name="before-google-maps"></a>Voor: Google Maps

In Google Maps kunnen verkeersstroomgegevens boven de kaart worden weergegeven door true door te geven aan de methode `setTrafficEnabled` van de kaart.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Verkeer in Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Na: Azure Maps

Azure Maps biedt verschillende opties voor het weergeven van verkeer. Verkeersincidenten, zoals wegafzettingen en ongelukken, kunnen als pictogrammen op de kaart worden weer gegeven. De verkeersstroom en met kleuren gemarkeerde wegen kunnen boven de kaart worden weergegeven. De kleuren kunnen worden gewijzigd om te worden weergegeven op basis van de ingestelde snelheidslimiet, de gebruikelijke verwachte vertraging of de absolute vertraging. Incidentgegevens in Azure Maps worden elke minuut bijgewerkt en stroomgegevens worden elke twee minuten bijgewerkt.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Verkeer in Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources om op te schonen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het migreren van Azure Maps:

> [!div class="nextstepaction"]
> [Een Android-app migreren](migrate-from-google-maps-android-app.md)
