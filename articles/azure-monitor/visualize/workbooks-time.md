---
title: Tijd parameters van Azure Monitor werkmappen
description: Meer informatie over het instellen van tijd parameters om gebruikers toe te staan de tijd context van de analyse in te stellen. De tijd parameters worden door vrijwel alle rapporten gebruikt.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717159"
---
# <a name="workbook-time-parameters"></a>Tijd parameters van werkmap

Met tijd parameters kunnen gebruikers de tijd context van de analyse instellen en worden gebruikt door bijna alle rapporten. Het is relatief eenvoudig om-auteurs in te stellen en te gebruiken om de peri odes op te geven die in de vervolg keuzelijst moeten worden weer gegeven, inclusief de optie voor aangepaste Peri Oden. 

## <a name="creating-a-time-parameter"></a>Een tijd parameter maken
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `TimeRange`
    2. Parameter type: `Time range picker`
    3. Vereist: `checked`
    4. Beschik bare Peri Oden: vorig uur, laatste 12 uur, afgelopen 24 uur, afgelopen 48 uur, afgelopen 3 dagen, afgelopen 7 dagen en aangepast tijds bereik toestaan
5. Kies opslaan op de werk balk om de para meter te maken.

    ![Afbeelding van het maken van een tijds bereik parameter](./media/workbooks-time/time-settings.png)

Zo ziet de werkmap eruit als Lees modus.

![Afbeelding die een tijds bereik parameter weergeeft in de Lees modus](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Verwijzen naar een tijd parameter
### <a name="via-bindings"></a>Via bindingen
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. De meeste werkmap besturings elementen ondersteunen een bereik kiezer voor het _bereik van tijd_ . Open de vervolg keuzelijst _tijds bereik_ en selecteer aan de `{TimeRange}` onderkant de groep para meters voor periode.
3. Hiermee wordt de tijds bereik parameter gebonden aan het tijds bereik van de grafiek. Het tijds bereik van de voorbeeld query is nu de afgelopen 24 uur.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding van een tijds bereik parameter waarnaar wordt verwezen via bindingen](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. Voer in het KQL een tijd bereik filter in met behulp van de para meter: `| where timestamp {TimeRange}`
3. Dit wordt uitgebreid naar de evaluatie tijd `| where timestamp > ago(1d)` van de query tot, dat wil zeggen de waarde voor het tijds bereik van de para meter.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tijd bereik waarnaar wordt verwezen in KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>In tekst 
1. Voeg een besturings element tekst toe aan de werkmap.
2. Voer in de prijs verlaging `The chosen time range is {TimeRange:label}`
3. Kies _gereed bewerken_
4. In het tekst besturings element wordt tekst weer gegeven: _het gekozen tijds bereik is afgelopen 24 uur_

## <a name="time-parameter-options"></a>Opties voor tijd parameters
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Label voor het tijds bereik | Afgelopen 24 uur |
| `{TimeRange:label}` | Label voor het tijds bereik | Afgelopen 24 uur |
| `{TimeRange:value}` | Tijds bereik waarde | > geleden (1d) |
| `{TimeRange:query}` | Query voor tijds bereik | > geleden (1d) |
| `{TimeRange:start}` | Begin tijd van het tijds bereik | 3/20/2019 4:18 UUR |
| `{TimeRange:end}` | Eind tijd van tijds bereik | 3/21/2019 4:18 UUR |
| `{TimeRange:grain}` | Tijd bereik korrel | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Parameter opties gebruiken in een query
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](./workbooks-access-control.md) en delen.