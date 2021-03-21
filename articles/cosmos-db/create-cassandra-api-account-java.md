---
title: 'Zelfstudie: een Java-app bouwen om een Cassandra-API-account voor Azure Cosmos DB te maken'
description: In deze zelfstudie leert u hoe u een Cassandra-API-account maakt en een database (ook wel een keyspace genoemd) en een tabel toevoegt aan dat account met behulp van een Java-toepassing.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: ca1fdbd9aa2c98358489d91fe0839c98adec293b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212702"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Zelfstudie: een Cassandra-API-account maken in Azure Cosmos DB met behulp van een Java-toepassing om sleutel-/waardegegevens op te slaan
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van sleutel-/waardeparen. U kunt een Cassandra-API-account in Azure Cosmos DB gebruiken om de sleutel-/waardegegevens op te slaan. In deze zelfstudie wordt beschreven hoe u met een Java-toepassing een Cassandra-API-account maakt in Azure Cosmos DB en een database (ook wel een keyspace genoemd) en een tabel toevoegt. De Java-toepassing gebruikt het [Java-stuurprogramma](https://github.com/datastax/java-driver) om een gebruikersdatabase te maken die gegevens bevat, zoals gebruikers-id, gebruikersnaam en plaats van de gebruiker.  

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Cassandra-databaseaccount maken
> * Verbindingsreeks voor account ophalen
> * Maven-project en afhankelijkheden maken
> * Een database en een tabel toevoegen
> * De app uitvoeren

## <a name="prerequisites"></a>Vereisten 

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint. 

* Download de nieuwste versie van de [Java Development Kit (JDK)](/java/azure/jdk/). 

* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) het binaire archief van [Maven](https://maven.apache.org/). 
  - Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren. 

## <a name="create-a-database-account"></a>Een databaseaccount maken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). 

2. Selecteer **Een resource maken** > **Databases** > **Azure Cosmos DB**. 

3. Voer in het deelvenster **Nieuw account** de instellingen in voor het nieuwe Azure Cosmos-account. 

   |Instelling   |Voorgestelde waarde  |Beschrijving  |
   |---------|---------|---------|
   |Id   |   Voer een unieke naam in    | Voer een unieke naam in om dit Azure Cosmos-account te identificeren. <br/><br/>Omdat cassandra.cosmosdb.azure.com wordt toegevoegd aan de id die u opgeeft om het contactpunt te maken, moet u een unieke, maar herkenbare id gebruiken.         |
   |API    |  Cassandra   |  De API bepaalt het type te maken account. <br/> Selecteer **Cassandra**, aangezien u in dit artikel u een wide-column-database maakt waarop een query kan worden uitgevoerd met de syntaxis van Cassandra Query Language (CQL).  |
   |Abonnement    |  Uw abonnement        |  Selecteer het Azure-abonnement dat u wilt gebruiken voor dit Azure Cosmos-account.        |
   |Resourcegroep   | Voer een naam in    |  Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in. Gebruik dezelfde naam als uw id om het uzelf gemakkelijk te maken.    |
   |Locatie    |  Selecteer de regio het dichtst in de buurt van uw gebruikers    |  Selecteer de geografische locatie waar u het Azure Cosmos-account gaat hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="Account maken met portal":::

4. Selecteer **Maken**. <br/>Het duurt enkele minuten om het account te maken. Als de resource is gemaakt, ziet u rechts in de portal de melding **Implementatie voltooid**.

## <a name="get-the-connection-details-of-your-account"></a>De verbindingsgegevens van uw account ophalen  

Haal de gegevens van de verbindingsreeks op uit de Azure-portal en kopieer deze naar het Java-configuratiebestand. De verbindingsreeks stelt uw app in staat om te communiceren met de gehoste database. 

1. Ga vanuit [Azure Portal](https://portal.azure.com/) naar uw Azure Cosmos-account. 

2. Open het deelvenster **Verbindingsreeks**.  

3. Kopieer de waarden voor **CONTACTPUNT**, **POORT**, **GEBRUIKERSNAAM** en **PRIMAIR WACHTWOORD** voor gebruik in de volgende stappen.

## <a name="create-the-project-and-the-dependencies"></a>Project en afhankelijkheden maken 

Het Java-voorbeeldproject dat u in dit artikel gebruikt, wordt gehost in GitHub. U kunt de stappen in dit document uitvoeren of het voorbeeld downloaden in de opslagplaats [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

Als u de bestanden hebt gedownload, werkt u de gegevens van de verbindingsreeks in het bestand `java-examples\src\main\resources\config.properties` bij en voert u het bestand uit.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Gebruik de volgende stappen om het voorbeeld helemaal opnieuw te maken: 

1. Maak vanuit de terminal of de opdrachtprompt een nieuw Maven-project met de naam Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Zoek de map `cassandra-demo`. Open in een teksteditor het bestand `pom.xml` dat is gegenereerd. 

   Voeg de Cassandra-afhankelijkheden en invoegtoepassingen voor de build toe die zijn vereist voor uw project, zoals wordt weergegeven in het bestand [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml).  

3. Maak onder de map `cassandra-demo\src\main` een nieuwe map met de naam `resources`.  Voeg onder de map resources de bestanden config.properties en log4j.properties toe:

   - In het bestand [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) worden het verbindingseindpunt en de sleutelwaarden van het Cassandra-API-account opgeslagen. 
   
   - Het bestand [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) definieert het vereiste niveau van logboekregistratie voor interactie met de Cassandra-API.  

4. Blader naar de map `src/main/java/com/azure/cosmosdb/cassandra/`. Maak in de map cassandra een andere map met de naam `utils`. Deze nieuwe map is bedoeld voor het opslaan van de utils-klassen die nodig zijn om verbinding te maken met het Cassandra-API-account. 

   Voeg de klasse [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) toe om het cluster te maken, en om Cassandra-sessies te openen en te sluiten. Het cluster maakt verbinding met het Cassandra-API-account in Azure Cosmos DB en retourneert een sessie voor het verkrijgen van toegang. Gebruik de klasse [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) om gegevens van de verbindingsreeks te lezen uit het bestand config.properties. 

5. Het Java-voorbeeld maakt een database met gebruikersgegevens, zoals gebruikersnaam, gebruikers-id en plaats van gebruiker. U moet get- en set-methoden definiëren voor toegang tot gebruikersgegevens in de main-functie.
 
   Maak een klasse [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) onder de map `src/main/java/com/azure/cosmosdb/cassandra/` met get- en set-methoden. 

## <a name="add-a-database-and-a-table"></a>Een database en een tabel toevoegen  

In dit gedeelte wordt beschreven hoe u een database (keyspace) en een tabel toevoegt met behulp van CQL.

1. Maak onder de map `src\main\java\com\azure\cosmosdb\cassandra` een nieuwe map met de naam `repository`. 

2. Maak Java-klasse `UserRepository` en voeg er de volgende code aan toe: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Zoek de map `src\main\java\com\azure\cosmosdb\cassandra` en maak in de map een nieuwe submap met de naam `examples`.

4. Maak Java-klasse `UserProfile`. Deze klasse bevat de main-methode die de methoden createKeyspace en createTable aanroept die u eerder hebt gedefinieerd: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>De app uitvoeren 

1. Open een opdrachtprompt of terminalvenster. Plak het volgende blok met code. 

   Met deze code gaat u naar de map (cd) waarin u het project hebt gemaakt. Vervolgens wordt de opdracht `mvn clean install` uitgevoerd voor het genereren van het bestand `cosmosdb-cassandra-examples.jar` in de doelmap. Ten slotte wordt de Java-toepassing uitgevoerd.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   In het terminalvenster worden meldingen weergegeven dat de keyspace en de tabel zijn gemaakt. 
   
2. Open nu in de Azure-portal **Data Explorer** om te controleren of de keyspace en de tabel zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Cassandra-API-account in Azure Cosmos DB, een database en een tabel maakt met behulp van een Java-toepassing. U kunt nu verdergaan met het volgende artikel:

> [!div class="nextstepaction"]
> [Voorbeeldgegevens laden in de Cassandra-API-tabel](cassandra-api-load-data.md)
