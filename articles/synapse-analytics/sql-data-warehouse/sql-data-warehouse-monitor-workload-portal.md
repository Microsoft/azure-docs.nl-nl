---
title: Workload bewaken-Azure Portal
description: Synapse SQL bewaken met behulp van de Azure Portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: f780d5404d99435a61c4e3f9e90b1acbd9539af9
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118435"
---
# <a name="monitor-workload---azure-portal"></a>Workload bewaken-Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om uw workload te bewaken. Dit omvat het instellen van Azure Monitor logboeken voor het onderzoeken van de uitvoering van query's en het analyseren van werk belasting met behulp van log Analytics voor [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- SQL-groep: er worden logboeken verzameld voor een SQL-groep. Als u geen SQL-groep hebt ingericht, raadpleegt u de instructies in [een SQL-groep maken](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

Ga naar de Blade bladeren voor Log Analytics-werk ruimten en maak een werk ruimte

![Log Analytics-werkruimten](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Scherm afbeelding toont de Log Analytics-werk ruimten waar u toevoegen kunt selecteren.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Scherm afbeelding toont de Log Analytics werk ruimte waar u waarden kunt invoeren.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Raadpleeg de volgende [documentatie](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)voor meer informatie over werk ruimten.

## <a name="turn-on-resource-logs"></a>Resource logboeken inschakelen

Diagnostische instellingen configureren voor het verzenden van Logboeken uit uw SQL-groep. Logboeken bestaan uit telemetrie-weer gaven die gelijk zijn aan de meest gebruikte prestatie problemen met de Dmv's. Momenteel worden de volgende weer gaven ondersteund:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Resource logboeken inschakelen](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Logboeken kunnen worden verzonden naar Azure Storage, Stream Analytics of Log Analytics. Voor deze zelf studie selecteert u Log Analytics.

![Logboeken opgeven](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Query's uitvoeren op Log Analytics

Navigeer naar uw Log Analytics-werk ruimte waar u het volgende kunt doen:

- Logboeken analyseren met behulp van logboek query's en query's opslaan voor hergebruik
- Query's opslaan voor hergebruik
- Logboekwaarschuwingen maken
- Query resultaten vastmaken aan een dash board

Raadpleeg de volgende [documentatie](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)voor meer informatie over de mogelijkheden van logboek query's.

![Log Analytics werkruimte editor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics werkruimte query's](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Voorbeeld logboek query's

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Volgende stappen

Nu u de logboeken van Azure monitor hebt ingesteld en geconfigureerd, [past u Azure-Dash boards](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aan om te delen in uw team.