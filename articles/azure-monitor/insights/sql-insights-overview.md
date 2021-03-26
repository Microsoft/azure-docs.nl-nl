---
title: Uw SQL-implementaties bewaken met SQL Insights (preview-versie)
description: Overzicht van SQL Insights in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567828"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Uw SQL-implementaties bewaken met SQL Insights (preview-versie)
SQL Insights is een uitgebreide oplossing voor het bewaken van alle producten in de [Azure SQL-familie](../../azure-sql/index.yml). SQL Insights maakt gebruik van [dynamische beheer weergaven](../../azure-sql/database/monitoring-with-dmvs.md) om de gegevens weer te geven die u nodig hebt om de status te controleren, problemen op te sporen en prestaties af te stemmen.  

SQL Insights voert alle bewaking op afstand uit. Bewakings agenten op toegewezen virtuele machines maken verbinding met uw SQL-resources en verzamelen gegevens op afstand. De verzamelde gegevens worden opgeslagen in [Azure monitor logboeken](../logs/data-platform-logs.md), waardoor u eenvoudig aggregatie, filteren en trend analyse kunt instellen. U kunt de verzamelde gegevens weer geven uit de [sjabloon](../visualize/workbooks-overview.md)voor de SQL Insights-werkmap of u kunt de gegevens rechtstreeks in het logboek bekijken met behulp van [log-query's](../logs/get-started-queries.md).

## <a name="pricing"></a>Prijzen
Er zijn geen directe kosten verbonden aan SQL Insights. Alle kosten worden gemaakt door de virtuele machines die de gegevens verzamelen, de Log Analytics-werk ruimten waarin de gegevens worden opgeslagen en eventuele waarschuwings regels die zijn geconfigureerd voor de gegevens. 

**Virtuele machines**

Voor virtuele machines worden kosten in rekening gebracht op basis van de prijzen die zijn gepubliceerd op de [pagina met prijzen voor virtuele machines](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). Het aantal vereiste virtuele machines varieert, afhankelijk van het aantal verbindings reeksen dat u wilt bewaken. U wordt aangeraden om 1 virtuele machine van grootte Standard_B2s toe te wijzen voor elke 100-verbindings reeks. Zie [vereisten voor virtuele Azure-machines](sql-insights-enable.md#azure-virtual-machine-requirements) voor meer informatie.

**Log Analytics-werkruimten**

Voor de Log Analytics-werk ruimten worden kosten in rekening gebracht op basis van de prijzen die zijn gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). Met de Log Analytics-werk ruimten die door SQL Insights worden gebruikt, worden kosten in rekening gebracht voor gegevens opname, gegevens retentie en (optioneel) gegevens export. De exacte kosten variëren op basis van de hoeveelheid gegevens die wordt opgenomen, bewaard en geëxporteerd. De hoeveelheid van deze gegevens kan vervolgens variëren op basis van uw database activiteiten en de verzamelings instellingen die zijn gedefinieerd in uw [bewakings profielen](sql-insights-enable.md#create-sql-monitoring-profile).

**Waarschuwingsregels**

Voor waarschuwings regels in Azure Monitor worden kosten in rekening gebracht op basis van de prijzen die zijn gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). Als u ervoor kiest om [waarschuwingen te maken met SQL Insights](sql-insights-alerts.md), worden er waarschuwings regels en alle meldingen die worden verzonden, in rekening gebracht.

## <a name="supported-versions"></a>Ondersteunde versies 
SQL Insights ondersteunt de volgende versies van SQL Server:
- SQL Server 2012 en hoger

SQL Insights ondersteunt SQL Server die in de volgende omgevingen worden uitgevoerd:
- Azure SQL Database
- Azure SQL Managed Instance
- SQL Server op Azure Virtual Machines (SQL Server uitgevoerd op virtuele machines die zijn geregistreerd bij de [SQL virtual machine](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) provider)
- Azure-Vm's (SQL Server die worden uitgevoerd op virtuele machines die niet zijn geregistreerd bij de SQL-provider voor [virtuele](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) machines)

SQL Insights biedt geen ondersteuning of beperkte ondersteuning voor het volgende:
- **Niet-Azure-instanties**: SQL Server die worden uitgevoerd op virtuele machines buiten Azure, worden niet ondersteund
- **Azure SQL database elastische Pools**: meet gegevens kunnen niet worden verzameld voor elastische Pools. Er kunnen geen metrische gegevens worden verzameld voor data bases in elastische Pools.
- **Azure SQL database lage service lagen**: metrieken kunnen niet worden verzameld voor data bases in de [service lagen](../../azure-sql/database/resource-limits-dtu-single-databases.md) Basic, S0, S1 en S2
- **Azure SQL database laag zonder server**: meet waarden kunnen worden verzameld voor data bases met behulp van de serverloze Compute-laag. Met het proces van het verzamelen van metrische gegevens wordt echter de timer voor het automatisch onderbreken van de vertraging opnieuw ingesteld, waardoor de data base geen automatisch onderbroken status kan invoeren
- **Secundaire replica's**: meet gegevens kunnen alleen worden verzameld voor één secundaire replica per data base. Als een Data Base meer dan één secundaire replica heeft, kan alleen 1 worden bewaakt.
- **Verificatie met Azure Active Directory**: de enige [verificatie](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) methode die wordt ondersteund voor bewaking is SQL-verificatie. Voor SQL Server op een virtuele Azure-machine wordt geen ondersteuning geboden voor verificatie met behulp van Active Directory op een aangepaste domein controller.  

## <a name="open-sql-insights"></a>SQL Insights openen
Open SQL Insights door **SQL (preview)** te selecteren in het gedeelte **inzichten** van het menu **Azure monitor** van de Azure Portal. Klik op een tegel om de ervaring te laden voor het type SQL dat u wilt bewaken.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights in Azure Portal.":::

## <a name="enable-sql-insights"></a>SQL Insights inschakelen 
Zie [SQL Insights inschakelen](sql-insights-enable.md) voor instructies over het inschakelen van SQL Insights.

## <a name="troubleshoot-sql-insights"></a>Problemen met SQL Insights oplossen 
Zie [probleem oplossing voor SQL Insights](sql-insights-troubleshoot.md) voor instructies voor het oplossen van problemen met SQL Insights.

## <a name="data-collected-by-sql-insights"></a>Gegevens die worden verzameld door SQL Insights
SQL Insights voert alle bewaking op afstand uit. Er worden geen agents geïnstalleerd op de virtuele machines met SQL Server. 

SQL Insights maakt gebruik van toegewezen virtuele machines voor het extern verzamelen van gegevens uit uw SQL-resources. Op elke virtuele bewakings machine is de [Azure monitor agent](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) en de uitbrei ding workload INSIGHTS (WLI) geïnstalleerd. De WLI-extensie bevat de open source- [telegrafa-agent](https://www.influxdata.com/time-series-platform/telegraf/). SQL Insights maakt gebruik van [regels voor gegevens verzameling](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) om de instellingen voor het verzamelen van gegevens voor de [SQL Server-invoeg toepassing](https://www.influxdata.com/integration/microsoft-sql-server/)voor telegrafie op te geven.

Er zijn verschillende sets gegevens beschikbaar voor Azure SQL Database, Azure SQL Managed instance en SQL Server. De volgende tabellen beschrijven de beschik bare gegevens. U kunt bepalen welke gegevens sets moeten worden verzameld en hoe vaak de verzameling moet worden gemaakt wanneer u [een bewakings profiel maakt](sql-insights-enable.md#create-sql-monitoring-profile).

De onderstaande tabellen bevatten de volgende kolommen:
- **Beschrijvende naam**: naam van de query zoals weer gegeven op de Azure portal bij het maken van een bewakings profiel
- **Configuratie naam**: naam van de query zoals weer gegeven op de Azure portal bij het bewerken van een bewakings profiel
- Naam **ruimte**: naam van de query zoals deze in een log Analytics-werk ruimte is gevonden. Deze id wordt weer gegeven in de tabel **InsighstMetrics** van de `Namespace` eigenschap in de `Tags` kolom
- **Dmv's**: de dynamische beheerde weer gaven die worden gebruikt voor het produceren van de gegevensset
- **Standaard ingeschakeld**: of de gegevens standaard worden verzameld
- **Standaard frequentie van verzamelingen**: hoe vaak de gegevens standaard worden verzameld

### <a name="data-for-azure-sql-database"></a>Gegevens voor Azure SQL Database 
| Beschrijvende naam | Configuratie naam | Naamruimte | DMV's | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|:---|
| DB-wacht statistieken | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | NA |
| DBO-wacht statistieken | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 seconden |
| Geheugen-clerks | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 seconden |
| Database-IO | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Yes | 60 seconden |
| Servereigenschappen | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>laden. vinden<br>laden. [database_service_objectives] | Yes | 60 seconden |
| Prestatiemeteritems | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 seconden |
| Resource statistieken | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 seconden |
| Resourcebeheer | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 seconden |
| Aanvragen | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Planners| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Gegevens voor Azure SQL Managed instance 

| Beschrijvende naam | Configuratie naam | Naamruimte | DMV's | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|:---|
| Wachtstatus | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 seconden |
| Geheugen-clerks | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 seconden |
| Database-IO | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 seconden |
| Servereigenschappen | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 seconden |
| Prestatiemeteritems | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 seconden |
| Resource statistieken | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 seconden |
| Resourcebeheer | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 seconden |
| Aanvragen | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Planners | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |

### <a name="data-for-sql-server"></a>Gegevens voor SQL Server

| Beschrijvende naam | Configuratie naam | Naamruimte | DMV's | Standaard ingeschakeld | Standaard frequentie van verzamelingen |
|:---|:---|:---|:---|:---|:---|
| Wachtstatus | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 seconden | 
| Geheugen-clerks | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 seconden |
| Database-IO | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 seconden |
| Servereigenschappen | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 seconden |
| Prestatiemeteritems | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 seconden |
| Volume ruimte | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 seconden |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 seconden |
| Planners | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |
| Aanvragen | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Status van beschikbaarheids replica | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 seconden |
| Replica's van beschikbaarheids database | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 seconden |

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Insights inschakelen](sql-insights-enable.md) voor instructies over het inschakelen van SQL Insights
- Zie [Veelgestelde vragen](../faq.md#sql-insights-preview) over SQL Insights voor veelgestelde vragen
