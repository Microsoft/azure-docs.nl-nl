---
title: Verkeers gegevens weer geven op Android-kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u verkeers gegevens op een kaart kunt weer geven met behulp van de Microsoft Azure Maps Android SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/25/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5f7e67d159c2b7dea3ebac7fd4d0856f508cb298
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532751"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Verkeers gegevens op de kaart weer geven met behulp van Azure Maps Android SDK

Gegevens over stroom gegevens en incidenten zijn de twee typen verkeers gegevens die op de kaart kunnen worden weer gegeven. In deze hand leiding wordt beschreven hoe u beide typen verkeers gegevens kunt weer geven. Gegevens over incidenten bestaan uit punt-en lijn gegevens voor dingen, zoals constructies, wegsluitingen en ongel ukken. In stroom gegevens worden statistieken over de stroom van verkeer onderweg weer gegeven.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
3. Down load en installeer de [Azure Maps ANDROID SDK](./how-to-use-android-map-control-library.md).

## <a name="incidents-traffic-data"></a>Verkeers gegevens van incidenten

U moet de volgende bibliotheken importeren om te kunnen aanroepen `setTraffic` en `incidents` :

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Het volgende code fragment laat zien hoe u verkeers gegevens op de kaart kunt weer geven. We geven een Booleaanse waarde door aan de `incidents` methode en geven deze door aan de- `setTraffic` methode. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Verkeer gegevens stroom

Eerst moet u de volgende bibliotheken importeren om te kunnen aanroepen `setTraffic` en `flow` :

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Gebruik het volgende code fragment om gegevens over de verkeers stroom in te stellen. Net als bij de code in de vorige sectie geven we de retour waarde van de `flow` methode door aan de- `setTraffic` methode. Er zijn vier waarden die kunnen worden door gegeven aan `flow` , en elke waarde wordt geactiveerd `flow` om de desbetreffende waarde te retour neren. De geretourneerde waarde van `flow` wordt vervolgens door gegeven als het argument aan `setTraffic` . Zie de onderstaande tabel voor deze vier waarden:

|Stroom waarde | Beschrijving|
| :-- | :-- |
| Verkeers stroom. NONE | Verkeers gegevens worden niet weer gegeven op de kaart |
| Verkeers stroom. RELATIVE | Geeft verkeers gegevens weer die relatief zijn ten opzichte van de vrije stroom snelheid van de weg |
| TrafficFlow.RELATIVE_DELAY | Hiermee worden gebieden weer gegeven die langzamer zijn dan de gemiddelde verwachte vertraging |
| Verkeers stroom. ABSOLUTE | Toont de absolute snelheid van alle Voer tuigen op de weg |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Gegevens van incident verkeer weer geven door te klikken op een functie

U kunt de volgende code gebruiken om de incidenten voor een specifieke functie te verkrijgen. Wanneer er op een functie wordt geklikt, controleert de code logica op incidenten en wordt er een bericht over het incident gegenereerd. Aan de onderkant van het scherm wordt een bericht met de details weer gegeven.

1. Eerst moet u bewerken `res > layout > activity_main.xml` zodat deze er als volgt uitziet. U kunt de `mapcontrol_centerLat` , en door de `mapcontrol_centerLng` `mapcontrol_zoom` gewenste waarden vervangen. Intrekken, het zoom niveau is een waarde tussen 0 en 22. Op Zoom niveau 0 past de hele wereld op één tegel.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Voeg de volgende code toe aan het bestand **MainActivity. java** . Het pakket is standaard opgenomen, dus zorg ervoor dat u het pakket bovenaan bewaart.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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
       }
   }
   ```

3. Zodra u de bovenstaande code in uw toepassing hebt opgenomen, kunt u op een functie klikken en de details van de verkeers incidenten bekijken. Afhankelijk van de waarden voor de breedte graad, lengte graad en zoom niveau die u in uw **activity_main.xml** -bestand hebt gebruikt, worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende afbeelding:


    ![Incident-verkeer-op de kaart](./media/how-to-show-traffic-android/android-traffic.png)


## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende hand leidingen voor meer informatie over het toevoegen van meer gegevens aan uw kaart:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Shapes toevoegen aan Android-kaart](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)