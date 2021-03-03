---
title: de expressie Workspace () in Azure Monitor-logboek query | Microsoft Docs
description: De werkruimte expressie wordt gebruikt in een Azure Monitor logboek query om gegevens op te halen uit een specifieke werk ruimte in dezelfde resource groep, een andere resource groep of een ander abonnement.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 34b1f850b262a0027d3a9cb2e926bcb2c8a49665
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710903"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>de expressie Workspace () in Azure Monitor-logboek query

De `workspace` expressie wordt gebruikt in een Azure monitor query om gegevens op te halen uit een specifieke werk ruimte in dezelfde resource groep, een andere resource groep of een ander abonnement. Dit is handig als u logboek gegevens in een Application Insights query wilt gebruiken en gegevens wilt opvragen over meerdere werk ruimten in een logboek query.


## <a name="syntax"></a>Syntax

`workspace(`*Progid's*`)`

## <a name="arguments"></a>Argumenten

- *Id*: Hiermee wordt de werk ruimte geïdentificeerd aan de hand van een van de volgende indelingen in de tabel.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resourcenaam | Door de mens lees bare naam van de werk ruimte (ook wel "onderdeel naam") | werk ruimte ("contosoretail") |
| Gekwalificeerde naam | Volledige naam van de werk ruimte in de vorm: "subscriptionname/resourceGroup/onderdeel naam" | werk ruimte (' contoso/ContosoResource/ContosoWorkspace ') |
| Id | GUID van de werk ruimte | werk ruimte ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-Resource-ID | Id voor de Azure-resource | werk ruimte ("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notities

* U moet lees toegang hebben tot de werk ruimte.
* Een gerelateerde expressie is `app` waarmee u een query kunt uitvoeren op Application Insights toepassingen.

## <a name="examples"></a>Voorbeelden

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [app-expressie](./app-expression.md) om te verwijzen naar een Application Insights-app.
- Meer informatie over hoe [Azure monitor gegevens](./log-query-overview.md) worden opgeslagen.
- Toegang tot volledige documentatie voor de [Kusto-query taal](/azure/kusto/query/).