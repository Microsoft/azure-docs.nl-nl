---
title: De stijl van de Azure Maps Web-Map Control wijzigen
description: Meer informatie over het wijzigen van de stijl en opties van een kaart. Zie een besturings element stijl kiezer toevoegen aan een kaart in Azure Maps zodat gebruikers kunnen scha kelen tussen verschillende stijlen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680021"
---
# <a name="change-the-style-of-the-map"></a>Stijl van de kaart wijzigen

Het kaart besturings element ondersteunt meerdere verschillende [Opties](/javascript/api/azure-maps-control/atlas.styleoptions) voor kaart stijlen en [basis kaart stijlen](supported-map-styles.md). Alle stijlen kunnen worden ingesteld wanneer het kaart besturings element wordt geïnitialiseerd. Of u kunt stijlen instellen met behulp van de functie van het kaart besturings element `setStyle` . Dit artikel laat u zien hoe u deze stijl opties kunt gebruiken om de weer gave van de kaart aan te passen. U leert ook hoe u het besturings element stijl kiezer implementeert in uw kaart. Met het besturings element stijl kiezer kan de gebruiker scha kelen tussen verschillende basis stijlen.

## <a name="set-map-style-options"></a>Opties voor kaart stijl instellen

Stijl opties kunnen worden ingesteld tijdens de initialisatie van Webbe sturings elementen. U kunt ook stijl opties bijwerken door de functie kaart besturings element aan te roepen `setStyle` . Zie [stijl opties](/javascript/api/azure-maps-control/atlas.styleoptions)voor een overzicht van alle beschik bare stijl opties.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

Het volgende hulp programma laat zien hoe de verschillende stijl opties wijzigen hoe de kaart wordt weer gegeven. Als u de 3D-gebouwen wilt bekijken, zoomt u in op dicht bij een primaire plaats.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor kaart stijl" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de opties voor de <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>kaart stijl</a> van de Pen op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Een Basiskaart stijl instellen

U kunt het kaart besturings element ook initialiseren met een van de [basis kaart stijlen](supported-map-styles.md) die beschikbaar zijn in de Web-SDK. Vervolgens kunt u de `setStyle` functie gebruiken om de basis stijl bij te werken met een andere kaart stijl.

### <a name="set-a-base-map-style-on-initialization"></a>Een Basiskaart stijl instellen voor initialisatie

De basis stijlen van het kaart besturings element kunnen tijdens de initialisatie worden ingesteld. In de volgende code is de `style` optie van het kaart besturings element ingesteld op het [ `grayscale_dark` basis kaart type](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Stijl instellen voor toewijzings belasting' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>instellen van de stijl van de kaart belasting</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>De stijl van het basis overzicht bijwerken

De basiskaart stijl kan worden bijgewerkt met behulp van de `setStyle` functie en de `style` optie instellen om naar een andere basiskaart stijl te wijzigen of extra stijl opties toe te voegen.

```javascript
map.setStyle({ style: 'satellite' });
```

In de volgende code, nadat een kaart exemplaar is geladen, wordt de kaart stijl bijgewerkt van `grayscale_dark` naar `satellite` met behulp van de functie [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='De stijl bijwerken' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>het profiel bijwerken</a> met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Het besturings element stijl kiezer toevoegen

Het besturings element stijl kiezer biedt een gebruiks vriendelijke knop met het deel venster flyout dat kan worden gebruikt door de eind gebruiker om te scha kelen tussen basis stijlen.

De stijl kiezer heeft twee verschillende indelings opties: `icon` en `list` . Daarnaast kunt u met de stijl kiezer twee verschillende opties voor stijl kiezer kiezen `style` : `light` en `dark` . In dit voor beeld gebruikt de stijl kiezer de `icon` indeling en wordt een selectie lijst met basis stijlen weer gegeven in de vorm van pictogrammen. De kiezer voor stijl besturings elementen bevat de volgende basisset stijlen: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Zie [Opties voor stijl besturings](/javascript/api/azure-maps-control/atlas.stylecontroloptions)elementen voor meer informatie over de opties voor stijl kiezer.

In de onderstaande afbeelding ziet u het besturings element stijl kiezer dat wordt weer gegeven in de `icon` indeling.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Pictogram indeling stijl kiezer":::

In de onderstaande afbeelding ziet u het besturings element stijl kiezer dat wordt weer gegeven in de `list` indeling.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Indeling van lijst met stijl kiezer":::

> [!IMPORTANT]
> Standaard wordt in het besturings element stijl kiezer een lijst weer gegeven met alle stijlen die beschikbaar zijn in de prijs categorie S0 van Azure Maps. Als u het aantal stijlen in deze lijst wilt beperken, geeft u een matrix van de stijlen die u wilt weer geven in de lijst in de `mapStyle` optie van de stijl kiezer. Als u S1 gebruikt en alle beschik bare stijlen wilt weer geven, stelt u de `mapStyles` optie van de stijl kiezer in op `"all"` .

De volgende code laat zien hoe u de standaard `mapStyles` basisstijl lijst overschrijft. In dit voor beeld stellen we de `mapStyles` optie in om aan te geven welke basis stijlen moeten worden weer gegeven door het besturings element stijl kiezer.

<br/>

<iframe height='500' scrolling='no' title='De stijl kiezer toevoegen' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>de stijl kiezer toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kaartbesturingselementen toevoegen](map-add-controls.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)
