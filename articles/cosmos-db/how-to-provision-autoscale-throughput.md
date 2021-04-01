---
title: De door Voer van automatisch schalen inrichten in Azure Cosmos DB SQL-API
description: Meer informatie over het inrichten van de door Voer voor automatisch schalen op het niveau van de container en de data base in Azure Cosmos DB SQL-API met behulp van Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357618"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Automatisch schalen door Voer in te richten op Data Base of container in Azure Cosmos DB-SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u de door Voer van automatisch schalen kunt inrichten voor een Data Base of container (verzameling, grafiek of tabel) in Azure Cosmos DB SQL-API. U kunt automatisch schalen inschakelen voor één container, of de door Voer van automatisch schalen inrichten voor een Data Base en deze delen met alle containers in de data base.

Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-provision-throughput-mongodb.md), [CASSANDRA-API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artikelen om de door Voer in te richten.

## <a name="azure-portal"></a>Azure Portal

### <a name="create-new-database-or-container-with-autoscale"></a>Nieuwe data base of container maken met automatisch schalen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of de [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-account en open het tabblad **Data Explorer** .

1. Selecteer **nieuwe container.** Voer een naam in voor uw data base, container en partitie sleutel. Selecteer onder **door Voer** de optie voor **automatisch schalen** en stel de [maximale door Voer (ru/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) in waarvan u wilt dat de data base of container moet worden geschaald.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Een container maken en door Voer ingericht voor automatisch schalen configureren":::

1. Selecteer **OK**.

Als u automatisch schalen wilt inrichten voor een gedeelde doorvoer database, selecteert u de optie **doorvoer database inrichten** bij het maken van een nieuwe data base. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Automatisch schalen op een bestaande data base of container inschakelen

> [!IMPORTANT]
> In de huidige versie is het Azure Portal de enige manier om de ingerichte door Voer van automatisch schalen en standaard (hand matig) te migreren. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of de [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-account en open het tabblad **Data Explorer** .

1. Selecteer **schalen en instellingen** voor uw container, of **schaal** voor uw data base.

1. Onder **schalen** selecteert u de optie voor **automatisch schalen** en **slaat** u deze op.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Automatisch schalen inschakelen voor een bestaande container":::

> [!NOTE]
> Wanneer u automatisch schalen inschakelt voor een bestaande data base of container, wordt de begin waarde voor het maximale aantal RU/s bepaald door het systeem, op basis van uw huidige hand matige ingerichte instellingen voor de door Voer en opslag. Nadat de bewerking is voltooid, kunt u indien nodig het maximum aantal RU/s wijzigen. [Meer informatie.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET v3 SDK

Gebruik [versie 3,9 of hoger](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) van de Azure Cosmos db .NET SDK voor SQL API voor het beheren van resources voor automatisch schalen. 

> [!IMPORTANT]
> U kunt de .NET SDK gebruiken om nieuwe resources voor automatisch schalen te maken. De SDK biedt geen ondersteuning voor de migratie tussen automatisch schalen en standaard (hand matig) door voer. Het migratie scenario wordt momenteel alleen ondersteund in de Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Data base maken met gedeelde door Voer

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Container maken met specifieke door Voer

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>De huidige door Voer (RU/s) lezen

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>De maximale door Voer (RU/s) voor automatisch schalen wijzigen

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java v4 SDK

U kunt [versie 4,0 of hoger](https://mvnrepository.com/artifact/com.azure/azure-cosmos) van de Azure Cosmos DB Java SDK voor SQL API gebruiken voor het beheren van resources voor automatisch schalen.

> [!IMPORTANT]
> U kunt de Java-SDK gebruiken om nieuwe resources voor automatisch schalen te maken. De SDK biedt geen ondersteuning voor de migratie tussen automatisch schalen en standaard (hand matig) door voer. Het migratie scenario wordt momenteel alleen ondersteund in de Azure Portal.

### <a name="create-database-with-shared-throughput"></a>Data base maken met gedeelde door Voer

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Synchroniseren](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Container maken met specifieke door Voer

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Synchroniseren](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>De huidige door Voer (RU/s) lezen

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Synchroniseren](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>De maximale door Voer (RU/s) voor automatisch schalen wijzigen

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Synchroniseren](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager sjablonen kunnen worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op Data Base-of container niveau-resources voor alle Azure Cosmos DB-Api's. Zie [Azure Resource Manager sjablonen voor Azure Cosmos DB](./templates-samples-sql.md) voor voor beelden.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau voor alle Azure Cosmos DB-Api's. Zie Azure CLI-voor beelden [voor Azure Cosmos DB](cli-samples.md)voor meer informatie.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau resources voor alle Azure Cosmos DB-Api's. Zie [Azure PowerShell voor beelden voor Azure Cosmos DB](powershell-samples.md)voor steek proeven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [voor delen van ingerichte door Voer met automatisch schalen](provision-throughput-autoscale.md#benefits-of-autoscale).
* Meer informatie over hoe u [kunt kiezen tussen hand matige door Voer en automatisch schalen](how-to-choose-offer.md).
* Bekijk de [Veelgestelde vragen over automatisch schalen](autoscale-faq.md).
