---
title: Een gegevens bron maken voor Android-kaarten | Microsoft Azure kaarten
description: 'Meer informatie over het maken van een gegevens bron voor een kaart. Meer informatie over de gegevens bronnen die de Azure Maps Android SDK gebruikt: geojson-bronnen en vector tegels.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 2f383876963e3e1d310e7d93f7dc99bb58b189d3
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681527"
---
# <a name="create-a-data-source-android-sdk"></a>Een gegevens bron maken (Android SDK)

De Azure Maps Android-SDK slaat gegevens op in gegevens bronnen. Het gebruik van gegevens bronnen optimaliseert de gegevens bewerkingen voor het uitvoeren van query's en rendering. Er zijn momenteel twee soorten gegevens bronnen:

- **GEOjson-bron**: Hiermee worden gegevens van onbewerkte locaties lokaal beheerd in geojson-indeling. Geschikt voor kleine tot middel grote gegevens sets (van honderden duizenden vormen).
- **Vector tegel bron**: laadt gegevens die zijn opgemaakt als vector tegels voor de huidige kaart weergave, op basis van het Maps-systeem. Ideaal voor grote tot enorme gegevens sets (miljoenen of miljarden vormen).

## <a name="geojson-data-source"></a>Geojson-gegevens bron

Azure Maps gebruikt geojson als een van de primaire gegevens modellen. Geojson is een open georuimtelijke standaard methode voor het vertegenwoordigen van georuimtelijke gegevens in JSON-indeling. Geojson-klassen die beschikbaar zijn in de Azure Maps Android SDK om eenvoudig geojson-gegevens te maken en te serialiseren. De geojson-gegevens in de klasse laden en opslaan `DataSource` en deze weer geven met behulp van lagen. De volgende code laat zien hoe geojson-objecten kunnen worden gemaakt in Azure Maps.

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

U kunt de eigenschappen ook in een JsonObject laden en vervolgens door geven tijdens het maken van de-functie, zoals hieronder wordt weer gegeven.

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

Zodra u een geojson-functie hebt gemaakt, kunt u een gegevens bron aan de kaart toevoegen via de `sources` eigenschap van de kaart. De volgende code laat zien hoe u een maakt, hoe u `DataSource` deze toevoegt aan de kaart en hoe u een functie toevoegt aan de gegevens bron.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

De volgende code toont verschillende manieren om een geojson-functie, FeatureCollection en geometrie te maken.

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>Geojson serialiseren en deserialiseren

De klassen functie verzameling, functie en Geometry hebben alle `fromJson()` en `toJson()` statische methoden, die u helpen bij serialisatie. Met de geformatteerde geldige JSON-teken reeks die door de `fromJson()` methode is door gegeven, wordt het object Geometry gemaakt. Deze `fromJson()` methode betekent ook dat u Gson of andere strategieën voor serialisatie/deserialisatie kunt gebruiken. De volgende code laat zien hoe u een stringified geojson-functie kunt maken en deze kunt deserialiseren naar de functie klasse en vervolgens weer serialiseren naar een geojson-teken reeks.

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Geojson-gegevens importeren uit de map web of assets

De meeste geojson-bestanden bevatten een FeatureCollection. Geojson-bestanden lezen als teken reeksen en de `FeatureCollection.fromJson` methode gebruiken om deze te deserialiseren.

De volgende code is een herbruikbare klasse voor het importeren van gegevens uit de map web of lokale assets als teken reeks en retour neren naar de UI-thread via een call back-functie.

```java
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

De volgende code laat zien hoe u dit hulp programma gebruikt om geojson-gegevens te importeren als een teken reeks en deze te retour neren aan de UI-thread via een call back. In de call back kunnen de teken reeks gegevens worden geserialiseerd in een geojson-functie verzameling en worden toegevoegd aan de gegevens bron. U kunt de kaarten camera eventueel bijwerken om te focussen op de gegevens.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>Bron van vector tegel

Een vector tegel bron beschrijft hoe een vector tegel laag kan worden geopend. Gebruik de- `VectorTileSource` klasse om een vector tegel bron te instantiëren. Vector tegel lagen zijn vergelijkbaar met de tegel lagen, maar ze zijn niet hetzelfde. Een tegel laag is een raster afbeelding. Vector tegel lagen zijn een gecomprimeerd bestand in de **PBF** -indeling. Dit gecomprimeerde bestand bevat gegevens van een vector kaart en een of meer lagen. Het bestand kan worden gerenderd en opgemaakt op de client, op basis van de stijl van elke laag. De gegevens in een vector tegel bevatten geografische functies in de vorm van punten, lijnen en veelhoeken. Er zijn verschillende voor delen van het gebruik van vector tegel lagen in plaats van raster tegel lagen:

- Een bestands grootte van een vector tegel is doorgaans veel kleiner dan een gelijkwaardige raster tegel. Als zodanig wordt er minder band breedte gebruikt. Dit betekent een lagere latentie, een snellere kaart en een betere gebruikers ervaring.
- Omdat vector tegels worden weer gegeven op de client, worden ze aangepast aan de resolutie van het apparaat waarop ze worden weer gegeven. Als gevolg hiervan worden de gerenderde kaarten meer duidelijk gedefinieerd, met kristalheldere labels.
- Voor het wijzigen van de stijl van de gegevens in de vector kaarten hoeven de gegevens niet opnieuw te worden gedownload omdat de nieuwe stijl op de client kan worden toegepast. Als u daarentegen de stijl van een raster tegel laag wijzigt, moet meestal het laden van tegels van de server worden geladen en vervolgens de nieuwe stijl wordt toegepast.
- Omdat de gegevens in een vector vorm worden geleverd, is er minder server-side verwerking vereist om de gegevens voor te bereiden. Als gevolg hiervan kunnen de nieuwere gegevens sneller beschikbaar worden gemaakt.

Azure Maps voldoet aan de [tegel specificatie Mapbox vector](https://github.com/mapbox/vector-tile-spec), een open standaard. Azure Maps biedt de volgende vector tegel Services als onderdeel van het platform:

- Details van [](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)de  |  [gegevens indeling](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) voor wegtegels in de documentatie
- Details van [documentatie](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)voor verkeers incidenten  |  [gegevens indeling](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Details van [](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)de  |  [gegevens indeling](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) van de verkeers stroom
- Azure Maps Maker kunnen ook aangepaste vector tegels maken en openen via de [weer gave tegel ophalen v2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)

Als u gegevens uit een vector tegel bron op de kaart wilt weer geven, verbindt u de bron met een van de gegevens weergave lagen. Alle lagen die gebruikmaken van een vector bron moeten een `sourceLayer` waarde in de opties opgeven. Met de volgende code wordt de Azure Maps traffic flow vector-tegel service als een vector tegel bron geladen en vervolgens weer gegeven op een kaart met behulp van een laag. Deze vector tegel bron heeft één set gegevens in de bron laag met de naam ' verkeers stroom '. De regel gegevens in deze gegevensset hebben een eigenschap `traffic_level` met de naam die wordt gebruikt in deze code om de kleur te selecteren en de grootte van regels te schalen.

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://atlas.microsoft.com/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}&subscription-key=" + AzureMaps.getSubscriptionKey();

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource("flowLayer",
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![Toewijzen aan weglijnen met kleur die de verkeers stroom niveaus weer geven](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Een gegevens bron verbinden met een laag

De gegevens worden weer gegeven op de kaart met behulp van lagen renderen. Naar één gegevens bron kan worden verwezen door een of meer rendering-lagen. Voor de volgende weergave lagen is een gegevens bron vereist:

- [Bubble Layer](map-add-bubble-layer-android.md) : geeft punt gegevens weer als geschaalde cirkels op de kaart.
- [Symbol-laag](how-to-add-symbol-to-android-map.md) : geeft punt gegevens weer als pictogrammen of tekst.
- [Heatmap voor hitte](map-add-heat-map-layer-android.md) : geeft punt gegevens weer als een hitte heatmap.
- [Line Layer](android-map-add-line-layer.md) : een lijn weer geven en het overzicht van veelhoeken weer geven.
- [Veelhoek-laag](how-to-add-shapes-to-android-map.md) : Hiermee wordt het gebied van een veelhoek gevuld met een effen kleur of een patroon voor een afbeelding.

De volgende code laat zien hoe u een gegevens bron maakt, hoe u deze toevoegt aan de kaart en hoe u deze verbindt met een Bubble Layer. En importeer vervolgens geojson Point-gegevens vanaf een externe locatie naar de gegevens bron.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Er zijn extra rendering lagen die geen verbinding maken met deze gegevens bronnen, maar ze kunnen de gegevens rechtstreeks laden voor rendering.

- [Tegel laag](how-to-add-tile-layer-android-map.md) : er wordt een raster tegel laag boven op de kaart opgelegd.

## <a name="one-data-source-with-multiple-layers"></a>Eén gegevens bron met meerdere lagen

Meerdere lagen kunnen worden verbonden met één gegevens bron. Er zijn veel verschillende scenario's waarin deze optie handig is. Denk bijvoorbeeld na over het scenario waarin een gebruiker een veelhoek tekent. Het veelhoek gebied moet worden weer gegeven en gevuld wanneer de gebruiker punten toevoegt aan de kaart. Als u een lijn met stijl hebt toegevoegd om het overzicht van de veelhoek te maken, kunt u de randen van de veelhoek gemakkelijker zien, omdat de gebruiker tekent. Voor een gemakkelijke bewerking van een afzonderlijke positie in de veelhoek, kunnen we boven elke positie een koppeling toevoegen, zoals een pincode of een markering.

![Toewijzing met meerdere lagen die gegevens uit een enkele gegevens bron weer geven](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

In de meeste toewijzings platforms hebt u een veelhoek object, een lijn object en een pincode voor elke positie in de veelhoek nodig. Als de veelhoek is gewijzigd, moet u de regel en pincodes hand matig bijwerken. Dit kan snel complexer worden.

Met Azure Maps hoeft u alleen maar één veelhoek in een gegevens bron te hebben, zoals in de onderstaande code wordt weer gegeven.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> Wanneer u met behulp van de methode lagen aan de kaart toevoegt `map.layers.add` , kan de id of het exemplaar van een bestaande laag worden door gegeven als een tweede para meter. Hiermee wordt aangegeven dat de toewijzing van de nieuwe laag onder de bestaande laag moet worden ingevoegd. In naast die in een laag-ID worden door gegeven, ondersteunt deze methode ook de volgende waarden.
>
> - `"labels"` -Hiermee wordt de nieuwe laag onder de kaart label lagen ingevoegd.
> - `"transit"` -Voegt de nieuwe laag onder de lagen kaart en transit toe.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Een heatmap toevoegen](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Voor beelden van Web SDK-code](https://docs.microsoft.com/samples/browse/?products=azure-maps)
