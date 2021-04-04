---
title: Een tekenlaag toevoegen aan de Azure Maps Power BI Visual | Microsoft Azure kaarten
description: In dit artikel leert u hoe u de Bubble laag kunt gebruiken in de Microsoft Azure kaarten Visual voor Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896222"
---
# <a name="add-a-bubble-layer"></a>Een bubbellaag toevoegen

De **Bubble slaag** geeft locatie gegevens weer als geschaalde cirkels op de kaart.

> [!div class="mx-imgBorder"]
> ![Een kaart waarin punt gegevens worden weer gegeven met behulp van de Bubble Layer](media/power-bi-visual/bubble-layer-with-legend-color.png)

In eerste instantie hebben alle bellen dezelfde opvul kleur. Als een veld wordt door gegeven aan de Bucket van de **legenda** van het deel venster **velden** , worden de bellen gekleurd op basis van de categorisatie. Het overzicht van de bellen is wit standaard, maar kan worden gewijzigd in een nieuwe kleur of door de contour optie met hoog contrast in te scha kelen. Met de optie **Contour met hoog contrast** wordt dynamisch een contour kleur toegewezen die een variant met een hoog contrast van de opvul kleur is. Zo kunt u ervoor zorgen dat de bellen duidelijk zichtbaar zijn, ongeacht de stijl van de kaart. Hieronder vindt u de primaire instellingen in het deel venster **indeling** die beschikbaar zijn in de sectie **Bubble Layer** .

| Instelling               | Beschrijving    |
|-----------------------|----------------|
| Grootte                  | De grootte van elke bel. Deze optie is verborgen wanneer een veld wordt door gegeven aan de Bucket **grootte** van het deel venster **velden** . Aanvullende opties worden weer gegeven, zoals beschreven in het onderwerp [schaal aanpassen](#bubble-size-scaling) in de belgrootte verderop in dit artikel. |
| Opvulkleur            | Kleur van elke bel. Deze optie is verborgen wanneer een veld wordt door gegeven aan de Bucket **legenda** van het deel venster **velden** en een afzonderlijke **gegevens kleur** sectie wordt weer gegeven in het deel venster **opmaak** . |
| Transparantie van opvulling     | Transparantie van elke bel. |
| Overzicht met hoog contrast | Hiermee maakt u het contrast van de contour kleur met de opvul kleur voor betere toegankelijkheid door gebruik te maken van een variant met een hoog contrast van de opvul kleur. |
| Kleur van contour         | Kleur die de Bel omlijnt. Deze optie is verborgen wanneer de optie **Contour met hoog contrast** is ingeschakeld. |
| Transparantie van overzicht  | Transparantie van het overzicht. |
| Contour breedte         | De breedte van het overzicht in pixels. |
| Vervaagd                  | De hoeveelheid vervaging die wordt toegepast op het overzicht. De waarde 1 vervaagt de bellen zodanig dat alleen het middel punt geen transparantie heeft. Met de waarde 0 wordt een vervagend effect toegepast. |
| Uitlijning verkopen       | Hiermee wordt aangegeven hoe de bellen eruitzien wanneer de kaart wordt weer geven. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• View Port-bellen worden weer gegeven op de rand van de kaart ten opzichte van View Port. (standaard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Kaart-bellen zijn plat gerenderd op het Opper vlak van de kaart. |
| Zoom schaal            | De hoogte van de bellen ten opzichte van het zoom niveau. Een zoom schaal van één betekent dat deze niet kan worden geschaald. Bij grote waarden worden bellen kleiner wanneer ze zijn ingezoomd en groter wanneer er wordt ingezoomd. Zo kunt u het niveau van de kaart verkleinen tijdens het uitzoomen, maar zorgt u ervoor dat punten groter worden tijdens het inzoomen. Met de waarde 1 wordt geen schaaling toegepast. |
| Min. zoom niveau              | De tegels met mini maal zoom niveau zijn beschikbaar. |
| Maxi maal zoom niveau              | Er zijn maximale zoom niveau tegels beschikbaar. |
| Laag positie        | Geeft de positie van de laag aan ten opzichte van andere kaart lagen. |

## <a name="bubble-size-scaling"></a>Grootte van belgrootte aanpassen

Als een veld wordt door gegeven aan de Bucket **grootte** van het deel venster **velden** , worden de bellen relatief geschaald op basis van de meting waarde van elk gegevens punt. De optie **grootte** in de **laag bellen** van het deel venster **opmaak** verdwijnt wanneer een veld wordt door gegeven aan de Bucket **grootte** , omdat de stralen worden geschaald tussen een minimum-en maximum waarde. De volgende opties worden weer gegeven in de sectie **Bubble Layer** van het deel venster **indeling** wanneer er een veld in een Bucket **grootte** is opgegeven.

| Instelling             | Beschrijving  |
|---------------------|--------------|
| Minimale grootte            | Minimale belgrootte bij het schalen van de gegevens.|
| Maximale grootte            | De maximale belgrootte bij het schalen van de gegevens.|
| Schaal methode voor grootte | Schaal algoritme dat wordt gebruikt om de relatieve belgrootte te bepalen.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Lineair bereik van invoer gegevens die lineair zijn toegewezen aan de minimum-en maximum grootte. (standaard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Het logboek bereik van invoer gegevens logarithmically toegewezen aan de minimum-en maximum grootte.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier: Geef de waarden x1, y1, x2, y2 van een Cubic-Bezier curve op om een aangepaste schaal methode te maken. |

Wanneer de **methode** voor het schalen van de grootte is ingesteld op **logboek**, worden de volgende opties beschikbaar gesteld.

| Instelling   | Beschrijving      |
|-----------|------------------|
| Logaritmische schaal | De logaritmische schaal die moet worden toegepast bij het berekenen van de grootte van de bellen. |

Wanneer de **methode** voor het schalen van de grootte is ingesteld op **kubieke Bezier**, worden de volgende opties beschikbaar gesteld voor het aanpassen van de schaal curve.

| Instelling | Beschrijving                           |
|---------|---------------------------------------|
| X1      | X1-para meter van een kubieke Bézier-curve. |
| Y1      | X2-para meter van een kubieke Bézier-curve. |
| X2      | De Y1-para meter van een kubieke Bézier-curve. |
| Y2      | Y2-para meter van een kubieke Bézier-curve. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) beschikt over een handig hulp middel voor het maken van de para meters voor Cubic-Bezier curven.

## <a name="next-steps"></a>Volgende stappen

Wijzigen hoe uw gegevens op de kaart worden weer gegeven:

> [!div class="nextstepaction"]
> [Een laag met een staafdiagram toevoegen](power-bi-visual-add-bar-chart-layer.md)

Meer context toevoegen aan de kaart:

> [!div class="nextstepaction"]
> [Een referentielaag toevoegen](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Realtime verkeer weergeven](power-bi-visual-show-real-time-traffic.md)

De Visual aanpassen:

> [!div class="nextstepaction"]
> [Tips en trucs voor het gebruik van opmaak in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Titels, legenda's en achtergronden van visualisaties aanpassen](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)