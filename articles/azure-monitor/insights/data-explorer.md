---
title: Azure Data Explorer Insights (preview-versie van ADX Insights) | Microsoft Docs
description: In dit artikel wordt Azure Data Explorer Insights (ADX Insights) beschreven
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: a8aae2dc03ba87e9782cdf3952be1bfc4a1aae75
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767037"
---
# <a name="azure-data-explorer-insights-preview"></a>Azure Data Explorer Insights (preview)

Azure Data Explorer Insights (preview) biedt uitgebreide bewaking van uw clusters door een uniforme weergave te bieden van de prestaties, bewerkingen, gebruik en fouten van uw cluster.
Dit artikel helpt u inzicht te krijgen in het onboarden en gebruiken Azure Data Explorer Insights (preview).

## <a name="introduction-to-azure-data-explorer-insights-preview"></a>Inleiding tot Azure Data Explorer Insights (preview)

Voordat u in de ervaring gaat kijken, moet u begrijpen hoe deze informatie presenteert en visualiseert.
-    **In het perspectief van de** schaal een momentopnameweergave van de primaire metrische gegevens van uw clusters, om eenvoudig de prestaties van query's, opname- en exportbewerkingen bij te houden.
-   **Analyse van een bepaald** cluster Azure Data Explorer om gedetailleerde analyse uit te voeren.
-    **Aanpasbaar** waar u kunt wijzigen welke metrische gegevens u wilt zien, wijzigen of drempelwaarden instellen die zijn afgestemd op uw limieten, en uw eigen aangepaste werkmappen opslaan. Grafieken in de werkmap kunnen worden vastgemaakt aan Azure-dashboards.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Weergeven vanuit Azure Monitor (op schaalperspectief)

Vanuit Azure Monitor kunt u de belangrijkste metrische prestatiegegevens voor het cluster bekijken, inclusief metrische gegevens voor query's, opname- en exportbewerkingen vanuit meerdere clusters in uw abonnement, en helpen bij het identificeren van prestatieproblemen.

Voer de volgende stappen uit om de prestaties van uw clusters voor al uw abonnementen weer te geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)

2. Selecteer **Controleren** in het linkerdeelvenster in Azure Portal en selecteer onder de sectie Insights Hub de **optie Azure Data Explorer Clusters (preview)**.

![Schermopname van overzichtservaring met meerdere grafieken](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Tabblad Overzicht

Op het **tabblad Overzicht** voor het geselecteerde abonnement worden in de tabel interactieve metrische gegevens weergegeven voor de Azure Data Explorer clusters die zijn gegroepeerd binnen het abonnement. U kunt resultaten filteren op basis van de opties die u in de volgende vervolgkeuzelijsten selecteert:

* Abonnementen: alleen abonnementen met Azure Data Explorer clusters worden weergegeven.

* Azure Data Explorer clusters: standaard zijn slechts maximaal vijf clusters vooraf geselecteerd. Als u alle of meerdere clusters in de bereik selector selecteert, worden maximaal 200 clusters geretourneerd.

* Tijdsbereik: geeft standaard de afgelopen 24 uur aan informatie weer op basis van de bijbehorende selecties.

De tellertegel, onder de vervolgkeuzelijst, geeft het totale aantal Azure Data Explorer clusters in de geselecteerde abonnementen weer en geeft aan hoeveel er zijn geselecteerd. Er zijn voorwaardelijke kleurcoderingen voor de kolommen: Keep alive, CPU, Ingestion Utilization en Cache Utilization. Met oranje code gecodeerde cellen hebben waarden die niet duurzaam zijn voor het cluster. 

Voor een beter begrip van wat elk van de metrische gegevens vertegenwoordigt, raden we u aan de documentatie over de metrische [gegevens Azure Data Explorer lezen.](/azure/data-explorer/using-metrics#cluster-metrics)

### <a name="query-performance-tab"></a>Tabblad Queryprestaties

Op dit tabblad ziet u de queryduur, het totale aantal gelijktijdige query's en het totale aantal beperkt query's.

![Schermopname van het tabblad Queryprestaties](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Tabblad Opnameprestaties

Dit tabblad toont de opnamelatentie, de resultaten van de opname, de mislukte opnameresultaten, het opnamevolume en de gebeurtenissen die zijn verwerkt voor Event/IoT Hubs.

[![Schermopname van het tabblad Opnameprestaties](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Tabblad Prestaties streaming-opname

Dit tabblad bevat informatie over de gemiddelde gegevenssnelheid, gemiddelde duur en aanvraagsnelheid.

### <a name="export-performance-tab"></a>Tabblad Prestaties exporteren

Dit tabblad bevat informatie over geëxporteerde records, de vertraging, het aantal in behandeling en het gebruikspercentage voor continue exportbewerkingen.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Weergeven vanuit een Azure Data Explorer clusterresource (analyse van inzoomen)

Voor toegang Azure Data Explorer Insights rechtstreeks vanuit een Azure Data Explorer Cluster:

1. Selecteer in Azure Portal de Azure Data Explorer **Clusters.**

2. Kies in de lijst een Azure Data Explorer Cluster. Kies in de sectie Bewaking **de optie Inzichten (preview)**.

Deze weergaven zijn ook toegankelijk door de resourcenaam van een Azure Data Explorer cluster te selecteren in Azure Monitor inzichtweergave.

Azure Data Explorer Insights combineert logboeken en metrische gegevens om een globale bewakingsoplossing te bieden. Voor het opnemen van visualisaties op basis van logboeken moeten gebruikers diagnostische logboekregistratie van hun Azure Data Explorer-cluster inschakelen en ze [verzenden naar een Log Analytics-werkruimte.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). De diagnostische logboeken die moeten worden ingeschakeld, zijn: **Opdracht**, **Query**, **TableDetails** en **TableUsageStatistics.**

![Schermopname van de blauwe knop met de tekst 'Logboeken inschakelen voor bewaking'](./media/data-explorer/enable-logs.png)


 Op **het tabblad** Overzicht ziet u het volgende:

- Tegels met metrische gegevens die de beschikbaarheid en algemene status van het cluster markeren om snel de status ervan te beoordelen.

- Een samenvatting van actieve [Advisor-aanbevelingen en](/azure/data-explorer/azure-advisor) [status van de](/azure/data-explorer/monitor-with-resource-health) resource.

- Grafieken met de belangrijkste CPU- en geheugengebruikers en het aantal unieke gebruikers gedurende een periode.


[![Schermopname van de weergave van Azure Data Explorer clusterresource](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Op **het tabblad Belangrijke** metrische gegevens ziet u een uniforme weergave van enkele van de metrische gegevens van het cluster, gegroepeerd op: algemene metrische gegevens, querygerelateerde, opnamegerelateerde en streaminggegevens met betrekking tot opname.

[![Schermopname van de weergave Fouten](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Op **het tabblad** Gebruik kunnen gebruikers dieper in op de prestaties van de opdrachten en query's van het cluster. Op deze pagina kunt u het volgende doen:
 
 - Kijk welke workloadgroepen, gebruikers en toepassingen de meeste query's verzenden of de meeste CPU en geheugen gebruiken (zodat u begrijpt welke workloads de zwaarste query's verzenden die het cluster moet verwerken).
 - Identificeer de belangrijkste workloadgroepen, gebruikers en toepassingen op basis van mislukte query's.
 - Identificeer recente wijzigingen in het aantal query's, vergeleken met het historische dagelijkse gemiddelde (in de afgelopen 16 dagen), per werkbelastinggroep, gebruiker en toepassing.
 - Identificeer trends en pieken in het aantal query's, geheugen en CPU-verbruik per werkbelastinggroep, gebruiker, toepassing en opdrachttype.

[![Schermopname van de weergave Bewerkingen met de donut-grafieken van de bovenste toepassing op opdracht- en query-aantal, belangrijkste principals op opdracht- en querytelling en belangrijkste opdrachten op opdrachttypen](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Schermopname van de weergave Bewerkingen met lijndiagrammen van het aantal query's per toepassing, totaal geheugen per toepassing en het totale CPU-gebruik per toepassing](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Op **het tabblad** Tabellen worden de meest recente en historische eigenschappen van tabellen in het cluster weergegeven. U kunt zien welke tabellen de meeste ruimte verbruiken, de groeigeschiedenis bijhouden op tabelgrootte, hot data en het aantal rijen in de tijd.

Op **het tabblad Cache** kunnen gebruikers de zoekpatronen van hun werkelijke query's analyseren en deze vergelijken met het geconfigureerde cachebeleid (voor elke tabel). U kunt tabellen identificeren die worden gebruikt door de meeste query's en tabellen die helemaal niet worden opgevraagd en het cachebeleid dienovereenkomstig aanpassen. Mogelijk krijgt u bepaalde aanbevelingen voor cachebeleid voor specifieke tabellen in Azure Advisor (momenteel zijn aanbevelingen voor de cache alleen beschikbaar via het hoofddashboard [van het Azure Advisor),](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)op basis van de werkelijke query's in de afgelopen 30 dagen en een niet-geoptimaliseerd cachebeleid voor ten minste 95% van de query's. Aanbevelingen voor het verminderen van de cache in Azure Advisor zijn beschikbaar voor clusters die worden 'gebonden door gegevens' (wat betekent dat het cluster een laag CPU- en laag opnamegebruik heeft, maar vanwege een hoge gegevenscapaciteit kan het cluster niet worden ingeschaald of omlaag geschaald).

[![Schermopname van cachedetails](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

Op **het tabblad Clustergrenzen worden** de clustergrenzen weergegeven op basis van uw gebruik. Op dit tabblad kunt u het CPU-, opname- en cachegebruik inspecteren. Deze metrische gegevens worden als 'Laag', 'Gemiddeld' of 'Hoog' scoren. Deze metrische gegevens en scores zijn belangrijk bij het bepalen van de optimale SKU en het aantal exemplaren voor uw cluster. Deze worden meegenomen in Azure Advisor aanbeveling voor SKU/grootte. Op dit tabblad kunt u een tegel met metrische gegevens en diepgaande kennis selecteren om inzicht te krijgen in de trend en hoe de score wordt bepaald. U kunt ook de aanbeveling Azure Advisor SKU/grootte voor uw cluster weergeven. In de volgende afbeelding kunt u bijvoorbeeld zien dat alle metrische gegevens worden scoren als 'Laag', waardoor het cluster een kostenaanbeveling ontvangt, zodat het kan in-/omlaag schalen en kosten kan besparen.

> [!div class="mx-imgBorder"]
> [![Schermopname van de clustergrenzen.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Vastmaken aan Azure-dashboard

U kunt een van de metrische secties (van het perspectief op schaal) vastmaken aan een Azure-dashboard door rechtsboven in de sectie het punaisepictogram te selecteren.

![Schermopname van het geselecteerde speldpictogram](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Inzichten Azure Data Explorer aanpassen

In deze sectie worden algemene scenario's belicht voor het bewerken van de werkmap om deze aan te passen ter ondersteuning van uw behoeften voor gegevensanalyse:
* Bereik van de werkmap om altijd een bepaald abonnement of een bepaald Azure Data Explorer selecteren
* Metrische gegevens in het raster wijzigen
* Drempelwaarden wijzigen of kleuren weergeven/coderen

U kunt aanpassingen starten door de bewerkingsmodus in te schakelen door **de** knop Aanpassen te selecteren in de bovenste werkbalk.

![Schermopname van de knop Aanpassen](./media/data-explorer/customize.png)

Aanpassingen worden opgeslagen in een aangepaste werkmap om te voorkomen dat de standaardconfiguratie in onze gepubliceerde werkmap wordt overschreven. Werkmappen worden opgeslagen in een resourcegroep, in de sectie Mijn rapporten die privé voor u is of in de sectie Gedeelde rapporten die toegankelijk is voor iedereen met toegang tot de resourcegroep. Nadat u de aangepaste werkmap hebt op slaan, moet u naar de werkmapgalerie gaan om deze te starten.

![Schermopname van de werkmapgalerie](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg het artikel over het oplossen van problemen met toegewezen op werkmapken gebaseerde inzichten voor algemene [richtlijnen voor probleemoplossing.](troubleshoot-workbooks.md)

Deze sectie helpt u bij het diagnosticeren en oplossen van een aantal veelvoorkomende problemen die kunnen voorkomen bij het gebruik van Azure Data Explorer Insights (preview). Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Waarom zie ik niet al mijn abonnementen in de abonnementsverkender?

We geven alleen abonnementen weer die Azure Data Explorer Clusters bevatten, die zijn gekozen uit het geselecteerde abonnementsfilter, die zijn geselecteerd in de map + abonnement in de Azure Portal koptekst.

![Schermopname van het abonnementsfilter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Waarom zie ik geen gegevens voor mijn Azure Data Explorer cluster onder de secties Gebruik, Tabellen of Cache?

Als u uw gegevens op basis [](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) van logboeken wilt weergeven, moet u diagnostische logboeken inschakelen voor elk van de Azure Data Explorer Clusters die u wilt bewaken. Dit kan worden gedaan onder de diagnostische instellingen voor elk cluster. U moet uw gegevens verzenden naar een Log Analytics-werkruimte. De diagnostische logboeken die moeten worden ingeschakeld, zijn: Command, Query, TableDetails en TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Ik heb al logboeken ingeschakeld voor mijn Azure Data Explorer Cluster. Waarom kan ik mijn gegevens nog steeds niet zien onder Opdrachten en query's?

Diagnostische logboeken werken momenteel niet met terugwerkende kracht, dus de gegevens worden pas weergegeven wanneer er acties zijn ondernomen om uw Azure Data Explorer. Daarom kan het enige tijd duren, variërend van uren tot een dag, afhankelijk van hoe actief uw Azure Data Explorer cluster is.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de scenario's die werkmappen ondersteunen, hoe u nieuwe rapporten kunt maken en aanpassen, en meer door Interactieve rapporten maken met Azure Monitor [bekijken.](../visualize/workbooks-overview.md)
