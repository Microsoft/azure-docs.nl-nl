---
title: 'Quickstart: Spring Data Azure Cosmos DB v3 gebruiken om een documentdatabase te maken met behulp van Azure Cosmos DB'
description: Deze quickstart biedt een voorbeeldcode voor Spring Data Azure Cosmos DB die u kunt gebruiken om verbinding te maken met de SQL API van Azure Cosmos DB en er query's op uit te voeren
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090287"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quickstart: EenSpring Data Azure Cosmos DB v3-app maken voor het beheren van API-gegevens van Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een SQL API-account van Azure Cosmos DB via de Azure-portal en met een Spring Data Azure Cosmos DB v3-app die is gekloond van GitHub. Eerst maakt u een Azure Cosmos DB SQL API-account via de Azure-portal, dan maakt u een Spring Boot-app met behulp van de Spring Data Azure Cosmos DB v3-connector en dan voegt u met de Spring Boot-toepassing resources toe aan uw Cosmos DB-account. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

> [!IMPORTANT]  
> Deze release-opmerkingen horen bij versie 3 van Spring Data Azure Cosmos DB. Hier vindt u [opmerkingen bij de release van versie 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Lees deze artikelen voor informatie over Spring Data op andere Azure Cosmos DB-API's:
> * [Spring Data voor Apache Cassandra met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Sla dit anders over en ga naar [De app uitvoeren](#run-the-app). 

### <a name="application-configuration-file"></a>Configuratiebestand van toepassing

Hier laten we zien hoe Spring Boot en Spring Data de gebruikservaring verbeteren: het proces van het maken van een Cosmos-client en het verbinden met Cosmos-resources is nu een kwestie van configuratie in plaats van code. Bij het opstarten van Spring Boot wordt al deze standaardcode verwerkt met behulp van de instellingen in **application.properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Als u een Azure Cosmos DB-account, -database en -container hebt gemaakt, vult u in het configuratiebestand de lege waarden in en Spring Boot/Spring Data zal automatisch de volgende handelingen uitvoeren: (1) maak een onderliggende Java-SDK `CosmosClient`-instantie met de URI en sleutel, en (2) maak verbinding met de database en de container. Dat is alles: **u hebt geen resourcebeheercode meer nodig!**

### <a name="java-source"></a>Java-bron

De toegevoegde waarde van Spring Data is de eenvoudige, schone, gestandaardiseerde en platformonafhankelijke interface voor het werken met gegevensopslag. Op basis van het bovenstaande voorbeeld van Spring Data GitHub worden hieronder CRUD- en queryvoorbeelden weergegeven voor het bewerken van Azure Cosmos DB-documenten met Spring Data Azure Cosmos DB.

* Items kunnen worden gemaakt en bijgewerkt met behulp van de `save`-methode.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Puntleesbewerkingen met behulp van de afgeleide querymethode die in de opslagplaats is gedefinieerd. De `findByIdAndLastName` voert puntleesbewerkingen uit voor `UserRepository`. De velden die in de naam van de methode worden genoemd, zorgen ervoor dat Spring Data een puntleesbewerking uitvoert, gedefinieerd door de velden `id` en `lastName`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Items kunnen worden verwijderd met behulp van `deleteAll`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Afgeleide query op basis van de naam van de opslagplaatsmethode. Spring Data implementeert de `UserRepository` `findByFirstName`-methode als een Java SDK SQL-query in het veld `firstName` (deze query kan niet worden geïmplementeerd als puntleesbewerking):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>De app uitvoeren

Ga nu terug naar de Azure-portal om de verbindingsreeksgegevens op te halen en start de app met uw eindpuntgegevens. Hierdoor kan de app communiceren met de gehoste database.

1. Ga met de opdracht `cd` in het git-terminalvenster naar de map met voorbeeldcode.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om de vereiste pakketten voor Spring Data Azure Cosmos DB te installeren.

    ```bash
    mvn clean package
    ```

3. Gebruik in het git-terminalvenster de volgende opdracht om de Spring Data Azure Cosmos DB toepassing te starten:

    ```bash
    mvn spring-boot:run
    ```
    
4. De app laadt **application.properties** en verbindt de resources in uw Azure Cosmos DB-account.
5. In de app worden de hierboven beschreven punt CRUD-bewerkingen uitgevoerd.
6. De app zal een afgeleide query uitvoeren.
7. Uw resources worden niet verwijderd met de app. Ga terug naar de portal om [de resources](#clean-up-resources) van uw account op te schonen als u kosten wilt vermijden.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB SQL API-account, een documentdatabase maakt en een container kunt maken met behulp van Data Explorer, en hoe u een Spring Data-app kunt uitvoeren die hetzelfde doet via een programma. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)