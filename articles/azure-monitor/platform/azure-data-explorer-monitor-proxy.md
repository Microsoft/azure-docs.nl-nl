---
title: Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)
description: Gebruik Azure Data Explorer om query's voor meerdere producten uit te voeren tussen Azure Data Explorer, Log Analytics werk ruimten en klassieke Application Insights toepassingen in Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 8942735ed65f8aa0cf6d315568e00412adcb353a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060534"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)

De Azure Data Explorer ondersteunt query's van meerdere services tussen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)en [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). U kunt vervolgens een query uitvoeren op uw Log Analytics/Application Insights werk ruimte met behulp van Azure Data Explorer-hulpprogram ma's en hiernaar verwijzen in een query voor query's op meerdere services. In dit artikel wordt beschreven hoe u een query voor meerdere services maakt en hoe u de Log Analytics-Application Insights-werk ruimte toevoegt aan Azure Data Explorer web-UI.

De query stroom voor Azure Data Explorer query's op meerdere services: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="proxy stroom van Azure Data Explorer.":::

> [!NOTE]
> * De mogelijkheid om Azure Monitor gegevens uit Azure Data Explorer op te vragen, rechtstreeks vanuit Azure Data Explorer client hulpprogramma's of indirect door een query uit te voeren op een Azure Data Explorer-cluster, is in de preview-modus.
>* Neem contact op met het team voor [Query's tussen services](mailto:adxproxy@microsoft.com) .

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Een Log Analytics/Application Insights-werk ruimte toevoegen aan Azure Data Explorer client hulpprogramma's

1. Controleer of uw Azure Data Explorer systeem eigen cluster (zoals *Help* -cluster) wordt weer gegeven in het menu links voordat u verbinding maakt met uw Log Analytics of Application Insights cluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Data Explorer systeem eigen cluster.":::

 https://dataexplorer.azure.com/clusters)Selecteer **cluster toevoegen** in de gebruikers interface van Azure Data Explorer (.

2. Voeg in het venster **cluster toevoegen** de URL van het La-of AI-cluster toe.

    * Voor LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Voor AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecteer **Toevoegen**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Cluster toevoegen.":::
 
>[!NOTE]
>Als u een verbinding toevoegt aan meer dan één Log Analytics/Application Insights-werk ruimte, geeft u een andere naam op. Anders hebben alle dezelfde naam in het linkerdeel venster.

 Nadat de verbinding tot stand is gebracht, wordt uw Log Analytics of Application Insights werk ruimte in het linkerdeel venster weer gegeven met uw systeem eigen Azure Data Explorer-cluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics-en Azure Data Explorer-clusters.":::
 
> [!NOTE]
> Het aantal Azure Monitor-werk ruimten dat kan worden toegewezen, is beperkt tot 100.

## <a name="create-queries-using-azure-monitor-data"></a>Query's maken met behulp van Azure Monitor gegevens

U kunt de query's uitvoeren met client hulpprogramma's die ondersteuning bieden voor Kusto-query's, zoals: Kusto Explorer, Azure Data Explorer web UI, Jupyter Kqlmagic, flow, PowerQuery, Power shell, lens, REST API.

> [!NOTE]
> De functie voor query's op meerdere services wordt alleen gebruikt voor het ophalen van gegevens. Zie [functie-ondersteuning](#function-supportability)voor meer informatie.

> [!TIP]
> * De naam van de data base moet dezelfde naam hebben als de resource die in de query voor meerdere services is opgegeven. Namen zijn hoofdlettergevoelig.
> * In query's voor meerdere clusters moet u ervoor zorgen dat de naamgeving van Application Insights-apps en Log Analytics-werk ruimten juist is.
> * Als namen speciale tekens bevatten, worden deze vervangen door URL-code ring in de query op meerdere services.
> * Als namen tekens bevatten die niet voldoen aan de [KQL-id-naam regels](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), worden deze vervangen door het koppel **-** teken.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Direct query op uw Log Analytics-of Application Insights-werk ruimten van Azure Data Explorer client hulpprogramma's

Query's uitvoeren op uw Log Analytics-of Application Insights-werk ruimten. Controleer of uw werk ruimte is geselecteerd in het linkerdeel venster.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Query Log Analytics werk ruimte.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Meerdere query's van uw Log Analytics of Application Insights en de Azure Data Explorer systeem eigen cluster

Wanneer u query's voor meerdere Cluster-Services uitvoert, controleert u of uw Azure Data Explorer systeem eigen cluster is geselecteerd in het linkerdeel venster. In de volgende voor beelden ziet u hoe u Azure Data Explorer cluster tabellen combineert met [behulp van Union](/azure/data-explorer/kusto/query/unionoperator) met log Analytics-werk ruimte.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Query's voor meerdere services van Azure Data Explorer.":::

Voor het gebruik van de [ `join` operator](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator), in plaats van Union, kan het nodig zijn [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) om het uit te voeren op een Azure Data Explorer systeem eigen cluster.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Gegevens uit een Azure Data Explorer-cluster samen voegen in één Tenant met een Azure Monitor bron in een andere.

Query's tussen tenants tussen de services worden niet ondersteund. U bent aangemeld bij één Tenant voor het uitvoeren van de query die beide resources beslaat.

Als de Azure Data Explorer-bron zich in Tenant A bevindt en Log Analytics werk ruimte zich in de Tenant B bevindt, gebruikt u een van de volgende twee methoden:

1. Met Azure Data Explorer kunt u rollen toevoegen voor principals in verschillende tenants. Voeg uw gebruikers-ID in de Tenant ' B ' toe als geautoriseerde gebruiker op het Azure Data Explorer-cluster. Valideer de eigenschap *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* in het Azure Data Explorer-cluster bevat Tenant ' B '. Voer de Kruis query volledig uit in de Tenant B.

2. Gebruik [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) om de Azure monitor resource te projecteren in Tenant A.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Verbinding maken met Azure Data Explorer clusters van verschillende tenants

Kusto Explorer meldt u automatisch aan bij de Tenant waarvan het gebruikers account oorspronkelijk deel uitmaakt. Om toegang te krijgen tot bronnen in andere tenants met hetzelfde gebruikers account, moet `tenantId` expliciet worden opgegeven in de Connection String: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Functie ondersteuning

De Azure Data Explorer query's voor meerdere services ondersteunen functies voor zowel Application Insights als Log Analytics.
Met deze mogelijkheid kunnen query's van meerdere clusters rechtstreeks verwijzen naar een functie in de tabel Azure Monitor.
De volgende opdrachten worden ondersteund met de query voor meerdere services:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

In de volgende afbeelding ziet u een voor beeld van het uitvoeren van een query op een tabellaire functie vanuit de Web-UI van Azure Data Explorer.
Als u de functie wilt gebruiken, voert u de naam in het query venster uit.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Een query uitvoeren op een tabellaire functie vanuit de Web-UI van Azure Data Explorer.":::

## <a name="additional-syntax-examples"></a>Aanvullende voor beelden van syntaxis

De volgende syntaxis opties zijn beschikbaar wanneer u de Log Analytics-of Application Insights-clusters aanroept:

|Syntaxis beschrijving  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Data base binnen een cluster dat alleen de gedefinieerde resource bevat in dit abonnement (**Aanbevolen voor query's in meerdere clusters**) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Cluster dat alle apps/werk ruimten in dit abonnement bevat    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Een cluster dat alle apps/werk ruimten in het abonnement bevat en lid is van deze resource groep    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Een cluster dat alleen de gedefinieerde resource bevat in dit abonnement      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [gegevens structuur van log Analytics-werk ruimten en Application Insights](data-platform-logs.md).
- Meer informatie over het [schrijven van query's in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/write-queries).