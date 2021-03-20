---
title: Teken hulpprogramma's, interactie typen en toetsenbord snel toetsen op kaart | Microsoft Azure kaarten
description: Vormen tekenen en bewerken met behulp van een muis, een aanraak scherm of een toetsen bord in de Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895814"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interactie typen en sneltoetsen in de module teken hulpprogramma's

In dit artikel vindt u een overzicht van de verschillende manieren om shapes te tekenen en bewerken met behulp van een muis, een aanraak scherm of sneltoetsen.

De tekening beheerder ondersteunt drie verschillende manieren van interactie met de kaart, om vormen te tekenen.

* `click` -Coördinaten worden toegevoegd wanneer er met de muis of het aanraken wordt geklikt.
* `freehand ` -Coördinaten worden toegevoegd wanneer de muis of het aanraak scherm op de kaart wordt gesleept.
* `hybrid` -Coördinaten worden toegevoegd wanneer de muis of het aanraken wordt geklikt of gesleept.

## <a name="how-to-draw-shapes"></a>Vormen tekenen

 Voordat een shape kan worden getekend, stelt `drawingMode` u de optie van de tekening manager in op een ondersteunde teken instelling. Deze instelling kan worden geprogrammeerd of opgeroepen door op een van de teken knoppen op de werk balk te drukken. De teken modus blijft ingeschakeld, zelfs nadat een vorm is getekend, waardoor het gemakkelijk is om extra vormen van hetzelfde type te tekenen. De teken modus programmatisch instellen op niet-actieve status. Of schakel over naar een niet-actieve status door te klikken op de knop huidige teken modi op de werk balk.

De volgende secties bevatten een overzicht van de verschillende manieren waarop vormen kunnen worden getekend op de kaart.

### <a name="how-to-draw-a-point"></a>Een punt tekenen

Wanneer het tekening beheer zich in de teken modus bevindt `draw-point` , kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen. Deze methoden werken met alle interactie modi.

**Tekenen starten**
 - Klik op de linkermuisknop of druk op de kaart om een punt aan de kaart toe te voegen. 
 - Als de muis zich boven de kaart bevindt, drukt u op de `F` toets en wordt er een punt toegevoegd aan de coördinaat van de muis aanwijzer. Deze methode biedt een hogere nauw keurigheid voor het toevoegen van een punt aan de kaart. Er is minder beweging aan de muis als gevolg van het drukken op beweging van de linkermuisknop.
 - Blijf klikken, raken of ingedrukt `F` om meer punten aan de kaart toe te voegen.
 
**Tekenen volt ooien**
 - Klik op een knop op de werk balk tekenen. 
 - De teken modus programmatisch instellen. 
 - Druk op de `C` toets.

**Tekenen annuleren**
 - Druk op de `Escape` toets.

### <a name="how-to-draw-a-line"></a>Een lijn tekenen

Wanneer de teken beheer `draw-line` modus actief is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactie modus.

**Tekenen starten**
 - Klik modus
   * Klik op de linkermuisknop of druk op de kaart om elk punt van een regel op de kaart toe te voegen. Een coördinaat wordt toegevoegd aan de regel voor elke klik of aanraking. 
   * Als de muis zich boven de kaart bevindt, drukt u op de `F` toets en wordt er een punt toegevoegd aan de coördinaat van de muis aanwijzer. Deze methode biedt een hogere nauw keurigheid voor het toevoegen van een punt aan de kaart. Er is minder beweging aan de muis als gevolg van het drukken op beweging van de linkermuisknop.
   * Blijf klikken totdat alle gewenste punten aan de lijn zijn toegevoegd.
 - Free hand-modus
   * Houd de linkermuisknop ingedrukt of vervolg keuzelijst op de kaart en sleep de muis of het aanraak punt rond. Coördinaten worden toegevoegd aan de regel als de muis of het aanraak punt over de kaart wordt verplaatst. Zodra de muis of het aanraak gebeurtenis wordt geactiveerd, wordt de tekening voltooid. De frequentie waarmee coördinaten worden toegevoegd, wordt gedefinieerd door de optie teken beheer `freehandInterval` .
 - Hybride modus
   * Scha kelen tussen de methoden Click en Free hand, indien gewenst, tijdens het tekenen van één regel. Klik bijvoorbeeld op een paar punten, houd de muis knop ingedrukt en sleep om een aantal punten toe te voegen en klik vervolgens op enkele andere. 

**Tekenen volt ooien**
 - Hybride modus/klik
   * Dubbel klik op de kaart op het laatste punt. 
   * Klik op een knop op de werk balk tekenen. 
   * De teken modus programmatisch instellen. 
 - Free hand-modus
   * Laat de muis knop of het aanraak punt los.
 - Druk op de `C` toets.

**Tekenen annuleren**
 - Druk op de `Escape` toets.

### <a name="how-to-draw-a-polygon"></a>Een veelhoek tekenen

Wanneer de teken beheer `draw-polygon` modus actief is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactie modus.

**Tekenen starten**
 - Klik modus
   * Klik op de linkermuisknop of druk op de kaart om elk punt van een veelhoek op de kaart toe te voegen. Er wordt voor elke klik of aanraking een coördinaat toegevoegd aan de veelhoek. 
   * Als de muis zich boven de kaart bevindt, drukt u op de `F` toets en wordt er een punt toegevoegd aan de coördinaat van de muis aanwijzer. Deze methode biedt een hogere nauw keurigheid voor het toevoegen van een punt aan de kaart. Er is minder beweging aan de muis als gevolg van het drukken op beweging van de linkermuisknop.
   * Blijf klikken totdat alle gewenste punten aan de veelhoek zijn toegevoegd.
 - Free hand-modus
   * Houd de linkermuisknop ingedrukt of vervolg keuzelijst op de kaart en sleep de muis of het aanraak punt rond. Coördinaten worden toegevoegd aan de veelhoek als de muis of het aanraak punt zich verplaatst rond de kaart. Zodra de muis of het aanraak gebeurtenis wordt geactiveerd, wordt de tekening voltooid. De frequentie waarmee coördinaten worden toegevoegd, wordt gedefinieerd door de optie teken beheer `freehandInterval` .
 - Hybride modus
   * Scha kelen tussen de methoden Click en Free hand, indien gewenst, terwijl u één veelhoek tekent. Klik bijvoorbeeld op een paar punten, houd de muis knop ingedrukt en sleep om een aantal punten toe te voegen en klik vervolgens op enkele andere. 

**Tekenen volt ooien**
 - Hybride modus/klik
   * Dubbel klik op de kaart op het laatste punt. 
   * Klik op het eerste punt in de veelhoek.
   * Klik op een knop op de werk balk tekenen. 
   * De teken modus programmatisch instellen. 
 - Free hand-modus
   * Laat de muis knop of het aanraak punt los.
 - Druk op de `C` toets.

**Tekenen annuleren**
 - Druk op de `Escape` toets.

### <a name="how-to-draw-a-rectangle"></a>Een rechthoek tekenen

Wanneer de teken beheer `draw-rectangle` modus actief is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactie modus. De gegenereerde vorm volgt de [uitgebreide GEOjson-specificatie voor rechthoeken](extend-geojson.md#rectangle).

**Tekenen starten**
 - Houd de linkermuisknop ingedrukt, of druk op de kaart om de eerste hoek van de rechthoek toe te voegen en sleep om de rechthoek te maken. 

**Tekenen volt ooien**
 - Laat de muis knop of het aanraak punt los.
 - De teken modus programmatisch instellen. 
 - Druk op de `C` toets.

**Tekenen annuleren**
 - Druk op de `Escape` toets.

### <a name="how-to-draw-a-circle"></a>Een cirkel tekenen

Wanneer de teken beheer `draw-circle` modus actief is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactie modus. De gegenereerde vorm volgt de [uitgebreide GEOjson-specificatie voor cirkels](extend-geojson.md#circle).

**Tekenen starten**
 - Houd de linkermuisknop ingedrukt, of druk op de kaart om het midden van de cirkel toe te voegen en sleep de cirkels een straal geven. 

**Tekenen volt ooien**
 - Laat de muis knop of het aanraak punt los.
 - De teken modus programmatisch instellen. 
 - Druk op de `C` toets.

**Tekenen annuleren**
 - Druk op de `Escape` toets.

## <a name="keyboard-shortcuts"></a>Sneltoetsen

De teken hulpprogramma's ondersteunen sneltoetsen. Deze sneltoetsen werken als de kaart zich in de focus bevindt.

| Sleutel      | Bewerking                            |
|----------|-----------------------------------|
| `C` | Hiermee wordt de actieve tekening voltooid en wordt de teken modus ingesteld op inactief. De focus wordt verplaatst naar het map-element op het hoogste niveau.  |
| `Escape` | Hiermee wordt de actieve tekening geannuleerd en wordt de teken modus ingesteld op inactief. De focus wordt verplaatst naar het map-element op het hoogste niveau.  |
| `F` | Voegt een coördinaat toe aan een punt, lijn of veelhoek als de muis zich boven de kaart bevindt. Equivalente actie van klikken op de kaart wanneer u klikt of op de hybride modus. Met deze snelkoppeling kunnen nauw keurige en snellere tekeningen worden uitgevoerd. U kunt één hand gebruiken om de muis te positioneren en een andere om op de knop te drukken zonder de muis van de penbeweging voor drukken te verplaatsen. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen in de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Werk balk tekenen](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)