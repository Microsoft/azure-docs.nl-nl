---
title: Een tegel laag aan een kaart toevoegen met behulp van de Azure Maps Android SDK
description: Meer informatie over het toevoegen van een tegel laag aan een kaart. Bekijk een voor beeld waarin de Android-SDK van Microsoft Azure Maps wordt gebruikt om een weers radar-overlay toe te voegen aan een kaart.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 22618a28f1a87e68c19467aedf639e96ec2fb91e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532673"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Een tegel laag aan een kaart toevoegen met behulp van de Azure Maps Android SDK

In dit artikel wordt beschreven hoe u een tegel laag op een kaart kunt weer geven met behulp van de Azure Maps Android SDK. Met tegel lagen kunt u afbeeldingen boven op Azure Maps basis kaart tegels plaatsen. Meer informatie over Azure Maps tegel systeem vindt u in de documentatie over het [Zoom niveau en het tegel raster](zoom-levels-and-tile-grid.md) .

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven en opgeslagen, zoals elke andere installatie kopie op een server, met behulp van een naamgevings Conventie die de laag van de tegel begrijpt. Het is ook mogelijk dat deze installatie kopieën worden weer gegeven met een dynamische service die de afbeeldingen bijna in real time genereert. Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door Azure Maps klasse TileLayer:

* X, Y, zoom notatie: gebaseerd op het zoom niveau, x is de kolom en Y de rijpositie van de tegel in het tegel raster.
* Quadkey notatie: combi natie x, y en zoom informatie in een enkele teken reeks waarde die een unieke id voor een tegel is.
* Omsluitende Box-coördinaten kunnen worden gebruikt om een afbeelding op te geven in de indeling `{west},{south},{east},{north}` die wordt gebruikt door [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Een TileLayer is een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag. Door vector gegevens als een tegel laag te renderen, hoeft het kaart besturings element alleen de tegels te laden die veel kleiner kunnen zijn dan de vector gegevens die ze vertegenwoordigen. Deze techniek wordt gebruikt door veel die miljoenen rijen met gegevens op de kaart moeten weer geven.

De tegel-URL die wordt door gegeven aan een tegel laag moet een HTTP/HTTPS-URL zijn naar een TileJSON-resource of een tegel-URL-sjabloon die gebruikmaakt van de volgende para meters: 

* `{x}` -X-positie van de tegel. Ook nodig `{y}` en `{z}` .
* `{y}` -Y-positie van de tegel. Ook nodig `{x}` en `{z}` .
* `{z}` -Zoom niveau van de tegel. Ook nodig `{x}` en `{y}` .
* `{quadkey}` -Tegel quadkey-id gebaseerd op de naam Conventie voor Bing Maps-tegel systeem.
* `{bbox-epsg-3857}` -Een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het EPSG 3857 Spatial Reference System.
* `{subdomain}` -Een tijdelijke aanduiding voor de waarden van het subdomein als de subdomeinwaarde is opgegeven.

## <a name="prerequisites"></a>Vereisten

Om het proces in dit artikel te volt ooien, moet u [Azure Maps ANDROID SDK](./how-to-use-android-map-control-library.md) installeren om een kaart te laden.


## <a name="add-a-tile-layer-to-the-map"></a>Een tegel laag aan de kaart toevoegen

 Dit voor beeld laat zien hoe u een tegel laag maakt die verwijst naar een set tegels. Deze tegels gebruiken het tegel systeem x, y, zoomen. De bron van deze tegel laag is een weers radar-overlay van de [Iowa Environment Mesonet van de Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

U kunt een tegel laag toevoegen aan de kaart door de volgende stappen uit te voeren.

1. Wijzig de **indeling van res > > activity_main.xml** zodat deze er als volgt uitziet:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Kopieer het volgende code fragment hieronder in de methode **onCreate ()** van uw `MainActivity.java` klasse.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    In het bovenstaande code fragment wordt eerst een exemplaar van Azure Maps kaart besturings element opgehaald met de call back methode **onReady ()** . Vervolgens wordt een- `TileLayer` object gemaakt en wordt een geopmaakde **xyz** -tegel-URL in de optie door gegeven `tileUrl` . De dekking van de laag is ingesteld op `0.8` en sinds de tegels van de tegel service die wordt gebruikt, zijn 256 pixel tegels, wordt deze informatie door gegeven aan de `tileSize` optie. De laag van de tegel wordt vervolgens door gegeven aan de Maps Layer Manager.

    Nadat u het code fragment hierboven hebt toegevoegd, `MainActivity.java` ziet uw er als volgt uit:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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
        }    
    }
    ```

Als u de toepassing nu uitvoert, ziet u een regel op de kaart zoals hieronder wordt weer gegeven:

<center>

![Android-kaart lijn](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende artikel voor meer informatie over manieren om kaart stijlen in te stellen

> [!div class="nextstepaction"]
> [Kaartstijlen wijzigen in Android-kaarten](./set-android-map-styles.md)