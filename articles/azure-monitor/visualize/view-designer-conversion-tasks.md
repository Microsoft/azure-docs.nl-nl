---
title: Algemene taken voor de conversie van Designer naar werkmappen Azure Monitor weer geven
description: Algemene taken wanneer u overstapt van weer gaven naar werkmappen in Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 4e9d9e65dedae083138f3b29a2a609e124ce0fc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611778"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Algemene taken voor de conversie van Designer naar werkmappen weer geven
[View Designer](view-designer.md) is een functie van Azure monitor waarmee u aangepaste weer gaven kunt maken waarmee u gegevens in uw werk ruimte log Analytics kunt visualiseren, met grafieken, lijsten en tijd lijnen. Ze worden gefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel vindt u informatie over taken die gemeen schappelijk zijn in het converteren van weer gaven naar werkmappen.


## <a name="quickstart-with-preset-view-designer-templates"></a>Snelstartgids met vooraf ingestelde ontwerp sjablonen voor weer gaven

Werkmappen in Log Analytics-werk ruimten hebben al sjablonen gemaakt die overeenkomen met een aantal weer gaven in de ontwerp functie voor weer gave. Selecteer in de categorie **ontwerp handleidingen weer** geven de optie **ontwerp overgangs gids weer geven** voor meer informatie over uw opties of selecteer een van de vooraf gedefinieerde sjablonen.

![Voorbeeld sjablonen](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Het filter tijd bereik inschakelen
De weer gave Designer heeft een ingebouwd standaardtijd bereik filter, maar in werkmappen is deze instelling niet standaard ingeschakeld. Met werkmappen kunnen gebruikers hun eigen tijds bereik filters maken die mogelijk meer van toepassing zijn op de gegevens Logboeken. De stappen voor het genereren van het filter worden hieronder weer gegeven:

Selecteer de optie **para meters toevoegen** . De standaard **stijl** is ingesteld op *Pills*.

![Param toevoegen](media/view-designer-conversion-tasks/add-param.png)

 Selecteer de knop **para meter toevoegen** .

![Parameter toevoegen](media/view-designer-conversion-tasks/add-parameter.png)

Typ *time Range* in het tekstvak **parameter naam** van het menu Sidebar. Stel **parameter type** in als *tijd bereik kiezer*. Schakel het selectie vakje **vereist?** in.

![Parameter menu](media/view-designer-conversion-tasks/parameter-menu.png)

Sla de para meter op in de linkerbovenhoek van het menu Sidebar. U kunt de vervolg keuzelijst standaard *uitschakelen* of een standaard waarde voor **time Range** selecteren, bijvoorbeeld *24 uur*. Selecteer **gereed bewerken**.

Para meters kunnen worden gebruikt in query's door accolades toe te voegen {} rond uw parameter naam. Meer informatie over para meters vindt u in de [werkmappen-documentatie over para meters](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Query's bijwerken met de para meter time Range

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Optie 1: Selecteer Time Range in de vervolg keuzelijst tijds bereik

![Tijd parameter](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Optie 2: uw logboek query's bijwerken

Voeg in de query de regel toe: `| where TimeGenerated {TimeRange}` zoals in het volgende voor beeld:

Oorspronkelijke query
```KQL
search * 
| summarize count() by Type
```

Bijgewerkte query
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Inclusief een lijst
De meeste weergave ontwerp weergaven bevatten een lijst en u kunt deze standaard lijst in een werkmap reproduceren.

![Tegel lijst](media/view-designer-conversion-tasks/tile-list.png)

Voeg een visualisatie toe door te klikken op **query toevoegen** uit de Celopties.

![Param toevoegen](media/view-designer-conversion-tasks/add-param.png)

In de weer gave Designer wordt gebruikgemaakt van een standaard query die overeenkomt met de syntaxis van het oorspronkelijke voor beeld. Dit kan worden bijgewerkt door de query te wijzigen in het bijgewerkte formulier, zoals in het volgende voor beeld:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Bijgewerkte query
```KQL
search * 
| summarize Count = count() by Type
```

Hiermee wordt een lijst gegenereerd die er ongeveer als volgt uitziet:

![Voor beeld van lijst](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Sparklines inschakelen
Een gemeen schappelijke functie voor rasters is het toevoegen van sparklines voor het samenvatten van verschillende gegevens patronen in de loop van de tijd. De weer gave Designer biedt de functie **sparklines inschakelen** voor alle lijsten, zoals werkmappen. Als u sparklines wilt opnemen in uw gegevens die overeenkomen met de ontwerp functie voor weer gaven, voegt u de gegevens samen met uw oorspronkelijke query, zoals in het volgende voor beeld:

Oorspronkelijke query
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Bijgewerkte query
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selecteer **kolom instellingen**.
![Kolom instellingen](media/view-designer-conversion-tasks/column-settings.png)

Werk de vervolg keuzelijst van de **kolom renderer** naar een *Spark-gebied*.
![Groeperen](media/view-designer-conversion-tasks/sparkline.png)

Sla de instellingen op en voer de query opnieuw uit om de tabel bij te werken zodat deze een sparkline bevat.

Het resulterende raster ziet er ongeveer als volgt uit: ![ voor beeld van sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Geavanceerde instellingen voor cellen
Als u de weergave ontwerper wilt spie gelen, kunt u taken uitvoeren zoals het wijzigen van de grootte van werkmap cellen of het toevoegen van pincodes en externe koppelingen naar Logboeken.

Als u toegang wilt krijgen tot **Geavanceerde instellingen** , selecteert u het tandwiel pictogram onder aan elke cel.

![Geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings.png)

Hiermee wordt een menu met verschillende opties weer gegeven:

![Instellingen voor geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Als u een pincode en een koppeling naar een externe query wilt toevoegen, selecteert u de betreffende selectie vakjes. Als u een titel wilt toevoegen aan uw cel, typt u de gewenste titel in de sectie **grafiek titel** .

Standaard is het zo dat alle werkmappen-cellen worden ingesteld op de volledige pagina breedte, maar u kunt dit aanpassen door de cel omlaag te schalen op het tabblad **stijl** van het menu **Geavanceerde instellingen**

![Stijl van geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Aanvullende para meters
Selecteer **para meter toevoegen** om een nieuwe para meter in uw werkmap te maken. 

Als u een abonnement wilt selecteren, typt u *abonnement* in het veld **parameter naam** in het menu aan de zijkant en selecteert u de optie *abonnement* kiezen in de vervolg keuzelijst voor het **parameter type**

![Menu abonnement](media/view-designer-conversion-tasks/subscription-filter.png)

Als u een resource wilt selecteren, typt u *resource* in het veld **parameter naam** in het menu aan de zijkant en selecteert u *resource kiezer* in de vervolg keuzelijst **type para meter** .

![Resource menu](media/view-designer-conversion-tasks/resource-filter.png)

Hiermee worden vervolg keuzelijsten ingevoegd om toegang te krijgen tot uw verschillende abonnementen en resources.

![Vervolg keuzelijst voor abonnements resources](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Volgende stappen
- [Tegelconversies](view-designer-conversion-tiles.md)
