---
title: "Meerdere resources: query's uitvoeren op Azure Data Explorer met behulp van Azure Monitor"
description: Gebruik Azure Monitor om query's in meerdere producten uit te voeren tussen Azure Data Explorer, Log Analytics werk ruimten en klassieke Application Insights toepassingen in Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 1857f0e39cd5d9ddc616eed1db18cd58b98721a4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031120"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Meerdere resources: query's uitvoeren op Azure Data Explorer met behulp van Azure Monitor
Azure Monitor ondersteunt query's tussen de verschillende services van Azure Data Explorer, [Application Insights](../app/app-insights-overview.md)en [log Analytics](../logs/data-platform-logs.md). U kunt vervolgens een query uitvoeren op uw Azure Data Explorer-cluster met behulp van Log Analytics/Application Insights-hulpprogram ma's en hiernaar verwijzen in een query's voor meerdere services. In dit artikel wordt beschreven hoe u een query voor meerdere services maakt.

In het volgende diagram ziet u de Azure Monitor cross-service flow:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagram waarin de stroom van query's tussen een gebruiker, Azure Monitor, een proxy en Azure Data Explorer wordt weer gegeven.":::

>[!NOTE]
> Azure Monitor query's voor meerdere services bevindt zich in de open bare preview. Neem contact op met het [service team](mailto:ADXProxy@microsoft.com) .

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Query's uitvoeren op uw Log Analytics of Application Insights resources en Azure Data Explorer

U kunt query's voor meerdere bronnen uitvoeren met behulp van client hulpprogramma's die ondersteuning bieden voor Kusto-query's. Voor beelden van deze hulpprogram ma's zijn de Log Analytics Web-UI, werkmappen, Power shell en de REST API.

Voer de id in voor een Azure Data Explorer-cluster in een query binnen het `adx` patroon, gevolgd door de naam en tabel van de data base.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Scherm afbeelding van een voor beeld van een query tussen meerdere services.":::

> [!NOTE]
>* Database namen zijn hoofdletter gevoelig.
>* De query voor meerdere resources als een waarschuwing wordt niet ondersteund.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Azure Data Explorer-cluster tabellen combi neren met een Log Analytics-werk ruimte

Gebruik de `union` opdracht om cluster tabellen te combi neren met een log Analytics-werk ruimte.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Scherm opname van een voor beeld van een query op meerdere services met de opdracht samen voegen.":::

> [!Tip]
> Steno-indeling is toegestaan: *cluster* naam / *InitialCatalog*. `adx('help/Samples')`Is bijvoorbeeld vertaald naar `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Gegevens uit een Azure Data Explorer-cluster samen voegen in één Tenant met een Azure Monitor bron in een andere.

Query's tussen tenants tussen de services worden niet ondersteund. U bent aangemeld bij één Tenant voor het uitvoeren van de query die beide bronnen omvat.

Als de Azure Data Explorer-resource zich in Tenant A bevindt en de Log Analytics-werk ruimte zich in Tenant B bevindt, gebruikt u een van de volgende methoden:

*  Met Azure Data Explorer kunt u rollen toevoegen voor principals in verschillende tenants. Voeg uw gebruikers-ID toe aan Tenant B als geautoriseerde gebruiker op het Azure Data Explorer-cluster. Controleer of de eigenschap [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) op het Azure Data Explorer-cluster Tenant B bevat. Voer de query's volledig uit in Tenant b.
*  Gebruik [Lighthouse](../../lighthouse/index.yml) om de Azure monitor resource in Tenant A te projecteren.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Verbinding maken met Azure Data Explorer clusters van verschillende tenants

Kusto Explorer meldt u automatisch aan bij de Tenant waarvan het gebruikers account oorspronkelijk deel uitmaakt. Als u toegang wilt krijgen tot bronnen in andere tenants met hetzelfde gebruikers account, moet u expliciet `TenantId` de Connection String opgeven:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Volgende stappen
* [Query's schrijven](/azure/data-explorer/write-queries)
* [Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer](/azure/data-explorer/query-monitor-data)
* [Query's tussen bronnen en logboeken uitvoeren in Azure Monitor](../logs/cross-workspace-query.md)