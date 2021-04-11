---
title: app ()-expressie in Azure Monitor logboek query's | Microsoft Docs
description: De app-expressie wordt gebruikt in een Azure Monitor logboek query om gegevens op te halen uit een specifieke Application Insights-app in dezelfde resource groep, een andere resource groep of een ander abonnement.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 9d60857503402554381068fc36121fa3f7b61622
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283555"
---
# <a name="app-expression-in-azure-monitor-query"></a>app ()-expressie in Azure Monitor query

De `app` expressie wordt gebruikt in een Azure monitor query om gegevens op te halen uit een specifieke Application Insights-app in dezelfde resource groep, een andere resource groep of een ander abonnement. Dit is handig om toepassings gegevens op te halen in een Azure Monitor-logboek query en om gegevens over meerdere toepassingen in een Application Insights query op te vragen.

> [!IMPORTANT]
> De app ()-expressie wordt niet gebruikt als u gebruikmaakt van een [Application Insights resource op basis van een werk ruimte](../app/create-workspace-resource.md) , aangezien logboek gegevens worden opgeslagen in een log Analytics-werk ruimte. Gebruik de werk ruimte ()-expressie voor het schrijven van een query die toepassing bevat in meerdere werk ruimten. Voor meerdere toepassingen in dezelfde werk ruimte hebt u geen query op meerdere werk ruimten nodig.

## <a name="syntax"></a>Syntax

`app(`*Progid's*`)`


## <a name="arguments"></a>Argumenten

- *Id*: identificeert de app aan de hand van een van de volgende indelingen in de tabel hieronder.

| Id | Beschrijving | Voorbeeld
|:---|:---|:---|
| Resourcenaam | Door de mens lees bare naam van de app (ook wel ' component naam ' genoemd) | app ("fabrikamapp") |
| Gekwalificeerde naam | Volledige naam van de app in de vorm: ' subscriptionname/resourceGroup/naam van onderdeel ' | app (' AI-prototype/fabrikam/fabrikamapp ') |
| Id | GUID van de app | app ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-resource-id | Id voor de Azure-resource |app ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notities

* U moet lees toegang hebben tot de toepassing.
* Door de naam van een toepassing te identificeren, wordt ervan uitgegaan dat deze uniek is voor alle toegankelijke abonnementen. Als u meerdere toepassingen met de opgegeven naam hebt, mislukt de query vanwege de dubbel zinnigheid. In dit geval moet u een van de andere id's gebruiken.
* Gebruik de gerelateerde expressie- [werk ruimte](../logs/workspace-expression.md) om een query uit te kunnen log Analytics-werk ruimten.
* De expressie app () wordt momenteel niet ondersteund in de zoek query wanneer u de Azure Portal gebruikt om een [aangepaste waarschuwings regel voor logboek zoeken](../alerts/alerts-log.md)te maken, tenzij een Application Insights toepassing wordt gebruikt als de resource voor de waarschuwings regel.

## <a name="examples"></a>Voorbeelden

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Zie de [werkruimte expressie](../logs/workspace-expression.md) om te verwijzen naar een log Analytics-werk ruimte.
- Meer informatie over hoe [Azure monitor gegevens](./log-query-overview.md) worden opgeslagen.
- Toegang tot volledige documentatie voor de [Kusto-query taal](/azure/kusto/query/).
