---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734029"
---
### <a name="general-query-limits"></a>Algemene querylimieten

| Limiet | Beschrijving |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-querytaal](/azure/kusto/query/) als Azure Data Explorer. Zie [Verschillen in Azure Monitor-logboekquerytaal](/azure/data-explorer/kusto/query/) voor KQL-taalelementen die niet worden ondersteund in Azure Monitor. |
| Azure-regio's | Logboekquery's kunnen overmatige overhead ondervinden wanneer gegevens Log Analytics-werkruimten in meerdere Azure-regio's omvatten. Zie [Querylimieten](../articles/azure-monitor/logs/scope.md#query-scope-limits) voor meer informatie. |
| Query's voor meerdere resources | Het maximum aantal Application Insights-resources en Log Analytics-werkruimten in één query is beperkt tot 100.<br>Query's voor meerdere resources worden niet ondersteund in View Designer.<br>Query's voor meerdere resources in logboekwaarschuwingen worden ondersteund in de nieuwe cheduledQueryRules-API.<br>Zie [Limieten voor query's voor meerdere resources](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |

### <a name="user-query-throttling"></a>Beperking van gebruikersquery's
Azure Monitor beschikt over een aantal beperkingslimieten als bescherming tegen gebruikers die een overmatig aantal query's verzenden. Dit gedrag kan leiden tot overbelasting van de backendbronnen van het systeem en de servicereactietijd in gevaar brengen. De volgende limieten zijn bedoeld om klanten te beschermen tegen onderbrekingen en om een consistent serviceniveau te garanderen. De gebruikersbeperking en -limieten zijn zodanig ontworpen dat ze alleen van invloed zijn op extreme gebruiksscenario's en niet relevant zijn voor normaal gebruik.


| Measure | Limiet per gebruiker | Beschrijving |
|:---|:---|:---|
| Gelijktijdige query's | 5 | Als er al vijf query's worden uitgevoerd voor de gebruiker, worden nieuwe query's in een wachtrij voor gelijktijdig gebruik per gebruiker geplaatst. Wanneer een van de actieve query's eindigt, wordt de volgende query opgehaald uit de wachtrij en gestart. Dit omvat geen query's van waarschuwingsregels.
| Tijd in wachtrij voor gelijktijdig gebruik | 3 minuten | Als een query langer dan drie minuten in de wachtrij staat zonder te zijn gestart, wordt deze met een HTTP-foutreactie met code 429 beëindigd. |
| Totaal aantal query's in wachtrij voor gelijktijdig gebruik | 200 | Zodra er 200 query's in de wachtrij staan, worden eventuele extra query's geweigerd met een HTTP-foutcode 429. Dit aantal is een aanvulling op de vijf query's die tegelijkertijd kunnen worden uitgevoerd. |
| Queryfrequentie | 200 query's per 30 seconden | Dit is het totale aantal query's dat door één gebruiker aan alle werkruimten kan worden verzonden.  Deze limiet is van toepassing op programmatische query's of query's die worden geïnitieerd door visualisatieonderdelen als Azure-dashboards en de overzichtspagina van de Log Analytics-werkruimte. |

- Optimaliseer uw query's zoals beschreven in [Logboekquery's in Azure Monitor optimaliseren](../articles/azure-monitor/logs/query-optimization.md).
- Dashboards en werkmappen kunnen meerdere query's bevatten in één weergave die een burst van query's genereert elke keer dat ze worden geladen of vernieuwd. Overweeg deze op te splitsen in meerdere weergaven die op aanvraag worden geladen. 
- Overweeg om in Power BI alleen samengevoegde resultaten in plaats van onbewerkte logboeken te extraheren.