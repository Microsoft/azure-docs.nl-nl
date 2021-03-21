---
title: Quickstart - Java gebruiken om een documentdatabase maken met behulp van Azure Cosmos DB
description: Deze quickstart biedt een voorbeeld van Java-code dat u kunt gebruiken om verbinding te maken met de SQL API van Azure Cosmos DB en er query’s op uit te voeren
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090406"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quickstart: Een Java-app bouwen om Azure Cosmos DB SQL API-gegevens te beheren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een SQL API-account van Azure Cosmos DB via de Azure-portal en met een Java-app die is gekloond van GitHub. Eerst maakt u een Azure Cosmos DB SQL API-account via de Azure-portal, dan maakt u een Java-app met behulp van de SQL Java SDK en dan voegt u met de Java-toepassing resources toe aan uw Cosmos DB-account. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

> [!IMPORTANT]  
> Deze quickstart geldt alleen voor Azure Cosmos DB Java SDK v4. Bekijk de [release-opmerkingen](sql-api-sdk-java-v4.md) bij Azure Cosmos DB Java SDK v4, de [Maven-opslagplaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [prestatietips](performance-tips-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 en de [probleemoplossingsgids](troubleshoot-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 voor meer informatie. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8+](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Verwijs uw omgevingsvariabele `JAVA_HOME` naar de map waarin de JDK is geïnstalleerd.
- Een [binair Maven-archief](https://maven.apache.org/download.cgi). Voer op Ubuntu `apt-get install maven` uit om Maven te installeren.
- [Git](https://www.git-scm.com/downloads). Voer op Ubuntu `sudo apt-get install git` uit om Git te installeren.

## <a name="introductory-notes"></a>Inleidende opmerkingen

*De structuur van een Cosmos DB-account.* Ongeacht de API- of programmeer taal, bevat een Cosmos DB-*account* nul of meer *databases*, een *database* (DB) bevat nul of meer *containers* en een *container* bevat nul of meer items, zoals in het onderstaande diagram wordt weer gegeven:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entiteiten van Azure Cosmos-account" border="false":::

Vind [hier](account-databases-containers-items.md) meer informatie over databases, containers en items. Enkele belangrijke eigenschappen worden op het niveau van de container gedefinieerd, onder andere *ingerichte doorvoer* en *partitiesleutel*. 

De ingerichte doorvoer wordt gemeten in de aanvraageenheden (*RU’s*) die een monetaire prijs hebben en die een substantiële bepaling van de bedrijfskosten van het account zijn. Ingerichte doorvoer kan worden geselecteerd bij granulatie op basis van een container of nauwkeurigheid per database, maar de specificatie voor doorvoer op containerniveau verdient doorgaans de voorkeur. U kunt [hier](set-throughput.md) meer lezen over het inrichten van de doorvoer.

Naarmate items worden ingevoegd in een Cosmos DB-container, groeit de database horizontaal door meer opslag en rekenkracht toe te voegen voor het afhandelen van aanvragen. Opslag-en rekencapaciteit worden toegevoegd aan discrete eenheden die *partities worden genoemd* en u moet één veld in uw documenten selecteren als partitiesleutel die elk document aan een partitie toewijst. De manier waarop partities worden beheerd, is dat aan elke partitie een ongeveer gelijk segment uit het bereik van partitiesleutelwaarden wordt toegewezen. Daarom wordt u aangeraden een partitiesleutel te kiezen die relatief willekeurig of gelijkmatig gedistribueerd is. Als dat niet het geval is, zien sommige partities aanzienlijk meer aanvragen (*dynamische partitie*), terwijl andere partities aanzienlijk minder aanvragen (*statische partitie*) zien. Dit moet vermeden worden. U vind [hier](partitioning-overview.md) meer informatie over partitionering.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een SQL-API-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code. We gaan nu een SQL API-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Sla dit anders over en ga naar [De app uitvoeren](#run-the-app). 


# <a name="sync-api"></a>[API synchroniseren](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Database resources beheren met behulp van de synchrone API

* Initialisatie van `CosmosClient`. De `CosmosClient` biedt een logische weergave aan de clientzijde voor de databaseservice van Azure Cosmos. Deze client wordt gebruikt om aanvragen aan de service te configureren en uitvoeren.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` maken.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` maken.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Items maken met behulp van de `createItem`-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Puntleesbewerkingen worden uitgevoerd met behulp van `readItem`-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-query's via JSON worden uitgevoerd met behulp van de methode `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Async-API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Database resources beheren met behulp van de asynchrone API

* Asynchrone API-aanroepen worden direct geretourneerd zonder dat er wordt gewacht op een reactie van de server. In het licht hiervan geven de volgende codefragmenten goede ontwerppatronen weer voor het uitvoeren van alle voorgaande beheertaken met behulp van asynchrone API.

* Initialisatie van `CosmosAsyncClient`. De `CosmosAsyncClient` biedt een logische weergave aan de clientzijde voor de databaseservice van Azure Cosmos. Deze client wordt gebruikt om asynchrone aanvragen aan de service te configureren en uitvoeren.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` maken.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` maken.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Net als bij de synchrone API wordt het maken van items uitgevoerd met behulp van de `createItem`-methode. Dit voorbeeld laat zien hoe u op efficiënte wijze talloze asynchrone `createItem` aanvragen verstrekt door een reactieve stroom te gebruiken die de aanvragen verstrekt en meldingen verzendt. Aangezien dit eenvoudige voorbeeld wordt uitgevoerd om te worden voltooid en beëindigd, worden `CountDownLatch`-exemplaren gebruikt om ervoor te zorgen dat het programma niet wordt beëindigd tijdens het maken van het item. **De juiste asynchrone programmeringsprocedure is om niet blokkeren op asynchrone aanroepen. In werkelijkheid worden gebruiksvoorbeeldaanvragen gegenereerd op basis van een hoofd()-lus die voor onbepaalde tijd wordt uitgevoerd, waardoor men niet afhankelijk is van asynchrone aanroepen.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Net als met de synchrone API, worden puntleesbewerkingen uitgevoerd met behulp van `readItem`-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Net als met de synchrone API, worden SQL-query's via JSON uitgevoerd met behulp van de methode `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>De app uitvoeren

Ga nu terug naar de Azure-portal om de verbindingsreeksgegevens op te halen en start de app met uw eindpuntgegevens. Hierdoor kan de app communiceren met de gehoste database.

1. Ga met de opdracht `cd` in het git-terminalvenster naar de map met voorbeeldcode.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om de vereiste Java-pakketten te installeren.

    ```bash
    mvn package
    ```

3. Start in het git-terminalvenster met de volgende opdracht de Java-toepassing (vervang SYNCASYNCMODE door `sync` of `async` afhankelijk van welke voorbeeldcode u uit wilt voeren, vervang YOUR_COSMOS_DB_HOSTNAME door de URI-waarde van de portal tussen aanhalingstekens, en vervang YOUR_COSMOS_DB_MASTER_KEY door de primaire sleutel van de portal tussen aanhalingstekens)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Het terminalvenster geeft een melding dat de database FamilyDB is gemaakt. 
    
4. De app maakt een database met de naam `AzureSampleFamilyDB`
5. De app maakt een container met de naam `FamilyContainer`
6. De app voert puntleesbewerkingen uit met behulp van object-id's en de partitiesleutelwaarde (in het voorbeeld lastName). 
7. Met de app worden items gezocht voor het ophalen van alle families met achternaam in ("Andersen", "Wakefield", "Johnson")

7. De resources die zijn gemaakt, worden niet verwijderd door de app. Ga terug naar de portal om [de resources te verwijderen](#clean-up-resources).  uit uw account zodat u daarvoor geen kosten in rekening worden gebracht.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB SQL API-account, een documentdatabase maakt en een container kunt maken met behulp van Data Explorer, en hoe u een Java-app kunt uitvoeren die hetzelfde doet via een programma. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
