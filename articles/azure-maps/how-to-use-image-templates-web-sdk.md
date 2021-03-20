---
title: Afbeeldings sjablonen in de Azure Maps Web-SDK | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van afbeeldings pictogrammen en met patronen opgevulde veelhoeken met behulp van de Azure Maps Web-SDK. Bekijk de beschik bare afbeeldings-en opvul patroon sjablonen.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895678"
---
# <a name="how-to-use-image-templates"></a>Afbeeldingssjablonen gebruiken

Afbeeldingen kunnen worden gebruikt met HTML-markeringen en verschillende lagen in de Azure Maps Web-SDK:

 - Met symbool lagen kunnen punten op de kaart worden weer gegeven met een afbeeldings pictogram. Symbolen kunnen ook worden weer gegeven langs een pad naar een regel.
 - Veelhoek lagen kunnen worden weer gegeven met een opvul patroon afbeelding. 
 - HTML-markeringen kunnen punten weer geven met behulp van afbeeldingen en andere HTML-elementen.

Om te zorgen voor goede prestaties met lagen, laadt u de afbeeldingen in de kaart afbeelding sprite resource vóór rendering. De [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions), van de SymbolLayer, laadt standaard een aantal markerings afbeeldingen in een aantal kleuren in de kaart afbeelding sprite. Deze markerings afbeeldingen en meer zijn beschikbaar als SVG-sjablonen. Ze kunnen worden gebruikt voor het maken van installatie kopieën met aangepaste schalen of voor het gebruik van de primaire en secundaire kleur van klanten. In totaal zijn er 42 afbeeldings sjablonen beschikbaar: 27 symbool pictogrammen en 15 veelhoek patronen.

Afbeeldings sjablonen kunnen worden toegevoegd aan de kaart installatie kopie sprite-resources met behulp van de `map.imageSprite.createFromTemplate` functie. Met deze functie kunnen Maxi maal vijf para meters worden door gegeven in;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

De `id` is een unieke id die u maakt. De `id` wordt toegewezen aan de installatie kopie wanneer deze wordt toegevoegd aan de Maps-installatie kopie sprite. Gebruik deze id in de lagen om op te geven welke afbeeldings bron moet worden weer gegeven. `templateName`Hiermee geeft u de afbeeldings sjabloon op die moet worden gebruikt. `color`Met deze optie stelt u de primaire kleur van de afbeelding in en `secondaryColor` met de opties stelt u de secundaire kleur van de afbeelding in. `scale`Met de optie wordt de afbeeldings sjabloon geschaald voordat deze op de afbeelding Sprite wordt toegepast. Wanneer de installatie kopie wordt toegepast op de afbeelding sprite, wordt deze omgezet in een PNG-bestand. Voor een scherpe rendering is het beter om de afbeeldings sjabloon omhoog te schalen voordat u deze toevoegt aan de sprite, dan om deze te schalen in een laag.

Met deze functie wordt de afbeelding asynchroon geladen in de afbeelding sprite. Daarom wordt er een Promise geretourneerd die u kunt wachten tot deze functie is voltooid.

De volgende code laat zien hoe u een installatie kopie maakt van een van de ingebouwde sjablonen en deze kunt gebruiken met een symbool laag.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Een afbeeldings sjabloon met een symbool laag gebruiken

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven als een symbool in een symbool-laag door te verwijzen naar de bron-ID van de installatie kopie in de `image` optie van `iconOptions` .

In het volgende voor beeld wordt een symbool laag weer gegeven met behulp van de `marker-flat` afbeeldings sjabloon met een primaire groen kleur en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbool laag met ingebouwde pictogram sjabloon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de laag met het pictogram voor het Pendikte door Azure Maps () in de <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>sjabloon met ingebouwde pictogrammen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Een afbeeldings sjabloon gebruiken langs een pad naar een regel

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven langs het pad van een regel door een Lines Tring toe te voegen aan een gegevens bron en een Symbol-laag met een optie te gebruiken `lineSpacing` en door te verwijzen naar de id van de afbeeldings bron in de `image` optie van th `iconOptions` . 

In het volgende voor beeld wordt een roze lijn op de kaart weer gegeven en wordt een symbool laag gebruikt met de `car` afbeeldings sjabloon met een blauw primaire kleur van Dodger en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijn laag met ingebouwd pictogram sjabloon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>laag met de ingebouwde pictogram sjabloon met</a> behulp van Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>().
</iframe>

> [!TIP]
> Als de afbeeldings sjabloon omhoog wijst, stelt `rotation` u de pictogram optie van de laag symbool in op 90 als u wilt dat deze in dezelfde richting als de lijn moet wijzen.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Een afbeeldings sjabloon met een polygoon laag gebruiken

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven als een opvul patroon in een polygoon laag door te verwijzen naar de bron-ID van de installatie kopie in de `fillPattern` optie van de laag.

In het volgende voor beeld wordt een polygoon laag weer gegeven met behulp `dot` van de afbeeldings sjabloon met een rode primaire kleur en een transparante secundaire kleur.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Veelhoek vullen met ingebouwd pictogram sjabloon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>-opvul veelhoek met de ingebouwde pictogram sjabloon</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Het instellen van de secundaire kleur van opvul patronen maakt het gemakkelijker om te zien hoe de onderliggende kaart het primaire patroon blijft leveren. 

## <a name="use-an-image-template-with-an-html-marker"></a>Een afbeeldings sjabloon gebruiken met een HTML-markering

U kunt een afbeeldings sjabloon ophalen met behulp `altas.getImageTemplate` van de functie en gebruiken als de inhoud van een HTML-markering. De sjabloon kan worden door gegeven in de `htmlContent` optie van de markering en vervolgens worden aangepast met `color` de `secondaryColor` Opties, en `text` .

In het volgende voor beeld wordt de `marker-arrow` sjabloon gebruikt met een rode primaire kleur, een roze secundaire kleur en een tekst waarde van "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-markering met ingebouwde pictogram sjabloon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML-markering van de pen met ingebouwde pictogram sjabloon</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Afbeeldings sjablonen kunnen ook worden gebruikt buiten de kaart. De getImageTemplate funciton retourneert een SVG-teken reeks met tijdelijke aanduidingen. `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Vervang deze waarden voor tijdelijke aanduidingen om een geldige SVG-teken reeks te maken. U kunt de SVG-teken reeks vervolgens rechtstreeks toevoegen aan het HTML DOM of deze converteren naar een gegevens-URI en invoegen in een afbeeldings code. Bijvoorbeeld:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Aangepaste herbruikbare sjablonen maken

Als uw toepassing gebruikmaakt van hetzelfde pictogram met verschillende pictogrammen of als u een module maakt waarmee extra afbeeldings sjablonen worden toegevoegd, kunt u deze pictogrammen eenvoudig toevoegen en ophalen uit de Azure Maps Web-SDK. Gebruik de volgende statische functies in de `atlas` naam ruimte.

| Name | Retour type | Beschrijving | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Hiermee voegt u een aangepaste SVG-afbeeldings sjabloon toe aan de Atlas-naam ruimte. |
|  `getImageTemplate(templateName: string, scale?: number)`| tekenreeks | Hiermee wordt een SVG-sjabloon met de naam opgehaald. |
| `getAllImageTemplateNames()` | teken reeks [] |  Hiermee wordt een SVG-sjabloon met de naam opgehaald. |

SVG-afbeeldings sjablonen ondersteunen de volgende waarden voor de tijdelijke aanduiding:

| Tijdelijke aanduiding | Beschrijving |
|-|-|
| `{color}` | De primaire kleur. | 
| `{secondaryColor}` | De secundaire kleur. | 
| `{scale}` | De SVG-afbeelding wordt geconverteerd naar een PNG-afbeelding wanneer deze wordt toegevoegd aan de kaart afbeelding sprite. Deze tijdelijke aanduiding kan worden gebruikt om een sjabloon te schalen voordat deze wordt geconverteerd om ervoor te zorgen dat deze duidelijk wordt weer gegeven. | 
| `{text}` | De locatie voor het weer geven van tekst wanneer deze wordt gebruikt in combi natie met een HTML-markering. |

In het volgende voor beeld ziet u hoe u een SVG-sjabloon maakt en hoe u deze toevoegt aan de Azure Maps Web-SDK als een pictogram sjabloon die opnieuw kan worden gebruikt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pictogram sjabloon toevoegen aan de Atlas-naam ruimte" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>aangepaste pictogram sjabloon pen toevoegen aan de Atlas-naam ruimte</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lijst met afbeeldings sjablonen

Deze tabel bevat alle afbeeldings sjablonen die momenteel beschikbaar zijn in de websdk van Azure Maps. De naam van de sjabloon bevindt zich boven elke afbeelding. Standaard is de primaire kleur blauw en de secundaire kleur wit. Om ervoor te zorgen dat de secundaire kleur gemakkelijker te zien is op een witte achtergrond, hebben de volgende afbeeldingen de secundaire kleur ingesteld op zwart.

**Symbool pictogram sjablonen**

:::row:::
   :::column span="":::
      tijdlijnmarkeerteken
   :::column-end:::
   :::column span="":::
      markering-dik
   :::column-end:::
   :::column span="":::
      markering-cirkel
   :::column-end:::
   :::column span="":::
      markering-plat
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![markerings pictogram](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![markering-dik pictogram](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![markering: cirkel pictogram](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![markering-vlak pictogram](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      markering-vier kant
   :::column-end:::
   :::column span="":::
      markering-vier kant-cluster
   :::column-end:::
   :::column span="":::
      markering-pijl
   :::column-end:::
   :::column span="":::
      markering-bal-PIN
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram markering-vier kant](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![markering-vier kant-cluster pictogram](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![pictogram voor markering-pijl](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![markering-bal-PIN-pictogram](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      markering-vier kant rond
   :::column-end:::
   :::column span="":::
      markering-vier kant-afgerond-cluster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      vlag: drie hoek
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![markering: vier kant pictogram](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![markering-vier kant-afgerond-cluster pictogram](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![vlag pictogram](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![pictogram voor de vlag-drie hoek](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      driehoek
   :::column-end:::
   :::column span="":::
      drie hoek: dik
   :::column-end:::
   :::column span="":::
      drie hoek omlaag
   :::column-end:::
   :::column span="":::
      drie hoekje-pijl-links
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram drie hoek](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![drie hoek-dik pictogram](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![pictogram met drie hoek omlaag](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![pictogram met drie hoekje-pijl-links](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zeshoek
   :::column-end:::
   :::column span="":::
      zeshoek-dik
   :::column-end:::
   :::column span="":::
      zeshoek-afgerond
   :::column-end:::
   :::column span="":::
      zeshoek-afgerond-dik
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram van zeshoek](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![pictogram van zeshoek-dik](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![pictogram met zeshoek afgerond](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![pictogram van zeshoek-afgerond-dik](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      vastmaken
   :::column-end:::
   :::column span="":::
      vastmaken en afronden
   :::column-end:::
   :::column span="":::
      afgerond vier kant
   :::column-end:::
   :::column span="":::
      afgerond-vier kant-dik
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Speldpictogram](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![pictogram voor een afronding](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![pictogram afgerond op vier kant](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![pictogram rond afgerond-vier kant](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      pijl-omhoog
   :::column-end:::
   :::column span="":::
      pijl-omhoog-dun
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram pijl omhoog](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![pijl-omhoog-dun pictogram](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![pictogram auto](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Opvul patroon Sjablonen voor veelhoek**

:::row:::
   :::column span="":::
      blok
   :::column-end:::
   :::column span="":::
      controle-gedraaid
   :::column-end:::
   :::column span="":::
      kring
   :::column-end:::
   :::column span="":::
      cirkels-gespatieerd
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram van de controle](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![pictogram van de controle functie](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![pictogram cirkels](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![cirkels pictogram](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonaal omhoog
   :::column-end:::
   :::column span="":::
      diagonale lijnen-omlaag
   :::column-end:::
   :::column span="":::
      diagonale strepen-omhoog
   :::column-end:::
   :::column span="":::
      diagonale strepen-omlaag
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram voor diagonale lijnen](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![pictogram diagonale lijnen/omlaag](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![pictogram met diagonale strepen naar boven](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![pictogram met diagonale strepen-omlaag](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      raster lijnen
   :::column-end:::
   :::column span="":::
      gedraaid-raster-lijnen
   :::column-end:::
   :::column span="":::
      gedraaid-raster-stroken
   :::column-end:::
   :::column span="":::
      x-Fill
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pictogram raster lijnen](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![pictogram voor gedraaid raster-lijnen](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![pictogram voor gedraaid raster-strepen](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![pictogram x-opvul](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zigzag
   :::column-end:::
   :::column span="":::
      zigzag-zigzag-Vertical
   :::column-end:::
   :::column span="":::
      wijzen
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![zigzag-/-pictogram](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![het pictogram zigzag-zigzag-Vertical](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![pictogram punten](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Vooraf geladen afbeeldings pictogrammen**

Met de kaart wordt een set pictogrammen geladen in de Maps-installatie kopie sprite met `marker` de `pin` sjablonen, en `pin-round` . De namen van deze pictogrammen en hun kleur waarden worden weer gegeven in de volgende tabel.

| pictogram naam | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Hulp programma nu uitproberen

Met het volgende hulp programma kunt u de verschillende ingebouwde afbeeldings sjablonen op verschillende manieren weer geven en de primaire en secundaire kleuren en schaal aanpassen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opties voor pictogram sjablonen" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>sjabloon opties</a> voor het pictogram van de Pen op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas-naam ruimte](/javascript/api/azure-maps-control/atlas#functions
)

Raadpleeg de volgende artikelen voor meer code voorbeelden waarbij afbeeldings sjablonen kunnen worden gebruikt:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-makers toevoegen](map-add-bubble-layer.md)