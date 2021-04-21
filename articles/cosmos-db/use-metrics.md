---
title: Bewaken en fouten opsporen met metrische gegevens in Azure Cosmos DB
description: Gebruik metrische gegevens in Azure Cosmos DB om veelvoorkomende problemen op te sporen en de database te bewaken.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: cf92d9e1a1f92c2dc3294b71e3e620166fd90680
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818693"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Bewaken en fouten opsporen met metrische gegevens in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB biedt metrische gegevens voor doorvoer, opslag, consistentie, beschikbaarheid en latentie. De Azure-portal biedt een geaggregeerde weergave van deze metrische gegevens. U kunt ook metrische gegevens uit Azure Cosmos DB bekijken vanuit de Azure Monitor API. De dimensiewaarden voor de metrische gegevens, zoals containernaam, zijn niet-case-gevoelig. U moet dus niet-casegevoelige vergelijking gebruiken bij het maken van tekenreeksvergelijkingen op deze dimensiewaarden. Zie het artikel Metrische gegevens op Azure Monitor weergeven voor meer informatie over het weergeven [van metrische](./monitor-cosmos-db.md) gegevens van Azure Monitor.

In dit artikel worden algemene gebruikscases beschreven en wordt uitgelegd hoe metrische gegevens in Azure Cosmos DB kunnen worden gebruikt om deze problemen te analyseren en op te lossen. Metrische gegevens worden elke vijf minuten verzameld en worden zeven dagen bewaard.

## <a name="view-metrics-from-azure-portal"></a>Metrische gegevens van Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/)

1. Open het **deelvenster Metrische** gegevens. In het deelvenster met metrische gegevens worden standaard de metrische gegevens voor opslag, index en aanvraageenheden weergegeven voor alle databases in uw Azure Cosmos-account. U kunt deze metrische gegevens filteren per database, container of regio. U kunt de metrische gegevens ook filteren op een specifieke tijdgranulatie. Meer informatie over de metrische gegevens over doorvoer, opslag, beschikbaarheid, latentie en consistentie kunt u op afzonderlijke tabbladen bekijken. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB prestatiegegevens in Azure Portal":::

De volgende metrische gegevens zijn beschikbaar in het **deelvenster Metrische** gegevens:

*  Metrische gegevens over doorvoer: deze metrische gegevens geven het aantal verbruikte of mislukte aanvragen weer (429 responscode) omdat de doorvoer- of opslagcapaciteit die voor de container is ingericht, is overschreden.

* **Metrische gegevens** voor opslag: deze metrische gegevens geven de grootte van gegevens en indexgebruik weer.

* **Metrische gegevens** over beschikbaarheid: deze metrische gegevens geven het percentage geslaagde aanvragen weer voor het totale aantal aanvragen per uur. Het slagingspercentage wordt gedefinieerd door de Azure Cosmos DB SLA's.

* **Metrische latentiegegevens:** deze metrische gegevens tonen de lees- en schrijflatentie die door de Azure Cosmos DB in de regio waarin uw account wordt uitgevoerd. U kunt latentie visualiseren tussen regio's voor een geo-gerepliceerd account. Deze metrische gegevens vertegenwoordigen niet de end-to-end aanvraaglatentie.

* **Metrische gegevens voor** consistentie: deze metrische gegevens laten zien hoe mogelijk de consistentie is voor het consistentiemodel dat u kiest. Voor accounts voor meerdere regio's toont deze metrische gegevens ook de replicatielatentie tussen de regio's die u hebt geselecteerd.

* **Systeemmetrieken:** deze metrische gegevens laten zien hoeveel aanvragen voor metagegevens worden uitgevoerd door de primaire partitie. Het helpt ook bij het identificeren van de beperkt aanvragen.

In de volgende secties worden veelvoorkomende scenario's uitgelegd waarin u de metrische gegevens Azure Cosmos DB gebruiken. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Begrijpen hoeveel aanvragen slagen of fouten veroorzaken

Ga om te beginnen naar de [Azure Portal](https://portal.azure.com) en navigeer naar de blade **Metrische** gegevens. Zoek op de blade de grafiek **Aantal aanvragen heeft de capaciteit per minuut overschreden. In dit diagram wordt het totale aantal aanvragen per minuut weergegeven, gesegmenteerd op statuscode. Zie HTTP-statuscodes voor http-statuscodes [voor Azure Cosmos DB.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

De meest voorkomende foutstatuscode is 429 (snelheidsbeperking/beperking). Deze fout betekent dat aanvragen voor Azure Cosmos DB meer zijn dan de inrichtende doorvoer. De meest voorkomende oplossing voor dit probleem is het [omhoog schalen](./set-throughput.md) van de SCHALEN voor de opgegeven verzameling.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Aantal aanvragen per minuut":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>De doorvoerdistributie over partities bepalen

Een goede kardinaliteit van uw partitiesleutels is essentieel voor elke schaalbare toepassing. Navigeer naar de **blade** Metrische gegevens in de Azure Portal om de doorvoerdistributie van gepartities [te bepalen.](https://portal.azure.com) Op het **tabblad Doorvoer wordt** de uitsplitsing van de opslag weergegeven in de grafiek Maximaal aantal verbruikte **RU/seconde door elke fysieke partitie.** In de volgende afbeelding ziet u een voorbeeld van een slechte verdeling van gegevens, zoals wordt weergegeven door de scheef gemaakte partitie aan de linkerkant.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Eén partitie ziet intensief gebruik":::

Een ongelijke doorvoerdistributie kan leiden *tot hot* partities, wat kan leiden tot vertragingen in aanvragen en waarvoor opnieuw moet worden gepartitioneerd. Zie Partition and scale in Azure Cosmos DB (Partitioneren en schalen in Azure Cosmos DB) voor meer informatie over [partitionering in Azure Cosmos DB.](./partitioning-overview.md)

## <a name="determine-the-storage-distribution-across-partitions"></a>De opslagdistributie tussen partities bepalen

Een goede kardinaliteit van uw partitie is essentieel voor elke schaalbare toepassing. Ga naar de blade Metrische gegevens in de Azure Portal om de opslagdistributie van een gepartitiede container [te bepalen, onderverdeeld Azure Portal.](https://portal.azure.com) Op het tabblad Opslag wordt de uitsplitsing van de opslag weergegeven in de grafiek Gegevens en indexopslag die wordt gebruikt door de bovenste partitiesleutels. In de volgende afbeelding ziet u een slechte verdeling van de gegevensopslag, zoals wordt weergegeven door de scheef gemaakte partitie aan de linkerkant.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Voorbeeld van slechte gegevensdistributie":::

U kunt de hoofdoorzaak van welke partitiesleutel de distributie verslepen door te klikken op de partitie in de grafiek.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Partitiesleutel verscheeft de distributie":::

Nadat u hebt ingesteld welke partitiesleutel de scheefheid in de distributie veroorzaakt, moet u uw container mogelijk opnieuw partitioneren met een meer gedistribueerde partitiesleutel. Zie Partition and scale in Azure Cosmos DB (Partitioneren en schalen in Azure Cosmos DB) voor meer informatie over [partitionering in Azure Cosmos DB.](./partitioning-overview.md)

## <a name="compare-data-size-against-index-size"></a>Gegevensgrootte vergelijken met indexgrootte

In Azure Cosmos DB is de totale verbruikte opslag de combinatie van zowel de gegevensgrootte als de indexgrootte. Normaal gesproken is de indexgrootte een fractie van de gegevensgrootte. Zie het artikel [Indexgrootte voor meer](index-policy.md#index-size) informatie. Op de blade Metrische gegevens in [Azure Portal](https://portal.azure.com)wordt op het tabblad Opslag een overzicht weergegeven van het opslagverbruik op basis van gegevens en index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Als u indexruimte wilt besparen, kunt u het [indexeringsbeleid aanpassen.](index-policy.md)

## <a name="debug-why-queries-are-running-slow"></a>Fouten opsporen waarom query's traag worden uitgevoerd

In de SQL API-SDK's biedt Azure Cosmos DB statistieken voor het uitvoeren van query's.

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

*QueryMetrics biedt* details over hoe lang elk onderdeel van de query duurde om te worden uitgevoerd. De meest voorkomende hoofdoorzaak voor langlopende query's is scans, wat betekent dat de query geen gebruik kan maken van de indexen. Dit probleem kan worden opgelost met een betere filtervoorwaarde.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u problemen kunt bewaken en oplossen met behulp van de metrische gegevens in de Azure Portal. Lees de volgende artikelen voor meer informatie over het verbeteren van databaseprestaties:

* Zie het artikel Metrische gegevens op halen uit Azure Monitor voor meer informatie over het weergeven [van metrische](./monitor-cosmos-db.md) gegevens Azure Monitor Azure Monitor. 
* [Prestaties en schaal testen met Azure Cosmos DB](performance-testing.md)
* [Tips voor betere prestaties van Azure Cosmos DB](performance-tips.md)