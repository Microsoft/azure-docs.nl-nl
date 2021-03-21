---
title: Tekst gegevens parseren in Logboeken van Azure Monitor | Microsoft Docs
description: Beschrijft verschillende opties voor het parseren van logboek gegevens in Azure Monitor records wanneer de gegevens worden opgenomen en wanneer deze worden opgehaald in een query, waarbij de relatieve voor delen voor elk van elkaar worden vergeleken.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 2e2babd39dc9dadf06c9a3ca3292e021e0c1deaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037180"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Tekst gegevens parseren in Azure Monitor logboeken
Sommige logboek gegevens die worden verzameld door Azure Monitor, bevatten meerdere stukjes informatie in één eigenschap. Het parseren van deze gegevens in meerdere eigenschappen maakt het gemakkelijker om in query's te gebruiken. Een veelvoorkomend voor beeld is een [aangepast logboek](../agents/data-sources-custom-logs.md) dat een volledige logboek vermelding met meerdere waarden in één eigenschap verzamelt. Door afzonderlijke eigenschappen voor de verschillende waarden te maken, kunt u zoeken en samen voegen.

In dit artikel worden verschillende opties beschreven voor het parseren van logboek gegevens in Azure Monitor wanneer de gegevens worden opgenomen en wanneer deze worden opgehaald in een query, waarbij de relatieve voor delen voor elk van elkaar worden vergeleken.


## <a name="parsing-methods"></a>Methoden parseren
U kunt gegevens parseren op opname tijd wanneer de gegevens worden verzameld of op query tijdstip bij het analyseren van de gegevens met een query. Elke strategie heeft unieke voor delen, zoals hieronder wordt beschreven.

### <a name="parse-data-at-collection-time"></a>Gegevens parseren tijdens verzamelings tijd
Wanneer u gegevens parseert tijdens de verzamelings tijd, configureert u [aangepaste velden](../logs/custom-fields.md) die nieuwe eigenschappen in de tabel maken. Query's hoeven geen enige logica voor het parseren van gegevens op te vragen en deze eigenschappen eenvoudigweg als elk ander veld in de tabel te gebruiken.

Voor delen van deze methode zijn onder andere het volgende:

- Het is eenvoudiger om de verzamelde gegevens op te vragen omdat u geen parser-opdrachten in de query hoeft op te halen.
- Betere query prestaties omdat de query niet hoeft te worden geparseerd.
 
De nadelen van deze methode zijn onder andere:

- Moeten vooraf worden gedefinieerd. Kan geen gegevens bevatten die al zijn verzameld.
- Als u de parsering-logica wijzigt, is deze alleen van toepassing op nieuwe gegevens.
- Minder opties voor het parseren dan beschikbaar in query's.
- Verhoogt de latentie tijd voor het verzamelen van gegevens.
- Het kan lastig zijn fouten te verwerken.


### <a name="parse-data-at-query-time"></a>Gegevens parseren op query tijdstip
Wanneer u gegevens op query tijdstip parseert, neemt u in uw query logica op om gegevens te parseren in meerdere velden. De daad werkelijke tabel zelf wordt niet gewijzigd.

Voor delen van deze methode zijn onder andere het volgende:

- Is van toepassing op alle gegevens, inclusief gegevens die al zijn verzameld.
- Wijzigingen in logica kunnen direct worden toegepast op alle gegevens.
- Flexibele opties voor parseren, inclusief vooraf gedefinieerde logica voor bepaalde gegevens structuren.
 
De nadelen van deze methode zijn onder andere:

- Vereist complexere query's. Dit kan worden verholpen door gebruik [te maken van functies voor het simuleren van een tabel](#use-function-to-simulate-a-table).
- Moet het parseren van logica in meerdere query's repliceren. Kan enige logica delen via functies.
- Kan overhead maken bij het uitvoeren van complexe logica tegen zeer grote record sets (miljarden records).

## <a name="parse-data-as-its-collected"></a>Gegevens parseren wanneer deze worden verzameld
Zie [aangepaste velden maken in azure monitor](../logs/custom-fields.md) voor meer informatie over het parseren van gegevens die worden verzameld. Hiermee maakt u aangepaste eigenschappen in de tabel die kunnen worden gebruikt door query's, net als elke andere eigenschap.

## <a name="parse-data-in-query-using-patterns"></a>Gegevens in query's parseren met behulp van patronen
Wanneer de gegevens die u wilt parseren kunnen worden geïdentificeerd door een patroon dat voor alle records wordt herhaald, kunt u verschillende Opera tors in de [Kusto-query taal](/azure/kusto/query/) gebruiken om de specifieke gegevens in een of meer nieuwe eigenschappen te extra heren.

### <a name="simple-text-patterns"></a>Eenvoudige tekst patronen

Gebruik de operator [parse](/azure/kusto/query/parseoperator) in uw query om een of meer aangepaste eigenschappen te maken die kunnen worden geëxtraheerd uit een teken reeks expressie. U geeft het patroon op dat moet worden geïdentificeerd en de namen van de eigenschappen die moeten worden gemaakt. Dit is met name handig voor gegevens met sleutel waarde-teken reeksen met een vergelijkbaar formulier als _Key = waarde_.

Overweeg een aangepast logboek met gegevens in de volgende indeling.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Met de volgende query worden deze gegevens in afzonderlijke eigenschappen geparseerd. De regel met _project_ wordt toegevoegd om alleen de berekende eigenschappen te retour neren en niet _RawData_. Dit is de enige eigenschap die de volledige vermelding in het aangepaste logboek houdt.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Hieronder volgt een ander voor beeld dat de gebruikers naam van een UPN in de tabel _AzureActivity_ uitsplitst.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguliere expressies
Als uw gegevens kunnen worden geïdentificeerd met een reguliere expressie, kunt u [functies gebruiken die gebruikmaken van reguliere expressies](/azure/kusto/query/re2) om afzonderlijke waarden te extra heren. In het volgende voor beeld wordt [extract](/azure/kusto/query/extractfunction) gebruikt om het _UPN_ -veld uit _AzureActivity_ -records te verbreken en vervolgens afzonderlijke gebruikers te retour neren.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Azure Monitor maakt gebruik van een RE2-versie van reguliere expressies, die vergelijkbaar is met maar niet gelijk is aan enkele andere reguliere-expressie varianten om efficiënte parsering op grote schaal in te scha kelen. Raadpleeg de syntaxis van de [expressie RE2](https://aka.ms/kql_re2syntax) voor meer informatie.


## <a name="parse-delimited-data-in-a-query"></a>Gescheiden gegevens in een query parseren
Gescheiden gegevens scheiden velden met een gemeen schappelijk teken, zoals een komma in een CSV-bestand. Gebruik de functie [Split](/azure/kusto/query/splitfunction) om gescheiden gegevens te parseren met behulp van een scheidings teken dat u opgeeft. U kunt dit met de operator [Extend](/azure/kusto/query/extendoperator) gebruiken om alle velden in de gegevens te retour neren of om afzonderlijke velden op te geven die in de uitvoer moeten worden opgenomen.

> [!NOTE]
> Omdat splitsen een dynamisch object retourneert, moeten de resultaten mogelijk expliciet worden geconverteerd naar gegevens typen zoals teken reeks die moet worden gebruikt in Opera tors en filters.

Overweeg een aangepast logboek met gegevens in de volgende CSV-indeling.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Met de volgende query worden deze gegevens geparseerd en samenvatten met twee van de berekende eigenschappen. De eerste regel splitst de eigenschap  _RawData_ in een teken reeks matrix. Elk van de volgende regels geeft een naam aan afzonderlijke eigenschappen en voegt deze toe aan de uitvoer met behulp van functies om ze te converteren naar het juiste gegevens type.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Vooraf gedefinieerde structuren parseren in een query
Als uw gegevens zijn ingedeeld in een bekende structuur, kunt u een van de functies in de [Kusto-query taal](/azure/kusto/query/) gebruiken voor het parseren van vooraf gedefinieerde structuren:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL-query](/azure/kusto/query/parseurlqueryfunction)
- [Pad naar bestand](/azure/kusto/query/parsepathfunction)
- [Gebruikers agent](/azure/kusto/query/parse-useragentfunction)
- [Versie teken reeks](/azure/kusto/query/parse-versionfunction)

Met de volgende voorbeeld query wordt het veld _Eigenschappen_ van de tabel _AzureActivity_ , die is gestructureerd in JSON, geparseerd. De resultaten worden opgeslagen in een dynamische eigenschap met de naam _parsedProp_, die de afzonderlijke benoemde waarde bevat in de JSON. Deze waarden worden gebruikt voor het filteren en samenvatten van de query resultaten.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Deze functies voor het parseren van processoren kunnen intensief worden gebruikt, zodat ze alleen worden gebruiken als uw query meerdere eigenschappen van de opgemaakte gegevens gebruikt. Als dat niet het geval is, is de verwerking van eenvoudige patroon overeenkomsten sneller.

In het volgende voor beeld ziet u de uitsplitsing van het type domein controller TGT PreAuth. Het type bestaat alleen in het event data-veld. Dit is een XML-teken reeks, maar er zijn geen andere gegevens uit dit veld nodig. In dit geval wordt [parse](/azure/kusto/query/parseoperator) gebruikt om de vereiste gegevens te verzamelen.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Functie gebruiken om een tabel te simuleren
Mogelijk hebt u meerdere query's die hetzelfde parseren van een bepaalde tabel uitvoeren. In dit geval [maakt u een functie](../logs/functions.md) die de geparseerde gegevens retourneert in plaats van de parsering van de logica in elke query te repliceren. U kunt vervolgens de functie alias gebruiken in plaats van de oorspronkelijke tabel in andere query's.

Bekijk hierboven het voor beeld met een scheidings teken voor aangepaste Logboeken. Als u de geparseerde gegevens in meerdere query's wilt gebruiken, maakt u een functie met behulp van de volgende query en slaat u deze op met de alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

U kunt nu de alias _MyCustomCSVLog_ gebruiken in plaats van de werkelijke tabel naam in query's zoals in het volgende.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](./log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.