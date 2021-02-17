---
title: Azure Database for PostgreSQL-één server bewaken en afstemmen
description: In dit artikel worden de functies voor het controleren en afstemmen in Azure Database for PostgreSQL-één server beschreven.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 7ccd01f8c351ac2a7326a3b893bd78d14599db50
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588310"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-één server bewaken en afstemmen
Het bewaken van gegevens over uw servers helpt u bij het oplossen en optimaliseren van uw werk belasting. Azure Database for PostgreSQL biedt verschillende bewakings opties om inzicht te krijgen in het gedrag van uw server.

## <a name="metrics"></a>Metrische gegevens
Azure Database for PostgreSQL biedt diverse metrische gegevens die inzicht geven in het gedrag van de bronnen die de PostgreSQL-Server ondersteunen. Elke metriek wordt verzonden met een frequentie van één minuut en heeft een [geschiedenis van Maxi maal 93 dagen](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). U kunt waarschuwingen configureren voor de metrische gegevens. Zie voor stapsgewijze instructies voor [het instellen van waarschuwingen](howto-alert-on-metric.md). Andere taken zijn onder andere het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van de geschiedenis. Zie het overzicht van Azure- [metrische](../azure-monitor/data-platform.md)gegevens voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met metrische gegevens
Deze metrische gegevens zijn beschikbaar voor Azure Database for PostgreSQL:

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Description|
|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Het percentage CPU-gebruik.|
|memory_percent|Geheugen percentage|Percentage|Het percentage geheugen dat in gebruik is.|
|io_consumption_percent|IO-percentage|Percentage|Het percentage i/o dat in gebruik is. (Niet van toepassing op de Basic-laag servers.)|
|storage_percent|Opslag percentage|Percentage|Het percentage van de opslag ruimte dat uit het maximum van de server wordt gebruikt.|
|storage_used|Gebruikte opslag|Bytes|De hoeveelheid opslag ruimte die wordt gebruikt. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten.|
|storage_limit|Opslag limiet|Bytes|De maximale opslag voor deze server.|
|serverlog_storage_percent|Percentage server logboek opslag|Percentage|Het percentage server logboek opslag dat is gebruikt voor de maximale server logboek opslag van de server.|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|De hoeveelheid server logboek opslag die in gebruik is.|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|De maximale server logboek opslag voor deze server.|
|active_connections|Actieve verbindingen|Count|Het aantal actieve verbindingen met de server.|
|connections_failed|Mislukte verbindingen|Count|Het aantal gemaakte verbindingen dat is mislukt.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk uit over actieve verbindingen.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk in meerdere actieve verbindingen.|
|backup_storage_used|Gebruikte back-upopslag|Bytes|De hoeveelheid back-upopslag die wordt gebruikt. Deze waarde vertegenwoordigt de som van de opslag die wordt gebruikt door alle back-ups van de volledige data base, differentiële back-ups en logboek back-ups die worden bewaard op basis van de Bewaar periode voor back-ups die is ingesteld voor de server. De frequentie van de back-ups wordt beheerd door de service en uitgelegd in het [artikel concepten](concepts-backup.md). Voor geo-redundante opslag is het gebruik van back-upopslag twee keer zo dat van de lokaal redundante opslag.|
|pg_replica_log_delay_in_bytes|Maximale vertraging in Replica's|Bytes|De vertraging in bytes tussen de primaire en meest bewaarde replica. Deze metriek is alleen beschikbaar op de primaire server.|
|pg_replica_log_delay_in_seconds|Replica vertraging|Seconden|De tijd sinds de laatste geplayte trans actie. Deze metriek is alleen beschikbaar voor replica servers.|

## <a name="server-logs"></a>Serverlogboeken
U kunt logboek registratie inschakelen op uw server. Deze bron logboeken kunnen worden verzonden naar [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md), Event hubs en een opslag account. Ga naar de pagina [Server logboeken](concepts-server-logs.md) voor meer informatie over logboek registratie.

## <a name="query-store"></a>Query Store
In het [query archief](concepts-query-store.md) worden de query prestaties in de loop van de tijd bijgehouden, inclusief de statistieken voor query runtime en wacht gebeurtenissen. Met de functie wordt de prestatie gegevens van query runtime persistent gemaakt in een systeem database met de naam **azure_sys** onder het query_store-schema. U kunt het verzamelen en opslaan van gegevens beheren via verschillende configuratie knoppen.

## <a name="query-performance-insight"></a>Inzicht in queryprestaties
[Query Performance Insight](concepts-query-performance-insight.md) werkt in combi natie met query Store om visualisaties toegankelijk te maken vanuit de Azure Portal. Met deze grafieken kunt u belang rijke query's identificeren die van invloed zijn op de prestaties. Query Performance Insight is toegankelijk via de sectie **ondersteuning en probleem oplossing** van de portal pagina van uw Azure database for postgresql-server.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties
De functie voor [prestatie aanbevelingen](concepts-performance-recommendations.md) identificeert mogelijkheden voor het verbeteren van de prestaties van de werk belasting. Prestatie aanbevelingen bieden u aanbevelingen voor het maken van nieuwe indexen die de prestaties van uw workloads kunnen verbeteren. Voor het produceren van index aanbevelingen, neemt de functie rekening met diverse database kenmerken, waaronder het schema en de werk belasting zoals gerapporteerd door de query Store. Na de implementatie van de aanbevolen prestaties, moeten klanten de prestaties testen om de impact van deze wijzigingen te evalueren. 

## <a name="planned-maintenance-notification"></a>Melding over gepland onderhoud

Met [geplande onderhouds meldingen](./concepts-planned-maintenance-notification.md) kunt u waarschuwingen ontvangen voor gepland onderhoud op uw Azure database for PostgreSQL-één server. Deze meldingen zijn geïntegreerd met [het geplande onderhoud van service Health](../service-health/overview.md) en bieden u de mogelijkheid om het geplande onderhoud voor uw abonnementen op één plek weer te geven. Het helpt ook om de melding te schalen naar de juiste doel groep voor verschillende resource groepen, omdat u mogelijk verschillende contact personen hebt die verantwoordelijk zijn voor verschillende resources. U ontvangt de melding over het komende onderhoud van 72 uur vóór de gebeurtenis.

Meer informatie over het instellen van meldingen in het document [geplande onderhouds meldingen](./concepts-planned-maintenance-notification.md) .

## <a name="next-steps"></a>Volgende stappen
- Zie [waarschuwingen instellen](howto-alert-on-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
- Zie het [overzicht van Azure-metrische](../azure-monitor/data-platform.md) gegevens voor meer informatie over het openen en exporteren van metrische gegevens met behulp van de Azure Portal, rest API of cli.
- Lees onze blog [voor de aanbevolen procedures voor het bewaken van uw server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
- Meer informatie over [geplande onderhouds meldingen](./concepts-planned-maintenance-notification.md) in azure database for PostgreSQL-één server.