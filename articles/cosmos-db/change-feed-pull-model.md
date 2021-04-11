---
title: Pull-model voor feed wijzigen
description: Meer informatie over het gebruik van het Azure Cosmos DB model voor het activeren van wijzigingen in feed voor het lezen van de wijzigings feed en de verschillen tussen het pull-model en de wijzigings processor
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 9279dddc92629b17a2a73f3a41fe261d322d677e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463677"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Pull-model voor feed wijzigen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met het pull-model voor wijzigings invoer kunt u de Azure Cosmos DB wijzigings feed gebruiken in uw eigen tempo. Zoals u al kunt doen met de [Change feed-processor](change-feed-processor.md), kunt u het pull-model wijzigings feed gebruiken om de verwerking van wijzigingen in meerdere parallelliseren te verwerken.

> [!NOTE]
> Het pull-model voor de wijzigings feed is momenteel in [de preview-versie van de Azure Cosmos db .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) . De preview-versie is nog niet beschikbaar voor andere SDK-versies.

## <a name="comparing-with-change-feed-processor"></a>Vergelijken met de processor voor wijzigings invoer

In veel scenario's kan de wijzigings feed worden verwerkt met behulp van de [Change feed-processor](change-feed-processor.md) of het pull-model. De vervolg tokens van het pull-model en de lease-container van de wijzigings feed zijn beide ' blad wijzers ' voor het laatst verwerkte item (of een batch met items) in de wijzigings feed.

U kunt vervolg tokens echter niet converteren naar een lease container (of andersom).

> [!NOTE]
> In de meeste gevallen moet u de optie voor het wijzigen van de [feed-processor](change-feed-processor.md)gebruiken als u de wijzigings feed wilt lezen.

Overweeg het gebruik van het pull-model in de volgende scenario's:

- Wijzigingen van een bepaalde partitie sleutel lezen
- Het tempo bepalen waarmee uw client wijzigingen voor verwerking ontvangt
- Een eenmalige het lezen van de bestaande gegevens in de feed wijzigen (bijvoorbeeld om een gegevens migratie uit te voeren)

Hier volgen enkele belang rijke verschillen tussen de Change feed-processor en het pull-model:

|Functie  | Verwerker van wijzigingenfeed| Pull-model |
| --- | --- | --- |
| Bijhouden van huidige punt in verwerking wijzigings feed | Lease (opgeslagen in een Azure Cosmos DB container) | Vervolg token (opgeslagen in het geheugen of hand matig bewaard) |
| Mogelijkheid om eerdere wijzigingen opnieuw af te spelen | Ja, met push model | Ja, met pull-model|
| Navragen voor toekomstige wijzigingen | Controleert automatisch op wijzigingen op basis van door de gebruiker opgegeven `WithPollInterval` | Handmatig |
| Gedrag waarbij er geen nieuwe wijzigingen zijn | Automatisch wachten `WithPollInterval` en opnieuw controleren | Moet uitzonde ring afvangen en hand matig opnieuw controleren |
| Wijzigingen van de hele container verwerken | Ja, en automatisch geparallelleerd voor meerdere threads/machine die uit dezelfde container worden verbruikt| Ja, en hand matig geparallelleerd met behulp van FeedTokens |
| Wijzigingen van slechts één partitie sleutel verwerken | Niet ondersteund | Yes|
| Ondersteunings niveau | Algemeen beschikbaar | Preview |

> [!NOTE]
> In tegens telling tot bij het lezen van de Change feed-processor moet u expliciet aanvragen afhandelen waarbij er geen nieuwe wijzigingen zijn. 

## <a name="consuming-an-entire-containers-changes"></a>De wijzigingen van een hele container gebruiken

U kunt een maken `FeedIterator` om de wijzigings feed te verwerken met het pull-model. Wanneer u voor het eerst een maakt `FeedIterator` , moet u een vereiste `ChangeFeedStartFrom` waarde opgeven die bestaat uit de begin positie voor het lezen van wijzigingen en de gewenste waarden `FeedRange` . Het `FeedRange` is een bereik van partitie sleutel waarden en Hiermee geeft u de items op die van de wijzigings feed worden gelezen met die specifieke `FeedIterator` .

U kunt optioneel opgeven `ChangeFeedRequestOptions` om een in te stellen `PageSizeHint` . Het `PageSizeHint` is het maximum aantal items dat wordt geretourneerd op één pagina.

De `FeedIterator` is beschikbaar in twee soorten. Naast de voor beelden hieronder die entiteits objecten retour neren, kunt u ook het antwoord verkrijgen met `Stream` ondersteuning. Met streams kunt u gegevens lezen zonder dat deze eerst worden gedeserialiseerd, op client bronnen worden opgeslagen.

Hier volgt een voor beeld voor het verkrijgen van het `FeedIterator` retour neren van entiteits objecten, in dit geval een `User` object:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Hier volgt een voor beeld voor het verkrijgen van een `FeedIterator` `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Als u geen `FeedRange` aan een hebt geleverd `FeedIterator` , kunt u de wijzigings feed van een hele container in uw eigen tempo verwerken. Hier volgt een voor beeld waarin wordt begonnen met het lezen van alle wijzigingen, beginnend bij de huidige tijd:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Omdat de wijzigings feed in feite een oneindig lijst items is die alle toekomstige schrijf bewerkingen en updates omvatten, is de waarde van `HasMoreResults` is altijd waar. Wanneer u de wijzigings feed probeert te lezen en er geen nieuwe wijzigingen beschikbaar zijn, ontvangt u een uitzonde ring. In het bovenstaande voor beeld wordt de uitzonde ring afgehandeld door vijf seconden te wachten voordat wijzigingen opnieuw worden gecontroleerd.

## <a name="consuming-a-partition-keys-changes"></a>De wijzigingen van een partitie sleutel gebruiken

In sommige gevallen wilt u mogelijk alleen de wijzigingen van een specifieke partitie sleutel verwerken. U kunt een `FeedIterator` voor een specifieke partitie sleutel verkrijgen en de wijzigingen op dezelfde manier verwerken als voor een hele container.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>FeedRange gebruiken voor parallel Lise ring

In de [Change feed-processor](change-feed-processor.md)wordt werk automatisch verspreid over meerdere gebruikers. In het pull-model voor het wijzigen van de feed kunt u de gebruiken `FeedRange` om de parallelliseren van de wijzigings feed te verwerken. Een `FeedRange` vertegenwoordigt een bereik van partitie sleutel waarden.

Hier volgt een voor beeld waarin wordt getoond hoe u een lijst met bereiken voor uw container kunt verkrijgen:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Wanneer u een lijst met FeedRanges voor uw container ontvangt, krijgt u één `FeedRange` per [fysieke partitie](partitioning-overview.md#physical-partitions).

Met behulp `FeedRange` van een kunt u vervolgens een `FeedIterator` parallelliseren maken om de verwerking van de wijzigings feed op meerdere computers of threads te verwerken. In tegens telling tot het vorige voor beeld waarin wordt uitgelegd hoe `FeedIterator` u een voor de volledige container of een enkele partitie sleutel kunt verkrijgen, gebruikt u FeedRanges om meerdere FeedIterators te verkrijgen waarmee de wijziging in de feed parallel kan worden verwerkt.

In het geval waar u FeedRanges wilt gebruiken, moet u een Orchestrator-proces hebben dat FeedRanges verkrijgt en distribueert naar deze machines. Deze distributie kan het volgende zijn:

* `FeedRange.ToJsonString`Deze teken reeks waarde gebruiken en distribueren. De consumenten kunnen deze waarde gebruiken met `FeedRange.FromJsonString`
* Als de distributie in het proces wordt uitgevoerd, wordt de `FeedRange` object verwijzing door gegeven.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u vanaf het begin van de wijzigings feed van de container kunt lezen met behulp van twee hypothetische afzonderlijke machines die parallel worden gelezen:

Machine 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Machine 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="saving-continuation-tokens"></a>Bezig met opslaan van vervolg tokens

U kunt de positie van uw opslaan `FeedIterator` door een vervolg token te maken. Een vervolg token is een teken reeks waarde waarmee de laatst verwerkte wijzigingen van de FeedIterator worden bijgehouden. Hierdoor kan de service `FeedIterator` op dit moment later worden hervat. Met de volgende code wordt de wijzigings feed gelezen sinds het maken van de container. Nadat er geen wijzigingen meer beschikbaar zijn, wordt er een vervolg token bewaard, zodat het gebruik van de wijzigings toevoer later kan worden hervat.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Zolang de Cosmos-container nog bestaat, verloopt de vervolg token van FeedIterator nooit.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van wijzigings feed](change-feed.md)
* [De Change feed-processor gebruiken](change-feed-processor.md)
* [Azure Functions activeren](change-feed-functions.md)
