---
title: Visualisaties van de werkmap structuur Azure Monitor
description: Meer informatie over alle Azure Monitor werkmap structuur visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100612285"
---
# <a name="tree-visualizations"></a>Structuur visualisaties

Werkmappen ondersteunen hiërarchische weer gaven via structuur rasters. Met structuren kunnen sommige rijen worden uitgebreid naar het volgende niveau voor een inzoom ervaring.

In het onderstaande voor beeld ziet u de metrische gegevens over de container status (werkset-grootte) visualiseren als een structuur raster. De knoop punten op het hoogste niveau hier zijn knoop punten van Azure Kubernetes service (AKS), het volgende niveau is een Peul en het laatste niveau is containers. U kunt uw kolommen zoals in een raster (heatmap, pictogrammen en koppelingen) ook opmaken. De onderliggende gegevens bron in dit geval is een Log Analytics-werk ruimte met AKS-Logboeken.

[![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Een structuur raster toevoegen
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Selecteer **toevoegen** en vervolgens *query toevoegen* om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Visualisatie instellen op **raster**
6. Selecteer de knop **kolom instellingen** om het deel venster instellingen te openen
7. Stel in de sectie **structuur/groep per instellingen** onderaan het volgende in:
    * Structuur type: `Parent/Child`
    * Id-veld: `Id`
    * Bovenliggend id-veld: `ParentId`
    * Uitvouwen weer geven op: `Name`
    * Selecteer *het selectie vakje het hoogste niveau van de structuur uitbreiden* .
8. In het gedeelte _kolommen_ bovenaan, stelt u het volgende in:
    * _Id_ -kolom weergave: `Hidden`
    * _Bovenliggende id_ -kolom weergave: `Hidden`
    * _Aanvragen_ -kolom weergave: `Bar` , kleur: `Blue` , minimum waarde: `0`
9. Selecteer de knop **opslaan en sluiten** onder aan het deel venster.

[![Scherm opname van de weer gave samen vatting van tegel met de bovenstaande query en instellingen.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Structuur instellingen

| Instelling | Uitleg |
|:------------- |:-------------|
| `Id Field` | De unieke ID van elke rij in het raster. |
| `Parent Id Field` | De ID van het bovenliggende item van de huidige rij. |
| `Show the expander on` | De kolom waarin de structuur weergave wordt weer gegeven. Het is gebruikelijk dat structuur rasters hun ID en bovenliggend ID-veld verbergen, omdat ze niet goed leesbaar zijn. In plaats daarvan wordt de uitvouwing weer gegeven in een veld met een meer Lees bare waarde, zoals de naam van de entiteit. |
| `Expand the top level of the tree` | Als u dit selectie vakje inschakelt, wordt het structuur raster uitgebreid op het hoogste niveau. Dit is handig als u standaard meer informatie wilt weer geven. |

## <a name="grouping-in-a-grid"></a>Groeperen in een raster

Met groepering kunt u hiërarchische weer gaven maken die vergelijkbaar zijn met die hierboven, met aanzienlijk eenvoudiger query's. U verliest de aggregatie op de binnenste knoop punten van de structuur, maar deze is wel acceptabel voor bepaalde scenario's. Gebruik *Group by* om structuur weergaven te maken wanneer de onderliggende resultatenset niet kan worden omgezet in een juist vrije vorm, bijvoorbeeld: waarschuwing, status en metrische gegevens.

## <a name="adding-a-tree-using-grouping"></a>Een structuur toevoegen met groepering

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Selecteer **toevoegen** en vervolgens *query toevoegen* om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Stel de visualisatie in op *raster*.
2. Selecteer de **knop kolom instellingen** om het deel venster instellingen te openen.
3. Stel in de sectie *structuur/groep per instellingen* onderaan het volgende in:
    * Structuur type: `Group By`
    * Groeperen op: `App`
    * Vervolgens op: `None`
    * Selecteer *het selectie vakje het hoogste niveau van de structuur uitbreiden* .
4. In het gedeelte *kolommen* bovenaan, stelt u het volgende in:
    * *App* -kolom renderer: `Hidden`
    * *Aanvragen* -kolom weergave: `Bar` , kleur: `Blue` , minimum waarde: `0`
5. Selecteer de knop **opslaan en sluiten** onder aan het deel venster.

[![Scherm afbeelding van het maken van een structuur visualisatie in werkmappen](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken [van een grafiek in werkmappen](workbooks-graph-visualizations.md).
* Meer informatie over het maken [van een tegel in werkmappen](workbooks-tile-visualizations.md).
