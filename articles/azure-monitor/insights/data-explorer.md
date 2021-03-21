---
title: Azure Monitor voor Azure Data Explorer (preview) | Microsoft Docs
description: In dit artikel wordt Azure Monitor inzichten voor Azure Data Explorer-clusters beschreven.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: dcfe12b30e336863c8e112d9ad675a2f57fe48f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179133"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor voor Azure Data Explorer (preview-versie)

Azure Monitor voor Azure Data Explorer (preview) biedt uitgebreide bewaking van uw clusters door een uniforme weer gave te bieden van uw cluster prestaties, bewerkingen, gebruik en fouten.
In dit artikel wordt uitgelegd hoe u Azure Monitor voor de onboarding en het gebruik van Azure Data Explorer (preview) kunt gebruiken.

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Inleiding tot Azure Monitor voor Azure Data Explorer (preview-versie)

Voordat u naar de ervaring gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd.
-    **Op schaal perspectief** met een moment opname weergave van de primaire meet waarden van uw clusters kunt u eenvoudig de prestaties van query's, opname en export bewerkingen bijhouden.
-   **Zoom analyse** van een bepaald Azure Data Explorer-cluster uit om gedetailleerde analyse uit te voeren.
-    **Aanpasbaar** waar u kunt wijzigen welke metrische gegevens u wilt zien, wijzigen of instellen van drempel waarden die met uw limieten worden uitgelijnd en uw eigen aangepaste werkmappen opslaan. Grafieken in de werkmap kunnen worden vastgemaakt aan Azure-Dash boards.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Weer geven van Azure Monitor (op schaal perspectief)

Vanuit Azure Monitor kunt u de belangrijkste prestatie gegevens voor het cluster weer geven, met inbegrip van metrische gegevens voor query's, opname en export bewerkingen uit meerdere clusters in uw abonnement, en kunt u prestatie problemen identificeren.

Voer de volgende stappen uit om de prestaties van uw clusters in al uw abonnementen weer te geven:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)

2. Selecteer **monitor** in het linkerdeel venster in het Azure Portal en selecteer in de sectie Insights-Hub **Azure Data Explorer-clusters (preview)**.

![Scherm afbeelding van overzichts ervaring met meerdere grafieken](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Tabblad Overzicht

Op het tabblad **overzicht** voor het geselecteerde abonnement worden in de tabel interactieve metrische gegevens weer gegeven voor de Azure-Data Explorer clusters gegroepeerd in het abonnement. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* Abonnementen: alleen abonnementen met Azure Data Explorer-clusters worden weer gegeven.

* Azure Data Explorer-clusters: standaard zijn Maxi maal vijf clusters vooraf geselecteerd. Als u in de scope selector alle of meerdere clusters selecteert, worden er Maxi maal 200 clusters geretourneerd.

* Tijds bereik: standaard wordt de laatste 24 uur aan gegevens weer gegeven op basis van de overeenkomstige gemaakte selecties.

De tegel item, onder de vervolg keuzelijst, geeft het totale aantal Azure Data Explorer-clusters in de geselecteerde abonnementen weer en laat zien hoeveel er is geselecteerd. Er zijn voorwaardelijke kleur codes voor de kolommen: behoud Alive, CPU, opname gebruik en cache gebruik. Oranje gecodeerde cellen hebben waarden die niet duurzaam zijn voor het cluster. 

Voor een beter begrip van de metrische gegevens, raden we u aan de documentatie over [Azure Data Explorer metrische gegevens](/azure/data-explorer/using-metrics#cluster-metrics)te lezen.

### <a name="query-performance-tab"></a>Tabblad Query prestaties

Dit tabblad bevat de query duur, het totale aantal gelijktijdige query's en het totale aantal vertraagde query's.

![Scherm afbeelding van het tabblad Query prestaties](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Tabblad opname prestaties

Dit tabblad bevat de opname latentie, geslaagde opname resultaten, mislukte opname resultaten, opname volume en gebeurtenissen die zijn verwerkt voor Event/IoT hubs.

[![Scherm afbeelding van het tabblad opname prestaties](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Het tabblad prestaties van streaming-opname

Dit tabblad bevat informatie over de gemiddelde gegevens frequentie, de gemiddelde duur en de aanvraag frequentie.

### <a name="export-performance-tab"></a>Tabblad Export prestaties

Dit tabblad bevat informatie over geëxporteerde records, de achterstand, het aantal in behandeling en het gebruiks percentage voor doorlopende export bewerkingen.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Weer geven vanuit een Azure Data Explorer-cluster resource (analyse in-en uitzoomen)

Voor toegang tot Azure Monitor voor Azure Data Explorer-clusters rechtstreeks vanuit een Azure Data Explorer-cluster:

1. Selecteer in de Azure Portal **Azure Data Explorer-clusters**.

2. Kies een Azure Data Explorer-cluster in de lijst. Klik in de sectie bewaking op **inzichten (preview-versie)**.

Deze weer gaven zijn ook toegankelijk door de resource naam van een Azure Data Explorer-cluster te selecteren in de Azure Monitor Insights-weer gave.

Azure Monitor voor Azure Data Explorer combineert logboeken en metrische gegevens om een algemene bewakings oplossing te bieden. Voor het opnemen van op Logboeken gebaseerde visualisaties moeten gebruikers de [Diagnostische logboek registratie van hun Azure Data Explorer-cluster inschakelen en naar een log Analytics-werk ruimte verzenden.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) De diagnostische logboeken die moeten worden ingeschakeld, zijn: **opdracht**, **query**, **TableDetails** en **TableUsageStatistics**.

![Scherm afbeelding van een blauwe knop waarmee de tekst ' logboeken voor bewaking inschakelen ' wordt weer gegeven](./media/data-explorer/enable-logs.png)


 Op het tabblad **overzicht** ziet u:

- Tegels met metrische gegevens markeren de beschik baarheid en de algehele status van het cluster om de status snel te beoordelen.

- Een samen vatting van actieve [Advisor-aanbevelingen](/azure/data-explorer/azure-advisor) en de status van de [resource status](/azure/data-explorer/monitor-with-resource-health) .

- Grafieken met de belangrijkste CPU-en geheugen gebruikers en het aantal unieke gebruikers gedurende een bepaalde periode.


[![Scherm opname van de weer gave van een Azure Data Explorer-cluster resource](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Op het tabblad **belangrijkste metrische** gegevens ziet u een overzicht van een deel van de metrische gegevens van het cluster, gegroepeerd op: algemene metrische gegevens, aan query's gerelateerde, opname-en streaming-gerelateerde metrische gegevens.

[![Scherm opname van fouten weergave](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Op het tabblad **gebruik** kunnen gebruikers de prestaties van de opdrachten en query's van het cluster dieper opduiken. Op deze pagina kunt u het volgende doen:
 
 - Bekijk welke werkbelasting groepen, gebruikers en toepassingen de meeste query's verzenden of de meeste CPU en het geheugen verbruiken (zodat u weet welke workloads de zwaarste query's voor het cluster moeten worden uitgevoerd).
 - Bepaal de belangrijkste werkbelasting groepen, gebruikers en toepassingen op mislukte query's.
 - Identificeer recente wijzigingen in het aantal query's, vergeleken met het historische dagelijks gemiddelde (in de afgelopen 16 dagen), per werkbelasting groep, gebruiker en toepassing.
 - Spoor trends en pieken in het aantal query's, het geheugen en het CPU-gebruik op werkbelasting groep, gebruiker, toepassing en opdracht type.

[![Scherm afbeelding van een bewerkings weergave met ring grafieken van de bovenste toepassing op basis van de opdracht en het aantal query's, Top-principals op opdrachten en query's en de belangrijkste opdrachten op opdracht typen](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Scherm afbeelding van een bewerkings weergave met lijn diagrammen van het aantal query's per toepassing, totaal geheugen per toepassing en totale CPU per toepassing](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Op het tabblad **tabellen** worden de laatste en historische eigenschappen van tabellen in het cluster weer gegeven. U kunt zien welke tabellen de meeste ruimte verbruiken, de historie van de groei bijhouden op tabel grootte, dynamische gegevens en het aantal rijen in de loop van de tijd.

Op het tabblad **cache** kunnen gebruikers hun werkelijke Zoek patronen van query's analyseren en deze vergelijken met het geconfigureerde cache beleid (voor elke tabel). U kunt tabellen identificeren die worden gebruikt door de meeste query's en tabellen die helemaal niet worden opgevraagd, en het cache beleid dienovereenkomstig aan te passen. U kunt bepaalde aanbevelingen voor cache beleid verkrijgen voor specifieke tabellen in Azure Advisor (momenteel zijn cache aanbevelingen alleen beschikbaar vanuit het [hoofd Azure Advisor dash board](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), op basis van de werkelijke query's in de afgelopen 30 dagen en een niet-geoptimaliseerd cache beleid voor ten minste 95% van de query's. Aanbevelingen voor cache reductie in Azure Advisor zijn beschikbaar voor clusters die ' gebonden aan gegevens ' zijn (wat betekent dat het cluster weinig CPU en weinig opname verbruikt, maar vanwege een hoge gegevens capaciteit kan het cluster niet worden geschaald of geschaald).

[![Scherm opname van cache Details](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Aan Azure-dash board vastmaken

U kunt een van de metrische gedeeltes (van het perspectief ' op schaal ') aan een Azure-dash board vastmaken door het pictogram met het punaise te selecteren in de rechter bovenhoek van de sectie.

![Scherm afbeelding van het pictogram pincode geselecteerd](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Azure Monitor voor Azure Data Explorer-cluster aanpassen

In deze sectie worden algemene scenario's beschreven voor het bewerken van de werkmap om uw behoeften aan te passen aan uw gegevens analyse:
* De werkmap zo beperken dat er altijd een bepaald abonnement of Azure Data Explorer cluster (s) wordt geselecteerd
* Metrische gegevens in het raster wijzigen
* Drempel waarden wijzigen of kleur rendering/coderen

U kunt de aanpassingen beginnen door de bewerkings modus in te scha kelen door de knop **aanpassen** te selecteren in de bovenste werk balk.

![Scherm afbeelding van de knop aanpassen](./media/data-explorer/customize.png)

Aanpassingen worden opgeslagen in een aangepaste werkmap om te voor komen dat de standaard configuratie in onze gepubliceerde werkmap wordt overschreven. Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte Mijn rapporten dat persoonlijk is voor u of in de sectie gedeelde rapporten die toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Scherm opname van de werkmap galerie](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg voor algemene richt lijnen voor probleem oplossing het [artikel specifieke informatie over probleem](troubleshoot-workbooks.md)oplossing op basis van een werkmap.

Deze sectie helpt u bij het diagnosticeren en oplossen van problemen met enkele veelvoorkomende problemen die kunnen optreden bij het gebruik van Azure Monitor voor Azure Data Explorer cluster (preview). Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Waarom zie ik niet al mijn abonnementen in de abonnements kiezer?

Er worden alleen abonnementen weer gegeven die Azure Data Explorer-clusters bevatten, die u hebt gekozen in het geselecteerde abonnements filter, die zijn geselecteerd in het "adres lijst" in de Azure Portal-header.

![Scherm opname van het abonnements filter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Waarom zie ik geen gegevens voor mijn Azure Data Explorer-cluster in de secties gebruik, tabellen of cache?

Als u gegevens op basis van uw logboeken wilt weer geven, moet u [Diagnostische logboeken inschakelen](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) voor elk van de Azure Data Explorer-clusters die u wilt bewaken. Dit kan worden gedaan onder de diagnostische instellingen voor elk cluster. U moet uw gegevens verzenden naar een Log Analytics-werk ruimte. De diagnostische logboeken die moeten worden ingeschakeld, zijn: opdracht, query, TableDetails en TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Ik heb logboeken voor mijn Azure Data Explorer-cluster al ingeschakeld, waarom kan ik mijn gegevens nog steeds niet zien onder opdrachten en Query's?

Op dit moment werken Diagnostische logboeken niet met terugwerkende kracht, zodat de gegevens alleen worden weer gegeven wanneer er acties zijn uitgevoerd op uw Azure-Data Explorer. Daarom kan het enige tijd duren, variërend van uren tot een dag, afhankelijk van de manier waarop uw Azure Data Explorer-cluster actief is.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../visualize/workbooks-overview.md)te controleren.
