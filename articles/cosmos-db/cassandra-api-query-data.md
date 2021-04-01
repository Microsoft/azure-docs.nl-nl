---
title: 'Zelfstudie: Gegevens opvragen uit een Cassandra-API-account in Azure Cosmos DB'
description: Deze zelfstudie laat zien hoe u met behulp van een Java-toepassing gebruikersgegevens opvraagt uit een Cassandra-API-account in Azure Cosmos DB.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100424"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Zelfstudie: Gegevens opvragen uit een Cassandra-API-account in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van sleutel-/waardeparen. U kunt een Cassandra-API-account in Azure Cosmos DB gebruiken om de sleutel-/waardegegevens op te slaan en op te vragen. Deze zelfstudie laat zien hoe u met behulp van een Java-toepassing gebruikersgegevens opvraagt uit een Cassandra-API-account in Azure Cosmos DB. De Java-toepassing maakt gebruik van het [Java-stuurprogramma](https://github.com/datastax/java-driver) en vraagt gebruikersgegevens op zoals gebruikers-id, gebruikersnaam en woonplaats van de gebruiker. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens opvragen uit een Cassandra-tabel
> * De app uitvoeren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Dit artikel maakt deel uit van een meerdelige zelfstudie. Begin pas met deze zelfstudie nadat u de stappen hebt voltooid voor het maken van het Cassandra-API-account, de keyspace en de tabel, en nadat de [voorbeeldgegevens zijn geladen in de tabel](cassandra-api-load-data.md). 

## <a name="query-data"></a>Querygegevens

Gebruik de volgende stappen om een query uit te voeren op gegevens van uw Cassandra-API-account:

1. Open het bestand `UserRepository.java` in de map `src\main\java\com\azure\cosmosdb\cassandra`. Voeg het volgende codeblok toe aan het einde van het bestand. Deze code biedt drie methoden: 

   * Een query uitvoeren op alle gebruikers in de database
   * Een query uitvoeren op een specifieke gebruiker, gefilterd op gebruikers-ID
   * Een tabel verwijderen

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Open het bestand `UserProfile.java` in de map `src\main\java\com\azure\cosmosdb\cassandra`. Deze klasse bevat de main-methode die de methoden createKeyspace en createTable aanroept, de methoden voor het invoegen van gegevens die u eerder hebt gedefinieerd. Voeg nu de volgende code toe om een query uit te voeren op alle gebruikers of een specifieke gebruiker:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>De Java-app uitvoeren
1. Open een opdrachtprompt of terminalvenster. Plak het volgende blok met code. 

   Met deze code gaat u naar de map (cd) waarin u het project hebt gemaakt. Vervolgens wordt de opdracht `mvn clean install` uitgevoerd voor het genereren van het bestand `cosmosdb-cassandra-examples.jar` in de doelmap. Ten slotte wordt de Java-toepassing uitgevoerd.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Open nu **Data Explorer** in de Azure-portal en controleer of de gebruikerstabel is verwijderd.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, het Azure Cosmos-account en alle gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Daarvoor selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u gegevens kunt opvragen uit een Cassandra-API-account in Azure Cosmos DB. U kunt nu verdergaan met het volgende artikel:

> [!div class="nextstepaction"]
> [Gegevens migreren naar Cassandra API-account](cassandra-import-data.md)


