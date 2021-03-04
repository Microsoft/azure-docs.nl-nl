---
title: De tegel conversies van Azure Monitor Designer naar Workbooks weer geven
description: Details voor het converteren van tegels naar werkmappen bij het overstappen van weer gaven in Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043334"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Conversies van de tegel Designer Azure Monitor weer geven
[View Designer](view-designer.md) is een functie van Azure monitor waarmee u aangepaste weer gaven kunt maken waarmee u gegevens in uw werk ruimte log Analytics kunt visualiseren, met grafieken, lijsten en tijd lijnen. Ze worden gefaseerd en vervangen door werkmappen die extra functionaliteit bieden. Dit artikel bevat informatie over het converteren van verschillende tegels naar werkmappen.

## <a name="donut--list-tile"></a>Tegel voor ring-& lijst

![Ring lijst](media/view-designer-conversion-tiles/donut-list.png)

Het opnieuw maken van de ring & lijst tegel in werkmappen omvat twee afzonderlijke visualisaties. Voor de ring deel zijn er twee opties.
Voor beide beginnen door **query toevoegen** te selecteren en de oorspronkelijke query in de weer gave designer in de cel te plakken.

**Optie 1:** **Cirkel diagram** selecteren in de vervolg keuzelijst **visualisatie** : ![ cirkel diagram, visualisatie menu](media/view-designer-conversion-tiles/pie-chart.png)

**Optie 2:** Selecteer **instellen op query** in de vervolg keuzelijst **visualisatie** en voeg `| render piechart` deze toe aan de query:

 ![Visualisatie menu](media/view-designer-conversion-tiles/set-by-query.png)

**Voorbeeld**

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Bijgewerkte query
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Zie het artikel over [algemene taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder ziet u een voor beeld van hoe de tegel ring & List kan worden hergeïnterpreteerd in werkmappen:

![Donut lijst werkmappen](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Tegel & lijst met lijn diagrammen
![Lijst met lijn grafieken](media/view-designer-conversion-tiles/line-list.png) 

Als u het deel van de lijn diagram opnieuw wilt maken, moet u de query als volgt bijwerken:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Bijgewerkte query
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Er zijn twee opties voor het visualiseren van het lijn diagram

**Optie 1:** Selecteer **lijn diagram** in de vervolg keuzelijst voor **Visualisaties** :
 
 ![Menu lijn diagram](media/view-designer-conversion-tiles/line-visualization.png)

**Optie 2:** Selecteer **instellen op query** in de vervolg keuzelijst **visualisatie** en voeg `| render linechart` deze toe aan de query:

 ![Visualisatie menu](media/view-designer-conversion-tiles/set-by-query.png)

**Voorbeeld**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Zie het artikel over [algemene taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder ziet u een voor beeld van hoe de tegel van het lijn diagram & lijst kan worden hergeïnterpreteerd in werkmappen:

![Werkmappen met lijn grafieken](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Tegel & lijst nummer

 ![Tegel lijst](media/view-designer-conversion-tiles/tile-list-example.png)

Voor de tegel getal werkt u de query als volgt bij:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Bijgewerkte query
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Wijzig de vervolg keuzelijst voor visualisaties naar **tegels** en selecteer vervolgens **tegel instellingen**.
 ![Tegel visualisatie](media/view-designer-conversion-tiles/tile-visualization.png)

Laat de sectie **titel** leeg en selecteer **links**. Wijzig de waarde voor de **kolom gebruik:** om het **aantal** en de **kolom weergave** te wijzigen in een **groot aantal**:

![Tegel instellingen](media/view-designer-conversion-tiles/tile-settings.png)

 
Zie het artikel over [algemene taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder ziet u een voor beeld van hoe de tegel nummer & lijst kan worden hergeïnterpreteerd in werkmappen:

![Aantal werkmappen in lijst](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Tijdlijn en lijst

 ![Tijdlijn lijst](media/view-designer-conversion-tiles/time-list.png)

Voor de tijd lijn werkt u de query als volgt bij:

Oorspronkelijke query
```KQL
search * 
| sort by TimeGenerated desc
```

Bijgewerkte query
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Er zijn twee opties voor het visualiseren van de query als een staaf diagram:

**Optie 1:** **Staaf diagram** selecteren in de vervolg keuzelijst **visualisatie** : ![ Barchart-visualisatie](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Optie 2:** Selecteer **instellen op query** in de vervolg keuzelijst **visualisatie** en voeg `| render barchart` deze toe aan de query:

 ![Visualisatie menu](media/view-designer-conversion-tiles/set-by-query.png)

 
Zie het artikel over [algemene taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder ziet u een voor beeld van hoe de tegel van de tijdlijn & lijst kan worden hergeïnterpreteerd in werkmappen:

![Werkmappen van de tijdlijn lijst](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de overgang ontwerp weer geven naar werkmappen](view-designer-conversion-overview.md)
