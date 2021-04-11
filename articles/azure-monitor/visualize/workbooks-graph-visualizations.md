---
title: Visualisaties van werkmap grafieken Azure Monitor
description: Meer informatie over de visualisaties van de Azure Monitor werkmap.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100612702"
---
# <a name="graph-visualizations"></a>Grafiek visualisaties

Werkmappen ondersteunen het visualiseren van wille keurige grafieken op basis van gegevens uit Logboeken om de relaties tussen bewakings entiteiten weer te geven.

In de volgende grafiek ziet u gegevens die in/uit een computer worden uitgestroomd via verschillende poorten naar/van externe computers. Het is gekleurd op type (computer versus poort versus extern IP-adres) en de rand grootten komen overeen met de hoeveelheid gegevens die tussen tussen worden geplaatst. De onderliggende gegevens zijn afkomstig van KQL query gericht op VM-verbindingen.

[![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Een grafiek toevoegen
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. De visualisatie instellen op **Graph**
6. Selecteer de knop **grafiek instellingen** om het deel venster instellingen te openen
7. Stel in _indelings velden_ onderaan het volgende in:
    * Knoop punt-id: `Id`
    * Bron-id: `SourceId`
    * Doel-id: `TargetId`
    * Rand label: `None`
    * Grootte van rand: `Calls`
    * Knooppunt grootte: `None`
    * Kleur type: `Categorical`
    * Kleur veld van knoop punt: `Kind`
    * Kleuren palet: `Pastel`
8. Stel in _instellingen voor knooppunt indeling_ bovenaan het volgende in:
    * _Bovenste inhoud_-use-kolom: `Name` , kolom weergave: `Text`
    * _Inhoud centreren_-kolom gebruiken: `Calls` , kolom renderer: `Big Number` , kleuren palet: `None`
    * _Onderste inhoud_-gebruik kolom: `Kind` , kolom weergave: `Text`
9. Selecteer de knop _opslaan en sluiten_ onder aan het deel venster.

[![Scherm opname van de weer gave samen vatting van tegel met de bovenstaande query en instellingen.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Grafiek instellingen

| Instelling         | Uitleg                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Hiermee wordt een kolom geselecteerd die de unieke ID van knoop punten in de grafiek bevat. De waarde van de kolom kan een teken reeks of een getal zijn. |
| `Source Id`     | Hiermee selecteert u een kolom die de Id's van bron knooppunten voor de randen van de grafiek bevat. Waarden moeten worden toegewezen aan een waarde in de kolom _knoop punt-id_ . |
| `Target Id`     | Hiermee wordt een kolom geselecteerd die de Id's van doel knooppunten voor de randen van de grafiek bevat. Waarden moeten worden toegewezen aan een waarde in de kolom _knoop punt-id_ . |
| `Edge Label`    | Hiermee wordt een kolom geselecteerd waarmee Edge-labels in de grafiek worden geboden.                                                            |
| `Edge Size`     | Hiermee wordt een kolom geselecteerd die de metriek levert waarop de rand breedten worden gebaseerd.                                |
| `Node Size`     | Hiermee wordt een kolom geselecteerd die de metriek levert waarop de knooppunt gebieden worden gebaseerd.                                 |
| `Coloring Type` | Wordt gebruikt om het knoop punt kleur schema te kiezen.                                                                            |

## <a name="node-coloring-types"></a>Kleur typen voor knoop punten

| Kleur type | Uitleg |
|:------------- |:------------|
| `None`        | Alle knoop punten hebben dezelfde kleur. |
| `Categorical` | Aan knoop punten worden kleuren toegewezen op basis van de waarde of categorie van een kolom in de resultatenset. In het bovenstaande voor beeld is de kleur op basis van het kolom _type_ van de resultatenset. Ondersteunde paletten zijn `Default` , `Pastel` en `Cool tone` .  |
| `Field Based` | In dit type biedt een kolom specifieke RGB-waarden die voor het knoop punt kunnen worden gebruikt. Biedt de meeste flexibiliteit, maar vereist meestal meer werk om in te scha kelen.  |

## <a name="node-format-settings"></a>Instellingen voor knooppunt indeling

Ontwerpers van grafieken kunnen opgeven welke inhoud wordt verplaatst naar de verschillende onderdelen van een knoop punt: boven, links, midden, rechts en onder. Grafieken kunnen gebruikmaken van een van de weer gaven van de werk bladen (tekst, Big-getal, Spark-lijnen, pictogram, enzovoort).

## <a name="field-based-node-coloring"></a>Kleur van het knoop punt op basis van een veld

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. De visualisatie instellen op *Graph*
6. Selecteer de knop **grafiek instellingen**  om het deel venster instellingen te openen.
7. Stel in *indelings velden* onderaan het volgende in:
    * Knooppunt-id:`Id`
    * Bron-id: `SourceId`
    * Doel-id: `TargetId`
    * Rand label: `None`
    * Grootte van rand: `Calls`
    * Knooppunt grootte: `Node`
    * Kleur type: `Field Based`
    * Kleur veld van knoop punt: `Color`
8. Voer het volgende in bij *instellingen voor knooppunt indeling* bovenaan.
    * In *bovenste inhoud* instellen:
        * Kolom gebruiken: `Name` .
        * Kolom weergave: `Text` .
    * In *Centreren inhoud* instellen:
        * Kolom gebruiken: `Calls`
        * Kolom weergave: `Big Number`
        * Kleuren palet: `None`
    * In *onderste inhoud*, stelt u het volgende in:
        * Kolom gebruiken: `Kind`
        * Kolom weergave: `Text` .
9. Selecteer de **knop Opslaan en sluiten** onder aan het deel venster.

[![Scherm afbeelding van het maken van een grafiek visualisatie met het veld basis knooppunt kleur.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Grafieken bieden ook ondersteuning voor samengestelde balk renderer. Ga voor meer informatie naar de documentatie van de [composiet-balk](workbooks-composite-bar.md).
* Meer informatie over de [gegevens bronnen](workbooks-data-sources.md) die u in werkmappen kunt gebruiken.
