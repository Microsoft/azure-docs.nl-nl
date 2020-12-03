---
title: Een symbool laag aan een kaart toevoegen met Azure Maps Android SDK
description: Meer informatie over het toevoegen van een markering aan een kaart. Bekijk een voor beeld waarin de Android SDK van Microsoft Azure Maps wordt gebruikt om een Symbol-laag toe te voegen die op punten gebaseerde gegevens uit een gegevens bron bevat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/24/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 300a7968b2072459d6d7709e4d89388e1bcf59f3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531204"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Een symbool laag aan een kaart toevoegen met Azure Maps Android SDK

Dit artikel laat u zien hoe u punt gegevens van een gegevens bron kunt weer geven als een symbool laag op een kaart met behulp van de Azure Maps Android SDK.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
3. Down load en installeer de [Azure Maps ANDROID SDK](./how-to-use-android-map-control-library.md).

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Volg de onderstaande stappen om een markering op de kaart toe te voegen met behulp van de Symbol-laag:

1. **res**  >  **layout**  >  U kunt de indeling van de res bewerken **activity_main.xml** zodat deze eruitziet als de volgende XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Kopieer het volgende code fragment in de methode **onCreate ()** van uw `MainActivity.java` klasse.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a red custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Nadat u het code fragment hierboven hebt toegevoegd, `MainActivity.java` ziet uw er als volgt uit:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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

Wanneer u de toepassing uitvoert, ziet u een markering op de kaart, zoals hier wordt weer gegeven:

![Pincode van Android-kaart](./media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Standaard optimaliseert symbool lagen de rendering van symbolen door symbolen te verbergen die elkaar overlappen. Wanneer u inzoomt, worden de verborgen symbolen zichtbaar. Als u deze functie wilt uitschakelen en alle symbolen op elk moment wilt weer geven, stelt `iconAllowOverlap` u de optie in op `true` .

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het toevoegen van meer gegevens aan uw kaart:

> [!div class="nextstepaction"]
> [Vormen toevoegen aan een Android-kaart](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)