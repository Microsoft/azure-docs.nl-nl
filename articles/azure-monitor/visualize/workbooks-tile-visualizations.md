---
title: Tegel visualisaties van Azure Monitor werkmap
description: Meer informatie over de tegel visualisaties van Azure Monitor werkmap.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610148"
---
# <a name="tile-visualizations"></a>Tegel visualisaties

Tegels zijn een handige manier om samenvattings gegevens te presen teren in werkmappen. In de onderstaande afbeelding ziet u een algemeen gebruik van tegels met een samen vatting op app-niveau boven op een gedetailleerd raster.

[![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Werkmap tegels bieden ondersteuning voor een titel, subtitel, grote tekst, pictogrammen, kleur overgangen op basis van metrische gegevens, vonk lijnen/staven, voet tekst, enzovoort.

## <a name="adding-a-tile"></a>Een tegel toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Selecteer **toevoegen** en vervolgens *query toevoegen* om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Stel grootte in op **Full**.
6. Stel de visualisatie in op **tegels**.
7. Selecteer de knop **instellingen voor tegels** om het deel venster instellingen te openen.
    1. Stel in *titel*:
        * Kolom gebruiken: `name` .
    2. *Links* instellen:
        * Kolom gebruiken: `Requests` .
        * Kolom weergave: `Big Number` .
        * Kleuren palet: `Green to Red`
        * Minimum waarde: `0` .
    3. *Onder* instellen:
        * Kolom gebruiken: `appName` .
8. Selecteer de knop **opslaan en sluiten** onder aan het deel venster.

[![Scherm opname van de weer gave samen vatting van tegel met de bovenstaande query-en tegel instellingen.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

De tegels in Lees modus:

[![Scherm opname van de weer gave tegel overzicht in Lees modus.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Spark-lijnen in tegels

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Voeg een tijds bereik parameter met de naam toe `TimeRange` .
    1. Selecteer **toevoegen** en *Voeg vervolgens para meters toe*.
    2. Selecteer **para meter toevoegen** in het parameter besturings element.
    3. Voer `TimeRange` in het veld *parameter naam* in en kies `Time range picker` voor het *parameter type*.
    4. Selecteer **Opslaan** boven aan het deel venster en selecteer vervolgens **gereed bewerken** in het parameter besturings element.
3. Selecteer **toevoegen** en vervolgens *query toevoegen* om een besturings element voor een logboek query toe te voegen onder het parameter besturings element.
4. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
5. Gebruik de query-editor om de KQL voor uw analyse in te voeren.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Selecteer **Query uitvoeren**. (Zorg ervoor dat u instelt `TimeRange` op een waarde van uw keuze voordat u de query uitvoert.)
7. Stel de *visualisatie* in op ' tegels '.
8. Stel de *grootte* in op ' volledig '.
9. Selecteer **tegel instellingen**.
    1. In de *tegel* instellen:
        * Kolom gebruiken: `name` .
    2. In *subtegel* instellen:
        *  Kolom gebruiken: `appNAme` .
    3. *Links* instellen:
        *  Kolom gebruiken: `Requests` .
        * Kolom weergave: `Big Number` .
        * Kleuren palet: `Green to Red` .
        * Minimum waarde: `0` .
    4. *Onder* instellen:
        * Kolom gebruiken: `Trend` .
        * Kolom weergave: `Spark line` .
        * Kleuren palet: `Green to Red` .
        * Minimum waarde: `0` .
10. Selecteer **opslaan en sluiten** onder aan het deel venster.

![Scherm opname van Tegel visualisatie met een Spark-lijn](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Grootte van tegels

De auteur heeft een optie om de tegel breedte in te stellen in de tegel instellingen.

* `fixed` prijs

    Het standaard gedrag van tegels is hetzelfde als de vaste breedte, ongeveer 160 pixels breed, plus de ruimte rond de tegels.

    ![Scherm opname van tegels met een vaste breedte](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Elke titel wordt verkleind of verg root om de inhoud aan te passen, maar de tegels zijn beperkt tot de breedte van de tegels (geen horizon taal schuiven).

    ![Scherm opname van automatische breedte tegels](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Elke titel is altijd de volledige breedte van de weer gave naast elkaar, één titel per regel.

     ![Scherm opname van de tegel breedte van volledig formaat](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Volgende stappen

* Tegels ondersteunen ook samengestelde balk renderer. Ga voor meer informatie naar de documentatie van de [samengestelde balk](workbooks-composite-bar.md).
* Als u meer wilt weten over tijd parameters, zoals de `TimeRange` [tijd parameters](workbooks-time.md)van de werkmap, raadpleegt u de documentatie.