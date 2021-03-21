---
title: Een laagmap toevoegen aan een kaart | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van lijnen aan Maps. Zie de voor beelden die gebruikmaken van de Azure Maps Web-SDK om lijn lagen toe te voegen aan Maps en om lijnen met symbolen en kleur overgangen aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: fedf1bc37661100404106859d3aef52b3eddf06d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048134"
---
# <a name="add-a-line-layer-to-the-map"></a>Een laagmap toevoegen aan de kaart

Een line-laag kan worden gebruikt om te renderen `LineString` en te `MultiLineString` functies als paden of routes op de kaart. Een line-laag kan ook worden gebruikt om het overzicht van `Polygon` en functies weer te geven `MultiPolygon` . Een gegevens bron is verbonden met een laag om deze te voorzien van gegevens die moeten worden weer gegeven. 

> [!TIP]
> Met lijn lagen worden standaard de coördinaten van veelhoeken en lijnen in een gegevens bron weer gegeven. Als u de laag zodanig wilt beperken dat de Lines Tring-functies alleen worden weer gegeven `filter` , stelt u de eigenschap van de laag in `['==', ['geometry-type'], 'LineString']` of `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` Als u ook multi line string-functies wilt toevoegen.

De volgende code laat zien hoe u een regel kunt maken. Voeg de lijn toe aan een gegevens bron en geef deze vervolgens weer met behulp van de [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) -klasse.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een regel aan een kaart toevoegen' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>een regel toevoegen aan een kaart</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Lijn lagen kunnen worden opgemaakt met behulp van [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) en [gebruiken gegevensgestuurde stijl expressies](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Symbolen toevoegen langs een regel

In dit voor beeld ziet u hoe u pijl pictogrammen kunt toevoegen langs een regel op de kaart. Wanneer u een symbool-laag gebruikt, stelt u de optie plaatsing in op regel. Met deze optie worden de symbolen op de regel weer gegeven en de pictogrammen geroteerd (0 graden = rechts).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pijl langs lijn weer geven" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/drBJwX/'>pijl weer geven naast regel</a> voor Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> De Azure Maps Web-SDK biedt verschillende aanpas bare afbeeldings sjablonen die u kunt gebruiken met de Symbol-laag. Zie het document [Image-sjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Een lijn kleur overgang toevoegen aan een lijn

U kunt één lijn kleur op een regel Toep assen. U kunt ook een lijn vullen met een kleur overgang om de overgang van het ene lijn segment naar het volgende lijn segment weer te geven. Lijn kleur overgangen kunnen bijvoorbeeld worden gebruikt om wijzigingen in de loop van de tijd en afstand te vertegenwoordigen, of verschillende Tempe raturen over een verbonden object regel. Als u deze functie op een regel wilt Toep assen, moet de optie zijn ingesteld op de gegevens bron `lineMetrics` `true` en vervolgens een expressie voor kleur verloop kunnen worden door gegeven aan de `strokeColor` optie van de regel. De expressie voor de kleur overgang van de lijn moet verwijzen naar de `['line-progress']` gegevens expressie die de berekende regel metrieken voor de expressie beschrijft.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijn met lijn kleur overgang" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>met de lijn kleur overgang</a> op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Een lijn laag aanpassen

De laag Layer heeft verschillende opmaak opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor lijn lagen' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>laag opties</a> van de Pen op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)