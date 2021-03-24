---
title: Uw SQL-implementaties bewaken met SQL Insights (preview-versie)
description: Overzicht van SQL Insights in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b5add466a60bc855e08917d02fecaf60a35deeb1
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889566"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Uw SQL-implementaties bewaken met SQL Insights (preview-versie)
SQL Insights bewaakt de prestaties en de status van uw SQL-implementaties.  Dit kan helpen om voorspel bare prestaties en beschik baarheid te leveren van essentiële workloads die u rond een SQL-back-end hebt gemaakt door prestatie knelpunten en problemen te identificeren. De gegevens van SQL Insights worden opgeslagen in [Azure monitor-logboeken](../logs/data-platform-logs.md), zodat IT krachtige aggregatie en filteren kan leveren en gegevens trends in de loop van de tijd kan analyseren. U kunt deze gegevens weer geven van Azure Monitor in de weer gaven die worden geleverd als onderdeel van deze aanbieding. u kunt ook dieper in de logboek gegevens zoeken om query's uit te voeren en trends te analyseren.

SQL Insights installeert niets van uw SQL IaaS-implementaties. In plaats daarvan worden toegewezen virtuele machines gebruikt voor het extern verzamelen van gegevens voor SQL PaaS-en SQL IaaS-implementaties.  Met het SQL Insights-bewakings profiel kunt u de gegevens sets beheren die moeten worden verzameld op basis van het type SQL, waaronder Azure SQL DB, Azure SQL Managed instance en SQL Server die op een virtuele machine van Azure worden uitgevoerd.

## <a name="pricing"></a>Prijzen

Er zijn geen directe kosten voor SQL Insights, maar u betaalt voor de activiteiten in de werk ruimte Log Analytics. Op basis van de prijzen die worden gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/), wordt SQL Insights gefactureerd voor:

- Gegevens die zijn opgenomen van agents en die zijn opgeslagen in de werk ruimte.
- Waarschuwings regels op basis van logboek gegevens.
- Meldingen die worden verzonden vanuit waarschuwings regels.

De logboek grootte is afhankelijk van de teken reeks lengten van de verzamelde gegevens en kan toenemen met de hoeveelheid database activiteit. 

## <a name="supported-versions"></a>Ondersteunde versies 
SQL Insights ondersteunt de volgende versies van SQL Server:

- SQL Server 2012 en hoger

SQL Insights ondersteunt SQL Server die in de volgende omgevingen worden uitgevoerd:

- Azure SQL Database
- Azure SQL Managed Instance
- Azure SQL-Vm's
- Azure-VM's

SQL Insights biedt geen ondersteuning of beperkte ondersteuning voor het volgende:

- SQL Server die worden uitgevoerd op virtuele machines buiten Azure, worden momenteel niet ondersteund.
- Azure SQL Database elastische Pools: beperkte ondersteuning tijdens de open bare preview. Wordt volledig ondersteund voor algemene Beschik baarheid.  
- Azure SQL Serverloze implementaties: zoals actief geo-DR, kunnen de huidige bewakings agenten verhinderen dat serverloze implementatie naar de slaap stand gaat. Dit kan hoger zijn dan de verwachte kosten van serverloze implementaties.  
- Lees bare secundairen: momenteel worden alleen implementatie typen met een enkel Lees bare secundair eind punt (Bedrijfskritiek of grootschalige) ondersteund. Wanneer grootschalige-implementaties ondersteuning bieden voor benoemde replica's, kunnen we meerdere Lees bare secundaire eind punten ondersteunen voor één logische data base.  
- Azure Active Directors: momenteel ondersteunen we alleen SQL-aanmeldingen voor de bewakings agent. We zijn van plan Azure Active Directors in een aanstaande release te ondersteunen en hebben geen huidige ondersteuning voor SQL-VM-verificatie met behulp van Active Directory op een Bespoke-domein controller.  


## <a name="open-sql-insights"></a>SQL Insights openen
Open SQL Insights door **SQL (preview)** te selecteren in het gedeelte **inzichten** van het menu **Azure monitor** van de Azure Portal. Klik op een tegel om de ervaring te laden voor het type SQL dat u wilt bewaken.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights in Azure Portal.":::


## <a name="enable-sql-insights"></a>SQL Insights inschakelen 
Zie [SQL Insights inschakelen](sql-insights-enable.md) voor gedetailleerde procedure om SQL Insights naast stappen voor het oplossen van problemen in te scha kelen.


## <a name="data-collected-by-sql-insights"></a>Gegevens die worden verzameld door SQL Insights

SQL Insights ondersteunt alleen de externe methode voor het bewaken van SQL. Er worden geen agents geïnstalleerd op de virtuele machines met SQL Server. Een of meer specifieke Vm's voor het controleren van virtuele machines zijn vereist voor het op afstand verzamelen van gegevens uit uw SQL-resources. 

Voor elk van deze bewakings-Vm's is de [Azure monitor-agent](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) geïnstalleerd samen met de uitbrei ding workload INSIGHTS (WLI). 

De WLI-extensie bevat de open source- [telegrafa-agent](https://www.influxdata.com/time-series-platform/telegraf/).  We gebruiken [regels](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) voor het verzamelen van gegevens om de [sqlserver-invoer-invoeg toepassing](https://www.influxdata.com/integration/microsoft-sql-server/) te configureren om de gegevens op te geven die moeten worden verzameld uit Azure SQL DB, Azure SQL managed instance en SQL Server uitgevoerd op een virtuele Azure-machine. 

In de volgende tabellen ziet u een overzicht van het volgende:

- De naam van de query in de sqlserver-Telegraf-invoeg toepassing
- Dynamische beheerde weer gaven de query aanroepen
- Naam ruimte de gegevens worden weer gegeven onder in de tabel *InsighstMetrics*
- Hiermee wordt aangegeven of de gegevens standaard worden verzameld
- Hoe vaak de gegevens standaard worden verzameld
 
U kunt wijzigen welke query's worden uitgevoerd en hoe vaak gegevens worden verzameld wanneer u uw bewakings profiel maakt. 

### <a name="azure-sql-db-data"></a>Azure SQL DB-gegevens 

| Query naam | DMV | Naamruimte | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | Nee | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Ja | 60 seconden |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Ja | 60 seconden |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Ja | 60 seconden |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>laden. vinden<br>laden. [database_service_objectives] | sqlserver_server_properties | Ja | 60 seconden |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 seconden |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 seconden |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Ja | 60 seconden |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nee | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nee | NA  |

### <a name="azure-sql-managed-instance-data"></a>Azure SQL Managed instance-gegevens 

| Query naam | DMV | Naamruimte | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Ja | 60 seconden |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Ja | 60 seconden |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Ja | 60 seconden |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Ja | 60 seconden |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 seconden |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 seconden |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Ja | 60 seconden |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nee | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nee | NA |

### <a name="sql-server-data"></a>SQL Server gegevens

| Query naam | DMV | Naamruimte | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Ja | 60 seconden |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 seconden | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Ja | 60 seconden |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Ja | 60 seconden |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 seconden |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nee | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nee | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Ja | 60 seconden |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Ja | 60 seconden |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 seconden |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 seconden |




## <a name="next-steps"></a>Volgende stappen

Zie [SQL Insights inschakelen](sql-insights-enable.md) voor gedetailleerde procedure om SQL Insights in te scha kelen.
Zie [Veelgestelde vragen](../faq.md#sql-insights-preview) over SQL Insights voor veelgestelde vragen.
