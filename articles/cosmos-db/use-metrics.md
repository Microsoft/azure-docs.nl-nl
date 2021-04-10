---
title: Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB
description: Gebruik metrische gegevens in Azure Cosmos DB om veelvoorkomende problemen op te sporen en de data base te bewaken.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0711d764514e45d3c28e26cf99b45dc711ef201c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868236"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB biedt metrische gegevens voor doorvoer, opslag, consistentie, beschikbaarheid en latentie. De Azure-portal biedt een geaggregeerde weergave van deze metrische gegevens. U kunt ook metrische gegevens uit Azure Cosmos DB bekijken vanuit de Azure Monitor API. De dimensie waarden voor de metrische gegevens, zoals container naam, zijn niet hoofdletter gevoelig. U moet dus hoofdletter gevoelige vergelijking gebruiken wanneer u teken reeksen vergelijkt met deze dimensie waarden. Zie het artikel [metrische gegevens ophalen uit Azure monitor](./monitor-cosmos-db.md) voor meer informatie over het weer geven van metrische gegevens in azure monitor.

In dit artikel worden algemene gebruikscases beschreven en wordt uitgelegd hoe metrische gegevens in Azure Cosmos DB kunnen worden gebruikt om deze problemen te analyseren en op te lossen. Metrische gegevens worden elke vijf minuten verzameld en gedurende zeven dagen bewaard.

## <a name="view-metrics-from-azure-portal"></a>Metrische gegevens van Azure Portal weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/)

1. Open het deel venster **metrische gegevens** . Standaard toont het deel venster metrieken de metrische gegevens over de opslag, index en aanvraag eenheden voor alle data bases in uw Azure Cosmos-account. U kunt deze metrische gegevens per data base, container of regio filteren. U kunt de metrische gegevens ook filteren op een specifieke tijd granulariteit. Meer informatie over de metrische gegevens voor door Voer, opslag, Beschik baarheid, latentie en consistentie is beschikbaar op afzonderlijke tabbladen. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Meet gegevens voor prestaties Cosmos DB in Azure Portal":::

De volgende metrische gegevens zijn beschikbaar in het deel venster **metrische gegevens** : 

* **Metrische gegevens over door Voer** -deze metriek toont het aantal verbruikte of mislukte aanvragen (429 respons code), omdat de door Voer of opslag capaciteit die is ingericht voor de container, is overschreden.

* **Metrische opslag** gegevens: deze meet waarde toont de grootte van het data-en index gebruik.

* **Metrische beschikbaarheids gegevens** : met deze metriek wordt het percentage voltooide aanvragen voor het totaal aantal aanvragen per uur weer gegeven. Het voltooiings percentage wordt gedefinieerd door de Azure Cosmos DB Sla's.

* **Metrische gegevens over latentie** : in deze metrische gegevens wordt de lees-en schrijf latentie weer gegeven die wordt waargenomen door Azure Cosmos db in de regio waar uw account wordt uitgevoerd. U kunt latentie in verschillende regio's voor een geografisch gerepliceerd account visualiseren. Deze metrische waarde vertegenwoordigt niet de end-to-end-latentie van de aanvraag.

* **Consistentie metrieken** : deze metrische gegevens geven aan hoe uiteindelijk de consistentie is voor het consistentie model dat u kiest. Voor accounts met meerdere regio's toont deze metrische gegevens ook de replicatie latentie tussen de regio's die u hebt geselecteerd.

* **Systeem metrieken** : in deze metrische gegevens wordt weer gegeven hoeveel meta data-aanvragen worden verwerkt door de primaire partitie. Het helpt ook de vertraagde aanvragen te identificeren.

In de volgende secties worden veelvoorkomende scenario's beschreven waarin u Azure Cosmos DB metrische gegevens kunt gebruiken. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Begrijpen hoeveel aanvragen slagen of fouten veroorzaken

Als u aan de slag wilt gaan, gaat u naar de [Azure Portal](https://portal.azure.com) en navigeert u naar de Blade **metrische gegevens** . Zoek in de Blade naar de * * aantal aanvragen met een capaciteit van meer dan één minuut diagram. Dit diagram toont het totale aantal minuten per minuut dat is gesegmenteerd door de status code. Zie [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)voor meer informatie over HTTP-status codes.

De meest voorkomende fout status code is 429 (beperking of beperking). Deze fout betekent dat aanvragen voor Azure Cosmos DB meer zijn dan de ingerichte door voer. De meest voorkomende oplossing voor dit probleem is het [opschalen van het RUs](./set-throughput.md) voor de gegeven verzameling.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Aantal aanvragen per minuut":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>De doorvoer distributie voor alle partities bepalen

Een goede kardinaliteit van uw partitie sleutels is essentieel voor elke schaal bare toepassing. Ga naar de **Blade metrische gegevens** in de [Azure Portal](https://portal.azure.com)om de doorvoer distributie te bepalen van een gepartitioneerde container die is onderverdeeld in partities. Op het tabblad **door Voer** wordt de opslag specificatie weer gegeven in de tabel **Max. aantal geconsumeerde ru/seconde per fysieke partitie** . In de volgende afbeelding ziet u een voor beeld van een slechte distributie van gegevens, zoals wordt weer gegeven door de gescheefe partitie helemaal links.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Enkelvoudige partitie Zie intensief gebruik":::

Een ongelijke doorvoer distributie kan leiden tot *dynamische* partities, wat kan leiden tot getraagde aanvragen en mogelijk opnieuw partitioneert. Zie [partitioneren en schalen in azure Cosmos DB](./partitioning-overview.md)voor meer informatie over partitioneren in azure Cosmos db.

## <a name="determine-the-storage-distribution-across-partitions"></a>De opslag distributie op meerdere partities bepalen

Een goede kardinaliteit van uw partitie is essentieel voor elke schaal bare toepassing. Als u de opslag distributie wilt bepalen van een gepartitioneerde container die is onderverdeeld in partities, kop naar de Blade metrische gegevens in de [Azure Portal](https://portal.azure.com). Op het tabblad opslag wordt de opslag specificatie weer gegeven in de grafiek gegevens en index opslag die worden gebruikt door de bovenste partitie sleutels. In de volgende afbeelding ziet u een slechte distributie van gegevens opslag, zoals wordt weer gegeven door de gescheefe partitie helemaal links.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Voor beeld van slechte gegevens distributie":::

U kunt de hoofd oorzaak van de partitie sleutel scheef trekken door te klikken op de partitie in de grafiek.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="De partitie sleutel verscheeft de distributie":::

Nadat u hebt vastgesteld welke partitie sleutel het hellen van de verdeling veroorzaakt, moet u de container wellicht opnieuw partitioneren met een meer gedistribueerde partitie sleutel. Zie [partitioneren en schalen in azure Cosmos DB](./partitioning-overview.md)voor meer informatie over partitioneren in azure Cosmos db.

## <a name="compare-data-size-against-index-size"></a>Gegevens grootte vergelijken met index grootte

In Azure Cosmos DB is de totale hoeveelheid verbruikte opslag de combi natie van de gegevens grootte en de index grootte. Normaal gesp roken is de index grootte een fractie van de gegevens grootte. Zie het artikel [Index grootte](index-policy.md#index-size) voor meer informatie. Op het tabblad opslag in de Blade metrische gegevens in de [Azure Portal](https://portal.azure.com)wordt de uitsplitsing van het opslag verbruik op basis van gegevens en index gedemonstreerd.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Als u index ruimte wilt behouden, kunt u het [indexerings beleid](index-policy.md)aanpassen.

## <a name="debug-why-queries-are-running-slow"></a>Debug waarom query's langzaam worden uitgevoerd

In de SQL API-Sdk's Azure Cosmos DB levert statistieken voor query-uitvoering.

```csharp
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

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* biedt informatie over hoe lang elk onderdeel van de query duurde te worden uitgevoerd. De meest voorkomende hoofd oorzaak voor langlopende query's is scans, wat betekent dat de query geen gebruik kan maken van de indexen. Dit probleem kan worden opgelost met een betere filter voorwaarde.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u problemen kunt controleren en opsporen met behulp van de metrische gegevens in de Azure Portal. Lees de volgende artikelen voor meer informatie over het verbeteren van de prestaties van de Data Base:

* Zie het artikel [metrische gegevens ophalen uit Azure monitor](./monitor-cosmos-db.md) voor meer informatie over het weer geven van metrische gegevens in azure monitor. 
* [Prestaties en schaal testen met Azure Cosmos DB](performance-testing.md)
* [Tips voor betere prestaties van Azure Cosmos DB](performance-tips.md)