---
title: Aan de slag met Azure Maps Android SDK
description: Vertrouwd raken met de Microsoft Azure Mapss Android SDK. Zie een project maken in Android Studio, de SDK installeren en een interactieve kaart maken.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531250"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Aan de slag met Azure Maps Android SDK

De Azure Maps Android SDK is een vector map-Bibliotheek voor Android. In dit artikel vindt u instructies voor het installeren van de Azure Maps Android SDK en het laden van een kaart.

## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.
3. [Down load en installeer de Android Studio van Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Een project maken in Android Studio

Voer de volgende stappen uit om een Android Studio project te maken:

1. Start Android Studio.
2. Klik op **+ Nieuw project maken**.
3. Klik op het tabblad **telefoon en Tablet** op **lege activiteit**. Klik op **Volgende**.
4. Onder **uw project configureren** selecteert u `API 21: Android 5.0.0 (Lollipop)` als minimale SDK.
5. Selecteer `Java` als de taal.
6. Accepteer de standaard waarde `Name` voor het project. Klik op **Voltooien**.

Raadpleeg de [Android Studio-documentatie](https://developer.android.com/studio/intro/) voor meer informatie over het installeren van Android Studio en het maken van een nieuw project.

![Een project maken in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Een apparaat instellen

Als u de toepassing tijdens de ontwikkeling wilt testen, kunt u een Android-telefoon of een Android-Emulator gebruiken.

Zie de [Android Studio-documentatie](https://developer.android.com/studio/run/managing-avds)voor meer informatie over het instellen van een AVD (virtueel Android-apparaat).

## <a name="install-the-azure-maps-android-sdk"></a>De Azure Maps Android SDK installeren

De volgende stap bij het bouwen van uw toepassing is het installeren van de Azure Maps Android SDK.

Voer de volgende stappen uit om de SDK te installeren:

1. Vouw op het tabblad project **Gradle-scripts** uit. Open **Build. gradle (project: My_Application)** en voeg de volgende code toe aan de sectie **alle projecten** `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Open **Build. gradle (module: My_Application)**.

3. Zorg ervoor dat **minSdkVersion** in de `defaultConfig` sectie op API 21 of hoger is.

4. Voeg de volgende code toe aan de Android-sectie:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Voeg de volgende code toe aan de `dependencies` sectie:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Klik op het menu **bestand** op de hoofdwerk balk en selecteer vervolgens **project synchroniseren met Gradle-bestanden**.

7. Open `res > layout > activity_main.xml`. Klik `Code` in de rechter bovenhoek op weer gave. Voeg de volgende XML-code toe aan het `<androidx.constraintlayout.widget.ConstraintLayout>` element.
    
    ```XML
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

8. In het `java > com.example.myapplication > MainActivity.java` bestand moet u het volgende doen:

    * Voeg import bewerkingen toe voor de Azure Maps SDK.
    * Stel uw Azure Maps-verificatie-informatie in.
    * Haal het exemplaar van het kaart besturings element op in de methode **onCreate** .

    Om te voor komen dat u verificatie gegevens voor elke toepassings weergave hoeft toe te voegen, worden verificatie gegevens globaal ingesteld door te roepen `AzureMaps.setSubscriptionKey` . U kunt ook bellen `AzureMaps.setAadProperties` Als u wilt verifiëren met behulp van Azure Active Directory.

    Het kaart besturings element overschrijft de volgende levenscyclus methoden van de MainActivity-klasse. Deze methoden zijn verantwoordelijk voor het beheren van de OpenGL-levens duur van Android.

    * onCreate (bundel)
    * onstart ()
    * onResume()
    * onPause ()
    * onStop ()
    * onDestroy()
    * onSaveInstanceState (bundel)
    * onLowMemory()

    Bewerk het `MainActivity.java` bestand als volgt:

    ```java
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
        }
    }
    ```

>[!WARNING]
>Android Studio zijn mogelijk de vereiste klassen niet geïmporteerd.  Als gevolg hiervan bevat de code enkele onherleidbare-verwijzingen. Als u de vereiste klassen wilt importeren, houdt u de muis aanwijzer boven elke onopgeloste verwijzing en drukt u op `Alt + Enter` (Option + Return op een Mac).

Het duurt een paar seconden Android Studio om de toepassing te bouwen. Nadat de build is voltooid, kunt u de toepassing testen in het geëmuleerde Android-apparaat. U ziet een kaart zoals deze als volgt:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps in Android-toepassing":::

## <a name="localizing-the-map"></a>De kaart lokaliseren

De Azure Maps Android SDK biedt drie verschillende manieren om de taal-en land instellingen van de kaart in te stellen.

1. Taal-en land instellingen instellen door statische methoden aan te roepen voor de klasse AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Definieer de taal-en land instellingen in het kaart besturings element XML.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Taal-en land instellingen instellen door methoden op het kaart besturings element aan te roepen. Met deze optie kunt u de instellingen tijdens runtime wijzigen.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Hier volgt een voor beeld van Azure Maps waarvan de taal is ingesteld op `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, kaart afbeelding met labels in het Frans":::

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="navigating-the-map"></a>Navigeren in de kaart

Er zijn verschillende manieren waarop de kaart kan worden ingezoomd, panned, gedraaid en in hoogte kan worden gesteld. Hieronder vindt u meer informatie over de verschillende manieren waarop u kunt navigeren door de kaart.

**De kaart zoomen**

- Raak de kaart met twee vingers en knijp samen om uit te zoomen of de vingers op elkaar uit te breiden om in te zoomen.
- Dubbeltik op de kaart om op één niveau te zoomen.
- Dubbeltik met twee vingers om uit te zoomen op de kaart één niveau.
- Twee keer tikken; op de tweede Tik houdt u uw vinger op de kaart op en sleept u omhoog om in te zoomen of omlaag om uit te zoomen.

**Kaart pannen**

- Raak de kaart op en sleep deze naar een wille keurige richting.

**De kaart draaien**

- Raak de kaart met twee vingers en roteer.

**De kaart verkopen**

- Raak de kaart met twee vingers en sleep deze naar boven of beneden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van overlay-gegevens op de kaart:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen aan een Android-kaart](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Vormen toevoegen aan een Android-kaart](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Kaartstijlen wijzigen in Android-kaarten](./set-android-map-styles.md)