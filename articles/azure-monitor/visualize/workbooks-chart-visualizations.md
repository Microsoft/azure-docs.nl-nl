---
title: Visualisaties van Azure Monitor werkmap diagram
description: Meer informatie over alle Azure Monitor werkmap diagram visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611649"
---
# <a name="chart-visualizations"></a>Grafiekvisualisaties

Met werkmappen kunnen bewakings gegevens worden weer gegeven als grafieken. Ondersteunde grafiek typen zijn onder andere lijn-, staaf-, staaf-categorische, vlak-, spreidings-en cirkel diagrammen en tijd. Auteurs kunnen ervoor kiezen om de hoogte, breedte, kleuren palet, legenda, titels, no-data-bericht, enzovoort, enzovoort aan te passen en de typen assen en reeks kleuren aan te passen met behulp van grafiek instellingen.

Werkmappen ondersteunen grafieken voor logboeken en metrische gegevens bronnen.

## <a name="log-charts"></a>Logboek grafieken

Azure Monitor Logboeken biedt bronnen eigen aren gedetailleerde informatie over de werking van hun apps en infra structuur. In tegens telling tot metrische gegevens wordt de logboek informatie niet standaard verzameld en is een type verzameling vereist. Als er echter logboeken aanwezig zijn, is er een groot aantal informatie over de status van de resource en gegevens die nuttig is voor diagnose. Met werkmappen kunnen logboek gegevens worden gepresenteerd als visuele diagrammen voor gebruikers analyse.

### <a name="adding-a-log-chart"></a>Een logboek grafiek toevoegen

In het onderstaande voor beeld ziet u de trend van aanvragen naar een app in de vorige dagen.

1. Schakel de werkmap over naar de bewerkings modus door het item werkbalk opdracht **bewerken** te selecteren.
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de [KQL](/azure/kusto/query/) voor uw analyse in te voeren (bijvoorbeeld trend van aanvragen).
5. Stel de visualisatie in op een van de volgende elementen: **vlak**, **staaf**, **staaf (categorische)**, **lijn**, **cirkel**, **sprei ding** of **tijd**.
6. Stel andere para meters in als dat nodig is, zoals een tijds bereik, visualisatie, grootte, kleuren palet en legenda.

[![Scherm afbeelding van de logboek grafiek in de bewerkings modus](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Para meters van het logboek diagram

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Query Type` | Het type query dat moet worden gebruikt. | Log, Azure-resource grafiek, etc. |
| `Resource Type` | Het bron type van het doel. | Application Insights, Log Analytics of Azure-eerst |
| `Resources` | Een set resources waaruit de metrische waarde moet worden opgehaald. | MyApp1 |
| `Time Range` | Het tijd venster om de logboek grafiek weer te geven. | Vorig uur, afgelopen 24 uur, etc. |
| `Visualization` | De visualisatie die moet worden gebruikt. | Vlak, staaf, lijn, cirkel, sprei ding, tijd, staaf categorische |
| `Size` | De verticale grootte van het besturings element. | Klein, gemiddeld, groot of volledig |
| `Color palette` | Het kleuren palet dat in de grafiek moet worden gebruikt. Genegeerd in de modus met meerdere metrieken of segmenten. | Blauw, groen, rood, etc. |
| `Legend` | De aggregatie functie die moet worden gebruikt voor de legenda. | Som of gemiddelde van waarden of Max, min, eerste, laatste waarde |
| `Query` | Een KQL-query waarmee gegevens worden geretourneerd in de indeling die wordt verwacht door de grafiek visualisatie. | _aanvragen \| voor het maken van reeksen aanvragen = aantal () standaard = 0 op tijds tempel van geleden (1d) tot nu () stap 1U_ |

### <a name="time-series-charts"></a>Grafieken van de tijd reeks

Tijdreeks grafieken, zoals vlak, staaf, lijn, sprei ding en tijd, kunnen eenvoudig worden gemaakt met behulp van het besturings element query in werkmappen. De sleutel heeft informatie over tijd en metrische gegevens in de resultatenset.

#### <a name="simple-time-series"></a>Eenvoudige time-series

De onderstaande query retourneert een tabel met twee kolommen: *tijds tempel* en *aanvragen*. Het query besturings element gebruikt *tijds tempel* voor de X-as en *aanvragen* voor de Y-as.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Scherm afbeelding van een eenvoudig logboek lijn diagram met tijd reeksen.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Time-Series met meerdere metrische gegevens

De onderstaande query retourneert een tabel met drie kolommen: *tijds tempel*, *aanvragen* en *gebruikers*. Het query besturings element gebruikt *tijds tempel* voor de X-as en *vraagt*  &  *gebruikers* als afzonderlijke reeksen op de Y-as.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Scherm afbeelding van een tijd reeks met een logboek lijn diagram met meerdere metrische gegevens.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Gesegmenteerde tijd reeksen

De onderstaande query retourneert een tabel met drie kolommen: *Time Stamp*, *aanvragen* en *aanvraag* naam, waarbij de naam van de *aanvraag* een categorische kolom met de namen van aanvragen is. Het query besturings element maakt hier gebruik van een *tijds tempel* voor de X-as en voegt een reeks toe per waarde van de *aanvraagnaam*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Scherm afbeelding van een logboek lijn diagram met gesegmenteerde tijd reeksen.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Samenvatten versus maken-serie

In de voor beelden in de vorige sectie `summarize` wordt de operator gebruikt, omdat deze gemakkelijker te begrijpen is. Samen vatting heeft echter een grote beperking omdat hiermee de rij met resultaten wordt wegge laten als er geen items in de Bucket staan. Het kan het effect van het verschuiven van het diagram tijd venster hebben, afhankelijk van het feit of de lege buckets zich in de voor-of Backside van het tijds bereik bevinden.

Normaal gesp roken is het beter om de `make-series` operator te gebruiken voor het maken van tijd reeks gegevens. deze heeft de optie om standaard waarden op te geven voor lege buckets.

De volgende query maakt gebruik van de `make-series` operator.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

In de onderstaande query ziet u een vergelijk bare grafiek met de `summarize` operator

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Hoewel de onderliggende resultatenset anders is. Alle gebruikers moeten de visualisatie instellen op gebied, lijn, staaf of tijd en werkmappen zorgen voor de rest.

[![Scherm afbeelding van een logboek regel diagram dat is gemaakt op basis van een tabelmaakquery](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Categorische-staaf diagram of-histogram

Met categorische-grafieken kunnen gebruikers een dimensie of kolom op de X-as van een grafiek weer geven. Dit is vooral nuttig in histogrammen. In het onderstaande voor beeld ziet u de distributie van aanvragen op basis van de bijbehorende resultaat code.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

De query retourneert twee kolommen: de metrische gegevens en de *resultaat* categorie van *aanvragen* . Elke waarde van de *resultaten* kolom krijgt een eigen balk in het diagram met een hoogte die evenredig is met de *metrische gegevens* van de aanvragen.

[![Scherm afbeelding van een categorische-staaf diagram voor aanvragen op resultaat code](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Cirkeldiagrammen

Cirkel diagrammen bieden de visualisatie van het numerieke gedeelte. In het onderstaande voor beeld ziet u het aandeel van aanvragen op basis van de bijbehorende resultaat code.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

De query retourneert twee kolommen: de metrische gegevens en de *resultaat* categorie van *aanvragen* . Elke waarde van de *resultaten* kolom krijgt een eigen segment in de cirkel met een proportionele grootte voor de metrische gegevens van de *aanvragen* .

[![Scherm afbeelding van een cirkel diagram met segmenten die de resultaat code vertegenwoordigen](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Metrische grafieken

De meeste Azure-resources geven metrische gegevens over de status en status (bijvoorbeeld CPU-gebruik, Beschik baarheid van opslag, aantal database transacties, mislukte app-aanvragen enz.). Werkmappen staan de visualisatie van deze gegevens toe als grafieken van de tijd reeks.)

### <a name="adding-a-metric-chart"></a>Een metrische grafiek toevoegen

In het volgende voor beeld wordt het aantal trans acties in een opslag account in het vorige uur weer gegeven. Hierdoor kan de opslag eigenaar de transactie trend zien en er wordt gekeken naar afwijkingen in het gedrag.

1. Schakel de werkmap over naar de bewerkings modus door het item werkbalk opdracht **bewerken** te selecteren.
2. Gebruik de koppeling **metrische gegevens toevoegen** om een metrische besturings element toe te voegen aan de werkmap.
3. Selecteer een resource type (bijvoorbeeld opslag account), de resources die het doel moeten zijn, de metrische naam ruimte en naam en de aggregatie die moet worden gebruikt.
4. Stel andere para meters in als dat nodig is, zoals een tijds bereik, Split-op, visualisatie, grootte en kleuren palet.

[![Scherm opname van grafiek met metrische gegevens in de bewerkings modus](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Metrische diagram parameters

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Resource Type` | Het bron type van het doel. | Opslag of virtuele machine. |
| `Resources` | Een set resources waaruit de metrische waarde moet worden opgehaald. | MyStorage1 |
| `Namespace` | De naam ruimte met de metriek. | Opslag >-BLOB |
| `Metric` | De metrische gegevens die moeten worden gevisualiseerd. | Opslag > BLOB-> transacties |
| `Aggregation` | De aggregatie functie die moet worden toegepast op de metriek. | Som, aantal, gemiddelde, etc. |
| `Time Range` | Het tijd venster waarin de metriek moet worden weer gegeven. | Vorig uur, afgelopen 24 uur, etc. |
| `Visualization` | De visualisatie die moet worden gebruikt. | Vlak, staaf, lijn, sprei ding, Raster |
| `Split By` | U kunt eventueel de metriek splitsen op een dimensie. | Trans acties op geo-type |
| `Size` | De verticale grootte van het besturings element. | Klein, gemiddeld of groot |
| `Color palette` | Het kleuren palet dat in de grafiek moet worden gebruikt. Wordt genegeerd als de `Split by` para meter wordt gebruikt. | Blauw, groen, rood, etc. |

### <a name="examples"></a>Voorbeelden

Trans acties gesplitst op API-naam als lijn diagram:

[![Scherm afbeelding van een lijn diagram met metrische gegevens voor opslag transacties die zijn gesplitst op naam van de API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Trans acties gesplitst op antwoord type als een groot staaf diagram:

[![Scherm afbeelding van een grote staven staaf diagram voor opslag transacties gesplitst op antwoord type](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Gemiddelde latentie als een spreidings diagram:

[![Scherm afbeelding van een metrisch spreidings diagram voor de latentie van de opslag](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Grafiek instellingen

Auteurs kunnen grafiek instellingen gebruiken om aan te passen welke velden worden gebruikt in de grafiek assen, de eenheden van assen, aangepaste opmaak, bereiken, gedrag van groeperingen, legenda's en reeks kleuren.

### <a name="the-settings-tab"></a>Het tabblad Instellingen

De besturings elementen op het tabblad instellingen:

- De instellingen van de as, inclusief welke velden, aangepaste opmaak waarmee gebruikers de getalnotatie kunnen instellen op de aswaarden en aangepaste bereiken.
- Groeperings instellingen, inclusief welk veld, de limieten voor de groep ' overige ' wordt gemaakt.
- Legenda-instellingen, inclusief de metrische gegevens (reeks naam, kleuren en getallen) onderaan en/of een legenda (reeks namen en kleuren).

![Scherm afbeelding van grafiek instellingen.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Aangepaste opmaak

Opties voor de getalnotatie zijn onder andere:

| Opmaak optie             | Uitleg                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | De eenheden voor de kolom: diverse opties voor percentage, aantallen, tijd, byte, aantal/tijd, bytes/tijd, enzovoort. De eenheid voor de waarde 1234 kan bijvoorbeeld worden ingesteld op milliseconden en wordt weer gegeven als 1,234 s.                                  |
| `Style`                      | De notatie om deze weer te geven als-decimaal, valuta, procent.                                               |
| `Show group separator`       | Selectie vakje om groeps scheidings tekens weer te geven. 1234 wordt weer gegeven als 1.234 in de Verenigde Staten.                                    |
| `Minimum integer digits`     | Minimum aantal te gebruiken integere cijfers (standaard 1).                                                   |
| `Minimum fractional digits`  | Minimum aantal decimalen dat moet worden gebruikt (standaard 0).                                                |
| `Maximum fractional digits`  | Het maximum aantal decimalen dat moet worden gebruikt.                                                            |
| `Minimum significant digits` | Minimum aantal significante cijfers dat moet worden gebruikt (standaard 1).                                               |
| `Maximum significant digits` | Het maximum aantal significante cijfers dat moet worden gebruikt.                                                           |

![Scherm opname van de instellingen voor de X-as.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Het tabblad reeks

Op het tabblad reeks instelling kunt u de labels en kleuren aanpassen die worden weer gegeven voor reeksen in de grafiek.

- Het `Series name` veld wordt gebruikt om een reeks in de gegevens te vergelijken en als er een overeenkomst wordt gevonden, worden de weergave label en de kleur weer gegeven.
- Het `Comment` veld is handig voor sjabloon auteurs, omdat deze opmerking door vertalers kan worden gebruikt om de weergave labels te lokaliseren.

![Scherm opname van reeks instellingen.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken [van een tegel in werkmappen](workbooks-tile-visualizations.md).
- Meer informatie over het maken van [interactieve werkmappen](workbooks-interactive.md).