---
title: Standaard kolommen in Azure Monitor logboek records | Microsoft Docs
description: Hierin worden kolommen beschreven die gemeen schappelijk zijn voor meerdere gegevens typen in Azure Monitor-Logboeken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: dc3d119479d2dce45b286463f3d6a76410220dd0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014217"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Standaard kolommen in Azure Monitor-logboeken
Gegevens in Azure Monitor logboeken worden [opgeslagen als een set records in een log Analytics werk ruimte of Application Insights toepassing](./data-platform-logs.md), elk met een bepaald gegevens type met een unieke set kolommen. Veel gegevens typen hebben standaard kolommen die gemeen schappelijk zijn voor meerdere typen. In dit artikel worden deze kolommen beschreven en vindt u voor beelden van hoe u deze kunt gebruiken in query's.

Op werk ruimte gebaseerde toepassingen in Application Insights slaan hun gegevens op in een Log Analytics-werk ruimte en gebruiken dezelfde standaard kolommen als andere tabellen in de werk ruimte. Klassieke toepassingen slaan hun gegevens afzonderlijk op en hebben verschillende standaard kolommen zoals beschreven in dit artikel.

> [!NOTE]
> Sommige standaard kolommen worden niet weer gegeven in de schema weergave of IntelliSense in Log Analytics en ze worden niet weer gegeven in query resultaten tenzij u de kolom expliciet opgeeft in de uitvoer.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated en tijds tempel
De kolommen **TimeGenerated** (log Analytics werk ruimte) en **tijds tempel** (Application Insights toepassing) bevatten de datum en tijd waarop de record is gemaakt door de gegevens bron. Zie [opname tijd van logboek gegevens in azure monitor](data-ingestion-time.md) voor meer informatie.

**TimeGenerated** en **Time Stamp** bieden een gemeen schappelijke kolom die wordt gebruikt voor filteren of samen vatting op tijd. Wanneer u een tijds bereik voor een weer gave of dash board in de Azure Portal selecteert, wordt TimeGenerated of Time Stamp gebruikt om de resultaten te filteren. 

### <a name="examples"></a>Voorbeelden

De volgende query retourneert het aantal fout gebeurtenissen dat voor elke dag in de vorige week wordt gemaakt.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

De volgende query retourneert het aantal uitzonde ringen dat voor elke dag in de vorige week is gemaakt.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
De kolom **\_ TimeReceived** bevat de datum en tijd waarop de record is ontvangen door het Azure monitor opname punt in de Azure-Cloud. Dit kan handig zijn om latentie problemen tussen de gegevens bron en de cloud te identificeren. Een voor beeld hiervan is een netwerk probleem dat een vertraging veroorzaakt bij het verzenden van gegevens van een agent. Zie [opname tijd van logboek gegevens in azure monitor](data-ingestion-time.md) voor meer informatie.

De volgende query geeft de gemiddelde latentie per uur voor gebeurtenis records van een agent. Dit omvat de tijd van de agent naar de Cloud en de totale tijd voor de record die beschikbaar is voor logboek query's.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ en item type
De kolommen **type** (log Analytics-werk ruimte **) en** ingedeelde lijst (Application Insights toepassing) bevatten de naam van de tabel waaruit de record is opgehaald, die ook als het record type kan worden beschouwd. Deze kolom is handig in query's die records uit meerdere tabellen combi neren, zoals de gegevens die gebruikmaken van de `search` operator om onderscheid te maken tussen records van verschillende typen. **$Table** kan in plaats van het **type** op sommige locaties worden gebruikt.

### <a name="examples"></a>Voorbeelden
Met de volgende query wordt het aantal records geretourneerd op basis van het type dat in het afgelopen uur is verzameld.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
De kolom **\_ Itemid** bevat een unieke id voor de record.


## <a name="_resourceid"></a>\_ResourceId
De kolom **\_ ResourceID** bevat een unieke id voor de resource waaraan de record is gekoppeld. Dit geeft u een standaard kolom die wordt gebruikt om uw query te beperken tot alleen records van een bepaalde resource, of om gerelateerde gegevens over meerdere tabellen samen te voegen.

Voor Azure-resources is de waarde van **_ResourceId** de [URL van de Azure-resource-id](../../azure-resource-manager/templates/template-functions-resource.md). De kolom is momenteel beperkt tot Azure-resources, maar wordt uitgebreid naar bronnen buiten Azure, zoals on-premises computers.

> [!NOTE]
> Sommige gegevens typen bevatten al velden met een Azure-Resource-ID of ten minste delen daarvan, zoals de abonnements-ID. Hoewel deze velden voor compatibiliteit met eerdere versies worden bewaard, is het raadzaam om de _ResourceId te gebruiken voor het uitvoeren van kruis correlatie, omdat het consistenter is.

### <a name="examples"></a>Voorbeelden
Met de volgende query worden de prestatie-en gebeurtenis gegevens voor elke computer samengevoegd. Alle gebeurtenissen met de ID _101_ en processor gebruik worden weer gegeven via 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Met de volgende query worden _AzureActivity_ -records samengevoegd met _SecurityEvent_ -records. Het geeft alle activiteiten bewerkingen weer met gebruikers die zijn aangemeld bij deze machines.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Met de volgende query worden **_ResourceId** geparseerd en worden gefactureerde gegevens volumes per Azure-abonnement geaggregeerd.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Gebruik deze `union withsource = tt *` query's spaarzaam als scans over gegevens typen duur zijn om uit te voeren.

## <a name="_isbillable"></a>\_IsBillable
De kolom **\_ IsBillable** geeft aan of geconsumeerde gegevens Factureerbaar zijn. Gegevens met **\_ IsBillable** gelijk aan `false` worden gratis verzameld en worden niet in rekening gebracht voor uw Azure-account.

### <a name="examples"></a>Voorbeelden
Gebruik de volgende query om een lijst op te halen met computers die gefactureerde gegevens typen verzenden:

> [!NOTE]
> Gebruik query's met `union withsource = tt *` spaarzaam als scans over gegevens typen duur zijn om uit te voeren. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Dit kan worden uitgebreid om het aantal computers per uur te retour neren dat gefactureerde gegevens typen verzendt:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
De kolom **\_ BilledSize** geeft de grootte in bytes aan gegevens aan die worden gefactureerd voor uw Azure-account als **\_ IsBillable** is ingesteld op True.


### <a name="examples"></a>Voorbeelden
Als u de grootte van de factureer bare gebeurtenissen die per computer zijn opgenomen, wilt zien, gebruikt u de `_BilledSize` kolom die de grootte in bytes levert:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Gebruik de volgende query om de omvang van de factureer bare gebeurtenissen die per abonnement zijn opgenomen te bekijken:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Gebruik de volgende query om de omvang van de factureer bare gebeurtenissen per resource groep te bekijken:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Voor een overzicht van het aantal gebeurtenissen dat per computer is opgenomen, gebruikt u de volgende query:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Gebruik de volgende query om het aantal factureer bare gebeurtenissen per computer te bekijken: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Gebruik de volgende query om het aantal factureer bare gegevens typen van een specifieke computer te bekijken:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Volgende stappen

- Lees meer over hoe [Azure monitor logboek gegevens worden opgeslagen](../log-query/log-query-overview.md).
- Lees een les over het [schrijven van logboek query's](../log-query/get-started-queries.md).
- Maak een les over het [koppelen van tabellen in logboek query's](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).