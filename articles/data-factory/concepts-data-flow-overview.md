---
title: Toewijzing gegevensstromen
description: Een overzicht van het toewijzen van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: 1529d2d546227880fee71d1823482e040d2e57c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564317"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Gegevens stromen toewijzen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Wat zijn toewijzingsgegevensstromen?

Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties in Azure Data Factory. Met gegevens stromen kunnen gegevens technici logica voor gegevens transformatie ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen die gebruikmaken van uitgeschaalde Apache Spark clusters. Gegevens stroom activiteiten kunnen worden operationeel met bestaande Azure Data Factory plannings-, beheer-, stroom-en bewakings mogelijkheden.

Het toewijzen van gegevens stromen biedt een volledig visuele ervaring zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op ADF-beheerde uitvoerings clusters voor uitgeschaalde gegevens verwerking. Azure Data Factory verwerkt alle code omzetting, optimalisatie van paden en de uitvoering van uw gegevens stroom taken.

## <a name="getting-started"></a>Aan de slag

Gegevens stromen worden gemaakt op basis van het deel venster resources, zoals pijp lijnen en gegevens sets. Als u een gegevens stroom wilt maken, selecteert u het plus teken naast **Factory-resources** en selecteert u vervolgens **gegevens stroom**. 

![Nieuwe gegevens stroom](media/data-flow/new-data-flow.png)

Met deze actie gaat u naar het canvas voor gegevens stromen, waar u uw transformatie logica kunt maken. Selecteer **bron toevoegen** om het configureren van de bron transformatie te starten. Zie [bron transformatie](data-flow-source.md)voor meer informatie.

## <a name="authoring-data-flows"></a>Gegevens stromen ontwerpen

De toewijzing van gegevens stroom heeft een uniek ontwerp teken dat is ontworpen om het maken van transformatie logica eenvoudig te maken. Het canvas voor de gegevens stroom is onderverdeeld in drie delen: de bovenste balk, de grafiek en het configuratie paneel. 

![Scherm afbeelding toont het canvas gegevensstroom met de bovenste balk, het diagram en het configuratie venster met het label.](media/data-flow/canvas-1.png "Canvas")

### <a name="graph"></a>Graph

In de grafiek wordt de transformatie stroom weer gegeven. De afkomst van de bron gegevens worden weer gegeven terwijl deze in een of meer sinks worden stromen. Selecteer **bron toevoegen** om een nieuwe bron toe te voegen. Als u een nieuwe trans formatie wilt toevoegen, selecteert u het plus teken aan de rechter benedenhoek van een bestaande trans formatie. Meer informatie over [het beheren van de gegevens stroom grafiek](concepts-data-flow-manage-graph.md).

![Scherm afbeelding toont het grafiek gedeelte van het canvas met een zoekvak.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Configuratie paneel

In het configuratie scherm worden de instellingen weer gegeven die specifiek zijn voor de momenteel geselecteerde trans formatie. Als er geen trans formatie is geselecteerd, wordt de gegevens stroom weer gegeven. In de algehele configuratie van de gegevens stroom kunt u para meters toevoegen via het tabblad **para meters** . Zie [Data flow-para meters toewijzen](parameters-data-flow.md)voor meer informatie.

Elke trans formatie bevat ten minste vier configuratie tabbladen.

#### <a name="transformation-settings"></a>Instellingen voor trans formatie

Het eerste tabblad in het configuratie venster van elke trans formatie bevat de instellingen die specifiek zijn voor die trans formatie. Zie de documentatie pagina van die trans formatie voor meer informatie.

![Tabblad Bron instellingen](media/data-flow/source1.png "Tabblad Bron instellingen")

#### <a name="optimize"></a>Optimaliseren

Het tabblad **Optimize** bevat instellingen voor het configureren van partitie schema's. Voor meer informatie over hoe u uw gegevens stromen optimaliseert, raadpleegt u de [richt lijnen voor het toewijzen van gegevens stromen](concepts-data-flow-performance.md).

![Scherm afbeelding toont het tabblad Optimize, inclusief partitie optie, partitie type en aantal partities.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecteren

Het tabblad **controleren** bevat een weer gave van de meta gegevens van de gegevensstroom die u transformeert. U kunt kolom aantallen zien, de kolommen gewijzigd, de kolommen die zijn toegevoegd, de gegevens typen, de kolom volgorde en kolom verwijzingen. **Inspecteer** is een alleen-lezen weer gave van uw meta gegevens. U hoeft de foutopsporingsmodus niet in te scha kelen om meta gegevens in het deel venster **controleren** weer te geven.

![Inspecteren](media/data-flow/inspect1.png "Inspecteren")

Wanneer u de vorm van uw gegevens via trans formaties wijzigt, worden de wijzigingen in de meta gegevens in het deel venster **controleren** weer gegeven. Als er geen gedefinieerd schema is in uw bron transformatie, worden de meta gegevens niet weer gegeven in het deel venster **controleren** . Het ontbreken van meta gegevens is gebruikelijk in schema-drift-scenario's.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, biedt het tabblad **voor beeld van gegevens** een interactieve moment opname van de gegevens bij elke trans formatie. Zie voor meer informatie de [Preview van gegevens in de foutopsporingsmodus](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevens stroom, zoals opslaan en valideren. U kunt ook de onderliggende JSON-code en het gegevensstroom script van uw transformatie logica weer geven. Meer informatie vindt u in het [script voor gegevens stromen](data-flow-script.md).

## <a name="available-transformations"></a>Beschik bare trans formaties

Bekijk het [overzicht van de toewijzings gegevens stroom transformatie](data-flow-transformation-overview.md) voor een lijst met beschik bare trans formaties.

## <a name="data-flow-activity"></a>Activiteit gegevens stroom

Het toewijzen van gegevens stromen is operationeel in ADF-pijp lijnen met behulp van de [activiteit gegevens stroom](control-flow-execute-data-flow-activity.md). Alle een gebruiker heeft te maken met de Integration runtime die moet worden gebruikt en waarmee parameter waarden worden door gegeven. Meer informatie vindt u in de [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Foutopsporingsmodus

Met de foutopsporingsmodus kunt u de resultaten van elke transformatie stap interactief bekijken terwijl u uw gegevens stromen bouwt en oplost. De foutopsporingssessie kan worden gebruikt bij het samen stellen van de logica van de gegevens stroom en het uitvoeren van de fout opsporing van pijp lijnen met gegevens stroom activiteiten. Zie de documentatie voor de [foutopsporingsmodus](concepts-data-flow-debug-mode.md)voor meer informatie.

## <a name="monitoring-data-flows"></a>Gegevens stromen bewaken

De stroom voor het toewijzen van gegevens kan worden geïntegreerd met bestaande Azure Data Factory bewakings mogelijkheden. Zie [toewijzings gegevens stroom controleren](concepts-data-flow-monitoring.md)voor meer informatie over het bewaken van de uitvoer van de bewaking van gegevens stromen.

Het Azure Data Factory-team heeft een [hulp programma voor het afstemmen van prestaties](concepts-data-flow-performance.md) gemaakt om u te helpen bij het optimaliseren van de uitvoerings tijd van uw gegevens stromen na het maken van uw bedrijfs logica.

## <a name="available-regions"></a>Beschikbare regio's

= = = = = = De toewijzing van gegevens stromen zijn beschikbaar in de volgende regio's in ADF:

| Azure-regio | Gegevens stromen in ADF |
| ------------ | ----------------- |
|  Australië - centraal | |
| Australië - centraal 2 | |
| Australië - oost | ✓ |
| Australië - zuidoost   | ✓ |
| Brazilië - zuid  | ✓ |
| Canada - midden | ✓ |
| India - centraal | ✓ |
| VS - centraal    | ✓ |
| China East |      |
| China - oost 2  |   |
| China niet-regionaal | |
| China - noord |     |
| China - noord 2 | |
| Azië - oost | ✓ |
| VS - oost   | ✓ |
| VS - oost 2 | ✓ |
| Frankrijk - centraal | ✓ |
| Frankrijk - zuid  | |
| Duitsland-centraal (soeverein) | |
| Duitsland-niet-regionaal (soeverein) | |
| Duitsland-noord (openbaar) | |
| Duitsland-noordoost (soeverein) | |
| Duitsland-west-centraal (openbaar) |  |
| Japan - oost | ✓ |
| Japan - west |  |
| Korea - centraal | ✓ |
| Korea - zuid | |
| VS - noord-centraal  | ✓ |
| Europa - noord  | ✓ |
| Noorwegen - oost | |
| Noorwegen - west | |
| Zuid-Afrika - noord    | ✓ |
| Zuid-Afrika - west |  |
| VS - zuid-centraal  | |
| India - zuid | |
| Azië - zuidoost    | ✓ |
| Zwitserland - noord |   |
| Zwitserland - west | |
| UAE - centraal | |
| VAE - noord |  |
| Verenigd Koninkrijk Zuid  | ✓ |
| Verenigd Koninkrijk West |     |
| US DoD Central | |
| US DoD East | |
| VS (overheid) - Arizona |      |
| US Gov - niet-regionaal | |
| VS (overheid) - Texas | |
| VS (overheid) - Virginia |     |
| VS - west-centraal |     |
| Europa -west   | ✓ |
| India - west | |
| VS - west   | ✓ |
| VS - west 2 | ✓ |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [bron transformatie](data-flow-source.md).
* Meer informatie over het bouwen van uw gegevens stromen in de [foutopsporingsmodus](concepts-data-flow-debug-mode.md).
