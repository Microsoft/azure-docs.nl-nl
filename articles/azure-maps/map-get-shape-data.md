---
title: Gegevens ophalen van shapes op een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u shapegegevens kunt ophalen die op een kaart zijn getekend met behulp van de Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890697"
---
# <a name="get-shape-data"></a>Vormgegevens ophalen

Dit artikel laat u zien hoe u gegevens kunt ophalen van shapes die op de kaart worden getekend. We gebruiken de functie **drawingManager. getSource ()** in [Draw Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Er zijn verschillende scenario's waarin u de geojson-gegevens van een getekende vorm wilt extra heren en deze elders kunt gebruiken.  


## <a name="get-data-from-drawn-shape"></a>Gegevens ophalen van een teken vorm

De volgende functie haalt de bron gegevens van de getekende vorm op en voert deze uit naar het scherm. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Hieronder ziet u het volledige programma voor het uitvoeren van code, waarin u een vorm kunt tekenen om de functionaliteit te testen:

<br/>

<iframe height="686" title="Vormgegevens ophalen" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get Shape Data</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Reageren op tekengebeurtenissen](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interactietypen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Werk balk tekenen](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)