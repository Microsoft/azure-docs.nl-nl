---
title: Problemen met Azure Cosmos DB niet gevonden uitzonde ringen oplossen
description: Meer informatie over het vaststellen en oplossen van niet-gevonden uitzonde ringen.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7b112cc80984a761e780f134731476f9dff4f687
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525768"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Problemen vaststellen en oplossen Azure Cosmos DB niet-gevonden uitzonde ringen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De HTTP-statuscode 404 geeft aan dat de resource niet meer bestaat.

## <a name="expected-behavior"></a>Verwacht gedrag
Er zijn veel geldige scenario's waarbij een toepassing een code 404 verwacht en het scenario correct verwerkt.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Er is een niet-gevonden uitzonde ring geretourneerd voor een item dat bestaat of bestaat
Dit zijn de mogelijke redenen waarom de status code 404 wordt geretourneerd als het item bestaat of bestaat.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>De leessessie is niet beschikbaar voor het token voor de invoersessie

#### <a name="solution"></a>Oplossing:
1. Werk uw huidige SDK bij naar de meest recente versie die beschikbaar is. De meest voorkomende oorzaken voor deze specifieke fout zijn opgelost in de nieuwste SDK-versies.

### <a name="race-condition"></a>Racevoorwaarde
Er zijn meerdere exemplaren van de SDK-client en het lezen is voorgekomen voordat de schrijf bewerking werd uitgevoerd.

#### <a name="solution"></a>Oplossing:
1. De standaard consistentie van het account voor Azure Cosmos DB is sessie consistentie. Wanneer een item wordt gemaakt of bijgewerkt, retourneert het antwoord een sessie token dat kan worden door gegeven tussen SDK-instanties om te garanderen dat de Lees aanvraag wordt gelezen van een replica met die wijziging.
1. Wijzig het [consistentie niveau](./consistency-levels.md) in een [sterker niveau](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>Ongeldige combi natie van partitie sleutel en ID
De combi natie van de partitie sleutel en-ID is niet geldig.

#### <a name="solution"></a>Oplossing:
Los de toepassings logica op die de onjuiste combi natie veroorzaakt. 

### <a name="invalid-character-in-an-item-id"></a>Ongeldig teken in een item-ID
Een item wordt ingevoegd in Azure Cosmos DB met een [ongeldig teken](/dotnet/api/microsoft.azure.documents.resource.id?preserve-view=true&view=azure-dotnet#remarks) in de item-id.

#### <a name="solution"></a>Oplossing:
Wijzig de ID in een andere waarde die geen speciale tekens bevat. Als het wijzigen van de ID geen optie is, kunt u de ID met Base64 coderen om de speciale tekens te escapen. Met base64 kan nog steeds een naam worden gemaakt met een ongeldig teken/. dit moet worden vervangen.

Items die al in de container voor de ID zijn ingevoegd, kunnen worden vervangen door RID-waarden in plaats van verwijzingen op basis van namen.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Time to Live opschonen
De eigenschap [time to Live (TTL)](./time-to-live.md) van het item is ingesteld. Het item is verwijderd omdat de eigenschap TTL is verlopen.

#### <a name="solution"></a>Oplossing:
Wijzig de eigenschap TTL om te voor komen dat het item wordt leeg gemaakt.

### <a name="lazy-indexing"></a>Luie indexering
De [luie indexering](index-policy.md#indexing-mode) is niet gevonden.

#### <a name="solution"></a>Oplossing:
Wacht totdat het indexeren is gestart of het indexerings beleid heeft gewijzigd.

### <a name="parent-resource-deleted"></a>Bovenliggende resource verwijderd
De data base of container waarin het item zich bevindt, is verwijderd.

#### <a name="solution"></a>Oplossing:
1. [Herstel](./configure-periodic-backup-restore.md#request-restore) de bovenliggende resource of maak de resources opnieuw.
1. Maak een nieuwe resource om de verwijderde resource te vervangen.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. namen van containers en verzamelingen zijn hoofdletter gevoelig
Namen van containers en verzamelingen zijn hoofdletter gevoelig in Cosmos DB.

#### <a name="solution"></a>Oplossing:
Zorg ervoor dat u de exacte naam gebruikt terwijl u verbinding maakt met Cosmos DB.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
