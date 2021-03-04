---
title: Opname tijd van gegevens vastleggen in Azure Monitor | Microsoft Docs
description: Hierin worden de verschillende factoren beschreven die van invloed zijn op de latentie bij het verzamelen van logboek gegevens in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 6037ef9c539c3c57f2ba5a19f371237159d1bf69
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030882"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Opnametijd van gegevens vastleggen in een logboek in Azure Monitor
Azure Monitor is een grootschalige gegevensservice die elke maand een groeiend aantal terabytes aan gegevens van duizenden klanten verwerkt. Er zijn vaak vragen over de tijd die nodig is om te zorgen dat logboek gegevens beschikbaar worden nadat deze zijn verzameld. In dit artikel worden de verschillende factoren beschreven die van invloed zijn op deze latentie.

## <a name="typical-latency"></a>Normale latentie
De latentie verwijst naar het tijdstip waarop gegevens worden gemaakt op het bewaakte systeem en de tijd die beschikbaar is voor analyse in Azure Monitor. De gemiddelde latentie voor opname van logboek gegevens ligt tussen 2 en 5 minuten. De specifieke latentie voor bepaalde gegevens kan variëren, afhankelijk van de verschillende factoren die hieronder worden beschreven.


## <a name="factors-affecting-latency"></a>Factoren die invloed hebben op latentie
De totale opname tijd voor een bepaalde set gegevens kan worden onderverdeeld in de volgende gebieden op hoog niveau. 

- Tijd van de agent: de tijd om een gebeurtenis te detecteren, te verzamelen en deze vervolgens te verzenden naar Azure Monitor opname punt als een logboek record. In de meeste gevallen wordt dit proces verwerkt door een agent.
- Pijplijntijd. Dit is de tijd die het kost om de logboekrecord te verwerken via de opnamepijplijn. Dit omvat het parseren van de eigenschappen van de gebeurtenis en het mogelijk toevoegen van berekende gegevens.
- Indexerings tijd – de tijd die is besteed aan het opnemen van een logboek record in Azure Monitor big data store.

Details over de verschillende latentie die in dit proces wordt geïntroduceerd, worden hieronder beschreven.

### <a name="agent-collection-latency"></a>Verzamelingslatentie van agent
Agents en beheer oplossingen gebruiken verschillende strategieën voor het verzamelen van gegevens van een virtuele machine. Dit kan van invloed zijn op de latentie. Enkele specifieke voor beelden zijn:

- Windows-gebeurtenissen, syslog-gebeurtenissen en metrische prestatie gegevens worden direct verzameld. Linux-prestatie meter items worden met intervallen van 30 seconden gecontroleerd.
- IIS-logboeken en aangepaste logboeken worden verzameld zodra hun tijds tempel wordt gewijzigd. Voor IIS-logboeken wordt dit beïnvloed door het [rollover schema dat is geconfigureerd op IIS](../agents/data-sources-iis-logs.md). 
- Active Directory-replicatie oplossing voert de evaluatie uit om de vijf dagen, terwijl de Active Directory-evaluatie oplossing een wekelijkse evaluatie van uw Active Directory infra structuur uitvoert. Deze logboeken worden alleen door de agent verzameld wanneer de evaluatie is voltooid.

### <a name="agent-upload-frequency"></a>Uploadfrequentie van agent
Om ervoor te zorgen dat de Log Analytics agent lichter is, buffert de agent de logboeken en uploadt deze regel matig naar Azure Monitor. De upload frequentie varieert van 30 seconden tot 2 minuten, afhankelijk van het type gegevens. De meeste gegevens worden binnen 1 minuut geüpload. Netwerk omstandigheden kunnen een negatieve invloed hebben op de latentie van deze gegevens om Azure Monitor opname punt te bereiken.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Activiteiten logboeken van Azure, resource logboeken en metrische gegevens
Azure-gegevens voegen extra tijd toe om op Log Analytics opname punt beschikbaar te komen voor verwerking:

- Gegevens uit de bron logboeken nemen 2-15 minuten in beslag, afhankelijk van de Azure-service. Zie de [onderstaande query](#checking-ingestion-time) om deze latentie te controleren in uw omgeving
- De metrische gegevens van het Azure-platform duren drie minuten om te worden verzonden naar Log Analytics opname punt.
- De gegevens van het activiteiten logboek nemen ongeveer 10-15 minuten in beslag om te worden verzonden naar Log Analytics opname punt.

Zodra de gegevens beschikbaar zijn op het opname punt, duurt het meer dan 2-5 minuten voor het uitvoeren van query's.

### <a name="management-solutions-collection"></a>Verzameling beheer oplossingen
Sommige oplossingen verzamelen geen gegevens van een agent en kunnen een verzamelings methode gebruiken waarmee extra latentie wordt geïntroduceerd. Sommige oplossingen verzamelen gegevens met regel matige tussen pozen zonder dat dit bijna in realtime kan worden verzameld. Specifieke voor beelden zijn:

- Microsoft 365-oplossing vraagt activiteiten logboeken met behulp van de API voor beheer activiteit, die momenteel geen bijna-real-time latentie garanties biedt.
- Windows Analytics-oplossingen (Updatenaleving bijvoorbeeld) gegevens worden verzameld door de oplossing op basis van een dagelijkse frequentie.

Raadpleeg de documentatie voor elke oplossing om de verzamelings frequentie te bepalen.

### <a name="pipeline-process-time"></a>Pijp lijn-verwerkings tijd
Zodra logboek records zijn opgenomen in de Azure Monitor-pijp lijn (zoals aangegeven in de eigenschap [_TimeReceived](./log-standard-columns.md#_timereceived) ), worden ze geschreven naar tijdelijke opslag om Tenant isolatie te garanderen en ervoor te zorgen dat de gegevens niet verloren gaan. Dit proces voegt doorgaans 5-15 seconden toe. Sommige beheer oplossingen implementeren zwaarere algoritmen voor het verzamelen van gegevens en het afleiden van inzichten naarmate gegevens worden gestreamd. Zo worden met de bewaking van netwerk prestaties inkomende gegevens in intervallen van drie minuten geaggregeerd, waardoor er een latentie van drie minuten wordt toegevoegd. Een ander proces dat latentie toevoegt, is het proces waarmee aangepaste logboeken worden verwerkt. In sommige gevallen kan dit proces enkele minuten latentie toevoegen aan logboeken die worden verzameld van bestanden door de agent.

### <a name="new-custom-data-types-provisioning"></a>Nieuwe aangepaste gegevens typen inrichten
Wanneer er een nieuw type aangepaste gegevens wordt gemaakt op basis van een [aangepast logboek](../agents/data-sources-custom-logs.md) of de [Data Collector-API](../logs/data-collector-api.md), maakt het systeem een speciale opslag container. Dit is een eenmalige overhead die alleen optreedt bij de eerste keer dat dit gegevenstype wordt gebruikt.

### <a name="surge-protection"></a>Piek beveiliging
De belangrijkste prioriteit van Azure Monitor is om ervoor te zorgen dat er geen klant gegevens verloren gaan, dus heeft het systeem ingebouwde beveiliging voor gegevens pieken. Dit omvat buffers om ervoor te zorgen dat het systeem zelfs onder een enorme belasting blijft functioneren. Onder normale belasting voegen deze besturings elementen minder dan een minuut toe, maar in extreme omstandigheden en storingen kunnen ze aanzienlijke tijd toevoegen terwijl de gegevens veilig zijn.

### <a name="indexing-time"></a>Indexerings tijd
Er is een ingebouwd saldo voor elk big data platform tussen het bieden van analyses en geavanceerde zoek functies, in tegens telling tot onmiddellijke toegang tot de gegevens. Met Azure Monitor kunt u krachtige query's uitvoeren op miljarden records en de resultaten binnen een paar seconden ophalen. Dit is mogelijk omdat de infra structuur de gegevens ingrijpend transformeert tijdens de opname en opslaat in unieke compacte structuren. Het systeem buffert de gegevens totdat er genoeg zijn om deze structuren te maken. Dit moet worden voltooid voordat de logboek record wordt weer gegeven in de zoek resultaten.

Dit proces neemt op dit moment ongeveer 5 minuten in beslag wanneer er weinig gegevens zijn, maar minder tijd bij hogere gegevens snelheden. Dit lijkt op onlogisch, maar dit proces staat het optimaliseren van latentie toe voor productiewerk belastingen met een hoog volume.



## <a name="checking-ingestion-time"></a>Opname tijd controleren
De opname tijd kan variëren voor verschillende bronnen onder verschillende omstandigheden. U kunt logboek query's gebruiken om specifiek gedrag van uw omgeving te identificeren. In de volgende tabel wordt aangegeven hoe u de verschillende tijdstippen kunt bepalen voor een record die wordt gemaakt en verzonden naar Azure Monitor.

| Stap | Eigenschap of functie | Opmerkingen |
|:---|:---|:---|
| Record gemaakt in gegevens bron | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Als de gegevens bron deze waarde niet instelt, wordt deze ingesteld op dezelfde tijd als _TimeReceived. |
| Record ontvangen door Azure Monitor opname-eind punt | [_TimeReceived](./log-standard-columns.md#_timereceived) | |
| Record opgeslagen in de werk ruimte en beschikbaar voor query's | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Vertragingen bij opname latentie
U kunt de latentie van een specifieke record meten door het resultaat van de functie [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) te vergelijken met de eigenschap _TimeGenerated_ . Deze gegevens kunnen met verschillende aggregaties worden gebruikt om te ontdekken hoe de latentie van opname wordt gedraagt. Bekijk een aantal percentiel van de opname tijd om inzicht te krijgen in grote hoeveel heden gegevens. 

Met de volgende query kunt u bijvoorbeeld zien welke computers de hoogste 8 uur hebben geduurd: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

De voor gaande percentiel controles zijn geschikt voor het vinden van algemene trends in latentie. Als u een piek in de latentie van een korte termijn wilt identificeren, is het gebruik van het maximum ( `max()` ) mogelijk effectiever.

Als u wilt inzoomen op de opname tijd voor een specifieke computer gedurende een bepaalde periode, gebruikt u de volgende query, waarmee ook de gegevens van de afgelopen dag in een grafiek worden gevisualiseerd: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Gebruik de volgende query om computer opname tijd weer te geven op het land/de regio waarin ze zich bevinden, op basis van hun IP-adres: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Verschillende gegevens typen die afkomstig zijn van de agent, hebben mogelijk een andere latentie periode voor inslikken, zodat de vorige query's kunnen worden gebruikt met andere typen. Gebruik de volgende query om de opname tijd van verschillende Azure-Services te onderzoeken: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Bronnen die niet meer reageren 
In sommige gevallen kan een resource stoppen met het verzenden van gegevens. Als u wilt weten of een resource gegevens verzendt, bekijkt u de meest recente record die kan worden geïdentificeerd met het standaard _TimeGenerated_ -veld.  

Gebruik de _heartbeat_ -tabel om de beschik baarheid van een virtuele machine te controleren, omdat een heartbeat eenmaal per minuut door de agent wordt verzonden. Gebruik de volgende query om een lijst weer te geven met de actieve computers die geen heartbeat recent hebben gerapporteerd: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Volgende stappen
* Lees de [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) voor Azure monitor.