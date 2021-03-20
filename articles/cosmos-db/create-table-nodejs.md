---
title: 'Quickstart: Table-API met Node.js - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Node.js
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: a9f5a307bfefedf74f884e39b482bd90454fc5c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096866"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Quickstart: Een Table-API compileren met Node.js en Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

In deze snelstart maakt u een Azure Cosmos DB Table-API-account en gebruikt u Data Explorer en een Node.js-app die is gekloond van GitHub om tabellen en entiteiten te maken. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 0.10.29+](https://nodejs.org/).
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Een databaseaccount maken

> [!IMPORTANT] 
> U moet een nieuw Table-API-account maken om te kunnen werken met de algemeen beschikbare SDK's voor de Table-API. Table-API-accounts die zijn gemaakt tijdens de preview worden niet ondersteund door de algemeen beschikbare SDK's.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Een tabel toevoegen

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Table-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

> [!TIP]
> Zie het artikel [Voorbeeld van Cosmos DB Table-API](table-storage-how-to-use-nodejs.md) voor een gedetailleerd overzicht van soortgelijke code. 

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar de sectie [De verbindingsreeks bijwerken](#update-your-connection-string) van dit document.

* De volgende code laat zien hoe u een tabel maakt in de Azure Storage:

  ```javascript
  storageClient.createTableIfNotExists(tableName, function (error, createResult) {
    if (error) return callback(error);

    if (createResult.isSuccessful) {
      console.log("1. Create Table operation executed successfully for: ", tableName);
    }
  }

  ```

* De volgende code laat zien hoe u gegevens invoegt in de tabel:

  ```javascript
  var customer = createCustomerEntityDescriptor("Harp", "Walter", "Walter@contoso.com", "425-555-0101");

  storageClient.insertOrMergeEntity(tableName, customer, function (error, result, response) {
    if (error) return callback(error);

    console.log("   insertOrMergeEntity succeeded.");
  }
  ```

* De volgende code laat zien hoe u gegevens uit de tabel opvraagt:

  ```javascript
  console.log("6. Retrieving entities with surname of Smith and first names > 1 and <= 75");

  var storageTableQuery = storage.TableQuery;
  var segmentSize = 10;

  // Demonstrate a partition range query whereby we are searching within a partition for a set of entities that are within a specific range. 
  var tableQuery = new storageTableQuery()
      .top(segmentSize)
      .where('PartitionKey eq ?', lastName)
      .and('RowKey gt ?', "0001").and('RowKey le ?', "0075");
  
  runPageQuery(tableQuery, null, function (error, result) {
  
      if (error) return callback(error);
  
  ```

* De volgende code laat zien hoe u gegevens uit de tabel verwijdert:

  ```javascript
  storageClient.deleteEntity(tableName, customer, function entitiesQueried(error, result) {
      if (error) return callback(error);
  
      console.log("   deleteEntity succeeded.");
  }
  ```
  
## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Selecteer in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/) de optie **Verbindingsreeks**. 

    :::image type="content" source="./media/create-table-nodejs/connection-string.png" alt-text="De vereiste verbindingsreeksinformatie bekijken en kopiëren vanuit het deelvenster Verbindingsreeks":::

2. Kopieer de PRIMARY CONNECTION STRING met behulp van de knop Kopiëren aan de rechterkant.

3. Open het bestand *app.config* en plak de waarde in de connectionString op regel drie. 

    > [!IMPORTANT]
    > Als uw eindpunt documents.azure.com gebruikt, hebt u een preview-account en moet u een [nieuw Table-API-account](#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK voor Table- API.
    >

3. Sla het bestand *app.config* op.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="run-the-app"></a>De app uitvoeren

1. `cd` in het git-terminalvenster naar de map storage-table-java-getting-started.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. Voer de volgende opdracht uit om de modules [azure], [node-uuid], [nconf] en [async] lokaal uit te voeren en een vermelding voor deze modules op te slaan in het bestand *package.json*.

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. Voer in het git-terminalvenster de volgende opdrachten uit om de Node.js-toepassing uit te voeren.

    ```
    node ./tableSample.js 
    ```

    In het consolevenster worden de tabelgegevens weergegeven die worden toegevoegd aan de nieuwe tabeldatabase in Azure Cosmos DB.

    U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, opvragen en ermee werken. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account kunt maken, hoe u een tabel kunt maken met de Data Explorer en hoe u een Node.js-app uitvoert om tabelgegevens toe te voegen.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)