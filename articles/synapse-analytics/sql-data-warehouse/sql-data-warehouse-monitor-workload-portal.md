---
title: Workload bewaken - Azure Portal
description: Uw Synapse SQL bewaken met behulp van de Azure Portal
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568264"
---
# <a name="monitor-workload---azure-portal"></a>Workload bewaken - Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal om uw workload te bewaken. Dit omvat het instellen van Azure Monitor logboeken om query-uitvoering en workloadtrends te onderzoeken met behulp van log analytics [voor Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- SQL-pool: we verzamelen logboeken voor een SQL-pool. Als u geen SQL-pool hebt ingericht, bekijkt u de instructies in [Een SQL-pool maken.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

Navigeer naar de bladerblade voor Log Analytics-werkruimten en maak een werkruimte

![Log Analytics-werkruimten](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Schermopname van de Log Analytics-werkruimten waar u Toevoegen kunt selecteren.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Schermopname van de Log Analytics-werkruimte waar u waarden kunt invoeren.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Ga naar de volgende documentatie voor meer informatie over [werkruimten.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Resourcelogboeken in-

Configureer diagnostische instellingen om logboeken uit uw SQL-pool te zenden. Logboeken bestaan uit telemetrieweergaven die gelijk zijn aan de meest gebruikte dmv's voor het oplossen van prestatieproblemen. Momenteel worden de volgende weergaven ondersteund:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Resourcelogboeken inschakelen](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Logboeken kunnen worden Azure Storage, Stream Analytics of Log Analytics. Voor deze zelfstudie selecteert u Log Analytics.

![Logboeken opgeven](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Query's uitvoeren op Log Analytics

Navigeer naar uw Log Analytics-werkruimte waar u het volgende kunt doen:

- Logboeken analyseren met behulp van logboekquery's en query's opslaan voor hergebruik
- Query's opslaan voor hergebruik
- Logboekwaarschuwingen maken
- Queryresultaten vastmaken aan een dashboard

Ga naar de volgende documentatie voor meer informatie over de mogelijkheden van [logboekquery's.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Log Analytics-werkruimte-editor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Query's voor Log Analytics-werkruimten](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Voorbeeld van logboekquery's

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

Nu u Azure Monitor-logboeken hebt ingesteld en geconfigureerd, kunt u [Azure-dashboards aanpassen om](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ze in uw team te delen.