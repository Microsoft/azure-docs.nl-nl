---
title: Azure Monitor voor Azure-cache voor redis | Microsoft Docs
description: In dit artikel wordt de Azure Monitor voor Azure Redis Cache functie beschreven, die eigen aren van caches biedt met een duidelijk beeld van de prestaties en het gebruik van problemen.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573454"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Azure Monitor voor Azure cache verkennen voor redis

Azure Monitor voor Azure cache voor redis-resources voor al uw Azure cache voor redis biedt een uniforme, interactieve weer gave van:

- Algehele prestaties
- Fouten
- Capaciteit
- Operationele status

Dit artikel helpt u bij het begrijpen van de voor delen van deze nieuwe bewakings ervaring. Ook wordt uitgelegd hoe u de ervaring kunt aanpassen en aanpassen aan de unieke behoeften van uw organisatie.

## <a name="introduction"></a>Inleiding

Voordat u met de ervaring begint, moet u weten hoe Azure Monitor voor Azure cache voor redis visueel informatie weergeeft.

Het biedt:

- **Op schaal perspectief** van uw Azure-cache voor redis-resources op één locatie in al uw abonnementen. U kunt een selectief bereik voor alleen de abonnementen en resources die u wilt evalueren.

- **Inzoom analyse** van een bepaalde Azure-cache voor de redis-resource. U kunt problemen vaststellen en gedetailleerde analyse van het gebruik, fouten, capaciteit en bewerkingen bekijken. Selecteer een van deze categorieën om een gedetailleerde weer gave van relevante informatie te bekijken.  

- **Aanpassing** van deze ervaring, die is gebouwd hierop Azure monitor werkmap sjablonen. Met de-ervaring kunt u wijzigen welke metrische gegevens worden weer gegeven en aanpassen of drempel waarden instellen die met uw limieten worden uitgelijnd. U kunt de wijzigingen opslaan in een aangepaste werkmap en vervolgens werkmap grafieken vastmaken aan Azure-Dash boards.

Voor deze functie hoeft u niets in te scha kelen of te configureren. De Azure-cache voor redis-gegevens wordt standaard verzameld.

>[!NOTE]
>Er zijn geen kosten verbonden aan het verkrijgen van toegang tot deze functie. Er worden alleen kosten in rekening gebracht voor de Azure Monitor essentiële functies die u configureert of inschakelt, zoals wordt beschreven op de pagina met [Azure monitor prijs informatie](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Metrische gegevens over gebruik en prestaties voor Azure cache voor redis weer geven

Voer de volgende stappen uit om het gebruik en de prestaties van uw opslag accounts voor al uw abonnementen weer te geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek naar **monitor** en selecteer **monitor**.

    ![Zoekvak met het woord ' Monitor ' en het Zoek resultaat van de service waarbij ' Monitor ' wordt weer gegeven met een snelheids meter](./media/cosmosdb-insights-overview/search-monitor.png)

1. Selecteer **Azure-cache voor redis**. Als deze optie niet aanwezig is, selecteert u **meer**  >  **Azure-cache voor redis**.

### <a name="overview"></a>Overzicht

In het **overzicht** wordt in de tabel interactieve Azure-cache weer gegeven voor redis-metrische gegevens. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

- **Abonnementen**: er worden alleen abonnementen weer gegeven met een Azure-cache voor de redis-resource.  

- **Azure-cache voor redis**: u kunt alle, een subset of één Azure-cache voor redis-resource selecteren.

- **Tijds bereik**: standaard wordt in de tabel de laatste vier uur informatie weer gegeven op basis van de bijbehorende selecties.

Er is een item tegel in de vervolg keuzelijst. De tegel toont het totale aantal Azure-caches voor redis-resources in de geselecteerde abonnementen. Voorwaardelijke kleur codes of heatmap voor werkmap kolommen rapport met metrische gegevens over trans acties. De diepste kleur vertegenwoordigt de hoogste waarde. Lichtere kleuren vertegenwoordigen lagere waarden.

Als u een vervolg keuzelijst pijl naast een van de Azure-cache voor redis bronnen selecteert, wordt er een uitsplitsing van de prestatie gegevens op het niveau van de afzonderlijke resource weer gegeven.

![Scherm afbeelding van de overzichts ervaring](./media/redis-cache-insights-overview/overview.png)

Wanneer u de naam van het Azure-cache geheugen selecteert voor de redis-resource die blauw is gemarkeerd, ziet u de standaard **overzichts** tabel voor het gekoppelde account. Deze kolommen worden weer gegeven:

- **Gebruikt geheugen**
- **Percentage gebruikt geheugen**
- **Serverbelasting**
- **Tijdlijn voor serverbelasting**
- **CPU**
- **Verbonden clients**
- **Cachemissers**
- **Fouten (max)**

### <a name="operations"></a>Operations

Wanneer u **bewerkingen** boven aan de pagina selecteert, wordt de tabel **bewerkingen** van de werkmap sjabloon geopend. Deze kolommen worden weer gegeven:

- **Totaalaantal bewerkingen**
- **Tijdlijn voor totaalaantal bewerkingen**
- **Bewerkingen per seconde**
- **Ophalingen**
- **Sets**

![Scherm afbeelding van de Operations-ervaring](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Gebruik

Wanneer u het **gebruik** boven aan de pagina selecteert, wordt de tabel **gebruik** van de werkmap sjabloon geopend. Deze kolommen worden weer gegeven:

- **Gelezen uit cache**
- **Tijdlijn voor gelezen uit cache**
- **Geschreven naar cache**
- **Cachetreffers**
- **Cachemissers**

![Scherm opname van de gebruiks ervaring](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Fouten

Wanneer u de **fout** boven aan de pagina selecteert, wordt de tabel **fouten** van de werkmap sjabloon geopend. Deze kolommen worden weer gegeven:

- **Totaalaantal fouten**
- **Failover/fouten**
- **UnresponsiveClient/fouten**
- **RDB/fouten**
- **AOF/fouten**
- **Exporteren/fouten**
- **Dataloss/fouten**
- **Importeren/fouten**

![Scherm opname van fouten met een uitsplitsing van het type HTTP-aanvraag](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Metrische definities

Raadpleeg het [artikel over de beschik bare metrische gegevens en rapportage-intervallen](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals)voor een volledige lijst van de metrische definities die deze werkmappen vormen.

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Weer geven vanuit een Azure-cache voor redis-resource

Voor toegang tot Azure Monitor voor Azure cache voor redis, rechtstreeks vanuit een afzonderlijke resource:

1. Selecteer in de Azure Portal Azure-cache voor redis.

2. Kies in de lijst een afzonderlijke Azure-cache voor redis-resource. Kies inzichten in het gedeelte bewaking.

    ![Scherm afbeelding van menu opties met de woorden ' inzichten ' die in een rood vak zijn gemarkeerd](./media/redis-cache-insights-overview/insights.png)

Deze weer gaven zijn ook toegankelijk door de resource naam van een Azure-cache te selecteren voor de redis-resource in de werkmap van het Azure Monitor niveau.

### <a name="resource-level-overview"></a>Overzicht van resource niveau

Op de **overzichts** werkmap voor de Azure redis cache worden verschillende prestatie gegevens weer gegeven waarmee u toegang krijgt tot:

- Interactieve prestatie grafieken met de meest essentiële gegevens met betrekking tot Azure cache voor redis-prestaties.

- Met metrische gegevens en status tegels worden Shard-prestaties, het totale aantal verbonden clients en de algehele latentie gemarkeerd.

![Scherm afbeelding van het dash board overzicht waarin informatie wordt weer gegeven over CPU-prestaties, gebruikt geheugen, verbonden clients, fouten, verlopen sleutels en verwijderde sleutels](./media/redis-cache-insights-overview/resource-overview.png)

Als u een van de andere tabbladen voor **prestaties** of **bewerkingen** selecteert, worden de respectieve werkmappen geopend.

### <a name="resource-level-performance"></a>Prestaties op resource niveau

![Scherm afbeelding van grafieken voor bron prestaties](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Bewerkingen op resource niveau

![Scherm afbeelding van grafieken voor bron bewerkingen](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Vastmaken, exporteren en uitvouwen

Als u een metrische gedeelte aan een [Azure-dash board](../../azure-portal/azure-portal-dashboards.md)wilt vastmaken, selecteert u het markerings teken in de rechter bovenhoek van de sectie.

![Een metrische sectie met het symbool punaise gemarkeerd](./media/cosmosdb-insights-overview/pin.png)

Als u uw gegevens wilt exporteren naar een Excel-indeling, selecteert u de pijl-omlaag links van het punaise symbool.

![Een gemarkeerd export-werkmap symbool](./media/cosmosdb-insights-overview/export.png)

Als u alle weer gaven in een werkmap wilt uitvouwen of samen vouwen, selecteert u het symbool voor uitvouwen links van het symbool voor exporteren.

![Een gemarkeerd symbool voor een uitgevouwen werkmap](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Azure Monitor voor Azure-cache voor redis aanpassen

Omdat deze ervaring is gebouwd hierop Azure monitor werkmap sjablonen, kunt u de **optie voor** het  >  opslaan van **wijzigingen bewerken** selecteren  >   om een kopie van uw gewijzigde versie in een aangepaste werkmap op te slaan.

![Een opdracht balk met gemarkeerde aanpassen](./media/cosmosdb-insights-overview/customize.png)

Werkmappen worden opgeslagen in een resource groep in de sectie **mijn rapporten** of in de sectie **gedeelde rapporten** . **Mijn rapporten** is alleen voor u beschikbaar. **Gedeelde rapporten** zijn beschikbaar voor iedereen die toegang heeft tot de resource groep.

Nadat u een aangepaste werkmap hebt opgeslagen, gaat u naar de werkmap galerie om deze te openen.

![Een opdracht balk met een gemarkeerde galerie](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg het [artikel speciale probleemoplossings problemen](troubleshoot-workbooks.md)op basis van werkmappen voor hulp bij het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor metrische gegevens](../alerts/alerts-metric.md) en [service status meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md) configureren om automatische waarschuwingen in te stellen die ondersteuning bieden bij het detecteren van problemen.

* Meer informatie over de scenario's die door werkmappen worden ondersteund, over het ontwerpen of aanpassen van rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../visualize/workbooks-overview.md)te controleren.
