---
title: Toewijzing gegevensstromen
description: Een overzicht van toewijzingsgegevensstromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/14/2021
ms.openlocfilehash: 826183e09f2aa7f3f22ace8b5ce3e16767d49863
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515652"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Toewijzingsgegevensstromen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Wat zijn toewijzingsgegevensstromen?

Toewijzingsgegevensstromen zijn visueel ontworpen gegevenstransformaties in Azure Data Factory. Met gegevensstromen kunnen data engineers logica voor gegevenstransformatie ontwikkelen zonder code te schrijven. De resulterende gegevensstromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijplijnen die gebruikmaken van Apache Spark clusters. Gegevensstroomactiviteiten kunnen worden operationeel gemaakt met behulp Azure Data Factory mogelijkheden voor planning, controle, stroom en bewaking.

Toewijzingsgegevensstromen bieden een volledig visuele ervaring zonder dat codering is vereist. Uw gegevensstromen worden uitgevoerd op ADF-beheerde uitvoeringsclusters voor geschaalde gegevensverwerking. Azure Data Factory verwerkt alle codevertalingen, padoptimalisatie en uitvoering van uw gegevensstroomtaken.

## <a name="getting-started"></a>Aan de slag

Gegevensstromen worden gemaakt vanuit het deelvenster factory-resources, zoals pijplijnen en gegevenssets. Als u een gegevensstroom wilt maken, selecteert u het plusteken naast **Factory-resources** en selecteert u **Gegevensstroom**. 

![Nieuwe gegevensstroom](media/data-flow/new-data-flow.png)

Met deze actie gaat u naar het gegevensstroomvas, waar u uw transformatielogica kunt maken. Selecteer **Bron toevoegen om** de brontransformatie te configureren. Zie Brontransformatie voor [meer informatie.](data-flow-source.md)

## <a name="authoring-data-flows&quot;></a>Gegevensstromen maken

Toewijzingsgegevensstroom heeft een uniek ontwerpvas dat is ontworpen om het bouwen van transformatielogica eenvoudig te maken. Het canvas voor de gegevensstroom bestaat uit drie delen: de bovenste balk, de grafiek en het configuratiepaneel. 

![Schermopname van het canvas van de gegevensstroom met de bovenste balk, grafiek en configuratiepaneel gelabeld.](media/data-flow/canvas-1.png &quot;Canvas")

### <a name="graph"></a>Graph

In de grafiek wordt de transformatiestroom weergegeven. U ziet de herkomst van brongegevens wanneer deze naar een of meer sinks stromen. Als u een nieuwe bron wilt toevoegen, selecteert **u Bron toevoegen.** Als u een nieuwe transformatie wilt toevoegen, selecteert u het plusteken rechtsbeneden van een bestaande transformatie. Meer informatie over het beheren [van de gegevensstroomgrafiek](concepts-data-flow-manage-graph.md).

![Schermopname van het graafgedeelte van het canvas met een zoektekstvak.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Configuratiepaneel

In het configuratiepaneel worden de instellingen weergegeven die specifiek zijn voor de geselecteerde transformatie. Als er geen transformatie is geselecteerd, wordt de gegevensstroom weer geven. In de configuratie van de algehele gegevensstroom kunt u parameters toevoegen via het **tabblad Parameters.** Zie Toewijzingsgegevensstroomparameters [voor meer informatie.](parameters-data-flow.md)

Elke transformatie bevat ten minste vier configuratietabbladen.

#### <a name="transformation-settings"></a>Transformatie-instellingen

Het eerste tabblad in het configuratiedeelvenster van elke transformatie bevat de instellingen die specifiek zijn voor die transformatie. Zie de documentatiepagina van die transformatie voor meer informatie.

![Tabblad Broninstellingen](media/data-flow/source1.png "Tabblad Broninstellingen")

#### <a name="optimize"></a>Optimaliseren

Het **tabblad Optimaliseren bevat** instellingen voor het configureren van partitieschema's. Zie de prestatiehandleiding voor toewijzingsgegevensstromen voor meer informatie over het optimaliseren [van uw gegevensstromen.](concepts-data-flow-performance.md)

![Schermopname van het tabblad Optimaliseren, met de optie Partitie, Partitietype en Aantal partities.](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>Inspecteren

Het **tabblad** Inspecteren biedt een weergave van de metagegevens van de gegevensstroom die u transformeert. U ziet het aantal kolommen, de gewijzigde kolommen, de toegevoegde kolommen, gegevenstypen, de kolomorder en kolomverwijzingen. **Inspecteren** is een alleen-lezenweergave van uw metagegevens. U hoeft de foutopsporingsmodus niet in te schakelen om metagegevens weer te geven in het **deelvenster** Inspecteren.

![Inspecteren](media/data-flow/inspect1.png &quot;Inspecteren")

Wanneer u de vorm van uw gegevens wijzigt via transformaties, ziet u de stroom metagegevenswijzigingen in het **deelvenster** Inspecteren. Als er geen gedefinieerd schema in uw brontransformatie is, zijn metagegevens niet zichtbaar in het **deelvenster** Inspecteren. Gebrek aan metagegevens is gebruikelijk in schemadriftscenario's.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is geselecteerd, geeft het tabblad **Gegevensvoorbeeld** u een interactieve momentopname van de gegevens bij elke transformatie. Zie Voorbeeld van gegevens in de [foutopsporingsmodus voor meer informatie.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevensstroom, zoals opslaan en valideren. U kunt ook het onderliggende JSON-code- en gegevensstroomscript van uw transformatielogica bekijken. Lees meer over het [gegevensstroomscript voor meer informatie.](data-flow-script.md)

## <a name="available-transformations"></a>Beschikbare transformaties

Bekijk het overzicht [van de transformatie van toewijzingsgegevensstromen](data-flow-transformation-overview.md) voor een lijst met beschikbare transformaties.

## <a name="data-flow-data-types"></a>Gegevenstypen van gegevensstromen

matrix binair booleaanse complexe decimale datum float integer lange kaart korte tekenreeks tijdstempel

## <a name="data-flow-activity"></a>Gegevensstroomactiviteit

Toewijzingsgegevensstromen worden operationeel gemaakt in ADF-pijplijnen met behulp van [de gegevensstroomactiviteit](control-flow-execute-data-flow-activity.md). Het enige wat een gebruiker hoeft te doen, is opgeven welke integratieruntime moet worden gebruikt en parameterwaarden moeten worden doorgegeven. Meer informatie over de [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)vindt u hier.

## <a name="debug-mode"></a>Foutopsporingsmodus

Met de foutopsporingsmodus kunt u interactief de resultaten van elke transformatiestap bekijken tijdens het bouwen en opsporen van fouten in uw gegevensstromen. De foutopsporingssessie kan zowel in worden gebruikt bij het bouwen van uw gegevensstroomlogica als bij het uitvoeren van pijplijnbug-runs met gegevensstroomactiviteiten. Zie de documentatie over de [foutopsporingsmodus voor meer informatie.](concepts-data-flow-debug-mode.md)

## <a name="monitoring-data-flows"></a>Gegevensstromen bewaken

Toewijzingsgegevensstroom kan worden geïntegreerd met bestaande Azure Data Factory bewakingsmogelijkheden. Zie Bewaking van toewijzingsgegevensstromen voor meer informatie over het begrijpen van uitvoer van [gegevensstroombewaking.](concepts-data-flow-monitoring.md)

Het Azure Data Factory team heeft een [](concepts-data-flow-performance.md) handleiding voor het afstemmen van de prestaties gemaakt om u te helpen de uitvoeringstijd van uw gegevensstromen te optimaliseren na het bouwen van uw bedrijfslogica.


## <a name="available-regions"></a>Beschikbare regio's

Toewijzingsgegevensstromen zijn beschikbaar in de volgende regio's in ADF:

| Azure-regio | Gegevensstromen in ADF |
| ------------ | ----------------- |
| Australië - centraal | |
| Australië - centraal 2 | |
| Australië - oost | ✓ |
| Australië - zuidoost   | ✓ |
| Brazilië - zuid  | ✓ |
| Canada - midden | ✓ |
| India - centraal | ✓ |
| Central US    | ✓ |
| China East |      |
| China - oost 2  |   |
| China - niet-regionaal | |
| China - noord | ✓ |
| China - noord 2 | ✓ |
| Azië - oost | ✓ |
| VS - oost   | ✓ |
| VS - oost 2 | ✓ |
| Frankrijk - centraal | ✓ |
| Frankrijk - zuid  | |
| Duitsland - centraal (onafhankelijk) | |
| Duitsland - niet-regionaal (onafhankelijk) | |
| Duitsland - noord (openbaar) | |
| Duitsland - noordoost (onafhankelijk) | |
| Duitsland - west-centraal (openbaar) |  |
| Japan - oost | ✓ |
| Japan - west |  |
| Korea - centraal | ✓ |
| Korea - zuid | |
| VS - noord-centraal  | ✓ |
| Europa - noord  | ✓ |
| Noorwegen - oost | ✓ |
| Noorwegen - west | |
| Zuid-Afrika - noord    | ✓ |
| Zuid-Afrika - west |  |
| VS - zuid-centraal  | |
| India - zuid | |
| Azië - zuidoost    | ✓ |
| Zwitserland - noord |   |
| Zwitserland - west | |
| UAE - centraal | |
| VAE - noord | ✓ |
| Verenigd Koninkrijk Zuid  | ✓ |
| Verenigd Koninkrijk West |     |
| US DoD Central | |
| US DoD East | |
| VS (overheid) - Arizona | ✓ |
| US Gov - niet-regionaal | |
| VS (overheid) - Texas | |
| VS (overheid) - Virginia | ✓ |
| VS - west-centraal |     |
| Europa -west   | ✓ |
| India - west | |
| VS - west   | ✓ |
| VS - west 2 | ✓ |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van [een brontransformatie.](data-flow-source.md)
* Meer informatie over het bouwen van uw gegevensstromen in [de foutopsporingsmodus.](concepts-data-flow-debug-mode.md)
