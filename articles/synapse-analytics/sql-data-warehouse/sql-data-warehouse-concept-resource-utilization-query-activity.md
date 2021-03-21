---
title: Beheer baarheid en controle-query activiteit, resource gebruik
description: Meer informatie over de mogelijkheden die beschikbaar zijn voor het beheren en bewaken van Azure Synapse Analytics. Gebruik de Azure Portal en dynamische beheer weergaven (Dmv's) om inzicht te krijgen in de query-activiteiten en het resource gebruik van uw data warehouse.
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7f093860e9962db055a7551f5cdb608ecad9b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585669"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Resource gebruik en query activiteit bewaken in azure Synapse Analytics

Azure Synapse Analytics biedt een uitgebreide bewakings ervaring in het Azure Portal op oppervlakte inzichten met betrekking tot de werk belasting van uw data warehouse. De Azure Portal is het aanbevolen hulp programma voor het bewaken van uw data warehouse, zoals het biedt Configureer bare Bewaar perioden, waarschuwingen, aanbevelingen en aanpas bare grafieken en dash boards voor metrische gegevens en Logboeken. De portal biedt u ook de mogelijkheid om te integreren met andere Azure-bewakings Services, zoals Azure Monitor (Logboeken) met log Analytics om een holistische bewakings ervaring te bieden voor niet alleen uw data warehouse, maar ook uw volledige Azure Analytics-platform voor een geïntegreerde bewakings ervaring. In deze documentatie wordt beschreven welke bewakings mogelijkheden beschikbaar zijn voor het optimaliseren en beheren van uw analyse platform met Synapse SQL.

## <a name="resource-utilization"></a>Resourcegebruik

De volgende metrische gegevens zijn beschikbaar in de Azure Portal voor Synapse SQL. Deze metrische gegevens worden geoppereerd via [Azure monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Naam meetwaarde             | Description                                                  | Aggregatietype |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-percentage          | CPU-gebruik in alle knoop punten voor het Data Warehouse      | Gem, min, Max    |
| Gegevens-I/O-percentage      | IO-gebruik over alle knoop punten voor het Data Warehouse       | Gem, min, Max    |
| Geheugen percentage       | Geheugen gebruik (SQL Server) op alle knoop punten voor het Data Warehouse | Gem, min, Max   |
| Actieve Query's          | Aantal actieve query's dat op het systeem wordt uitgevoerd             | Sum              |
| Query's in de wachtrij          | Aantal query's in de wachtrij dat wacht om te worden uitgevoerd          | Sum              |
| Geslaagde verbindingen  | Aantal geslaagde verbindingen (aanmeldingen) voor de data base | Som, aantal       |
| Mislukte verbindingen      | Aantal mislukte verbindingen (aanmeldingen) voor de data base | Som, aantal       |
| Geblokkeerd door firewall     | Aantal aanmeldingen bij het data warehouse dat is geblokkeerd     | Som, aantal       |
| Limiet voor DWU               | Serviceniveau doelstelling van het Data Warehouse                | Gem, min, Max    |
| Percentage DWU          | Maximum tussen CPU-percentage en gegevens-i/o-percentage        | Gem, min, Max    |
| DWU gebruikt                | Limiet voor DWU * DWU percentage                                   | Gem, min, Max    |
| Percentage cache treffers    | (cache treffers/cache-missers) * 100 waarbij treffers in cache de som zijn van alle treffers in de lokale SSD-cache en de cache-Misser is de column Store-segmenten in de lokale SSD-cache die is opgeteld voor alle knoop punten | Gem, min, Max    |
| Percentage gebruikt cache   | (cache capaciteit gebruikt/cache) * 100 waarbij gebruikte cache de som is van alle bytes in de lokale SSD-cache op alle knoop punten en de cache capaciteit is de som van de opslag capaciteit van de lokale SSD-cache op alle knoop punten | Gem, min, Max    |
| Lokaal TempDB-percentage | Lokaal TempDB-gebruik voor alle reken knooppunten: de waarden worden elke vijf minuten verzonden | Gem, min, Max    |

Aandachtspunten bij het weer geven van metrische gegevens en het instellen van waarschuwingen:

- DWU die wordt gebruikt, duidt alleen op een **hoog niveau van gebruik** in de SQL-groep en is niet bedoeld als een uitgebreide indicatie van het gebruik. Als u wilt bepalen of u omhoog of omlaag wilt schalen, moet u rekening houden met alle factoren die van invloed kunnen zijn op DWU zoals gelijktijdigheid, geheugen, tempdb en adaptieve cache capaciteit. We raden u [aan om uw werk belasting uit te voeren op verschillende DWU-instellingen](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) om te bepalen wat het beste werkt om te voldoen aan uw bedrijfs doelstellingen.
- Mislukte en geslaagde verbindingen worden gerapporteerd voor een bepaald data warehouse, niet voor de server zelf.
- Het geheugen percentage weerspiegelt het gebruik, zelfs als het Data Warehouse zich in niet-actieve status bevindt, het actieve verbruik van het workload-geheugen niet weer spie gelen. Gebruik en volg deze metrische gegevens samen met anderen (TempDB, Gen2 cache) om een holistische beslissing te nemen over als het schalen voor extra cache capaciteit de prestaties van de werk belasting verg root om te voldoen aan uw vereisten.

## <a name="query-activity"></a>Query activiteit

Voor een programmatische ervaring bij het bewaken van Synapse SQL via T-SQL biedt de service een set dynamische beheer weergaven (Dmv's). Deze weer gaven zijn handig bij het oplossen van problemen met de prestaties en het identificeren van prestatie knelpunten met uw werk belasting.

Raadpleeg deze [documentatie](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs)om de lijst met dmv's die van toepassing zijn op Synapse SQL te bekijken. 

## <a name="metrics-and-diagnostics-logging"></a>Metrische gegevens en diagnoselogboeken 

Zowel metrische gegevens als logboeken kunnen worden geëxporteerd naar Azure Monitor, met name de [Azure monitor-logboeken](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) component en via een programma kunnen worden geopend via [logboek query's](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). De logboek latentie voor Synapse SQL is ongeveer 10-15 minuten. Raadpleeg de volgende documentatie voor meer informatie over de factoren die van invloed zijn op de latentie.

## <a name="next-steps"></a>Volgende stappen

In de volgende hand leiding worden algemene scenario's en gebruiks voorbeelden beschreven voor het bewaken en beheren van uw data warehouse:

- [Uw data warehouse-workload bewaken met Dmv's](sql-data-warehouse-manage-monitor.md)