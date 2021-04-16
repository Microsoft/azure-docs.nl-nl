---
title: Gegevensstromen
description: Een overzicht van gegevensstromen in Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 4769cc8abe121625f3bf77785cd681c0f649d166
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567686"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Gegevensstromen in Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Wat zijn gegevensstromen?

Gegevensstromen zijn visueel ontworpen gegevenstransformaties in Azure Synapse Analytics. Met gegevensstromen kunnen data engineers logica voor gegevenstransformatie ontwikkelen zonder code te schrijven. De resulterende gegevensstromen worden uitgevoerd als activiteiten binnen Azure Synapse Analytics pijplijnen die gebruikmaken van Apache Spark clusters. Gegevensstroomactiviteiten kunnen worden operationeel gemaakt met behulp Azure Synapse Analytics mogelijkheden voor planning, controle, stroom en bewaking.

Gegevensstromen bieden een volledig visuele ervaring zonder dat codering is vereist. Uw gegevensstromen worden uitgevoerd op synapse-beheerde uitvoeringsclusters voor uitschaalbare gegevensverwerking. Azure Synapse Analytics verwerkt alle codevertalingen, padoptimalisatie en uitvoering van uw gegevensstroomtaken.

## <a name="getting-started"></a>Aan de slag

Gegevensstromen worden gemaakt vanuit het deelvenster Ontwikkelen in Synapse Studio. Als u een gegevensstroom wilt maken, selecteert u het plusteken naast Ontwikkelen en selecteert u **Gegevensstroom**.  

![Nieuwe gegevensstroom](media/data-flow/new-data-flow.png)

Met deze actie gaat u naar het canvas van de gegevensstroom, waar u uw transformatielogica kunt maken. Selecteer **Bron toevoegen om** de brontransformatie te configureren. Zie Brontransformatie [voor meer informatie.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="authoring-data-flows"></a>Gegevensstromen maken

De gegevensstroom heeft een uniek ontwerpvas dat is ontworpen om het bouwen van transformatielogica eenvoudig te maken. Het canvas van de gegevensstroom is gescheiden in drie delen: de bovenste balk, de grafiek en het configuratiepaneel. 

![Schermopname van het canvas van de gegevensstroom met de bovenste balk, grafiek en configuratiepaneel gelabeld.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

In de grafiek wordt de transformatiestroom weergegeven. Het toont de herkomst van brongegevens wanneer deze naar een of meer sinks stromen. Als u een nieuwe bron wilt toevoegen, selecteert **u Bron toevoegen.** Als u een nieuwe transformatie wilt toevoegen, selecteert u het plusteken rechts onder aan een bestaande transformatie. Meer informatie over het beheren [van de gegevensstroomgrafiek](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Schermopname van het graafgedeelte van het canvas met een zoektekstvak.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Configuratiepaneel

In het configuratiepaneel worden de instellingen weergegeven die specifiek zijn voor de geselecteerde transformatie. Als er geen transformatie is geselecteerd, wordt de gegevensstroom weer geven. In de algehele configuratie van de gegevensstroom kunt u parameters toevoegen via het **tabblad Parameters.** Zie Gegevensstroomparameters [voor meer informatie.](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Elke transformatie bevat ten minste vier configuratietabbladen.

#### <a name="transformation-settings"></a>Transformatie-instellingen

Het eerste tabblad in het configuratiedeelvenster van elke transformatie bevat de instellingen die specifiek zijn voor die transformatie. Zie de documentatiepagina van de transformatie voor meer informatie.

![Tabblad Broninstellingen](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimaliseren

Het **tabblad Optimaliseren** bevat instellingen voor het configureren van partitioneringsschema's. Zie de prestatiehandleiding voor toewijzingsgegevensstromen voor meer informatie over het optimaliseren [van uw gegevensstromen.](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Schermopname van het tabblad Optimaliseren](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecteren

Het **tabblad** Inspecteren biedt een weergave van de metagegevens van de gegevensstroom die u transformeert. U ziet het aantal kolommen, de gewijzigde kolommen, de toegevoegde kolommen, gegevenstypen, de kolomorder en kolomverwijzingen. **Inspecteren** is een alleen-lezenweergave van uw metagegevens. U hoeft de foutopsporingsmodus niet in te schakelen om metagegevens te zien in het **deelvenster** Inspecteren.

![Tabblad Inspecteren](media/data-flow/inspect.png)

Wanneer u de vorm van uw gegevens wijzigt via transformaties, ziet u de stroom voor metagegevenswijzigingen in het **deelvenster** Inspecteren. Als uw brontransformatie geen gedefinieerd schema bevat, zijn metagegevens niet zichtbaar in het **deelvenster** Inspecteren. Gebrek aan metagegevens is gebruikelijk in scenario's met schemadrift.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is geselecteerd, geeft het tabblad **Gegevensvoorbeeld** u een interactieve momentopname van de gegevens bij elke transformatie. Zie Voorbeeld van gegevens in de [foutopsporingsmodus voor meer informatie.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevensstroom, zoals validatie- en foutopsporingsinstellingen. U kunt ook het onderliggende JSON-code- en gegevensstroomscript van uw transformatielogica bekijken.

## <a name="available-transformations"></a>Beschikbare transformaties

Bekijk het overzicht [van de transformatie van toewijzingsgegevensstromen](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor een lijst met beschikbare transformaties.

## <a name="data-flow-activity"></a>Gegevensstroomactiviteit

Gegevensstromen worden operationeel gemaakt binnen Azure Synapse Analytics pijplijnen met behulp van [de gegevensstroomactiviteit](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Het enige wat een gebruiker hoeft te doen, is opgeven welke Integration Runtime moet worden gebruikt en parameterwaarden moeten worden doorgegeven. Meer informatie over de [Azure Integration Runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)vindt u hier.

## <a name="debug-mode"></a>Foutopsporingsmodus

Met de foutopsporingsmodus kunt u de resultaten van elke transformatiestap interactief bekijken tijdens het bouwen en opsporen van fouten in uw gegevensstromen. De foutopsporingssessie kan worden gebruikt in bij het bouwen van uw gegevensstroomlogica en het uitvoeren van pijplijndebug-runs met gegevensstroomactiviteiten. Zie de documentatie voor [foutopsporingsmodus voor meer informatie.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="monitoring-data-flows"></a>Gegevensstromen bewaken

De gegevensstroom kan worden geïntegreerd met bestaande Azure Synapse Analytics bewakingsmogelijkheden. Zie Bewaking van toewijzingsgegevensstromen voor meer informatie over het begrijpen van uitvoer van [gegevensstroombewaking.](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Het Azure Synapse Analytics team heeft een [](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) handleiding voor het afstemmen van de prestaties gemaakt om u te helpen de uitvoeringstijd van uw gegevensstromen te optimaliseren na het bouwen van uw bedrijfslogica.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [brontransformatie.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Meer informatie over het bouwen van uw gegevensstromen in [de foutopsporingsmodus.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
