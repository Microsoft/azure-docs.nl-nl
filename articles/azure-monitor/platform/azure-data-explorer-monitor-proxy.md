---
title: Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)
description: Gebruik Azure Data Explorer om query's voor meerdere producten uit te voeren tussen Azure Data Explorer, Log Analytics werk ruimten en klassieke Application Insights toepassingen in Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185977"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)
Met het Azure Data Explorer-proxy cluster kunt u query's uitvoeren tussen Azure Data Explorer, Log Analytics werk ruimten en klassieke Application Insights toepassingen in Azure Monitor. U kunt Log Analytics-werk ruimten in Azure Monitor-of klassieke Application Insights-apps als proxy clusters toewijzen. U kunt vervolgens een query uitvoeren op het proxy cluster met behulp van Azure Data Explorer-hulpprogram ma's en hiernaar verwijzen in een cross-cluster query. In dit artikel wordt beschreven hoe u verbinding maakt met een proxy cluster, een proxy cluster toevoegt aan Azure Data Explorer web-UI en query's uitvoert op uw Log Analytics-werk ruimten of klassieke Application Insights-apps van Azure Data Explorer.

In het volgende diagram ziet u de Azure Data Explorer-proxy stroom:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Azure Data Explorer-proxy stroom.":::


> [!NOTE]
> De Azure Data Explorer-proxy bevindt zich in de open bare preview. [Maak verbinding met de proxy](#connect-to-the-proxy) om de proxy functie voor uw clusters in te scha kelen. 

## <a name="connect-to-the-proxy"></a>Verbinding maken met de proxy
Open de[Web-UI van Azure Data Explorer](https://dataexplorer.azure.com/clusters)om uw log Analytics-werk ruimte of klassieke Application Insights-app te verbinden. Controleer of uw Azure Data Explorer systeem eigen cluster (zoals *Help* -cluster) wordt weer gegeven in het menu links voordat u verbinding maakt met uw Log Analytics of Application Insights cluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Data Explorer systeem eigen cluster.":::

Klik op **cluster toevoegen** en voeg de URL van de Log Analytics of Application Insights cluster in een van de volgende indelingen toe. 
    
* Voor Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Voor Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Klik op **toevoegen** om de verbinding te maken.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Cluster toevoegen.":::
 
> [!NOTE]
> Als u een verbinding aan meer dan één proxy cluster toevoegt, geeft u een andere naam op. Anders hebben alle dezelfde naam in het linkerdeel venster.

Nadat de verbinding tot stand is gebracht, wordt uw Log Analytics-of Application Insights-cluster weer gegeven in het linkerdeel venster met uw systeem eigen Azure Data Explorer-cluster. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics-en Azure Data Explorer-clusters.":::
 
> [!NOTE]
> Het aantal Azure Monitor-werk ruimten dat kan worden toegewezen, is beperkt tot 100.

## <a name="create-queries-using-azure-monitor-data"></a>Query's maken met behulp van Azure Monitor gegevens

U kunt de query's uitvoeren met client hulpprogramma's die ondersteuning bieden voor Kusto-query's, zoals: Kusto Explorer, Azure Data Explorer web UI, Jupyter Kqlmagic, flow, PowerQuery, Power shell, Jarvis, lens en REST API.

> [!NOTE]
> De functie Azure Data Explorer proxy wordt alleen gebruikt voor het ophalen van gegevens. Zie [functie-ondersteuning](#function-supportability)voor meer informatie.

> [!TIP]
> * De naam van de data base moet dezelfde naam hebben als de resource die is opgegeven in het proxy cluster. Namen zijn hoofdlettergevoelig.
> * In query's voor meerdere clusters moet u ervoor zorgen dat de naamgeving van Application Insights-apps en Log Analytics-werk ruimten juist is.
>     * Als namen speciale tekens bevatten, worden deze vervangen door URL-code ring in de naam van het proxy cluster. 
>     * Als namen tekens bevatten die niet voldoen aan de [KQL-id-naam regels](/azure/data-explorer/kusto/query/schema-entities/entity-names), worden deze vervangen door het koppel **-** teken.

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Directe query van uw Log Analytics of Application Insights proxy cluster

Query's uitvoeren op uw Log Analytics of Application Insights cluster. Controleer of uw cluster is geselecteerd in het linkerdeel venster. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Query Log Analytics werk ruimte.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Query's uitvoeren op uw Log Analytics-of Application Insights-proxy cluster en het Azure Data Explorer systeem eigen cluster

Wanneer u cross-cluster query's uitvoert vanuit de proxy, controleert u of uw Azure Data Explorer systeem eigen cluster is geselecteerd in het linkerdeel venster. In de volgende voor beelden ziet u hoe u Azure Data Explorer cluster tabellen combineert met behulp van de operator [Union](/azure/data-explorer/kusto/query/unionoperator) met een log Analytics-werk ruimte.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Het gebruik van de [ `join` operator](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)in plaats van Union vereist mogelijk een [Hint](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) om deze uit te voeren op een native Azure Data Explorer-cluster (en niet op de proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Gegevens uit een Azure Data Explorer-cluster samen voegen in één Tenant met een Azure Monitor bron in een andere.

Query's voor meerdere tenants worden niet ondersteund door Azure Data Explorer proxy. U bent aangemeld bij één Tenant voor het uitvoeren van de query die beide resources beslaat.

Als de Azure Data Explorer-bron zich in Tenant A bevindt en Log Analytics werk ruimte zich in de Tenant B bevindt, gebruikt u een van de volgende twee methoden:

- Met Azure Data Explorer kunt u rollen toevoegen voor principals in verschillende tenants. Voeg uw gebruikers-ID in de Tenant ' B ' toe als geautoriseerde gebruiker op het Azure Data Explorer-cluster. Valideer de eigenschap *[' TrustedExternalTenant '](/powershell/module/az.kusto/update-azkustocluster)* in het Azure Data Explorer-cluster bevat Tenant ' B '. Voer de Kruis query volledig uit in de Tenant B.

- Gebruik [Lighthouse](../../lighthouse/index.yml) om de Azure monitor resource te projecteren in Tenant A.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Verbinding maken met Azure Data Explorer clusters van verschillende tenants

Kusto Explorer meldt u automatisch aan bij de Tenant waarvan het gebruikers account oorspronkelijk deel uitmaakt. Om toegang te krijgen tot bronnen in andere tenants met hetzelfde gebruikers account, moet `tenantId` expliciet worden opgegeven in de Connection String: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Functie ondersteuning

Het Azure Data Explorer-proxy cluster ondersteunt functies voor zowel Log Analytics als Application Insights. Met deze mogelijkheid kunnen query's van meerdere clusters rechtstreeks verwijzen naar een functie in de tabel Azure Monitor.

De volgende opdrachten worden ondersteund door de proxy:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

In de volgende afbeelding ziet u een voor beeld van het uitvoeren van een query op een tabellaire functie vanuit de Web-UI van Azure Data Explorer. Als u de functie wilt gebruiken, voert u de naam in het query venster uit.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Een query uitvoeren op een tabellaire functie vanuit de Web-UI van Azure Data Explorer.":::
 
> [!NOTE]
> Azure Monitor ondersteunt alleen tabellaire functies, die geen para meters ondersteunen.

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
- Meer informatie over het [schrijven van query's in Azure Data Explorer](/azure/data-explorer/write-queries).