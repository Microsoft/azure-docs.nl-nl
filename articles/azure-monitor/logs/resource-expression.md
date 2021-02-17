---
title: resource ()-expressie in Azure Monitor logboek query | Microsoft Docs
description: De resource-expressie wordt gebruikt in een resource gerichte Azure Monitor-logboek query om gegevens op te halen uit meerdere resources.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612018"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resource ()-expressie in Azure Monitor-logboek query

De `resource` expressie wordt gebruikt in een Azure monitor query [bereik voor een resource](scope.md#query-scope) om gegevens op te halen uit andere resources. 


## <a name="syntax"></a>Syntax

`resource(`*Progid's*`)`

## <a name="arguments"></a>Argumenten

- *Id*: Resource-id van een resource.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resource | Bevat gegevens voor de resource. | resource ("/Subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/providers/Microsoft.Compute/virtualmachines/myvm") |
| Resource groep of-abonnement | Bevat gegevens voor de resource en alle resources die deze bevat.  | resource ("/Subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Notities

* U moet lees toegang hebben tot de resource.


## <a name="examples"></a>Voorbeelden

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Volgende stappen

- Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](scope.md) voor meer informatie over een query bereik.
- Toegang tot volledige documentatie voor de [Kusto-query taal](/azure/kusto/query/).
