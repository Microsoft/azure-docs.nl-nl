---
title: Gegevensstromen
description: Een overzicht van gegevens stromen in azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592626"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Gegevens stromen in azure Synapse Analytics

## <a name="what-are-data-flows"></a>Wat zijn gegevens stromen?

Gegevens stromen zijn visueel ontworpen gegevens transformaties in azure Synapse Analytics. Met gegevens stromen kunnen gegevens technici logica voor gegevens transformatie ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten in azure Synapse Analytics-pijp lijnen die gebruikmaken van uitgeschaalde Apache Spark clusters. Gegevens stroom activiteiten kunnen worden operationeel met bestaande Azure Synapse Analytics-planningen, beheer-, stroom-en bewakings mogelijkheden.

Gegevens stromen bieden een volledig visuele ervaring zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op door Synapse beheerde uitvoerings clusters voor uitgeschaalde gegevens verwerking. Met Azure Synapse Analytics worden alle code vertalingen, de optimalisatie van paden en de uitvoering van uw gegevens stroom taken verwerkt.

## <a name="getting-started"></a>Aan de slag

Gegevens stromen worden gemaakt vanuit het deel venster ontwikkelen in Synapse Studio. Als u een gegevens stroom wilt maken, selecteert u het plus teken naast **ontwikkelen** en selecteert u vervolgens **gegevens stroom**. 

![Nieuwe gegevens stroom](media/data-flow/new-data-flow.png)

Met deze actie gaat u naar het canvas voor gegevens stromen, waar u uw transformatie logica kunt maken. Selecteer **bron toevoegen** om het configureren van de bron transformatie te starten. Zie [bron transformatie](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

## <a name="authoring-data-flows"></a>Gegevens stromen ontwerpen

De gegevens stroom heeft een uniek Designing-canvas waarmee u eenvoudig transformatie logica kunt bouwen. Het canvas voor de gegevens stroom is onderverdeeld in drie delen: de bovenste balk, de grafiek en het configuratie paneel. 

![Scherm afbeelding toont het canvas gegevensstroom met de bovenste balk, het diagram en het configuratie venster met het label.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

In de grafiek wordt de transformatie stroom weer gegeven. De afkomst van de bron gegevens worden weer gegeven terwijl deze in een of meer sinks worden stromen. Selecteer **bron toevoegen** om een nieuwe bron toe te voegen. Als u een nieuwe trans formatie wilt toevoegen, selecteert u het plus teken aan de rechter benedenhoek van een bestaande trans formatie. Meer informatie over [het beheren van de gegevens stroom grafiek](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Scherm afbeelding toont het grafiek gedeelte van het canvas met een zoekvak.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Configuratie paneel

In het configuratie scherm worden de instellingen weer gegeven die specifiek zijn voor de momenteel geselecteerde trans formatie. Als er geen trans formatie is geselecteerd, wordt de gegevens stroom weer gegeven. In de algehele configuratie van de gegevens stroom kunt u para meters toevoegen via het tabblad **para meters** . Zie [Data flow-para meters](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

Elke trans formatie bevat ten minste vier configuratie tabbladen.

#### <a name="transformation-settings"></a>Instellingen voor trans formatie

Het eerste tabblad in het configuratie venster van elke trans formatie bevat de instellingen die specifiek zijn voor die trans formatie. Zie de documentatie pagina van die trans formatie voor meer informatie.

![Tabblad Bron instellingen](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimaliseren

Het tabblad **Optimize** bevat instellingen voor het configureren van partitie schema's. Voor meer informatie over hoe u uw gegevens stromen optimaliseert, raadpleegt u de [richt lijnen voor het toewijzen van gegevens stromen](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Scherm afbeelding toont het tabblad optimaliseren](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecteren

Het tabblad **controleren** bevat een weer gave van de meta gegevens van de gegevensstroom die u transformeert. U kunt kolom aantallen zien, de kolommen gewijzigd, de kolommen die zijn toegevoegd, de gegevens typen, de kolom volgorde en kolom verwijzingen. **Inspecteer** is een alleen-lezen weer gave van uw meta gegevens. U hoeft de foutopsporingsmodus niet in te scha kelen om meta gegevens in het deel venster **controleren** weer te geven.

![Tabblad controleren](media/data-flow/inspect.png)

Wanneer u de vorm van uw gegevens via trans formaties wijzigt, worden de wijzigingen in de meta gegevens in het deel venster **controleren** weer gegeven. Als er geen gedefinieerd schema is in uw bron transformatie, worden de meta gegevens niet weer gegeven in het deel venster **controleren** . Het ontbreken van meta gegevens is gebruikelijk in schema-drift-scenario's.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, biedt het tabblad **voor beeld van gegevens** een interactieve moment opname van de gegevens bij elke trans formatie. Zie voor meer informatie de [Preview van gegevens in de foutopsporingsmodus](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevens stroom, zoals validatie-en debug-instellingen. U kunt ook de onderliggende JSON-code en het gegevensstroom script van uw transformatie logica weer geven.

## <a name="available-transformations"></a>Beschik bare trans formaties

Bekijk het [overzicht van de toewijzings gegevens stroom transformatie](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor een lijst met beschik bare trans formaties.

## <a name="data-flow-activity"></a>Activiteit gegevens stroom

Gegevens stromen worden operationeel in azure Synapse Analytics-pijp lijnen met behulp van de [activiteit gegevens stroom](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Alle een gebruiker heeft te maken met de Integration runtime die moet worden gebruikt en waarmee parameter waarden worden door gegeven. Meer informatie vindt u in de [Azure Integration runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Foutopsporingsmodus

Met de foutopsporingsmodus kunt u de resultaten van elke transformatie stap interactief bekijken terwijl u uw gegevens stromen bouwt en oplost. De foutopsporingssessie kan worden gebruikt bij het samen stellen van de logica van de gegevens stroom en het uitvoeren van de fout opsporing van pijp lijnen met gegevens stroom activiteiten. Zie de documentatie voor de [foutopsporingsmodus](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

## <a name="monitoring-data-flows"></a>Gegevens stromen bewaken

De gegevens stroom is geïntegreerd met bestaande Azure Synapse Analytics-bewakings mogelijkheden. Zie [toewijzings gegevens stroom controleren](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over het bewaken van de uitvoer van de bewaking van gegevens stromen.

Het Azure Synapse Analytics-team heeft een [hulp programma voor het afstemmen van prestaties](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) gemaakt om u te helpen bij het optimaliseren van de uitvoerings tijd van uw gegevens stromen na het maken van uw bedrijfs logica.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [bron transformatie](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Meer informatie over het bouwen van uw gegevens stromen in de [foutopsporingsmodus](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
