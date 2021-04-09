---
title: Metrische SQL-query gegevens voor Azure Cosmos DB SQL-API
description: Meer informatie over het instrumenteren en opsporen van fouten in de SQL-query prestaties van Azure Cosmos DB aanvragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102488083"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Query prestaties afstemmen met Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB biedt een [SQL-API voor het opvragen van gegevens](./sql-query-getting-started.md), zonder dat er schema-of secundaire indexen vereist zijn. In dit artikel vindt u de volgende informatie voor ontwikkel aars:

* Details op hoog niveau over de werking van SQL-query uitvoering van Azure Cosmos DB
* Details over query aanvraag-en reactie headers en opties voor client-SDK
* Tips en aanbevolen procedures voor de prestaties van query's
* Voor beelden van hoe u SQL-uitvoerings statistieken kunt gebruiken om de prestaties van query's op te sporen

## <a name="about-sql-query-execution"></a>SQL-query uitvoering

In Azure Cosmos DB slaat u gegevens op in containers, die kunnen worden uitgebreid naar elke [opslag grootte of door Voer van aanvraag](partitioning-overview.md). Azure Cosmos DB naadloze schaal baarheid van gegevens over fysieke partities onder de kaften om de gegevens groei te verwerken of de ingerichte door voer te verg Roten. U kunt SQL-query's verzenden naar een container met behulp van de REST API of een van de ondersteunde [SQL-sdk's](sql-api-sdk-dotnet.md).

Een kort overzicht van partitionering: u definieert een partitie sleutel zoals ' City ', die bepaalt hoe de gegevens over fysieke partities worden verdeeld. Gegevens die horen bij een enkele partitie sleutel (bijvoorbeeld ' City ' = = ' Seattle '), worden opgeslagen in een fysieke partitie, maar doorgaans heeft één fysieke partitie meerdere partitie sleutels. Wanneer een partitie de opslag grootte bereikt, splitst de service de partitie naadloos op in twee nieuwe partities en deelt de partitie sleutel gelijkmatig over deze partities. Omdat partities tijdelijk zijn, gebruiken de Api's een abstractie van een ' partitie sleutel bereik ', waarmee de bereiken van partitie sleutel-hashes worden aangeduid. 

Wanneer u een query naar Azure Cosmos DB geeft, voert de SDK de volgende logische stappen uit:

* Parseer de SQL-query om het uitvoerings plan voor query's te bepalen. 
* Als de query een filter op basis van de partitie sleutel bevat, `SELECT * FROM c WHERE c.city = "Seattle"` wordt deze doorgestuurd naar één partitie. Als de query geen filter op partitie sleutel heeft, wordt deze uitgevoerd in alle partities en worden de resultaten samengevoegd.
* De query wordt uitgevoerd binnen elke partitie in een serie of parallel, op basis van de client configuratie. Binnen elke partitie kan de query een of meer retouren maken, afhankelijk van de complexiteit van de query, de geconfigureerde pagina grootte en de ingerichte door Voer van de verzameling. Elke uitvoering retourneert het aantal verbruikte [aanvraag eenheden](request-units.md) tijdens het uitvoeren van query's en optioneel statistieken voor het uitvoeren van query's. 
* De SDK voert een samen vatting uit van de query resultaten voor alle partities. Als de query bijvoorbeeld een ORDER op basis van meerdere partities omvat, worden de resultaten van de afzonderlijke partities samen voegen, gesorteerd om de resultaten in de wereld wijd gesorteerde volg orde te retour neren. Als de query een aggregatie is zoals `COUNT` , worden de aantallen van afzonderlijke partities opgeteld om het totale aantal te produceren.

De Sdk's bieden verschillende opties voor het uitvoeren van query's. In .NET zijn deze opties bijvoorbeeld beschikbaar in de- `FeedOptions` klasse. In de volgende tabel worden deze opties beschreven en hoe deze van invloed zijn op de uitvoerings tijd van query's. 

| Optie | Beschrijving |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Moet worden ingesteld op True voor elke query die moet worden uitgevoerd in meer dan één partitie. Dit is een expliciete vlag waarmee u tijdens de ontwikkelings periode de prestaties kunt afnemen. |
| `EnableScanInQuery` | Moet worden ingesteld op True als u zich hebt afgemeld bij het indexeren, maar u wilt de query toch uitvoeren via een scan. Alleen van toepassing als indexering voor het aangevraagde filter pad is uitgeschakeld. | 
| `MaxItemCount` | Het maximum aantal items dat per retour zending naar de server wordt geretourneerd. Door in te stellen op-1 kunt u de server het aantal items laten beheren. Of u kunt deze waarde verlagen om slechts een klein aantal items per retour retour op te halen. 
| `MaxBufferedItemCount` | Dit is een client-side optie en wordt gebruikt om het geheugen gebruik te beperken bij het uitvoeren van een cross-partitie volgorde door. Een hogere waarde helpt de latentie van het sorteren van meerdere partities te verminderen. |
| `MaxDegreeOfParallelism` | Hiermee wordt het aantal gelijktijdige bewerkingen aan de client zijde opgehaald of ingesteld tijdens het uitvoeren van parallelle query's in de Azure Cosmos-database service. Een positieve eigenschaps waarde beperkt het aantal gelijktijdige bewerkingen tot de ingestelde waarde. Als deze is ingesteld op minder dan 0, bepaalt het systeem automatisch het aantal gelijktijdige bewerkingen dat moet worden uitgevoerd. |
| `PopulateQueryMetrics` | Biedt gedetailleerde logboek registratie van de tijd die is besteed aan verschillende fasen van uitvoering van query's, zoals compilatie tijd, index looptijd en document laad tijd. U kunt de uitvoer van query statistieken met Azure-ondersteuning delen om problemen met de query prestaties op te sporen. |
| `RequestContinuation` | U kunt de uitvoering van query's hervatten door door te geven in het dekkende vervolg token dat door een wille keurige query wordt geretourneerd. Het vervolg token inkapselt alle status die is vereist voor het uitvoeren van query's. |
| `ResponseContinuationTokenLimitInKb` | U kunt de maximale grootte van het voortzettings token dat door de server wordt geretourneerd, beperken. Mogelijk moet u dit instellen als uw toepassingshost limieten heeft voor de grootte van de reactie header. Als u dit instelt, kan de totale duur en het RUs-verbruik voor de query worden verhoogd.  |

Laten we bijvoorbeeld een voorbeeld query uitvoeren op de partitie sleutel die is aangevraagd voor een verzameling met `/city` als de partitie sleutel en ingericht met 100.000 ru/s van door voer. U vraagt deze query `CreateDocumentQuery<T>` op in .net, zoals in het volgende:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Het SDK-fragment dat hierboven wordt weer gegeven, komt overeen met de volgende REST API aanvraag:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Elke pagina voor het uitvoeren van query's komt overeen met een REST API `POST` met de `Accept: application/query+json` koptekst en de SQL-query in de hoofd tekst. Elke query maakt een of meer Retouren naar de server met het `x-ms-continuation` token dat is geechot tussen de client en de server om de uitvoering te hervatten. De configuratie opties in FeedOptions worden door gegeven aan de server in de vorm van aanvraag headers. Bijvoorbeeld, `MaxItemCount` komt overeen met `x-ms-max-item-count` . 

De aanvraag retourneert het volgende (afgekapt voor de Lees baarheid):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

De belangrijkste reactie headers die worden geretourneerd door de query zijn onder andere:

| Optie | Beschrijving |
| ------ | ----------- |
| `x-ms-item-count` | Het aantal items dat in het antwoord is geretourneerd. Dit is afhankelijk van de opgegeven `x-ms-max-item-count` waarde, het aantal items dat kan worden aangepast binnen de maximale grootte van de nettolading, de ingerichte door Voer en de uitvoerings tijd van de query. |  
| `x-ms-continuation:` | Het vervolg token om de uitvoering van de query te hervatten als er aanvullende resultaten beschikbaar zijn. | 
| `x-ms-documentdb-query-metrics` | De query statistieken voor de uitvoering. Dit is een gescheiden teken reeks met statistieken over de tijd die wordt besteed aan de verschillende fasen van het uitvoeren van query's. Geretourneerd als `x-ms-documentdb-populatequerymetrics` is ingesteld op `True` . | 
| `x-ms-request-charge` | Het aantal [aanvraag eenheden](request-units.md) dat door de query wordt verbruikt. | 

Zie [Query's uitvoeren op resources met behulp van de rest API](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)voor meer informatie over de rest API aanvraag headers en opties.

## <a name="best-practices-for-query-performance"></a>Aanbevolen procedures voor de prestaties van query's
Hier volgen de meest voorkomende factoren die van invloed zijn op de prestaties van Azure Cosmos DB query's. In elk van deze onderwerpen wordt in dit artikel dieper opgetreden.

| Factor | Tip | 
| ------ | -----| 
| Ingerichte doorvoer | Meet RU per query en zorg ervoor dat u beschikt over de vereiste ingerichte door Voer voor uw query's. | 
| Partitioneren en partitie sleutels | Voor keur query's met de partitie sleutel waarde in de component filter voor lage latentie. |
| SDK-en query opties | Volg de aanbevolen procedures voor de SDK, zoals directe connectiviteit en het afstemmen van de uitvoerings opties voor query's aan de client zijde. |
| Indexeringsbeleid | Zorg ervoor dat u beschikt over de vereiste indexerings paden/-beleid voor de query. |
| Metrische gegevens voor query uitvoering | Analyseer de metrische gegevens voor query uitvoering om mogelijke herschrijf bewerkingen van query's en gegevensshapes te identificeren.  |

### <a name="provisioned-throughput"></a>Ingerichte doorvoer
In Cosmos DB kunt u gegevens containers maken, elk met gereserveerde door Voer, uitgedrukt in aanvraag eenheden (RU) per seconde. Een lees bewerking van een document van 1 KB is 1 RU en alle bewerkingen (inclusief query's) worden genormaliseerd naar een vast aantal van RUs op basis van de complexiteit. Als u bijvoorbeeld 1000 RU/s hebt ingericht voor uw container en u een query hebt zoals `SELECT * FROM c WHERE c.city = 'Seattle'` die 5 RUs verbruikt, kunt u (1000 ru/s)/(5 ru/query) = 200 query's per seconde uitvoeren. 

Als u meer dan 200 query's per seconde verzendt, wordt de service gestart met de frequentie voor het beperken van binnenkomende aanvragen van meer dan 200/s. De Sdk's behandelen deze aanvraag automatisch door een uitstel/nieuwe poging uit te voeren. Daarom zou u een hogere latentie kunnen opmerken voor deze query's. Door de ingerichte door voer naar de vereiste waarde te verhogen, worden uw latentie en door Voer van query's verbeterd. 

Zie [aanvraag eenheden](request-units.md)voor meer informatie over aanvraag eenheden.

### <a name="partitioning-and-partition-keys"></a>Partitioneren en partitie sleutels
Met Azure Cosmos DB worden query's doorgaans in de volgende volg orde uitgevoerd, van snelst/meest efficiënte naar langzamer/minder efficiënte. 

* GET op één partitie sleutel en item sleutel
* Query met een filter component op één partitie sleutel
* Query zonder een filter component voor gelijkheid of bereik voor een eigenschap
* Query zonder filters

Query's waarbij alle partities moeten worden geraadpleegd, hebben een hogere latentie nodig en kunnen meer dan RUs gebruiken. Omdat elke partitie automatische indexering heeft voor alle eigenschappen, kan de query in dit geval efficiënt vanuit de index worden bediend. U kunt query's met behulp van de opties van de parallelle schijf sneller beslaan.

Zie [partitioneren in azure Cosmos DB](partitioning-overview.md)voor meer informatie over partitioneren en partitie sleutels.

### <a name="sdk-and-query-options"></a>SDK-en query opties
Bekijk [prestatie tips](performance-tips.md) en [prestatie tests](performance-testing.md) voor het verkrijgen van de beste prestaties aan de client zijde van Azure Cosmos db. Dit geldt ook voor het gebruik van de nieuwste Sdk's, het configureren van platformspecifieke configuraties zoals het standaard aantal verbindingen, de frequentie van de garbagecollection en het gebruik van de opties voor de licht gewicht connectiviteit zoals direct/TCP. 


#### <a name="max-item-count"></a>Maximum aantal items
Voor query's kan de waarde van `MaxItemCount` een grote invloed hebben op de end-to-end query tijd. Elke retour zending naar de server retourneert niet meer dan het aantal items in `MaxItemCount` (standaard van 100 items). Als u dit instelt op een hogere waarde (-1 is maximum en aanbevolen), wordt uw query duur in het algemeen verbeterd door het aantal retouren tussen de server en de client te beperken, met name voor query's met grote resultaten sets.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximale graad van parallelle uitvoering
Voor query's moet u de `MaxDegreeOfParallelism` voor het identificeren van de beste configuraties voor uw toepassing afstemmen, met name als u query's op meerdere partities uitvoert (zonder een filter voor de partitie sleutel waarde). `MaxDegreeOfParallelism`  Hiermee bepaalt u het maximum aantal parallelle taken, d.w.z. het maximum aantal partities dat parallel moet worden bezocht. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

We gaan ervan uit dat
* D = standaard maximum aantal parallelle taken (= totale aantal processors in de client computer)
* P = door gebruiker opgegeven maximum aantal parallelle taken
* N = het aantal partities dat moet worden bezocht voor het beantwoorden van een query

Hieronder vindt u gevolgen voor de manier waarop de parallelle query's zouden werken voor verschillende waarden van P.
* (P = = 0) => seriële modus
* (P = = 1) => maximum van een taak
* (P > 1) => min (P, N) parallelle taken 
* (P < 1) => min (N, D) parallelle taken

Raadpleeg [SQL-sdk's](sql-api-sdk-dotnet.md) voor SDK-release opmerkingen en informatie over geïmplementeerde klassen en methoden

### <a name="network-latency"></a>Netwerklatentie
Zie [Azure Cosmos DB globale distributie](tutorial-global-distribution-sql-api.md) voor informatie over het instellen van globale distributie en het maken van verbinding met de dichtstbijzijnde regio. Netwerk latentie heeft een grote invloed op de query prestaties wanneer u meerdere retouren wilt maken of een grote resultatenset wilt ophalen uit de query. 

In het gedeelte over metrische gegevens voor het uitvoeren van query's wordt uitgelegd hoe u de server uitvoerings tijd van query's () ophaalt `totalExecutionTimeInMs` , zodat u onderscheid kunt maken tussen de tijd die wordt besteed aan de uitvoering van query's en de tijd die wordt besteed aan netwerk doorvoer.

### <a name="indexing-policy"></a>Indexeringsbeleid
Zie het configureren van het [indexerings beleid](index-policy.md) voor het indexeren van paden, typen en modi, en hoe deze van invloed zijn op de uitvoering van query's. Het indexerings beleid maakt standaard gebruik van hash-indexering voor teken reeksen, die effectief zijn voor gelijkheids query's, maar niet voor bereik query's/order by-query's. Als u bereik query's voor teken reeksen nodig hebt, kunt u het beste het index type van het bereik opgeven voor alle teken reeksen. 

Azure Cosmos DB wordt standaard automatisch geïndexeerd toegepast op alle gegevens. Overweeg het uitsluiten van paden voor scenario's met hoge prestaties, omdat hierdoor de RU-kosten voor elke invoeg bewerking worden verminderd. 

## <a name="query-execution-metrics"></a>Metrische gegevens voor query uitvoering
U kunt gedetailleerde gegevens over het uitvoeren van query's verkrijgen door door te geven in de optionele `x-ms-documentdb-populatequerymetrics` header ( `FeedOptions.PopulateQueryMetrics` in de .NET SDK). De geretourneerde waarde in `x-ms-documentdb-query-metrics` heeft de volgende sleutel-waardeparen die bedoeld zijn voor geavanceerde probleem oplossing bij het uitvoeren van query's. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metrisch | Eenheid | Description | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milliseconden | Uitvoerings tijd van query | 
| `queryCompileTimeInMs` | milliseconden | Compilatie tijd van de query  | 
| `queryLogicalPlanBuildTimeInMs` | milliseconden | Tijd voor het maken van een logisch query plan | 
| `queryPhysicalPlanBuildTimeInMs` | milliseconden | Tijd voor het maken van een fysiek query plan | 
| `queryOptimizationTimeInMs` | milliseconden | Tijd besteed aan het optimaliseren van query's | 
| `VMExecutionTimeInMs` | milliseconden | Bestede tijd in query runtime | 
| `indexLookupTimeInMs` | milliseconden | Bestede tijd in fysieke index laag | 
| `documentLoadTimeInMs` | milliseconden | Tijd die is besteed aan het laden van documenten  | 
| `systemFunctionExecuteTimeInMs` | milliseconden | Totale gebruikte tijd voor het uitvoeren van systeem (ingebouwde) functies in milliseconden  | 
| `userFunctionExecuteTimeInMs` | milliseconden | Totale tijd besteed aan het uitvoeren van door de gebruiker gedefinieerde functies in milliseconden | 
| `retrievedDocumentCount` | count | Totaal aantal opgehaalde documenten  | 
| `retrievedDocumentSize` | bytes | Totale grootte van opgehaalde documenten in bytes  | 
| `outputDocumentCount` | count | Aantal uitvoer documenten | 
| `writeOutputTimeInMs` | milliseconden | Uitvoerings tijd van de query in milliseconden | 
| `indexUtilizationRatio` | ratio (<= 1) | Verhouding van aantal documenten dat overeenkomt met het filter tot het aantal geladen documenten  | 

De client-Sdk's kunnen intern meerdere query bewerkingen uitvoeren om de query binnen elke partitie te leveren. De client maakt meer dan één aanroep per partitie als de totale resultaten groter `x-ms-max-item-count` worden, als de query de ingerichte door Voer voor de partitie overschrijdt, of als de query lading de maximum grootte per pagina bereikt of als de query de time-outlimiet van het toegewezen systeem bereikt. Elke gedeeltelijke query-uitvoering retourneert een `x-ms-documentdb-query-metrics` voor die pagina. 

Hier volgen enkele voor beelden van query's en het interpreteren van enkele metrische gegevens die worden geretourneerd door de uitvoering van query's: 

| Query | Voorbeeld metriek | Description | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Het aantal opgehaalde documenten is 100 + 1 om overeen te komen met de component TOP. De query tijd wordt meestal gebruikt in `WriteOutputTime` en `DocumentLoadTime` omdat het een scan is. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount is nu hoger (500 + 1 zodat dit overeenkomt met de TOP-component). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Ongeveer 0,9 MS wordt in IndexLookupTime uitgegeven voor een sleutel zoekactie, omdat het een opzoek index is `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Iets meer tijd (1,7 MS) besteed aan IndexLookupTime over een bereik scan, omdat het een zoek opdracht op index is `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | De tijd `DocumentLoadTime` die wordt besteed aan eerdere query's, maar lager `WriteOutputTime` omdat er slechts één eigenschap wordt geprojecteerd. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Ongeveer 213 MS wordt besteed aan `UserDefinedFunctionExecutionTime` het uitvoeren van de UDF op elke waarde van `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Ongeveer 0,6 MS wordt uitgegeven in `IndexLookupTime` op `/Name/?` . De meeste uitvoer tijd van de query (~ 7 MS) in `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | De query wordt uitgevoerd als een scan omdat deze wordt gebruikt `LOWER` en er 500 van 2491 opgehaalde documenten worden geretourneerd. |


## <a name="next-steps"></a>Volgende stappen
* Zie [SQL query](sql-query-getting-started.md)voor meer informatie over de ondersteunde Opera tors en tref woorden voor SQL-query's. 
* Zie [aanvraag eenheden](request-units.md)voor meer informatie over aanvraag eenheden.
* Zie [indexerings beleid](index-policy.md) voor meer informatie over het indexerings beleid