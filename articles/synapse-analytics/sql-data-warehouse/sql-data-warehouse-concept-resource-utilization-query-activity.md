---
title: Beheerbaarheid en bewaking - queryactiviteit, resourcegebruik
description: Ontdek welke mogelijkheden beschikbaar zijn voor het beheren en controleren van Azure Synapse Analytics. Gebruik de Azure Portal dynamische beheerweergaven (DMV's) om inzicht te krijgen in de queryactiviteit en het resourcegebruik van uw datawarehouse.
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568400"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Resourcegebruik en queryactiviteit in de Azure Synapse Analytics

Azure Synapse Analytics biedt een uitgebreide bewakingservaring binnen de Azure Portal om inzicht te krijgen in uw datawarehouse-workload. De Azure Portal is het aanbevolen hulpprogramma bij het bewaken van uw datawarehouse, omdat het configureerbare bewaarperioden, waarschuwingen, aanbevelingen en aanpasbare grafieken en dashboards biedt voor metrische gegevens en logboeken. Met de portal kunt u ook integreren met andere Azure-bewakingsservices, zoals Azure Monitor (logboeken) met Log Analytics, om een holistische bewakingservaring te bieden voor niet alleen uw datawarehouse, maar ook uw hele Azure-analyseplatform voor een geïntegreerde bewakingservaring. In deze documentatie wordt beschreven welke bewakingsmogelijkheden beschikbaar zijn om uw analyseplatform te optimaliseren en te beheren met Synapse SQL.

## <a name="resource-utilization"></a>Resourcegebruik

De volgende metrische gegevens zijn beschikbaar in de Azure Portal voor Synapse SQL. Deze metrische gegevens worden aan de [Azure Monitor.](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)

| Naam meetwaarde             | Description                                                  | Aggregatietype |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-percentage          | CPU-gebruik op alle knooppunten voor het datawarehouse      | Gemiddelde, minimum, maximum    |
| Gegevens-I/O-percentage      | IO-gebruik op alle knooppunten voor het datawarehouse       | Gemiddelde, minimum, maximum    |
| Geheugenpercentage       | Geheugengebruik (SQL Server) op alle knooppunten voor het datawarehouse | Gemiddelde, minimum, maximum   |
| Actieve query's          | Aantal actieve query's dat op het systeem wordt uitgevoerd             | Sum              |
| Query's in de wachtrij          | Aantal query's in de wachtrij dat wacht om te worden uitgevoerd          | Sum              |
| Geslaagde verbindingen  | Aantal geslaagde verbindingen (aanmeldingen) met de database | Som, Aantal       |
| Mislukte verbindingen      | Aantal mislukte verbindingen (aanmeldingen) met de database | Som, Aantal       |
| Geblokkeerd door firewall     | Aantal aanmeldingen bij het datawarehouse dat is geblokkeerd     | Som, aantal       |
| DWU-limiet               | Serviceniveaudoelstelling van het datawarehouse                | Gem. , Min, Max    |
| DWU-percentage          | Maximum tussen CPU-percentage en gegevens-I/O-percentage        | Gem. , Min, Max    |
| DWU gebruikt                | DWU-limiet * DWU-percentage                                   | Gem. , Min, Max    |
| Percentage treffers in cache    | (cachetreffers/cache-missers) * 100 waarbij cachetreffers de som zijn van alle columnstore-segmenten in de lokale SSD-cache en cache-missers de columnstore-segmenten zijn die in de lokale SSD-cache worden opgeteld voor alle knooppunten | Gem. , Min, Max    |
| Percentage gebruikt cachegeheugen   | (cache gebruikt / cachecapaciteit) * 100 waarbij cache gebruikt is de som van alle bytes in de lokale SSD-cache op alle knooppunten en cachecapaciteit is de som van de opslagcapaciteit van de lokale SSD-cache op alle knooppunten | Gemiddelde, minimum, maximum    |
| Lokaal tempdb-percentage | Lokaal tempdb-gebruik voor alle rekenknooppunten: waarden worden elke vijf minuten uitgezonden | Gemiddelde, minimum, maximum    |

Dingen om rekening mee te houden bij het weergeven van metrische gegevens en het instellen van waarschuwingen:

- DWU die wordt gebruikt, vertegenwoordigt alleen een weergave op hoog niveau van het gebruik in de **SQL-pool** en is niet bedoeld als een uitgebreide indicator van het gebruik. Als u wilt bepalen of u omhoog of omlaag wilt schalen, moet u rekening houden met alle factoren die kunnen worden beïnvloed door DWU, zoals gelijktijdigheid, geheugen, tempdb en adaptieve cachecapaciteit. We raden u aan om uw workload uit te [werken met verschillende DWU-instellingen](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) om te bepalen wat het beste werkt om te voldoen aan uw zakelijke doelstellingen.
- Mislukte en geslaagde verbindingen worden gerapporteerd voor een bepaald datawarehouse, niet voor de server zelf.
- Het geheugenpercentage weerspiegelt het gebruik, zelfs als het datawarehouse in een niet-actieve status is. Het geeft niet het geheugenverbruik van de actieve werkbelasting aan. Gebruik en volg deze metrische gegevens samen met anderen (tempdb, Gen2-cache) om een holistische beslissing te nemen over of schalen voor extra cachecapaciteit de prestaties van de werkbelasting verhoogt om aan uw vereisten te voldoen.

## <a name="query-activity"></a>Queryactiviteit

Voor een programmatische ervaring bij het bewaken Synapse SQL via T-SQL biedt de service een set dynamische beheerweergaven (DMV's). Deze weergaven zijn handig bij het actief oplossen van problemen en het identificeren van prestatieknelpunten met uw workload.

Raadpleeg deze documentatie om de lijst weer te Synapse SQL dmv's die van toepassing zijn op [de Synapse SQL.](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs) 

## <a name="metrics-and-diagnostics-logging"></a>Metrische gegevens en diagnoselogboeken 

Zowel metrische gegevens als logboeken kunnen worden geëxporteerd naar Azure Monitor, met name het [onderdeel Azure Monitor](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) logboeken en kunnen programmatisch worden gebruikt via [logboekquery's.](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) De logboeklatentie voor Synapse SQL is ongeveer 10-15 minuten. Ga naar de volgende documentatie voor meer informatie over de factoren die van invloed zijn op latentie.

## <a name="next-steps"></a>Volgende stappen

In de volgende handleiding worden veelvoorkomende scenario's en gebruiksscenario's beschreven bij het bewaken en beheren van uw datawarehouse:

- [Uw datawarehouse-workload bewaken met DMV's](sql-data-warehouse-manage-monitor.md)