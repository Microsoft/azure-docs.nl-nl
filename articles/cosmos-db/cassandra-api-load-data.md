---
title: 'Zelfstudie: Java-app om voorbeeldgegevens te laden in een Cassandra-API-tabel in Azure Cosmos DB'
description: Deze zelfstudie laat zien hoe u voorbeeldgegevens van een gebruiker laadt in een Cassandra-API-tabel in Azure Cosmos DB met behulp van een Java-toepassing.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092463"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Zelfstudie: Voorbeeldgegevens laden in een Cassandra-API-tabel in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van sleutel-/waardeparen. U kunt een Cassandra-API-account gebruiken in Azure Cosmos DB om de sleutel-/waardegegevens op te slaan en te beheren. Deze zelfstudie laat zien hoe voorbeeldgegevens van een gebruiker worden geladen in een tabel van een Cassandra-API-account in Azure Cosmos DB met behulp van een Java-toepassing. De Java-toepassing maakt gebruik van het [Java-stuurprogramma](https://github.com/datastax/java-driver) en laadt gebruikersgegevens zoals gebruikers-id, gebruikersnaam, en woonplaats van de gebruiker. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens laden in een Cassandra-tabel
> * De app uitvoeren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Dit artikel maakt deel uit van een meerdelige zelfstudie. Voordat u met dit document begint, moet u eerst [de Cassandra-API-account, de keyspace en de tabel maken](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Gegevens laden in de tabel

Gebruik de volgende stappen om gegevens in uw Cassandra-API-tabel te laden:

1. Open het bestand UserRepository.java in de map src\main\java\com\azure\cosmosdb\cassandra en voeg de code toe om de velden user_id, user_name en user_bcity in de tabel in te voegen:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Open het bestand UserProfile.java in de map src\main\java\com\azure\cosmosdb\cassandra. Deze klasse bevat de belangrijkste methode waarmee de methoden createKeyspace en createTable die u eerder hebt gedefinieerd, worden aangeroepen. Voeg nu de volgende code toe om wat voorbeeldgegevens in te voegen in de Cassandra-API-tabel.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>De app uitvoeren

Open een opdrachtprompt of terminalvenster en wijzig het mappad in het pad waar u het project hebt gemaakt. Voer de opdracht mvn clean install uit om het bestand cosmosdb-cassandra-examples.jar in de doelmap te genereren, en voer de toepassing uit. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

U kunt nu Data Explorer openen in de Azure-portal openen om te bevestigen dat de gegevens van de gebruiker aan de tabel worden toegevoegd.
    
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u voorbeeldgegevens kunt laden in een Cassandra-API-account van Azure Cosmos DB. U kunt nu verdergaan met het volgende artikel:

> [!div class="nextstepaction"]
> [Gegevens uit de Cassandra-API-account opvragen](cassandra-api-query-data.md)
