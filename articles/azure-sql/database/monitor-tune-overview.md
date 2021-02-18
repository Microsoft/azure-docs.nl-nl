---
title: Prestaties bewaken en afstemmen
description: Een overzicht van de mogelijkheden en methodologie voor het afstemmen van bewaking en prestaties in Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 6b56da68b10bc40304097fbe9eeaf200d422b663
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592685"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Bewaking en prestatieafstemming van Azure SQL Database en Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Als u de prestaties van een data base in Azure SQL Database en Azure SQL Managed instance wilt bewaken, moet u eerst de CPU-en i/o-resources bewaken die door uw werk belasting worden gebruikt ten opzichte van het prestatie niveau van de data base dat u hebt gekozen bij het selecteren van een bepaalde service tier en U kunt dit doen door Azure SQL Database en Azure SQL Managed instance metrische gegevens te verzenden die kunnen worden weer gegeven in de Azure Portal of met een van deze SQL Server-beheer Programma's: [Azure Data Studio](/sql/azure-data-studio/what-is) of [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database biedt een aantal database adviseurs voor het leveren van aanbevelingen voor intelligente prestaties en opties voor automatisch afstemmen om de prestaties te verbeteren. Daarnaast bevat Query Performance Insight informatie over de query's die verantwoordelijk zijn voor het meeste CPU-en IO-gebruik voor één en gepoolde data bases.

Azure SQL Database en Azure SQL Managed instance bieden geavanceerde mogelijkheden voor bewaking en afstemming, die worden ondersteund door kunst matige intelligentie om u te helpen bij het oplossen van problemen en het maximaliseren van de prestaties van uw data bases en oplossingen. U kunt ervoor kiezen om de [streaming-export](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) van deze [intelligent Insights](intelligent-insights-overview.md) en andere database bron logboeken en-metrische gegevens te configureren naar een van de verschillende bestemmingen voor verbruik en analyse, met name met behulp van [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Azure SQL-analyse is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van al uw data bases op schaal en op meerdere abonnementen in één weer gave. Voor een lijst met de logboeken en metrische gegevens die u kunt exporteren, raadpleegt u [Diagnostische telemetrie voor exporteren](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server heeft zijn eigen bewakings-en diagnostische mogelijkheden die gebruikmaken van SQL Database en het gebruik van SQL Managed instance, zoals [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) en [dynamische beheer weergaven (dmv's)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Zie [bewaking met behulp van dmv's](monitoring-with-dmvs.md) voor scripts om te controleren op diverse prestatie problemen.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Mogelijkheden voor bewaking en afstemming in de Azure Portal

In de Azure Portal, Azure SQL Database en Azure SQL Managed instance bewaken de metrische gegevens van de resource. Azure SQL Database biedt database advies en Query Performance Insight biedt aanbevelingen voor het afstemmen van query's en analyse van query prestaties. In de Azure Portal kunt u automatisch afstemmen inschakelen voor [logische SQL-servers](logical-servers.md) en de afzonderlijke data bases en groepen.

> [!NOTE]
> Data bases met een extreem laag gebruik kunnen worden weer gegeven in de portal met minder dan het werkelijke gebruik. Als gevolg van de manier waarop telemetrie wordt verzonden bij het converteren van een dubbele waarde naar het dichtstbijzijnde gehele getal, moeten bepaalde gebruiks bedragen kleiner dan 0,5 worden afgerond op 0, waardoor een verlies in granulatie van de verzonden telemetrie wordt veroorzaakt. Zie voor meer informatie de metrische gegevens voor [lage data base en elastische groepen afronden op nul](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Bewakings bron voor Azure SQL Database en Azure SQL Managed instance

U kunt snel een groot aantal metrische resource gegevens in de Azure Portal bewaken in de weer gave **metrische gegevens** . Met deze metrische gegevens kunt u zien of een Data Base 100% van de processor, het geheugen of de i/o-resources bereikt. Het hoge DTU-of processor percentage, evenals het hoge IO-percentage geeft aan dat uw workload mogelijk meer CPU-of IO-resources nodig heeft. Dit kan ook duiden op query's die moeten worden geoptimaliseerd.

  ![Metrische gegevens van resources](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Data base Advisor in Azure SQL Database

Azure SQL Database omvatten [Data Base Advisor](database-advisor-implement-performance-recommendations.md) die aanbevelingen bieden voor het afstemmen van de prestaties voor één data base of gepoold. Deze aanbevelingen zijn beschikbaar in het Azure Portal en met behulp van [Power shell](/powershell/module/az.sql/get-azsqldatabaseadvisor). U kunt ook [automatisch afstemmen](automatic-tuning-overview.md) inschakelen, zodat Azure SQL database deze afstemmings aanbevelingen automatisch kunt implementeren.

### <a name="query-performance-insight-in-azure-sql-database"></a>Query Performance Insight in Azure SQL Database

[Query Performance Insight](query-performance-insight-use.md) toont de prestaties in het Azure portal van de meest gebruikte en langste query's die worden uitgevoerd voor één en gepoolde data bases.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>De metrische gegevens van de data base en de elastische pool worden afgerond op nul

Vanaf september 2020 kunnen data bases met een extreem laag gebruik worden weer gegeven in de portal met minder dan het werkelijke gebruik. Als gevolg van de manier waarop telemetrie wordt verzonden bij het converteren van een dubbele waarde naar het dichtstbijzijnde gehele getal, moeten bepaalde gebruiks bedragen kleiner dan 0,5 worden afgerond op 0, waardoor een verlies in granulatie van de verzonden telemetrie wordt veroorzaakt.

Bijvoorbeeld: overweeg een venster van één minuut met de volgende vier gegevens punten: 0,1, 0,1, 0,1, 0,1, deze lage waarden worden afgerond op 0, 0, 0, 0 en geven een gemiddelde van 0. Als een van de gegevens punten groter is dan 0,5, bijvoorbeeld: 0,1, 0,1, 0,9, 0,1, worden ze afgerond op 0, 0, 1, 0 en wordt een gemiddelde van 0,25 weer gegeven.

Betroffen data base-metrische gegevens:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Beïnvloede metrische gegevens van elastische groep:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Intelligente beoordelingen van prestatie problemen genereren

[Intelligent Insights](intelligent-insights-overview.md) voor Azure SQL database en Azure SQL Managed instance gebruiken ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detectie van storende gebeurtenissen die de prestaties nadelig beïnvloeden. Intelligent Insights detecteert automatisch prestatie problemen met data bases op basis van wacht tijden, fouten of time-outs van query's. Eenmaal gedetecteerd, wordt er een gedetailleerde analyse uitgevoerd waarmee een resource logboek (SQLInsights) wordt gegenereerd met een [intelligente evaluatie van de problemen](intelligent-insights-troubleshoot-performance.md). Deze evaluatie bestaat uit een analyse van de hoofd oorzaak van het prestatie probleem van de data base en, waar mogelijk, aanbevelingen voor betere prestaties.

Intelligent Insights is een unieke mogelijkheid van ingebouwde intelligentie van Azure die de volgende waarde biedt:

- Proactieve controle
- Aangepaste prestatie inzichten
- Vroegtijdige detectie van de prestaties van de data base verlagen
- Hoofd oorzaak analyse van gedetecteerde problemen
- Aanbevelingen voor prestatie verbetering
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Streaming-export van metrische gegevens en bron logboeken inschakelen

U kunt de [streaming-export van diagnostische telemetrie](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) inschakelen en configureren op een van de volgende bestemmingen, met inbegrip van het intelligent Insights-bron logboek. Gebruik [SQL Analytics](../../azure-monitor/insights/azure-sql.md) en andere mogelijkheden om deze extra diagnostische telemetrie te gebruiken om prestatie problemen te identificeren en op te lossen.

U kunt Diagnostische instellingen configureren voor het streamen van categorieën metrische gegevens en bron logboeken voor afzonderlijke data bases, gepoolde data bases, elastische groepen, beheerde exemplaren en data bases van instanties naar een van de volgende Azure-resources.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics werk ruimte in Azure Monitor

U kunt metrische gegevens en bron logboeken streamen naar een [log Analytics werk ruimte in azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Gegevens die hier worden gestreamd, kunnen worden gebruikt door [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Dit is een alleen-Cloud bewakings oplossing die intelligente bewaking van uw data bases bevat, waaronder prestatie rapporten, waarschuwingen en aanbevelingen voor risico beperking. Gegevens die naar een Log Analytics-werk ruimte worden gestreamd, kunnen worden geanalyseerd met andere bewakings gegevens die worden verzameld en biedt u ook de mogelijkheid om andere Azure Monitor functies, zoals waarschuwingen en visualisaties, te gebruiken.

### <a name="azure-event-hubs"></a>Azure Event Hubs

U kunt metrische gegevens en bron logboeken streamen naar [Azure Event hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs). Diagnostische telemetrie streamen naar Event hubs om de volgende functionaliteit te bieden:

- **Logboeken streamen naar logboekregistratie van derden en telemetrie-systemen**

  U kunt al uw metrische gegevens en bron logboeken streamen naar één Event Hub voor het pipeen van een logboek met een SIEM of een logboek analyse programma van derden.
- **Een aangepast platform voor telemetrie en logboekregistratie bouwen**

  Met de uiterst schaal bare functie voor publiceren en abonneren van Event hubs kunt u flexibele opname gegevens en resource logboeken maken in een aangepast telemetrie-platform. Zie [het ontwerp en de grootte van een telemetrie-platform op wereld wijd schalen op Azure Event hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.
- **Bekijk de servicestatus door gegevens te streamen naar Power BI**

  Gebruik Event Hubs, Stream Analytics en Power BI om uw diagnostische gegevens te transformeren naar bijna realtime inzichten op uw Azure-Services. Zie [Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) voor meer informatie over deze oplossing.

### <a name="azure-storage"></a>Azure Storage

Metrische gegevens van Stream en bron logboeken naar [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Gebruik Azure Storage om grote hoeveel heden diagnostische telemetrie te archiveren voor een fractie van de kosten van de vorige twee opties voor gegevens stromen.

## <a name="use-extended-events"></a>Uitgebreide gebeurtenissen gebruiken 

Daarnaast kunt u [uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/extended-events) in SQL Server gebruiken voor geavanceerde bewaking en probleem oplossing. Met de architectuur Extended Events kunnen gebruikers zoveel of zo weinig gegevens verzamelen als nodig is om problemen op te lossen of te identificeren. Zie [Extended Events in Azure SQL database](xevent-db-diff-from-svr.md)voor meer informatie over het gebruik van uitgebreide gebeurtenissen in Azure SQL database.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over intelligente prestatie aanbevelingen voor één en gepoolde data bases aanbevelingen voor de [prestaties van data base Advisor](database-advisor-implement-performance-recommendations.md).
- Zie [Azure SQL intelligent Insights](intelligent-insights-overview.md)voor meer informatie over het automatisch bewaken van database prestaties met automatische diagnose en de oorzaak van prestatie problemen.