---
title: Wat zijn lagen in de Azure Maps Power BI Visual | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over de verschillende lagen die beschikbaar zijn in de Microsoft Azure kaarten Visual voor Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261717"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Wat zijn lagen in de Azure Maps Power BI Visual?

Er zijn twee soorten lagen beschikbaar in het Azure Maps-visueel element. Het eerste type richt zich op het renderen van gegevens die worden door gegeven in het deel venster **velden** van het visuele element en die bestaat uit de volgende lagen. deze gegevens kunnen nu worden aangeroepen.

:::row:::
    :::column span="":::
        **Bellenlaag**

        Hiermee worden punten weer gegeven als geschaalde cirkels op de kaart.

        ![Bubble Layer op kaart](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Laag van staaf diagram**

        Hiermee worden punten weer gegeven als 3D-balken op de kaart.
        
        ![Staafdiagram laag op kaart](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Met het tweede type laag worden toegevoegde externe bronnen met gegevens gekoppeld aan meer context en bestaat uit de volgende lagen.

:::row:::
    :::column span="":::
        **Referentielaag**

        Een geüpload geojson-bestand boven op de kaart bedekken.

        ![Referentielaag op kaart](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Laag van Tegel**

        Bedekking van een aangepaste tegel laag boven op de kaart.
        
        ![Laag van Tegel op kaart](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Traffic Layer**

        Informatie over realtime verkeer op de kaart bedekken.
        
        ![Traffic Layer op kaart](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Alle gegevens weergave lagen, evenals de laag van de **tegel**, hebben opties voor minimale en maximale zoom niveaus die worden gebruikt om een zoom niveau bereik op te geven deze lagen moeten worden weer gegeven. Hierdoor kan één type rendering-laag worden gebruikt op één zoom niveau en een overgang naar een andere rendering-laag op een ander zoom niveau.

Deze lagen hebben ook een optie om te worden geplaatst ten opzichte van andere lagen in de kaart. Als er meerdere lagen voor het renderen van gegevens worden gebruikt, bepaalt de volg orde waarin ze worden toegevoegd aan de kaart, hun relatieve laag volgorde wanneer ze dezelfde **laag positie** waarde hebben.

## <a name="general-layer-settings"></a>Algemene laag instellingen

De sectie algemene laag van het deel venster **indeling** zijn algemene instellingen die van toepassing zijn op de lagen die zijn verbonden met de Power bi gegevensset in het deel venster **velden** (Bubble Layer, staaf diagram).

| Instelling     | Beschrijving   |
|-------------|---------------|
| Niet-geselecteerde transparantie | De transparantie van shapes die niet zijn geselecteerd wanneer een of meer shapes zijn geselecteerd.  |
| Nullen weer geven              | Geeft aan of punten met een grootte waarde van nul moeten worden weer gegeven op de kaart met de minimale radius. |
| Negatieven weer geven          | Hiermee wordt aangegeven of absolute waarde van negatieve grootte waarden moet worden getekend.   |
| Minimale gegevens waarde          | De minimale waarde van de invoer gegevens die moeten worden geschaald. Goed voor uitschieters.  |
| Maximale gegevens waarde          | De maximum waarde van de invoer gegevens die moeten worden geschaald. Goed voor uitschieters.  |

## <a name="next-steps"></a>Volgende stappen

Wijzigen hoe uw gegevens op de kaart worden weer gegeven:

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een laag met een staafdiagram toevoegen](power-bi-visual-add-bar-chart-layer.md)

Meer context toevoegen aan de kaart:

> [!div class="nextstepaction"]
> [Een referentielaag toevoegen](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Realtime verkeer weergeven](power-bi-visual-show-real-time-traffic.md)
