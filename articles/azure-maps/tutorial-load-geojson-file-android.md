---
title: 'Zelfstudie: GeoJSON-gegevens laden in Azure Maps Android SDK | Microsoft Azure Maps'
description: Zelfstudie over het laden van een GeoJSON-gegevensbestand in de Azure Maps Android-kaart-SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681235"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Zelfstudie: GeoJSON-gegevens laden in Azure Maps Android SDK

In deze zelfstudie wordt u begeleid bij het importeren van een GeoJSON-bestand met locatiegegevens in de Azure Maps Android SDK. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Maps toevoegen aan een Android-toepassing.
> * Een gegevensbron maken en laden in een GeoJSON-bestand vanuit een lokaal bestand of vanaf internet.
> * De gegevens op de kaart weergeven.

## <a name="prerequisites"></a>Vereisten

1. Voltooi de [Quickstart: een Android-app maken](quick-android-map.md). In deze zelfstudie wordt de code uitgebreid die in die quickstart wordt gebruikt.
2. Download het bestand [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json).

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON-gegevens importeren vanaf internet of uit een map met assets

In de meeste GeoJSON-bestanden zijn alle gegevens verpakt in een `FeatureCollection`. Als de GeoJSON-bestanden als een tekenreeks in de toepassing worden geladen, kunnen ze worden doorgegeven aan de statische `fromJson`-methode van de functieverzameling. Hiermee wordt de tekenreeks gedeserialiseerd in een GeoJSON `FeatureCollection`-object dat aan de kaart kan worden toegevoegd.

De volgende stappen laten zien hoe u een GeoJSON-bestand in de toepassing kunt importeren en vervolgens deserialiseren als een GeoJSON `FeatureCollection`-object.

1. Voltooi de [Quickstart: Maak een Android-app](quick-android-map.md) omdat de volgende stappen boven op deze toepassing worden gebouwd.
2. Klik in het projectdeelvenster van Android Studio met de rechtermuisknop op de map **app** en ga naar `New > Folder > Assets Folder`.
3. Sleep het GeoJSON-bestand [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) naar de map met assets.
4. Maak een nieuw bestand met de naam **Utils.java** en voeg de volgende code aan het bestand toe. Deze code biedt een statische methode met de naam `importData`. Hiermee wordt een bestand asynchroon geïmporteerd vanuit de map `assets` van de toepassing of vanaf internet met behulp van een URL als tekenreeks. Vervolgens wordt het naar de gebruikersinterface geretourneerd met een eenvoudige callback-methode.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
    import android.content.Context;
    import android.os.Handler;
    import android.os.Looper;
    import android.webkit.URLUtil;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.net.URL;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    import javax.net.ssl.HttpsURLConnection;
    
    public class Utils {
    
        interface SimpleCallback {
            void notify(String result);
        }
    
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
            importData(urlOrFileName, context, callback, null);
        }
        
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         * @param error A callback function to return errors to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
            if(urlOrFileName != null && callback != null) {
                ExecutorService executor = Executors.newSingleThreadExecutor();
                Handler handler = new Handler(Looper.getMainLooper());
    
                executor.execute(() -> {
                    String data = null;
    
                    try {
    
                        if(URLUtil.isNetworkUrl(urlOrFileName)){
                            data = importFromWeb(urlOrFileName);
                        } else {
                            //Assume file is in assets folder.
                            data = importFromAssets(context, urlOrFileName);
                        }
    
                        final String result = data;
    
                        handler.post(() -> {
                            //Ensure the resulting data string is not null or empty.
                            if (result != null && !result.isEmpty()) {
                                callback.notify(result);
                            } else {
                                error.notify("No data imported.");
                            }
                        });
                    } catch(Exception e) {
                        if(error != null){
                            error.notify(e.getMessage());
                        }
                    }
                });
            }
        }
    
        /**
         * Imports data from an assets file as a string.
         * @param context The context of the app.
         * @param fileName The asset file name.
         * @return
         * @throws IOException
         */
        private static String importFromAssets(Context context, String fileName) throws IOException {
            InputStream stream = null;
    
            try {
                stream = context.getAssets().open(fileName);
    
                if(stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
            }
    
            return null;
        }
    
        /**
         * Imports data from the web as a string.
         * @param url URL to the data.
         * @return
         * @throws IOException
         */
        private static String importFromWeb(String url) throws IOException {
            InputStream stream = null;
            HttpsURLConnection connection = null;
            String result = null;
    
            try {
                connection = (HttpsURLConnection) new URL(url).openConnection();
    
                //For this use case, set HTTP method to GET.
                connection.setRequestMethod("GET");
    
                //Open communications link (network traffic occurs here).
                connection.connect();
    
                int responseCode = connection.getResponseCode();
                if (responseCode != HttpsURLConnection.HTTP_OK) {
                    throw new IOException("HTTP error code: " + responseCode);
                }
    
                //Retrieve the response body as an InputStream.
                stream = connection.getInputStream();
    
                if (stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
                if (connection != null) {
                    connection.disconnect();
                }
            }
    
            return result;
        }
    
        /**
         * Reads an input stream as a string.
         * @param stream Stream to convert.
         * @return
         * @throws IOException
         */
        private static String readStreamAsString(InputStream stream) throws IOException {
            //Convert the contents of an InputStream to a String.
            BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));
    
            String inputLine;
            StringBuffer response = new StringBuffer();
    
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
    
            in.close();
    
            return response.toString();
        }
    }
    ```

5. Ga naar het bestand **MainActivity.java** en voeg de volgende code toe in de callback voor de gebeurtenis `mapControl.onReady`. Deze bevindt zich in de methode `onCreate`. Deze code maakt gebruik van het importeerhulpprogramma om het bestand **SamplePoiDataSet.json** als tekenreeks in te lezen. Vervolgens wordt het gedeserialiseerd als een functieverzameling met behulp van de statische `fromJson`-methode van de klasse `FeatureCollection`. Met deze code wordt ook het gebied van het begrenzingsvak voor alle gegevens in de functieverzameling berekend en wordt dit gebruikt om de camera van de kaart zo in te stellen dat deze zich op de gegevens richt.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Dankzij de code om de GeoJSON-gegevens in de kaart te laden met behulp van een gegevensbron, moeten we opgeven hoe die gegevens op de kaart moeten worden weergegeven. Er zijn verschillende weergavelagen voor puntgegevens, waarvan de [bellenlaag](map-add-bubble-layer-android.md), de [symboollaag](how-to-add-symbol-to-android-map.md) en de [heatmap-laag](map-add-heat-map-layer-android.md) de meestgebruikte lagen zijn. Voeg na de code voor het importeren van de gegevens de volgende code toe om de gegevens in een bellenlaag weer te geven in de callback voor de `mapControl.onReady`-gebeurtenis.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. Voer de toepassing uit. Er wordt een kaart weergegeven die op de Verenigde Staten is gericht, met cirkels boven elke locatie in het GeoJSON-bestand.

    ![Kaart van de Verenigde Staten met gegevens uit een GeoJSON-bestand](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende stappen uit om de resources uit deze zelfstudie op te schonen:

1. Sluit Android Studio en verwijder de toepassing die u hebt gemaakt.
2. Als u de toepassing op een extern apparaat hebt getest, verwijdert u de toepassing van dat apparaat.

## <a name="next-steps"></a>Volgende stappen

Voor meer voorbeelden van code en interactieve codering:

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
