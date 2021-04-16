---
title: Consistentie beheren in Azure Cosmos DB
description: Meer informatie over het configureren en beheren van consistentieniveaus in Azure Cosmos DB met Azure Portal, .NET SDK, Java SDK en verschillende andere SDK's
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7cab67b49196a3d50ce5483282971bbb7b9ece1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483262"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Consistentieniveaus in Azure Cosmos DB beheren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u consistentieniveaus beheert in Azure Cosmos DB. U leert hoe u het standaardconsistentieniveau configureert, de standaardconsistentie overschrijft, handmatig sessietokens beheert en wat metrische PBS-gegevens (Probabilistically Bounded Staleness) zijn.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Het standaardconsistentieniveau configureren

Het [standaardconsistentieniveau](consistency-levels.md) is het consistentieniveau dat clients standaard gebruiken.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u het standaardconsistentieniveau wilt weergeven of wijzigen, moet u zich aanmelden bij de Azure-portal. Zoek uw Azure Cosmos-account en open het **deelvenster Standaardconsistentie.** Selecteer het consistentieniveau dat u als nieuwe standaard wilt gebruiken en selecteer **Opslaan**. De Azure Portal biedt ook een visualisatie van verschillende consistentieniveaus met muzieknotities. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Menu Consistentie in de Azure-portal":::

# <a name="cli"></a>[CLI](#tab/cli)

Maak een Cosmos-account met sessieconsistentie en werk vervolgens de standaardconsistentie bij.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Maak een Cosmos-account met sessieconsistentie en werk vervolgens de standaardconsistentie bij.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Het standaardconsistentieniveau overschrijven

Clients kunnen het standaardconsistentieniveau dat is ingesteld door de service overschrijven. Het consistentieniveau kan per aanvraag worden ingesteld, waardoor het standaardconsistentieniveau dat op accountniveau is ingesteld, wordt overgenomen.

> [!TIP]
> Consistentie kan alleen worden **versoepeld** op aanvraagniveau. Als u van zwakkere naar sterkere consistentie wilt gaan, moet u de standaardconsistentie voor het Cosmos-account bijwerken.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2 SDK's

# <a name="async"></a>[Async](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Sessietokens gebruiken

Een van de consistentieniveaus in Azure Cosmos DB is *Sessieconsistentie.* Dit is het standaardniveau dat standaard wordt toegepast op Cosmos-accounts. Bij het werken met sessieconsistentie gebruikt de client intern een sessie-token bij elke lees-/queryaanvraag om ervoor te zorgen dat het in te stellen consistentieniveau behouden blijft. 

Als u sessietokens handmatig wilt beheren, haalt u het sessietoken op uit het antwoord en stelt u het in per aanvraag. Als u sessietokens niet handmatig hoeft te beheren, hoeft u deze voorbeelden niet te gebruiken. De SDK houdt sessietokens automatisch bij. Als u het sessietoken niet handmatig instelt, gebruikt de SDK standaard het meest recente sessietoken.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2 SDK's

# <a name="async"></a>[Async](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Metrische gegevens van aan waarschijnlijkheid gebonden veroudering (PBS) controleren

Hoe uiteindelijk is uiteindelijke consistentie? Voor het gemiddelde geval kunnen we verouderingsgrens met betrekking tot versiegeschiedenis en -tijd aanbieden. De [**metrische gegevens Probabilistically Bounded Verouderingess (PBS)**](https://pbs.cs.berkeley.edu/) proberen de waarschijnlijkheid van veroudering te kwantificeren en deze weer te laten zien als metrische gegevens. Als u de metrische PBS-gegevens wilt weergeven, gaat u naar uw Azure Cosmos-account in Azure Portal. Open het **deelvenster Metrische** gegevens en selecteer **het tabblad** Consistentie. Bekijk de grafiek met de **naam Waarschijnlijkheid van sterk consistente leesingen op basis van uw workload (zie PBS).**

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="PBS-grafiek in de Azure-portal":::

## <a name="next-steps"></a>Volgende stappen

Lees meer informatie over het beheer van gegevensconflicten of ga verder met het volgende sleutelbegrip in Azure Cosmos DB. Zie de volgende artikelen:

* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)
* [Partitionering en gegevensdistributie](./partitioning-overview.md)
* [Conflicten tussen regio 's beheren](how-to-manage-conflicts.md)
* [Partitionering en gegevensdistributie](partitioning-overview.md)
* [Consistentie-afwegingen in het ontwerp van moderne gedistribueerde databasesystemen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Hoge beschikbaarheid](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
