---
title: Een heatmap aan een kaart toevoegen | Microsoft Azure kaarten
description: Meer informatie over het maken van een heatmap. Zie How to use the Azure Maps Web SDK om een heatmap toe te voegen aan een kaart. Meer informatie over het aanpassen van heatmap.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b15ee7091a68f7fcc79c71877c4af28b511b84de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680141"
---
# <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Een heatmap, heel soms ook wel een puntdichtheidskaart genoemd, is een type gegevensvisualisatie. Ze worden gebruikt voor de densiteit van gegevens met behulp van een reeks kleuren en de gegevens ' HOTS Pots ' op een kaart worden weer gegeven. Hitte kaarten zijn een uitstekende manier om gegevens sets met een groot aantal punten weer te geven. 

Rendering van tien duizenden punten als symbolen kan het grootste deel van het kaart gebied beslaan. Dit geval leidt waarschijnlijk tot veel symbolen die elkaar overlappen. Het is moeilijk om een beter inzicht te krijgen in de gegevens. Als u echter dezelfde gegevensset visualiseren als een heatmap, is het eenvoudig om de densiteit en de relatieve dichtheid van elk gegevens punt te zien.

U kunt heatmap gebruiken in veel verschillende scenario's, zoals:

- **Temperatuur gegevens**: biedt benaderingen voor wat de Tempe ratuur tussen twee gegevens punten is.
- **Gegevens voor ruis Sens oren**: toont niet alleen de intensiteit van de ruis waarbij de sensor zich bevindt, maar kan ook inzicht krijgen in de dissipatie over een afstand. Het geluids niveau van een site kan niet hoog zijn. Als het gebied van de ruis dekking van meerdere Sens oren overlapt, is het mogelijk dat dit overlappende gebied hogere geluids niveaus kan ondervinden. Als zodanig is de overlappende Opper vlakte zichtbaar in de heatmap.
- **GPS-tracering**: bevat de snelheid als een kaart met een gewogen hoogte, waarbij de intensiteit van elk gegevens punt is gebaseerd op de snelheid. Deze functionaliteit biedt bijvoorbeeld een manier om te zien waar een voer tuig versnellen.

> [!TIP]
> Hitte kaart lagen standaard worden de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag wilt beperken zodat deze alleen functies van punt geometrie weergeeft, stelt `filter` u de eigenschap van de laag in op `['==', ['geometry-type'], 'Point']` . Als u ook multi point-functies wilt toevoegen, stelt u de `filter` eigenschap van de laag in op `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` .

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>Een heatmap-laag toevoegen

Als u een gegevens bron van punten wilt weer geven als een heatmap, geeft u uw gegevens bron door aan een instantie van de `HeatMapLayer` klasse en voegt u deze toe aan de kaart.

In de volgende code heeft elk verwarmings punt een straal van 10 pixels op alle zoom niveaus. Om ervoor te zorgen dat de gebruikers ervaring beter is, is de heatmap onder de label laag. De labels blijven duidelijk zichtbaar. De gegevens in dit voor beeld zijn afkomstig uit het [USGS aardings risico programma](https://earthquake.usgs.gov/). Het is voor aanzienlijke aard bevingen die in de afgelopen 30 dagen zijn opgetreden.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Hier volgt het volledige code voorbeeld van de voor gaande code.

<br/>

<iframe height='500' scrolling='no' title='Eenvoudige kaart laag voor hitte' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen- <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>eenvoudige heatmap laag</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>De laag van de heatmap aanpassen

In het vorige voor beeld is de heatmap aangepast door de opties voor RADIUS en dekking in te stellen. De laag van de heatmap biedt verschillende opties voor aanpassing, waaronder:

* `radius`: Hiermee definieert u een pixel RADIUS waarin elk gegevens punt wordt weer gegeven. U kunt de RADIUS instellen als een vast getal of als een expressie. U kunt met behulp van een expressie de RADIUS schalen op basis van het zoom niveau en een consistent ruimte gebied op de kaart weer geven (bijvoorbeeld een RADIUS van 5 mijl).
* `color`: Hiermee geeft u op hoe de heatmap wordt gekleurd. Een kleur overgang is een gemeen schappelijke functie van heatmap. U kunt het effect met een expressie behaalt `interpolate` . U kunt ook een `step` expressie gebruiken voor het inkleuren van de heatmap, waarbij u de densiteit visueel opsplitst in bereiken die lijken op een contour-of radar stijl kaart. Met deze kleuren paletten worden de kleuren van het minimum naar de maximale dichtheids waarde gedefinieerd. 

  U geeft kleur waarden voor hitte kaarten op als een expressie voor de `heatmap-density` waarde. De kleur van het gebied waarin er geen gegevens worden gedefinieerd, worden op index 0 van de expressie ' interpolatie ' of de standaard kleur van een ' Steped ' expressie opgegeven. U kunt deze waarde gebruiken om een achtergrond kleur te definiëren. Deze waarde is vaak ingesteld op transparant of een semi-transparante zwart. 
   
  Hier volgen enkele voor beelden van kleur expressies:

  | Expressie voor interpolatie kleur | Expressie met stap kleur | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolatie ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[lineair \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-densiteit ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, ' transparant ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, paars,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' stap ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-densiteit ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;transparent,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' Navy ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' groen ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, geel,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' rood '<br/>\] |   

- `opacity`: Hiermee geeft u op hoe ondoorzichtige of transparante de laag van de heatmap.
- `intensity`: Past een vermenigvuldiger toe op het gewicht van elk gegevens punt om de algehele intensiteit van de heatmap te verg Roten. Dit veroorzaakt een verschil in het gewicht van gegevens punten, waardoor het eenvoudiger wordt om te visualiseren.
- `weight`: Standaard hebben alle gegevens punten een gewicht van 1 en worden ze gelijk gewogen. De optie gewicht fungeert als een vermenigvuldiger en u kunt deze instellen als een getal of een expressie. Als een getal als gewicht is ingesteld, is het de gelijkwaardigheid van het plaatsen van elk gegevens punt op de kaart twee keer. Als het gewicht bijvoorbeeld 2 is, verdubbelt de dichtheid. Als u de optie gewicht instelt op een getal, wordt de heatmap op een vergelijk bare manier weer gegeven als met de optie intensiteit. 

  Als u echter een expressie gebruikt, kan het gewicht van elk gegevens punt worden gebaseerd op de eigenschappen van elk gegevens punt. Stel bijvoorbeeld dat elk gegevens punt een aard beving vertegenwoordigt. De waarde voor de grootte is belang rijk voor elk gegevens punt in de aard van de kosten. Aard bevingen gebeuren altijd, maar de meeste hebben een lage grootte en zijn niet opgemerkt. Gebruik de waarde magnitude in een expressie om het gewicht aan elk gegevens punt toe te wijzen. Door gebruik te maken van de waarde voor het gewicht, krijgt u een beter overzicht van de significantie van aard bevingen in de heatmap.
- `source` en `source-layer` : Hiermee kunt u de gegevens bron bijwerken.

Hier vindt u een hulp programma voor het testen van de verschillende opties voor de heatmap.

<br/>

<iframe height='700' scrolling='no' title='Opties voor de laag van de heatmap' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de opties van de pen voor het <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>heatmap</a> van peninstellingen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistente, zoom bare heatmap

De RADIUS van gegevens punten die worden weer gegeven in de laag van de heatmap hebben standaard een straal van vaste pixels voor alle zoom niveaus. Wanneer u de kaart inzoomt, worden de gegevens samen geaggregeerd en de heatmap van de laag. 

Gebruik een `zoom` expressie om de RADIUS voor elk zoom niveau te schalen, zodat elk gegevens punt overeenkomt met hetzelfde fysieke gebied van de kaart. Met deze expressie zorgt u ervoor dat de heatmap van de warmte kaart statisch en consistent is. Elk zoom niveau van de kaart heeft twee keer zoveel pixels verticaal en horizon taal als het vorige zoom niveau. 

Het schalen van de RADIUS zodat deze wordt verdubbeld met elk zoom niveau, maakt een heatmap die consistent is op alle zoom niveaus. Als u deze schaal wilt Toep assen, gebruikt u `zoom` met een basis-2- `exponential interpolation` expressie, waarbij de pixel RADIUS is ingesteld voor het minimale zoom niveau en een geschaalde RADIUS voor het maximale zoom niveau dat wordt berekend, zoals `2 * Math.pow(2, minZoom - maxZoom)` wordt weer gegeven in het volgende voor beeld. Zoom in op de kaart om te zien hoe de heatmap met het zoom niveau wordt geschaald.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistente, zoom bare heatmap" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>consistente, zoom bare heatmap</a> met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u Clustering op de gegevens bron inschakelt, worden de punten die zich dicht bij elkaar bevinden, gegroepeerd als een geclusterd punt. U kunt de punt telling van elk cluster gebruiken als de gewichts expressie voor de heatmap. Hierdoor kan het aantal punten dat moet worden gerenderd aanzienlijk verminderen. De punt telling van een cluster wordt opgeslagen in een `point_count` eigenschap van de functie punt: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Als de cluster RADIUS slechts een paar pixels is, is er een klein visueel verschil in de rendering. Een grotere RADIUS-groep wijst meer punten in elk cluster en verbetert de prestaties van de heatmap.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw Maps:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)