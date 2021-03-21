---
title: Een afbeelding slaag toevoegen aan een kaart | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van installatie kopieën aan een kaart. Bekijk hoe u de Azure Maps Web-SDK gebruikt om afbeeldings lagen en overlay-afbeeldingen op vaste sets coördinaten aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: f0c24940e606020dc45e5a000cfcb030fd806d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047482"
---
# <a name="add-an-image-layer-to-a-map"></a>Een afbeelding slaag toevoegen aan een kaart

Dit artikel laat u zien hoe u een afbeelding kunt bedekken naar een vaste set coördinaten. Hier volgen enkele voor beelden van verschillende afbeeldings typen die kunnen worden overlapt met kaarten:

* Afbeeldingen die zijn vastgelegd vanuit Drones
* Floorplans bouwen
* Historische of andere gespecialiseerde kaart afbeeldingen
* Blauw drukken van taak sites
* Weer radar afbeeldingen

> [!TIP]
> Een [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) is een eenvoudige manier om een afbeelding op een kaart te bedekken. Houd er rekening mee dat browsers problemen kunnen hebben met het laden van een grote afbeelding. In dit geval kunt u overwegen om uw afbeelding in tegels te verdelen en ze te laden in de kaart als een [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer).

De afbeelding slaag ondersteunt de volgende afbeeldings indelingen:

- INDELING
- PNG
- BMP
- GIF (geen animaties)

## <a name="add-an-image-layer"></a>Een afbeeldingslaag toevoegen

De volgende code bedekken een afbeelding van een [kaart van Newark, New Jersey, van 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) op de kaart. Er wordt een [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) gemaakt door een URL door te geven aan een afbeelding en coördinaten voor de vier hoeken in de notatie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Hier volgt het volledige code voorbeeld van de voor gaande code.

<br/>

<iframe height='500' scrolling='no' title='Laag met eenvoudige afbeeldingen' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/eQodRo/'>eenvoudige afbeelding slaag</a> van de Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Een KML-bestand importeren als een wegdek bedekking

In dit voor beeld wordt gedemonstreerd hoe u KML-oppervlak bedekkings gegevens kunt toevoegen als een afbeelding slaag op de kaart. KML-bedekkingen bieden Noord-, Zuid-, Oost-en West-coördinaten en een rotatie linksom. Maar de afbeelding slaag verwacht coördinaten voor elke hoek van de afbeelding. De KML-bedekking in dit voor beeld is voor de Chartres-Cathedral en is bron van [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

De code maakt gebruik van de statische `getCoordinatesFromEdges` functie van de [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) -klasse. De vier hoeken van de afbeelding worden berekend met behulp van de Noord-, Zuid-, Oost-, West-en rotatie gegevens van de KML-vlak bedekking.

<br/>

<iframe height='500' scrolling='no' title='KML-oppervlak overlay als afbeelding slaag' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de KML-bedekking van de pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>als afbeelding slaag</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Gebruik de `getPixels` `getPositions` functies en van de klasse afbeelding slaag om te converteren tussen geografische coördinaten van de gepositioneerde afbeelding slaag en de pixel coördinaten van de lokale afbeelding.

## <a name="customize-an-image-layer"></a>Een afbeelding slaag aanpassen

De afbeelding slaag heeft veel stijl opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor afbeelding slaags' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>laag opties</a> van de pen-afbeelding per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](./map-add-tile-layer.md)