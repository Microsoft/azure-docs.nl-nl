---
title: Besturings elementen toevoegen aan een kaart | Microsoft Azure kaarten
description: Het toevoegen van een zoom besturings element, besturings element pitch, Control en een stijl kiezer aan een kaart in Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: bbcfe4935843a00bc5274273e79f4ffc72dc25ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051052"
---
# <a name="add-controls-to-a-map"></a>Besturings elementen aan een kaart toevoegen

Dit artikel laat u zien hoe u besturings elementen aan een kaart kunt toevoegen. U leert ook hoe u een kaart maakt met alle besturings elementen en een [stijl kiezer](./choose-map-style.md).

## <a name="add-zoom-control"></a>Zoom besturings element toevoegen

Een zoom besturings element voegt knoppen toe om in en uit te zoomen op de kaart. Met het volgende code voorbeeld maakt u een instantie van de klasse [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) en voegt u deze toe aan de rechter benedenhoek van de kaart.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een zoom besturings element toevoegen' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>een zoom besturings element toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Besturings element voor Pitch toevoegen

Met een besturings element pitch voegt u knoppen toe voor het kantelen van de hoogte om toe te wijzen aan de horizon. Met het volgende code voorbeeld maakt u een instantie van de klasse [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . De PitchControl wordt toegevoegd aan de rechter bovenhoek van de kaart.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een besturings element voor Pitch toevoegen' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>een besturings element voor Pitch toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Besturings element kompas toevoegen

Met een kompas besturings element wordt een knop voor het draaien van de kaart toegevoegd. Met het volgende code voorbeeld maakt u een instantie van de klasse [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) en voegt u deze toe aan de linkerbenedenhoek van de kaart.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een besturings element draaien toevoegen' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>een rotatie besturings element toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Een kaart met alle besturings elementen

Meerdere besturings elementen kunnen in een matrix worden geplaatst en in één keer aan de kaart worden toegevoegd en in hetzelfde gebied van de kaart worden geplaatst om de ontwikkeling te vereenvoudigen. In het volgende voor beeld wordt de standaard navigatie besturings elementen aan de kaart toegevoegd met behulp van deze methode.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

In het volgende code voorbeeld worden de besturings elementen zoom, kompas, Pitch en stijl kiezer toegevoegd aan de rechter bovenhoek van de kaart. U ziet hoe ze automatisch worden gestapeld. De volg orde van de besturings objecten in het script bepaalt de volg orde waarin ze op de kaart worden weer gegeven. U kunt de volg orde van de besturings elementen op de kaart wijzigen door de volg orde in de matrix te wijzigen.

<br/>

<iframe height='500' scrolling='no' title='Een kaart met alle besturings elementen' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map met alle besturings elementen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Het besturings element stijl kiezer wordt gedefinieerd door de [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) -klasse. Zie [een kaart stijl kiezen](choose-map-style.md)voor meer informatie over het gebruik van het besturings element stijl kiezer.

## <a name="customize-controls"></a>Besturings elementen aanpassen

Hier volgt een hulp programma voor het testen van de verschillende opties voor het aanpassen van de besturings elementen.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor navigatie beheer" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de opties voor het <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>besturings element</a> voor pen-navigatie per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Als u aangepaste navigatie besturings elementen wilt maken, maakt u een klasse die uitbreidt van de `atlas.Control` -klasse of maakt u een HTML-element en plaatst u het boven het kaart-div. Laat dit besturings element voor de gebruikers interface de functie Maps aanroepen `setCamera` om de kaart te verplaatsen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Besturings element kompas](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Raadpleeg de volgende artikelen voor volledige code:

> [!div class="nextstepaction"]
> [Een speld toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)