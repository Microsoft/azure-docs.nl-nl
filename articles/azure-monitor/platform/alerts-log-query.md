---
title: Logboek waarschuwings query's optimaliseren | Microsoft Docs
description: Aanbevelingen voor het schrijven van efficiënte waarschuwings query's
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: e7c9c76816b5d1ee2eedfb7e54645e056906feef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186623"
---
# <a name="optimizing-log-alert-queries"></a>Logboek waarschuwings query's optimaliseren
In dit artikel wordt beschreven hoe u [logboek waarschuwings](alerts-unified-log.md) query's schrijft en converteert om optimale prestaties te krijgen. Geoptimaliseerde query's verminderen de latentie en het laden van waarschuwingen, die regel matig worden uitgevoerd.

## <a name="how-to-start-writing-an-alert-log-query"></a>Beginnen met het schrijven van een query voor een waarschuwings logboek

Waarschuwings query's beginnen met [het uitvoeren van query's in de logboek gegevens in log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) die het probleem aangeven. U kunt het [onderwerp voor beelden van waarschuwings query's](../log-query/example-queries.md) gebruiken om te begrijpen wat u kunt ontdekken. U kunt ook aan [de slag gaan met het schrijven van uw eigen query](../log-query/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Query's die het probleem aangeven en niet de waarschuwing

De waarschuwings stroom is gebouwd om de resultaten te transformeren die het probleem aan een waarschuwing wijzen. In het geval van een query bijvoorbeeld:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Als het doel van de gebruiker is om te waarschuwen wanneer dit gebeurtenis type plaatsvindt, wordt de waarschuwings logica toegevoegd `count` aan de query. De query die wordt uitgevoerd, is:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

U hoeft geen waarschuwings logica aan de query toe te voegen en dat kan zelfs problemen veroorzaken. Als u in het bovenstaande voor beeld uw query opneemt `count` , resulteert dit altijd in de waarde 1, omdat de waarschuwings service van wordt `count` uitgevoerd `count` .

### <a name="avoid-limit-and-take-operators"></a>Voor komen `limit` en `take` Opera tors

Met behulp van `limit` en `take` in query's kan de latentie toenemen en waarschuwingen worden geladen omdat de resultaten gedurende een bepaalde periode niet consistent zijn. U kunt het beste alleen gebruiken als dat nodig is.

## <a name="log-query-constraints"></a>Query beperkingen voor logboek
[Logboek query's in azure monitor](../log-query/log-query-overview.md) beginnen met een tabel, [`search`](/azure/kusto/query/searchoperator) [`union`](/azure/kusto/query/unionoperator) operator of.

Query's voor waarschuwings regels in het logboek moeten altijd beginnen met een tabel om een duidelijk bereik te definiëren. Dit verbetert de prestaties van query's en de relevantie van de resultaten. Query's in waarschuwings regels worden regel matig uitgevoerd, dus met `search` en `union` kunnen leiden tot buitensporige overhead bij het toevoegen van latentie aan de waarschuwing, omdat er moet worden gescand in meerdere tabellen. Deze opera tors verminderen ook de mogelijkheid van de service waarschuwingen om de query te optimaliseren.

We bieden geen ondersteuning voor het maken of wijzigen van regels voor logboek waarschuwingen die gebruikmaken `search` `union` van or-Opera Tors, voor query's op meerdere resources.

De volgende waarschuwings query ligt bijvoorbeeld binnen het bereik van de _SecurityEvent_ -tabel en zoekt naar een specifieke gebeurtenis-id. Het is de enige tabel die de query moet verwerken.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Het vastleggen van waarschuwings regels in [meerdere bron query's](../log-query/cross-workspace-query.md) wordt niet beïnvloed door deze wijziging omdat query's voor meerdere bronnen gebruikmaken van een type `union` , waarmee het query bereik wordt beperkt tot specifieke resources. Het volgende voor beeld is een geldige query voor een logboek waarschuwing:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [Query's voor meerdere resources](../log-query/cross-workspace-query.md) worden ondersteund in de nieuwe [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules). Als u nog steeds de [verouderde API voor log Analytics waarschuwingen](api-alerts.md) gebruikt voor het maken van logboek waarschuwingen, kunt u [hier](alerts-log-api-switch.md)meer informatie over het overschakelen.

## <a name="examples"></a>Voorbeelden
De volgende voor beelden zijn logboek query's die gebruikmaken van `search` en `union` en stappen bieden die u kunt gebruiken om deze query's te wijzigen voor gebruik in waarschuwings regels.

### <a name="example-1"></a>Voorbeeld 1
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die de prestatie gegevens ophaalt met `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen horen:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Het resultaat van deze query geeft aan dat de eigenschap _CounterName_ afkomstig is van de _prestatie_ tabel.

U kunt dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Voorbeeld 2
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die de prestatie gegevens ophaalt met `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen horen:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Het resultaat van deze query geeft aan dat de eigenschap _ObjectName_ en _CounterName_ afkomstig is uit de _prestatie_ tabel.

U kunt dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Voorbeeld 3

U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query waarbij zowel als informatie wordt gebruikt `search` `union` om prestatie gegevens op te halen: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen in het eerste deel van de query deel uitmaken: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Het resultaat van deze query geeft aan dat al deze eigenschappen afkomstig zijn uit de _prestatie_ tabel.

Gebruik `union` with `withsource` Command om te bepalen welke bron tabel aan elke rij heeft bijgedragen.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Het resultaat van deze query geeft aan dat deze eigenschappen ook afkomstig zijn uit de _prestatie_ tabel.

U kunt deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Voorbeeld 4
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die de resultaten van twee query's samenvoegt `search` :

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Als u de query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren die de eigenschappen aan de linkerkant van de samen voeging bevat: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Het resultaat geeft aan dat de eigenschappen aan de linkerkant van de samen voeging deel uitmaken van de tabel _SecurityEvent_ . 

Gebruik nu de volgende query om de tabel te identificeren die de eigenschappen in de rechter kant van de koppeling bevat: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Het resultaat geeft aan dat de eigenschappen in de rechter kant van de samen voeging deel uitmaken van de _heartbeat_ -tabel.

U kunt deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboek waarschuwingen](alerts-log.md) in azure monitor.
- Meer informatie over [logboek query's](../log-query/log-query-overview.md).