---
title: Aan de slag met logboekquery’s in Azure Monitor | Microsoft Docs
description: In dit artikel vindt u een zelfstudie voor het schrijven van logboekquery's in Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: c87b70c74a5de9cc4b6c48935b93fdd13295f0f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052327"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Aan de slag met logboekquery’s in Azure Monitor

> [!NOTE]
> U kunt deze oefening in uw eigen omgeving doorlopen als u gegevens verzamelt van ten minste één virtuele machine. Als dat niet het geval is, kunt u onze [demo-omgeving](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) gebruiken. Deze bevat veel voorbeeldgegevens.  Raadpleeg het [deelvenster met opgeslagen voorbeeldquery’s](../logs/example-queries.md) als u al weet hoe u een query in KQL kunt uitvoeren, maar alleen snel nuttige query's hoeft te maken op basis van resourcetype(n).

In deze zelfstudie leert u hoe u logboekquery's schrijft in Azure Monitor. U leert het volgende:

- Querystructuur begrijpen
- Queryresultaten sorteren
- Queryresultaten filteren
- Een periode opgeven
- Selecteren welke velden moeten worden meegenomen in de resultaten
- Aangepaste velden definiëren en gebruiken
- Resultaten samenvoegen en groeperen

Raadpleeg [Aan de slag met Azure Monitor Log Analytics](./log-analytics-tutorial.md) voor een zelfstudie over het gebruik van Log Analytics in de Azure Portal.<br>
Raadpleeg [Overzicht van logboekquery's in Azure Monitor](../logs/log-query-overview.md) voor meer informatie over logboekquery's in Azure Monitor.

Volg hieronder een videoversie van deze zelfstudie:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Een nieuwe query schrijven

Query's kunnen beginnen met een tabelnaam of de opdracht *search*. U moet beginnen met een tabelnaam omdat deze een duidelijk bereik voor de query definieert en zowel de queryprestaties als relevantie van de resultaten verbetert.

> [!NOTE]
> De Kusto-querytaal die door Azure Monitor wordt gebruikt, is hoofdlettergevoelig. Trefwoorden worden meestal in kleine letters geschreven. Als u namen van tabellen of kolommen in een query gebruikt, moet u ervoor zorgen dat u de juiste letters gebruikt, zoals wordt weergegeven in het deelvenster met het schema.

### <a name="table-based-queries"></a>Query's op basis van een tabel

Azure Monitor organiseert logboekgegevens in tabellen, die elk bestaan uit meerdere kolommen. Alle tabellen en kolommen worden weergegeven in het deelvenster met het schema in Log Analytics in de analyseportal. Identificeer een tabel waarin u bent geïnteresseerd en bekijk vervolgens een stukje van de gegevens:

```Kusto
SecurityEvent
| take 10
```

De query die hierboven wordt weergegeven, retourneert 10 resultaten van de tabel *SecurityEvent* zonder specifieke volgorde. Dit is een zeer veelvoorkomende manier om een tabel in een oogopslag te bekijken en inzicht te krijgen in de structuur en inhoud ervan. Laten we eens kijken hoe deze is opgebouwd:

* De query begint met de tabelnaam *SecurityEvent* - dit onderdeel definieert het bereik van de query.
* Het sluisteken (|) scheidt opdrachten, dus de uitvoer van de eerste opdracht is de invoer van de volgende opdracht. U kunt elk gewenst aantal sluistekens toevoegen.
* Na het sluisteken volgt de **take**-opdracht, waarmee een specifiek aantal willekeurige records uit de tabel wordt geretourneerd.

We kunnen de query ook uitvoeren zonder `| take 10` toe te voegen. Dat is gewoon mogelijk, maar levert maximaal 10.000 resultaten op.

### <a name="search-queries"></a>Zoekquery 's

Zoekquery's zijn minder gestructureerd en zijn over het algemeen geschikt voor het zoeken van records die een specifieke waarde bevatten in een van de kolommen:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Met deze query zoekt u in de tabel *SecurityEvent* naar records die de woordgroep 'Cryptographic' bevatten. Van deze records worden 10 records geretourneerd en weergegeven. Als we het `in (SecurityEvent)`-gedeelte weglaten en alleen `search "Cryptographic"` uitvoeren, zoekt de zoekopdracht in *alle* tabellen. Dat duurt langer en is minder efficiënt.

> [!WARNING]
> Zoekquery's zijn meestal trager dan query's op basis van een tabel omdat ze meer gegevens moeten verwerken. 

## <a name="sort-and-top"></a>Sorteren en bovenaan
Hoewel **take** handig is om een paar records te verkrijgen, worden de resultaten niet in een bepaalde volgorde geselecteerd en weergegeven. Als u een geordende weergave wilt krijgen, kunt u **sorteren** op de gewenste kolom:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Dat kan echter ook te veel resultaten opleveren en een tijdje duren. De bovenstaande query sorteert *de hele* SecurityEvent-tabel op de kolom TimeGenerated. De analyseportal beperkt vervolgens de weergave tot 10.000 records. Deze benadering is natuurlijk niet optimaal.

De beste manier om alleen de 10 meest recente records op te halen, is door **top** te gebruiken, waarmee de gehele tabel aan de serverzijde wordt gesorteerd en de bovenste records worden geretourneerd:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

De standaard sorteervolgorde is aflopend. Daarom laten we het argument **desc** meestal gewoon weg. De uitvoer ziet er als volgt uit:

![Bovenste 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: filteren volgens een voorwaarde
Zoals hun naam al aangeeft, filteren filters de gegevens op een specifieke voorwaarde. Dit is de meest voorkomende manier om de queryresultaten te beperken tot relevante informatie.

Gebruik de **where**-operator, gevolgd door een of meerdere voorwaarden om een filter toe te voegen aan een query. De volgende query retourneert bijvoorbeeld alleen *SecurityEvent*-records waarbij _Niveau_ gelijk is aan _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Wanneer u filtervoorwaarden schrijft, kunt u de volgende expressies gebruiken:

| Expressie | Beschrijving | Voorbeeld |
|:---|:---|:---|
| == | Gelijkheid controleren<br>(hoofdlettergevoelig) | `Level == 8` |
| =~ | Gelijkheid controleren<br>(niet hoofdlettergevoelig) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Ongelijkheid controleren<br>(beide expressies zijn identiek) | `Level != 4` |
| *and*, *or* | Vereist tussen voorwaarden| `Level == 16 or CommandLine != ""` |

Als u wilt filteren op meerdere voorwaarden, kunt u gebruikmaken van **and**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

of meerdere **where**-elementen tussen sluistekens achter elkaar plaatsen:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Waarden kunnen verschillende typen hebben, dus u moet deze mogelijk converteren om een vergelijking op het juiste type uit te voeren. Zo heeft de kolom *Niveau* in SecurityEvent het type Tekenreeks, dus moet u deze converteren naar een numeriek type, zoals *int* of *long* voordat u numerieke operators kunt gebruiken: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Een periode opgeven

### <a name="time-picker"></a>Tijdkiezer

De tijdkiezer bevindt zich naast de knop Uitvoeren en geeft aan dat er alleen records uit de afgelopen 24 uur worden opgevraagd. Dit is de standaardperiode die op alle query's wordt toegepast. Selecteer _Afgelopen uur_ en voer de query opnieuw uit om alleen records uit het afgelopen uur te verkrijgen.

![Tijdkiezer](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tijdfilter in query

U kunt ook uw eigen tijdsbereik definiëren door een tijdfilter toe te voegen aan de query. Het is raadzaam om het tijdfilter onmiddellijk na de naam van de tabel te plaatsen: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

In het bovenstaande filter betekent `ago(30m)` '30 minuten geleden'. Deze query retourneert dus alleen records van de laatste 30 minuten. Andere tijdseenheden zijn dagen (2d), minuten (25m) en seconden (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projecteren en uitbreiden: kolommen selecteren en berekenen

Gebruik **project** om specifieke kolommen te selecteren die in de resultaten moeten worden meegenomen:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

In het vorige voorbeeld wordt deze uitvoer gegenereerd:

![Resultaten van queryproject](media/get-started-queries/project.png)

U kunt **project** ook gebruiken om de namen van kolommen te wijzigen en nieuwe te definiëren. In het volgende voorbeeld wordt project gebruikt om het volgende te doen:

* Alleen de oorspronkelijke kolommen *Computer* en *TimeGenerated* selecteren.
* De kolom *Activity* weergeven als *EventDetails*.
* Een nieuwe kolom maken met de naam *EventCode*. De functie **subtekenreeks()** wordt gebruikt om alleen de eerste vier tekens uit het veld Activiteit op te halen.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

Met **extend** worden alle oorspronkelijke kolommen in de resultatenset bewaard en worden aanvullende waarden gedefinieerd. De volgende query maakt gebruik van **extend** om de kolom *EventCode* toe te voegen. Houd er rekening mee dat deze kolom mogelijk niet wordt weergegeven aan het einde van de tabelresultaten. In dat geval moet u de details van een record uitvouwen om deze weer te geven.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Samenvatten: groepen rijen samenvoegen
Gebruik **summarize** om groepen records te identificeren op basis van een of meer kolommen en daar aggregaties op toe te passen. Het meest voorkomende gebruik van **summarize** is *count*, waarmee het aantal resultaten in elke groep wordt geretourneerd.

Met de volgende query worden alle *Perf*-records van het afgelopen uur gecontroleerd en gegroepeerd op *ObjectName*. Daarna worden de records in elke groep geteld: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Soms is het zinvol om groepen te definiëren op basis van meerdere dimensies. Elke unieke combinatie van deze waarden definieert een afzonderlijke groep:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Een ander algemeen gebruik is het uitvoeren van wiskundige of statistische berekeningen op elke groep. In het volgende voorbeeld wordt bijvoorbeeld de gemiddelde *CounterValue* voor elke computer berekend:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Helaas hebben de resultaten van deze query geen betekenis omdat we verschillende prestatiemeters hebben gecombineerd. Om ze meer betekenis te geven, moeten we het gemiddelde berekenen voor elke combinatie van *CounterName* en *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Samenvatten op een tijdkolom
Het groeperen van resultaten kan ook worden gebaseerd op een tijdkolom of een andere doorlopende waarde. Als u eenvoudigweg samenvat op `by TimeGenerated` worden er groepen gemaakt voor elke milliseconde gedurende het tijdsbereik, omdat dit unieke waarden zijn. 

Als u groepen op basis van continue waarden wilt maken kunt het bereik het beste opsplitsen in beheerbare eenheden door **bin** te gebruiken. Met de volgende query worden *Perf*-records geanalyseerd die het beschikbare geheugen (*Available MBytes*) op een specifieke computer meten. De query berekent de gemiddelde waarde van elke periode van 1 uur in de afgelopen 7 dagen:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Als u de uitvoer helderder wilt maken, selecteert u dat deze moet worden weergegeven als een tijdsdiagram met daarin het beschikbare geheugen in de loop van de tijd:

![Geheugen door de tijd heen opvragen](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van tekenreeksgegevens in een logboekquery vindt u in [Werken met tekenreeksen in Azure Monitor-logboekquery's](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Meer informatie over het samenvoegen van gegevens in een logboekquery vindt u in [Geavanceerde aggregaties in Azure Monitor-logboekquery's](/azure/data-explorer/write-queries#advanced-aggregations).
- Leer hoe u gegevens uit meerdere tabellen kunt samenvoegen met [Joins in Azure Monitor-logboekquery's](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Krijg documentatie over de volledige Kusto-querytaal in de [naslaginformatie over de KQL-taal](/azure/kusto/query/).