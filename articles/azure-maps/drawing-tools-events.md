---
title: Gebeurtenissen voor het tekenen van gereedschappen | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een werk balk tekenen aan een kaart kunt toevoegen met behulp van Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089340"
---
# <a name="drawing-tool-events"></a>Gebeurtenissen voor het tekenen van gereedschappen

Wanneer u teken hulpprogramma's op een kaart gebruikt, is het handig om te reageren op bepaalde gebeurtenissen wanneer de gebruiker op de kaart tekent. Deze tabel geeft een lijst van alle gebeurtenissen die door de klasse worden ondersteund `DrawingManager` .

| Gebeurtenis | Description |
|-------|-------------|
| `drawingchanged` | Deze gebeurtenis wordt gestart wanneer een coördinaat in een vorm is toegevoegd of gewijzigd. | 
| `drawingchanging` | Deze gebeurtenis wordt gestart wanneer een voor beeld-coördinaat voor een vorm wordt weer gegeven. Deze gebeurtenis wordt bijvoorbeeld meerdere keren geactiveerd wanneer een coördinaat wordt gesleept. | 
| `drawingcomplete` | Deze gebeurtenis wordt gestart wanneer een vorm is getekend of uit de bewerkings modus is gehaald. |
| `drawingmodechanged` | Deze gebeurtenis wordt gestart wanneer de teken modus is gewijzigd. De nieuwe teken modus wordt door gegeven aan de gebeurtenis-handler. |
| `drawingstarted` | Deze gebeurtenis wordt gestart wanneer de gebruiker een vorm tekent of een vorm in de bewerkings modus plaatst.  |

De volgende code laat zien hoe de gebeurtenissen in de module teken Hulpprogramma's werken. Teken vormen op de kaart en kijk wanneer de gebeurtenissen worden gestart.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenen hulpprogram ma's gebeurtenissen" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de gebeurtenissen van de pen <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>tekenen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Voorbeelden

Laten we eens kijken naar enkele veelvoorkomende scenario's die gebruikmaken van de teken hulpprogramma's gebeurtenissen.

### <a name="select-points-in-polygon-area"></a>Punten in het gebied veelhoek selecteren

Deze code laat zien hoe u een gebeurtenis van een teken vorm van een gebruiker kunt bewaken. Voor dit voor beeld worden met de code shapes van veelhoeken, rechthoeken en cirkels gecontroleerd. Vervolgens wordt bepaald welke gegevens punten op de kaart zich in het teken gebied bevinden. De `drawingcomplete` gebeurtenis wordt gebruikt om de Select Logic te activeren. In de Select Logic worden door de code alle gegevens punten op de kaart door lopen. Er wordt gecontroleerd of er een snij punt is van het punt en het gebied van de getekende vorm. In dit voor beeld wordt het gebruik van de open-source [Turf.js](https://turfjs.org/) bibliotheek gebruikt voor het uitvoeren van een ruimtelijke intersectie berekening.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gegevens selecteren in een getekend veelhoek gebied" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>gegevens selecteren in getekend veelhoek</a> van het gebied Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Tekenen en zoeken in veelhoek gebied

Met deze code zoekt u naar belang stellingen binnen het gebied van een vorm nadat de gebruiker klaar is met het tekenen van de vorm. U kunt de code wijzigen en uitvoeren door te klikken op bewerken op de code pen in de rechter bovenhoek van het kader. De `drawingcomplete` gebeurtenis wordt gebruikt om de zoek logica te activeren. Als de gebruiker een rechthoek of veelhoek tekent, wordt er een zoek opdracht in geometrie uitgevoerd. Als een cirkel wordt getekend, wordt de straal en de positie van het middel punt gebruikt om een zoek opdracht op de interesse uit te voeren. De `drawingmodechanged` gebeurtenis wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar de teken modus en met deze gebeurtenis wordt het teken papier gewist.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenen en zoeken in veelhoek gebied" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>tekenen en zoek in het gebied veelhoek</a> op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Een meet programma maken

De volgende code laat zien hoe de teken gebeurtenissen kunnen worden gebruikt voor het maken van een meet programma. De `drawingchanging` wordt gebruikt voor het bewaken van de vorm, terwijl deze wordt getekend. Wanneer de gebruiker de muis verplaatst, worden de afmetingen van de vorm berekend. De `drawingcomplete` gebeurtenis wordt gebruikt om een uiteindelijke berekening uit te voeren op de vorm nadat deze is getekend. De `drawingmodechanged` gebeurtenis wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar een teken modus. Met de gebeurtenis wordt ook  `drawingmodechanged` het teken papier gewist en de oude meet gegevens gewist.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Meet gereedschap" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>meet hulpprogramma's</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Vormgegevens ophalen](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interactietypen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de module Services:

> [!div class="nextstepaction"]
> [Services-module](how-to-use-services-module.md)

Meer code voorbeelden bekijken:

> [!div class="nextstepaction"]
> [Voorbeeld pagina voor code](https://aka.ms/AzureMapsSamples)
