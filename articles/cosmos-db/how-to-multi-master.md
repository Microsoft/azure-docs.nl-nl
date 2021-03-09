---
title: Schrijf bewerkingen voor meerdere regio's configureren in Azure Cosmos DB
description: Meer informatie over het configureren van schrijf bewerkingen voor meerdere regio's voor uw toepassingen met behulp van verschillende Sdk's in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 08d50b18605fd833e6b0efca987338d0ca1eef8d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488508"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Schrijf bewerkingen in meerdere regio's configureren in uw toepassingen die gebruikmaken van Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Als een account is gemaakt terwijl meerdere schrijf regio's zijn ingeschakeld, moet u twee wijzigingen aanbrengen in uw toepassing voor de Connection Policy voor de Cosmos-client om de schrijf bewerkingen met meerdere regio's in Azure Cosmos DB in te scha kelen. In de Connection Policy stelt u UseMultipleWriteLocations in op True en geeft u de naam van de regio waar de toepassing wordt geïmplementeerd naar ApplicationRegion. Hiermee wordt de eigenschap PreferredLocations ingevuld op basis van de geografische nabijheid van de locatie die wordt door gegeven in. Als er later een nieuwe regio wordt toegevoegd aan het account, de toepassing niet hoeft te worden bijgewerkt of opnieuw wordt geïmplementeerd, wordt de regio dichter automatisch gedetecteerd en wordt er automatisch een regionale gebeurtenis weer gegeven.

> [!Note]
> Cosmos-accounts die in eerste instantie zijn geconfigureerd met één schrijf regio kunnen worden geconfigureerd voor meerdere schrijf regio's met een onbepaalde tijd. Zie [meerdere regio's configureren](how-to-manage-database-account.md#configure-multiple-write-regions) voor meer informatie.

## <a name="azure-portal"></a><a id="portal"></a> Azure Portal

Voer de volgende stappen uit om het schrijven van meerdere regio's vanaf Azure Portal in te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Ga naar uw Azure Cosmos-account en open in het menu het deel venster **gegevens globaal repliceren** .

1. Kies **inschakelen** onder de optie voor **schrijf bewerkingen met meerdere regio's** . De bestaande regio's worden automatisch toegevoegd aan de regio's lezen en schrijven.

1. U kunt extra regio's toevoegen door de pictogrammen op de kaart te selecteren of door de knop **regio toevoegen** te selecteren. Voor alle regio's die u toevoegt, zijn lees-en schrijf bewerkingen ingeschakeld.

1. Nadat u de regio lijst hebt bijgewerkt, selecteert u **Opslaan** om de wijzigingen toe te passen.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Scherm afbeelding voor het inschakelen van schrijf bewerkingen met meerdere regio's met behulp van Azure Portal" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET-SDK v2

Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, stelt `UseMultipleWriteLocations` u in op `true` . Stel ook `SetCurrentLocation` de regio in waarin de toepassing wordt geïmplementeerd en waar Azure Cosmos DB worden gerepliceerd:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET-SDK v3

Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, stelt `ApplicationRegion` u in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

U kunt ook de gebruiken `CosmosClientBuilder` `WithApplicationRegion` om hetzelfde resultaat te krijgen:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, roept u `.multipleWriteRegionsEnabled(true)` `.preferredRegions(preferredRegions)` de client-opbouw functie op en gebruikt u deze, waarbij `preferredRegions` een `List` bevat één element, dat de regio is waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Asynchrone Java v2 SDK

De Java v2 SDK heeft de Maven [com. micro soft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)gebruikt. Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, stelt `policy.setUsingMultipleWriteLocations(true)` u in en stelt `policy.setPreferredLocations` u deze in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, java script en type script Sdk's

Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, stelt `connectionPolicy.UseMultipleWriteLocations` u in op `true` . Stel ook `connectionPolicy.PreferredLocations` de regio in waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python-SDK

Als u schrijf bewerkingen in meerdere regio's in uw toepassing wilt inschakelen, stelt `connection_policy.UseMultipleWriteLocations` u in op `true` . Stel ook `connection_policy.PreferredLocations` de regio in waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen:

* [Sessie tokens gebruiken voor het beheren van consistentie in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflictsoorten en oplossingsbeleid in Azure Cosmos DB](conflict-resolution-policies.md)
* [Hoge beschikbaarheid in Azure Cosmos DB](high-availability.md)
* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)
* [Kies het juiste consistentie niveau in Azure Cosmos DB](./consistency-levels.md)
* [Consistentie, Beschik baarheid en prestatie afweging in Azure Cosmos DB](./consistency-levels.md)
* [Beschik baarheid en prestaties voor diverse consistentie niveaus](./consistency-levels.md)
* [Wereldwijd schalen van ingerichte doorvoer](./request-units.md)
* [Wereld wijde distributie: onder de motorkap](global-dist-under-the-hood.md)