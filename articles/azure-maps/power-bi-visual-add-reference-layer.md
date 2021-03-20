---
title: Een referentielaag toevoegen aan de Azure Maps Power BI Visual | Microsoft Azure kaarten
description: In dit artikel wordt beschreven hoe u de referentielaag kunt gebruiken in de Visual Microsoft Azure Maps voor Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261757"
---
# <a name="add-a-reference-layer"></a>Een referentielaag toevoegen

Met de functie voor de referentie laag kunt u een secundaire ruimtelijke gegevensset uploaden naar het visuele element en op de kaart neerzetten om de context toe te voegen. Deze gegevensset wordt gehost door Power BI en moet een [GEOjson-bestand](https://wikipedia.org/wiki/GeoJSON) met een `.json` of `.geojson` bestands extensie zijn.

Als u een **geojson** -bestand als een referentielaag wilt toevoegen, gaat u naar het deel venster **opmaak** , vouwt u **de sectie referentielaag** uit en drukt u op de knop **+ lokaal bestand toevoegen** .

Nadat een geojson-bestand is toegevoegd aan de referentielaag, wordt de naam van het bestand weer gegeven in plaats van de knop voor het **lokale bestand toevoegen** met een **X** ernaast. Druk op de knop **X** om de gegevens uit het visuele element te verwijderen en verwijder het geojson-bestand uit Power bi.

In het volgende overzicht worden de [2016-tellings trekkers voor Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), gekleurd op populatie, weer gegeven.

> [!div class="mx-imgBorder"]
> ![Een kaart met 2016-aftellings trekkers voor Colorado, gekleurd op populatie als een referentielaag](media/power-bi-visual/reference-layer-CO-census-tract.png)

Hieronder ziet u alle instellingen in het deel venster **indeling** die beschikbaar zijn in de sectie **referentielaag** .

| Instelling              | Beschrijving   |
|----------------------|---------------|
| Referentie laag gegevens | Het gegevens geojson-bestand dat naar het visuele element moet worden geüpload als een extra laag binnen de kaart. Met de knop voor het toevoegen van een **lokaal bestand** wordt een dialoog venster geopend waarmee de gebruiker een geojson-bestand met een `.json` of `.geojson` bestands extensie kan selecteren. |

> [!NOTE]
> In deze preview van de Azure Maps Visual worden met de referentielaag alleen de eerste 5.000 vorm functies geladen in de kaart. Deze limiet wordt verhoogd in een toekomstige update.

## <a name="styling-data-in-a-reference-layer"></a>Gegevens in een referentielaag opwaarderen

Eigenschappen kunnen worden toegevoegd aan elke functie binnen het geojson-bestand om de manier waarop deze op de kaart wordt opgemaakt, aan te passen. Deze functie maakt gebruik van de functie voor eenvoudige gegevens lagen in de Azure Maps Web-SDK. Zie dit document over [ondersteunde stijl eigenschappen](spatial-io-add-simple-data-layer.md#default-supported-style-properties)voor meer informatie. Aangepaste pictogram afbeeldingen worden niet ondersteund in het Azure Maps visuele element als beveiligings maatregel.

Hier volgt een voor beeld van een geojson Point-functie waarmee de weer gegeven kleur wordt ingesteld op rood.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Volgende stappen

Meer context toevoegen aan de kaart:

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Realtime verkeer weergeven](power-bi-visual-show-real-time-traffic.md)
