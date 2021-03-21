---
title: De Table-API en Java gebruiken om een app te bouwen - Azure Cosmos DB
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 48335e577ed248a42914bdaa1b1e662daf2d26cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090164"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Quickstart: Een Java-app bouwen om Azure Cosmos DB Table-API-gegevens te beheren
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

In deze snelstart maakt u een Azure Cosmos DB Table-API-account en gebruikt u Data Explorer en een Java-app die is gekloond van GitHub om tabellen en entiteiten te maken. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8+](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Verwijs uw omgevingsvariabele `JAVA_HOME` naar de map waarin de JDK is geïnstalleerd.
- Een [binair Maven-archief](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> [!TIP]
> Zie het artikel [Voorbeeld van Cosmos DB Table-API](table-storage-how-to-use-java.md) voor een gedetailleerd overzicht van soortgelijke code. 

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar de sectie [De verbindingsreeks bijwerken](#update-your-connection-string) van dit document.

* De volgende code laat zien hoe u een tabel maakt in de Azure Storage:

  ```java
  private static CloudTable createTable(CloudTableClient tableClient, String tableName) throws StorageException, RuntimeException, IOException, InvalidKeyException,   IllegalArgumentException, URISyntaxException, IllegalStateException {
  
    // Create a new table
    CloudTable table = tableClient.getTableReference(tableName);
    try {
        if (table.createIfNotExists() == false) {
            throw new IllegalStateException(String.format("Table with name \"%s\" already exists.", tableName));
        }
    }
    catch (StorageException s) {
        if (s.getCause() instanceof java.net.ConnectException) {
            System.out.println("Caught connection exception from the client. If running with the default configuration please make sure you have started the storage emulator.");
        }
        throw s;
    }

    return table;
  }
  ```

* De volgende code laat zien hoe u gegevens invoegt in de tabel:

  ```javascript
  private static void batchInsertOfCustomerEntities(CloudTable table) throws StorageException {
  
  // Create the batch operation
  TableBatchOperation batchOperation1 = new TableBatchOperation();
  for (int i = 1; i <= 50; i++) {
      CustomerEntity entity = new CustomerEntity("Smith", String.format("%04d", i));
      entity.setEmail(String.format("smith%04d@contoso.com", i));
      entity.setHomePhoneNumber(String.format("425-555-%04d", i));
      entity.setWorkPhoneNumber(String.format("425-556-%04d", i));
      batchOperation1.insertOrMerge(entity);
  }
  
  // Execute the batch operation
  table.execute(batchOperation1);
  }
  ```

* De volgende code laat zien hoe u gegevens uit de tabel opvraagt:

  ```java
  private static void partitionScan(CloudTable table, String partitionKey) throws StorageException {
  
      // Create the partition scan query
      TableQuery<CustomerEntity> partitionScanQuery = TableQuery.from(CustomerEntity.class).where(
          (TableQuery.generateFilterCondition("PartitionKey", QueryComparisons.EQUAL, partitionKey)));
  
      // Iterate through the results
      for (CustomerEntity entity : table.execute(partitionScanQuery)) {
          System.out.println(String.format("\tCustomer: %s,%s\t%s\t%s\t%s", entity.getPartitionKey(), entity.getRowKey(), entity.getEmail(), entity.getHomePhoneNumber(), entity.  getWorkPhoneNumber()));
      }
  }
  ```

* De volgende code laat zien hoe u gegevens uit de tabel verwijdert:

  ```java
  
  System.out.print("\nDelete any tables that were created.");
  
  if (table1 != null && table1.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table1.getName()));
  }
  
  if (table2 != null && table2.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table2.getName()));
  }
  ```

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Selecteer in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/) de optie **Verbindingsreeks**. 

   :::image type="content" source="./media/create-table-java/cosmos-db-quickstart-connection-string.png" alt-text="De verbindingsreeks in het deelvenster Verbindingsreeks weergeven":::

2. Kopieer de PRIMARY CONNECTION STRING met behulp van de knop Kopiëren aan de rechterkant.

3. Open *config.properties* vanuit de map *C:\git-samples\storage-table-java-getting-started\src\main\resources*. 

5. De volgende stap is het uitcommentariëren van regel 1, waarna u het commentaar van regel 2 verwijdert. De eerste twee regels moeten er nu als volgt uitzien.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Plak de PRIMARY CONNECTION STRING vanuit de portal in de waarde StorageConnectionString in regel 2. 

    > [!IMPORTANT]
    > Als uw eindpunt documents.azure.com gebruikt, hebt u een preview-account en moet u een [nieuw Table-API-account](#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK voor Table- API.
    >

7. Sla het bestand *config.properties* op.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="run-the-app"></a>De app uitvoeren

1. `cd` in het git-terminalvenster naar de map storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Voer in het git-terminalvenster de volgende opdrachten uit om de Java-toepassing uit te voeren.

    ```git
    mvn compile exec:java 
    ```

    In het consolevenster worden de tabelgegevens weergegeven die worden toegevoegd aan de nieuwe tabeldatabase in Azure Cosmos DB.

    U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, opvragen en ermee werken. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account kunt maken, hoe u een tabel kunt maken met de Data Explorer en hoe u een Java-app uitvoert om tabelgegevens toe te voegen.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)