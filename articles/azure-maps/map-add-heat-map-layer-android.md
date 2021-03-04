---
title: Een heatmap toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het maken van een heatmap. Zie hoe u de Azure MapsAndroid SDK gebruikt om een heatmap aan een kaart toe te voegen. Meer informatie over het aanpassen van heatmap.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100182"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Een hitte kaart laag (Android SDK) toevoegen

Een heatmap, heel soms ook wel een puntdichtheidskaart genoemd, is een type gegevensvisualisatie. Ze worden gebruikt voor de densiteit van gegevens met behulp van een reeks kleuren en de gegevens ' HOTS Pots ' op een kaart worden weer gegeven. Hitte kaarten zijn een uitstekende manier om gegevens sets met een groot aantal punten weer te geven. 

Rendering van tien duizenden punten als symbolen kan het grootste deel van het kaart gebied beslaan. Dit geval leidt waarschijnlijk tot veel symbolen die elkaar overlappen. Het is moeilijk om een beter inzicht te krijgen in de gegevens. Als u echter dezelfde gegevensset visualiseren als een heatmap, is het eenvoudig om de densiteit en de relatieve dichtheid van elk gegevens punt te zien.

U kunt heatmap gebruiken in veel verschillende scenario's, zoals:

- **Temperatuur gegevens**: biedt benaderingen voor wat de Tempe ratuur tussen twee gegevens punten is.
- **Gegevens voor ruis Sens oren**: toont niet alleen de intensiteit van de ruis waarbij de sensor zich bevindt, maar kan ook inzicht krijgen in de dissipatie over een afstand. Het geluids niveau van een site kan niet hoog zijn. Als het gebied van de ruis dekking van meerdere Sens oren overlapt, is het mogelijk dat dit overlappende gebied hogere geluids niveaus kan ondervinden. Als zodanig is de overlappende Opper vlakte zichtbaar in de heatmap.
- **GPS-tracering**: bevat de snelheid als een kaart met een gewogen hoogte, waarbij de intensiteit van elk gegevens punt is gebaseerd op de snelheid. Deze functionaliteit biedt bijvoorbeeld een manier om te zien waar een voer tuig versnellen.

> [!TIP]
> Hitte kaart lagen standaard worden de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag wilt beperken zodat deze alleen functies van punt geometrie weergeeft, stelt `filter` u de optie van de laag in op `eq(geometryType(), "Point")` . Als u ook multi point-functies wilt toevoegen, stelt u de `filter` optie van de laag in op `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Als u een gegevens bron van punten wilt weer geven als een heatmap, geeft u uw gegevens bron door aan een instantie van de `HeatMapLayer` klasse en voegt u deze toe aan de kaart.

In het volgende code voorbeeld wordt een geojson-feed van aard bevingen van de afgelopen week geladen en weer gegeven als een heatmap. Elk gegevens punt wordt weer gegeven met een straal van 10 pixels op alle zoom niveaus. Om ervoor te zorgen dat de gebruikers ervaring beter is, is de heatmap onder de label laag zodat de labels duidelijk zichtbaar blijven. De gegevens in dit voor beeld zijn afkomstig uit het [USGS aardings risico programma](https://earthquake.usgs.gov/). Met dit voor beeld worden geojson-gegevens van het web geladen met het hulp programma code blok voor het importeren van gegevens dat is opgenomen in het bestand [een gegevens bron maken](create-data-source-android-sdk.md) .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
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
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

De volgende scherm afbeelding toont een kaart die een heatmap laadt met behulp van de bovenstaande code.

![Kaart met heatmap voor warmte toewijzing van recente aard bevingen](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>De laag van de heatmap aanpassen

In het vorige voor beeld is de heatmap aangepast door de opties voor RADIUS en dekking in te stellen. De laag van de heatmap biedt verschillende opties voor aanpassing, waaronder:

- `heatmapRadius`: Hiermee definieert u een pixel RADIUS waarin elk gegevens punt wordt weer gegeven. U kunt de RADIUS instellen als een vast getal of als een expressie. U kunt met behulp van een expressie de RADIUS schalen op basis van het zoom niveau en een consistent ruimte gebied op de kaart weer geven (bijvoorbeeld een RADIUS van 5 mijl).
- `heatmapColor`: Hiermee geeft u op hoe de heatmap wordt gekleurd. Een kleur overgang is een gemeen schappelijke functie van heatmap. U kunt het effect met een expressie behaalt `interpolate` . U kunt ook een `step` expressie gebruiken voor het inkleuren van de heatmap, waarbij u de densiteit visueel opsplitst in bereiken die lijken op een contour-of radar stijl kaart. Met deze kleuren paletten worden de kleuren van het minimum naar de maximale dichtheids waarde gedefinieerd.

  U geeft kleur waarden voor hitte kaarten op als een expressie voor de `heatmapDensity` waarde. De kleur van het gebied waarin er geen gegevens worden gedefinieerd, worden op index 0 van de expressie ' interpolatie ' of de standaard kleur van een ' Steped ' expressie opgegeven. U kunt deze waarde gebruiken om een achtergrond kleur te definiëren. Deze waarde is vaak ingesteld op transparant of een semi-transparante zwart. 

  Hier volgen enkele voor beelden van kleur expressies:

  | Expressie voor interpolatie kleur | Expressie met stap kleur |
  |--------------------------------|--------------------------|
  | tijdstip<br/>&nbsp;&nbsp;&nbsp;&nbsp;lineair (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0, kleur (kleur. transparant)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0,01, kleur (kleur. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0,5, kleur (parseColor ("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, kleur (parseColor ("#00c3ff")))<br/>)` | wizardstap<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;kleur (kleur. TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0,01, kleur (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,25, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0,5, kleur (kleur. groen)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (0,5, kleur (kleur. geel)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppen (1, kleur (kleur. rood))<br/>) |

- `heatmapOpacity`: Hiermee geeft u op hoe ondoorzichtige of transparante de laag van de heatmap.
- `heatmapIntensity`: Past een vermenigvuldiger toe op het gewicht van elk gegevens punt om de algehele intensiteit van de heatmap te verg Roten. Dit veroorzaakt een verschil in het gewicht van gegevens punten, waardoor het eenvoudiger wordt om te visualiseren.
- `heatmapWeight`: Standaard hebben alle gegevens punten een gewicht van 1 en worden ze gelijk gewogen. De optie gewicht fungeert als een vermenigvuldiger en u kunt deze instellen als een getal of een expressie. Als een getal als gewicht is ingesteld, is het de gelijkwaardigheid van het plaatsen van elk gegevens punt op de kaart twee keer. Als het gewicht bijvoorbeeld is `2` , verdubbelt de dichtheid. Als u de optie gewicht instelt op een getal, wordt de heatmap op een vergelijk bare manier weer gegeven als met de optie intensiteit.

  Als u echter een expressie gebruikt, kan het gewicht van elk gegevens punt worden gebaseerd op de eigenschappen van elk gegevens punt. Stel bijvoorbeeld dat elk gegevens punt een aard beving vertegenwoordigt. De waarde voor de grootte is belang rijk voor elk gegevens punt in de aard van de kosten. Aard bevingen gebeuren altijd, maar de meeste hebben een lage grootte en zijn niet opgemerkt. Gebruik de waarde magnitude in een expressie om het gewicht aan elk gegevens punt toe te wijzen. Door gebruik te maken van de waarde voor het gewicht, krijgt u een beter overzicht van de significantie van aard bevingen in de heatmap.
- `minZoom` en `maxZoom` : het bereik van de zoom niveau waar de laag moet worden weer gegeven.
- `filter`: Een filter expressie die wordt gebruikt om de opgehaalde waarde van de bron te beperken en weer te geven in de laag.
- `sourceLayer`: Als de gegevens bron die is verbonden met de laag een vector tegel bron is, moet u een bronlaag in de vector tegels opgeven.
- `visible`: Hiermee wordt de laag verborgen of weer gegeven.

Hier volgt een voor beeld van een heatmap waarbij een interpolatie-expressie met een lijn wordt gebruikt om een vloeiende kleur overgang te maken. De `mag` eigenschap die in de gegevens is gedefinieerd, wordt gebruikt met een exponentiële interpolatie om het gewicht of de relevantie van elk gegevens punt in te stellen.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

In de volgende scherm afbeelding ziet u de bovenstaande aangepaste heatmap met dezelfde gegevens uit het vorige voor beeld van een heatmap.

![Kaart met aangepaste heatmap voor hitte van recente aard bevingen](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Consistente, zoom bare heatmap

De RADIUS van gegevens punten die worden weer gegeven in de laag van de heatmap hebben standaard een straal van vaste pixels voor alle zoom niveaus. Wanneer u de kaart inzoomt, worden de gegevens samen geaggregeerd en de heatmap van de laag. In de volgende video ziet u het standaard gedrag van de heatmap waarbij een pixel RADIUS wordt bijgehouden bij het inzoomen van de kaart.

![Animatie waarin een kaart wordt ingezoomd met een heatmap met een bitmap met een consistente pixel grootte](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Gebruik een `zoom` expressie om de RADIUS voor elk zoom niveau te schalen, zodat elk gegevens punt overeenkomt met hetzelfde fysieke gebied van de kaart. Met deze expressie zorgt u ervoor dat de heatmap van de warmte kaart statisch en consistent is. Elk zoom niveau van de kaart heeft twee keer zoveel pixels verticaal en horizon taal als het vorige zoom niveau.

Het schalen van de RADIUS zodat deze wordt verdubbeld met elk zoom niveau, maakt een heatmap die consistent is op alle zoom niveaus. Als u deze schaal wilt Toep assen, gebruikt u `zoom` met een basis-2- `exponential interpolation` expressie, waarbij de pixel RADIUS is ingesteld voor het minimale zoom niveau en een geschaalde RADIUS voor het maximale zoom niveau dat wordt berekend, zoals `2 * Math.pow(2, minZoom - maxZoom)` wordt weer gegeven in het volgende voor beeld. Zoom in op de kaart om te zien hoe de heatmap met het zoom niveau wordt geschaald.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

In de volgende video ziet u een kaart met de bovenstaande code, waarmee de RADIUS wordt geschaald terwijl de kaart wordt ingezoomd om een consistente rendering van een hitte kaart te maken voor verschillende zoom niveaus.

![Animatie waarin een kaart wordt ingezoomd met een heatmap met een consistente georuimtelijke grootte](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw Maps:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-android-sdk.md)
