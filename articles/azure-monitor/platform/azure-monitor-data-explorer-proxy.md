---
title: Meerdere bronnen query's uitvoeren op Azure Data Explorer met Azure Monitor
description: Gebruik Azure Monitor om query's voor meerdere producten uit te voeren tussen Azure Data Explorer, Log Analytics werk ruimten en klassieke Application Insights toepassingen in Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572147"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Meerdere bronnen query's uitvoeren op Azure Data Explorer met Azure Monitor
Azure Monitor ondersteunt query's van meerdere services tussen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)en [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). U kunt vervolgens een query uitvoeren op uw Azure Data Explorer-cluster met behulp van Log Analytics/Application Insights-hulpprogram ma's en hiernaar verwijzen in een query voor query's op meerdere services. In dit artikel wordt beschreven hoe u een query voor meerdere services maakt.

De Azure Monitor cross-service stroom: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure monitor en azure Data Explorer cross service flow.":::

>[!NOTE]
>* Azure Monitor query's voor meerdere services worden uitgevoerd in de private preview-AllowListing is vereist.
>* Neem contact op met het [service team](mailto:ADXProxy@microsoft.com) .
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Query's uitvoeren op uw Log Analytics of Application Insights resources en Azure Data Explorer

U kunt de query's voor meerdere resources uitvoeren met client hulpprogramma's die ondersteuning bieden voor Kusto-query's, zoals: Log Analytics Web-UI, werkmappen, Power shell, REST API en meer.

* Voer de id in voor een Azure Data Explorer-cluster in een query binnen het ' ADX-patroon, gevolgd door de naam en tabel van de data base.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Voor beeld van een query op meerdere services.":::

> [!NOTE]
>* Database namen zijn hoofdletter gevoelig.
>* Een query voor meerdere resources als een waarschuwing wordt niet ondersteund.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Het combi neren van Azure Data Explorer cluster tabellen (met behulp van samen voegen en lid worden) met de werk ruimte LA

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Voor beeld van een query op meerdere services met Union.":::

>[!Tip]
>* Steno-indeling is toegestaan-clustername/InitialCatalog. Bijvoorbeeld ADX (' Help/voor beelden ') wordt omgezet naar ADX (' Help. kusto. Windows. net/samples ')
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Gegevens uit een Azure Data Explorer-cluster samen voegen in één Tenant met een Azure Monitor bron in een andere.

Query's tussen tenants tussen de services worden niet ondersteund. U bent aangemeld bij één Tenant voor het uitvoeren van de query die beide resources beslaat.

Als de Azure Data Explorer-bron zich in Tenant A bevindt en Log Analytics werk ruimte zich in de Tenant B bevindt, gebruikt u een van de volgende twee methoden:

*  Met Azure Data Explorer kunt u rollen toevoegen voor principals in verschillende tenants. Voeg uw gebruikers-ID in de Tenant ' B ' toe als geautoriseerde gebruiker op het Azure Data Explorer-cluster. Valideer de eigenschap *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* in het Azure Data Explorer-cluster bevat Tenant ' B '. Voer de Kruis query volledig uit in de Tenant B.
*  Gebruik [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) om de Azure monitor resource te projecteren in Tenant A.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Verbinding maken met Azure Data Explorer clusters van verschillende tenants

Kusto Explorer meldt u automatisch aan bij de Tenant waarvan het gebruikers account oorspronkelijk deel uitmaakt. Om toegang te krijgen tot bronnen in andere tenants met hetzelfde gebruikers account, moet `tenantId` expliciet worden opgegeven in de Connection String: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Volgende stappen
* [Query's schrijven](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Query's tussen bronnen en logboeken uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
