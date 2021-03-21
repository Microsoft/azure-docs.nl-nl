---
title: Een symbool laag toevoegen aan een kaart | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van aangepaste symbolen, zoals tekst of pictogrammen, aan Maps. Zie gegevens bronnen en symbool lagen gebruiken in de Azure Maps Web-SDK voor dit doel.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891051"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Een symbool laag aan een kaart toevoegen

Een symbool verbinden met een gegevens bron en gebruiken om een pictogram of een tekst op een bepaald punt weer te geven. 

Symbool lagen worden gerenderd met behulp van WebGL. Gebruik een symbool laag om grote verzamelingen van punten op de kaart weer te geven. Vergeleken met HTML-markering, wordt met de laag Symbol een groot aantal punt gegevens op de kaart weer gegeven, met betere prestaties. De laag Symbol ondersteunt echter geen traditionele CSS-en HTML-elementen voor opmaak.  

> [!TIP]
> Met symbool lagen worden standaard de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag zo wilt beperken dat alleen de functies van de functie punt geometrie de `filter` eigenschap van de laag instellen op `['==', ['geometry-type'], 'Point']` of `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Als u wilt, kunt u ook multi point-functies toevoegen.

De Maps-afbeelding sprite Manager laadt aangepaste installatie kopieën die worden gebruikt door de laag van het symbool. Het ondersteunt de volgende afbeeldings indelingen:

- INDELING
- PNG
- SVG
- BMP
- GIF (geen animaties)

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Voordat u een Symbol-laag aan de kaart kunt toevoegen, moet u een aantal stappen uitvoeren. Maak eerst een gegevens bron en voeg deze toe aan de kaart. Maak een symbool laag. Geef vervolgens de gegevens bron door aan de symbool laag om de gegevens op te halen uit de gegevens bron. Voeg ten slotte gegevens toe aan de gegevens bron, zodat er iets wordt weer gegeven. 

De volgende code laat zien wat er aan de kaart moet worden toegevoegd nadat deze is geladen. In dit voor beeld wordt één punt op de kaart weer gegeven met behulp van een symbool laag. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Er zijn vier verschillende typen punt gegevens die aan de kaart kunnen worden toegevoegd:

- Geometrie van geojson-punt: dit object bevat alleen een coördinaat van een punt en niets anders. De `atlas.data.Point` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- Geojson multi point Geometry: dit object bevat de coördinaten van meerdere punten en niets anders. De `atlas.data.MultiPoint` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- Geojson-functie: dit object bestaat uit een geojson-geometrie en een set eigenschappen die meta gegevens bevatten die aan de geometrie zijn gekoppeld. De `atlas.data.Feature` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- `atlas.Shape` de klasse is vergelijkbaar met de geojson-functie. Beide bestaan uit een geojson-geometrie en een set eigenschappen die meta gegevens bevatten die aan de geometrie zijn gekoppeld. Als een geojson-object wordt toegevoegd aan een gegevens bron, kan het eenvoudig worden weer gegeven in een laag. Als de eigenschap coördinaten van het geojson-object echter wordt bijgewerkt, worden de gegevens bron en de kaart niet gewijzigd. Dat komt doordat het JSON-object geen mechanisme bevat om een update te activeren. De klasse Shape biedt functies voor het bijwerken van de gegevens die deze bevat. Als er een wijziging wordt aangebracht, worden de gegevens bron en de kaart automatisch gewaarschuwd en bijgewerkt. 

In het volgende code voorbeeld wordt een geometrie voor een geojson-punt gemaakt en door gegeven `atlas.Shape` aan de klasse, zodat deze gemakkelijk kan worden bijgewerkt. Het middel punt van de kaart wordt in eerste instantie gebruikt om een symbool weer te geven. Een Click-gebeurtenis wordt toegevoegd aan de kaart, zodat de coördinaten van de muis worden gebruikt in combi natie met de functie shapes als deze wordt geactiveerd `setCoordinates` . De muis coördinaten worden vastgelegd op het moment van de gebeurtenis Click. Vervolgens wordt de `setCoordinates` locatie van het symbool op de kaart bijgewerkt.

<br/>

<iframe height='500' scrolling='no' title='Switch pincode locatie' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>pincode locatie</a> van de Pen van Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>().
</iframe>

> [!TIP]
> Standaard optimaliseert symbool lagen de rendering van symbolen door symbolen te verbergen die elkaar overlappen. Wanneer u inzoomt, worden de verborgen symbolen zichtbaar. Als u deze functie wilt uitschakelen en alle symbolen wilt renderen, stelt `allowOverlap` u de eigenschap van de `iconOptions` Opties in op `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Een aangepast pictogram toevoegen aan een symbool-laag

Symbool lagen worden gerenderd met behulp van WebGL. Alle resources, zoals pictogram afbeeldingen, moeten worden geladen in de WebGL-context. In dit voor beeld ziet u hoe u een aangepast pictogram aan de kaart resources toevoegt. Dit pictogram wordt vervolgens gebruikt voor het weer geven van punt gegevens met een aangepast symbool op de kaart. `textField`Voor de eigenschap van de Symbol-laag moet een expressie worden opgegeven. In dit geval willen we de eigenschap Tempe ratuur weer geven. Omdat de Tempe ratuur een getal is, moet dit worden geconverteerd naar een teken reeks. Daarnaast willen we ' °F ' eraan toevoegen. Een expressie kan worden gebruikt om deze samen voeging uit te voeren. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Pictogram voor aangepaste symbool afbeelding' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>pictogram afbeelding voor aangepast symbool</a> voor Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> De Azure Maps Web-SDK biedt verschillende aanpas bare afbeeldings sjablonen die u kunt gebruiken met de Symbol-laag. Zie het document [Image-sjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="customize-a-symbol-layer"></a>Een symbool-laag aanpassen 

De Symbol-laag heeft veel stijl opties beschikbaar. Hier volgt een hulp programma om deze verschillende opmaak opties te testen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor symbool lagen' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/PxVXje/'>laag opties</a> van het pen-symbool per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Als u alleen tekst wilt weer geven met een symbool-laag, kunt u het pictogram verbergen door de `image` eigenschap van de pictogram opties in te stellen op `'none'` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

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
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML-makers toevoegen](map-add-bubble-layer.md)