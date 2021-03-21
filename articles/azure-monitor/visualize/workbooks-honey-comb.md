---
title: Visuele elementen voor de werk blad-honing Azure Monitor
description: Meer informatie over Azure Monitor werkmap honing-visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736930"
---
# <a name="honey-comb-visualizations"></a>Honing kam-visualisaties

Met honing Combs kunt u hoge dichtheids weergaven van metrische gegevens of categorieën toestaan die eventueel als clusters kunnen worden gegroepeerd. Ze zijn handig om HOTS pots visueel te identificeren en verder in te zoomen.

In de onderstaande afbeelding ziet u het CPU-gebruik van virtuele machines tussen twee abonnementen. Elke cel vertegenwoordigt een virtuele machine en de kleur/label vertegenwoordigt het gemiddelde CPU-gebruik (rood zijn hot machines). De virtuele machines worden geclusterd op basis van het abonnement.

[![Scherm afbeelding toont het CPU-gebruik van virtuele machines over twee abonnementen](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Een honing kam toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht bewerken.
2. Gebruik onderaan **toevoegen**  en *Voeg query* toe om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer de "logs" als *gegevens bron*, "log Analytics" als *resource type* en voor *resource* punt naar een werk ruimte met prestatie logboek van de virtuele machine.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Voer de query uit.
6. Stel de *visualisatie* in op ' graph '.
7. Selecteer de instellingen voor de **grafiek**.
    1. Stel in *indelings velden* onderaan het volgende in:
        1. Grafiek type: **Hive-clusters**.
        2. Knoop punt-id: `Id` .
        3. Groeperen op: `None` .
        4. Knooppunt grootte: 100.
        5. Marge tussen zeshoeken: `5` .
        6. Kleur type: **heatmap**.
        7. Kleur veld van knoop punt: `CouterValue` .
        8. Kleuren palet: `Red to Green` .
        9. Minimum waarde: `100` .
        10. Maximum waarde: `2000` .
    2. Stel in *instellingen voor knooppunt indeling* bovenaan het volgende in:
        1. Belangrijkste inhoud:
            1. Kolom gebruiken: `Computer` .
            2. Kolom weergave `Text` .
        9. Inhoud centreren:
            1. Kolom gebruiken: `CounterValue` .
            2. Kolom weergave: `Big Number` .
            3. Kleuren palet: `None` .
            4. Schakel het selectie vakje *aangepaste getalnotatie* in.
            5. Eenheden: `Megabytes` .
            6. Maximum aantal cijfers: `1` .
8. Selecteer de knop **opslaan en sluiten** onder aan het deel venster.

[![Scherm afbeelding van query besturings element, grafiek instellingen en honing kam met de bovenstaande query en instellingen](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Instellingen voor de indeling honing kam

| Instelling | Uitleg |
|:------------- |:-------------|
| `Node Id` | Hiermee wordt een kolom geselecteerd die de unieke ID van knoop punten levert. De waarde van de kolom kan een teken reeks of een getal zijn. |
| `Group By Field` | Selecteer de kolom waarin de knoop punten moeten worden geclusterd. |
| `Node Size` | Hiermee wordt de grootte van de zeshoekige cellen ingesteld. Gebruik met de `Margin between hexagons` eigenschap om het uiterlijk van het honing kam-diagram aan te passen. |
| `Margin between hexagons` | Hiermee wordt de tussen ruimte tussen de zeshoekige cellen ingesteld. Gebruik met de `Node size` eigenschap om het uiterlijk van het honing kam-diagram aan te passen. |
| `Coloring type` | Hiermee selecteert u het schema dat moet worden gebruikt om het knoop punt te kleuren. |
| `Node Color Field` | Hiermee wordt een kolom geselecteerd die de metriek levert waarop de knooppunt gebieden worden gebaseerd. |

## <a name="node-coloring-types"></a>Kleur typen voor knoop punten

| Kleur type | Uitleg |
|:------------- |:-------------|
| `None` | Alle knoop punten hebben dezelfde kleur. |
| `Categorical` | Aan knoop punten worden kleuren toegewezen op basis van de waarde of categorie van een kolom in de resultatenset. In het bovenstaande voor beeld is de kleur op basis van het kolom _type_ van de resultatenset. Ondersteunde paletten zijn `Default` , `Pastel` en `Cool tone` .  |
| `Heatmap` | In dit type worden de cellen gekleurd op basis van een metrische kolom en een kleuren palet. Dit biedt een eenvoudige manier om metrische gegevens te markeren tussen cellen. |
| `Thresholds` | In dit type worden celkleur ingesteld op basis van drempel waarden (bijvoorbeeld _CPU > 90% => rood, 60% > CPU > 90% => geel, cpu < 60% => groen_) |
| `Field Based` | In dit type biedt een kolom specifieke RGB-waarden die voor het knoop punt kunnen worden gebruikt. Biedt de meeste flexibiliteit, maar vereist meestal meer werk om in te scha kelen.  |
      
## <a name="node-format-settings"></a>Instellingen voor knooppunt indeling

Honing kam-auteurs kunnen opgeven welke inhoud wordt verplaatst naar de verschillende onderdelen van een knoop punt: boven, links, midden, rechts en onder. Auteurs kunnen een van de renderers-werkmappen gebruiken ondersteunt (tekst, Big-nummer, Spark-lijnen, pictogram, enzovoort).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken [van een samengestelde balk renderer in werkmappen](workbooks-composite-bar.md).
- Meer informatie over het [importeren van Azure monitor logboek gegevens in Power bi](./powerbi.md).