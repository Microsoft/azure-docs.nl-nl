---
title: Gebruik de Java-bibliotheek voor bulksgewijs uitvoeren in Azure Cosmos DB voor het uitvoeren van bulk import-en update bewerkingen
description: Azure Cosmos DB documenten bulksgewijs importeren en bijwerken met behulp van de Java-bibliotheek voor bulk-uitvoerder
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: d0ccaf15df62b566b5a52ee7a4031ba7e270a4ae
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218106"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Gebruik de BulkExecutor-bibliotheek voor Java om bulkbewerkingen uit te voeren in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In deze zelf studie vindt u instructies voor het gebruik van de uitvoerige Java-bibliotheek van de Azure Cosmos DB om Azure Cosmos DB documenten te importeren en bij te werken. Zie [overzichts artikel bulk](bulk-executor-overview.md) -uitvoerder bibliotheek voor meer informatie over de bibliotheek voor bulk doorvoer In deze zelf studie bouwt u een Java-toepassing die wille keurige documenten genereert en deze worden bulksgewijs geïmporteerd in een Azure Cosmos-container. Na het importeren worden enkele eigenschappen van een document bulksgewijs bijgewerkt. 

Op dit moment wordt de bibliotheek voor bulksgewijs uitvoering alleen ondersteund door Azure Cosmos DB-API-accounts voor SQL-API'S en Gremlin. In dit artikel wordt beschreven hoe u een bulk-uitvoerder Java-bibliotheek met SQL API-accounts gebruikt. Zie voor meer informatie over het gebruik van een bulk-uitvoerder .NET-bibliotheek met Gremlin-API [bulk bewerkingen uitvoeren in azure Cosmos DB Gremlin-API](bulk-executor-graph-dotnet.md). De hier beschreven bibliotheek voor bulk-uitvoerder is alleen beschikbaar voor de [Azure Cosmos DB Java Sync SDK v2](sql-api-sdk-java.md) en is de momenteel aanbevolen oplossing voor Java-bulk ondersteuning. De service is momenteel niet beschikbaar voor de versies van 3. x, 4. x of hoger.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.  

* U kunt [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en toezeg gingen. U kunt ook de Azure Cosmos DB- [emulator](./local-emulator.md) gebruiken met het `https://localhost:8081` eind punt. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticate-requests).  

* [Java Development Kit (JDK) 1.7 +](/java/azure/jdk/)  
  - Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.  

  - Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.

* Een binair archief van [maven](https://maven.apache.org/) [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)  
  
  - Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.

* Maak een Azure Cosmos DB SQL-API-account met behulp van de stappen die worden beschreven in de sectie [Database account maken](create-sql-api-java.md#create-a-database-account) van het artikel Java Quick Start.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu naar het werken met code door een Java-voorbeeld toepassing te downloaden van GitHub. Met deze toepassing worden bulk bewerkingen uitgevoerd op Azure Cosmos DB gegevens. Als u de toepassing wilt klonen, opent u een opdracht prompt, gaat u naar de map waar u de toepassing wilt kopiëren en voert u de volgende opdracht uit:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

De gekloonde opslag plaats bevat twee voor beelden van ' bulkimport ' en ' Bulkupdate ' ten opzichte van de map ' \azure-cosmosdb-bulkexecutor-Java-Getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor '. De toepassing ' bulkimport ' genereert wille keurige documenten en importeert deze in Azure Cosmos DB. Met de toepassing ' Bulkupdate ' worden sommige documenten in Azure Cosmos DB bijgewerkt. In de volgende secties wordt de code in elk van deze voor beeld-apps gecontroleerd. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Gegevens bulksgewijs importeren naar Azure Cosmos DB

1. De verbindings reeksen van de Azure Cosmos DB worden gelezen als argumenten en toegewezen aan variabelen die zijn gedefinieerd in het bestand CmdLineConfiguration. java.  

2. Daarna wordt het DocumentClient-object geïnitialiseerd met behulp van de volgende-instructies:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Het DocumentBulkExecutor-object wordt geïnitialiseerd met een hoge waarde voor nieuwe pogingen voor wacht tijden en vertraagde aanvragen. En ze worden vervolgens ingesteld op 0 om de levens duur van de congestie controle door te geven aan DocumentBulkExecutor.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Roep de importal API aan waarmee wille keurige documenten worden gegenereerd om bulksgewijs te importeren in een Azure Cosmos-container. U kunt de opdracht regel configuraties configureren in het bestand CmdLineConfiguration. java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   De Bulk Import-API accepteert een verzameling JSON-geserialiseerde documenten en heeft de volgende syntaxis, zie de [API-documentatie](/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor meer informatie:

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   De importal-methode accepteert de volgende para meters:
 
   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |isUpsert    |   Een markering om upsert van de documenten in te scha kelen. Als er al een document met de opgegeven ID bestaat, wordt het bijgewerkt.  |
   |disableAutomaticIdGeneration     |   Een markering om het automatisch genereren van ID uit te scha kelen. Deze waarde is standaard ingesteld op True.   |
   |maxConcurrencyPerPartitionRange    |  De maximale graad van gelijktijdigheid per partitie sleutel bereik. De standaard waarde is 20.  |

   **Definitie van antwoord object voor bulk import** Het resultaat van de API-aanroep voor Bulk Import bevat de volgende Get-methoden:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsImported ()  |   Het totale aantal documenten dat is geïmporteerd uit de documenten die zijn geleverd aan de API-aanroep voor bulk import.      |
   |dubbele getTotalRequestUnitsConsumed ()   |  Het totale aantal aanvraag eenheden (RU) dat wordt gebruikt door de API-aanroep voor bulk import.       |
   |Duration getTotalTimeTaken ()   |    De totale tijd die nodig is voor de API-aanroep voor bulk import om de uitvoering te volt ooien.     |
   |Lijst met \<Exception> getErrors () |  Hiermee wordt de lijst met fouten opgehaald als sommige documenten uit de batch die zijn opgegeven bij de API-aanroep voor bulk import, niet kunnen worden ingevoegd.       |
   |Lijst met \<Object> getBadInputDocuments ()  |    De lijst met documenten met onjuiste indeling die niet met succes zijn geïmporteerd in de API-aanroep voor bulk import. De gebruiker moet de geretourneerde documenten herstellen en het importeren opnieuw proberen. Documenten met een onjuiste indeling bevatten documenten waarvan de ID-waarde geen teken reeks is (null of een ander gegevens type wordt als ongeldig beschouwd).     |

5. Nadat u de toepassing voor bulk import gereed hebt, bouwt u het opdracht regel programma vanuit de bron met behulp van de opdracht ' MVN Cleanup package '. Met deze opdracht wordt een jar-bestand in de doelmap gegenereerd:  

   ```bash
   mvn clean package
   ```

6. Nadat de doel afhankelijkheden zijn gegenereerd, kunt u de toepassing voor bulk import aanroepen met behulp van de volgende opdracht:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   De functie voor bulk import maakt een nieuwe data base en een verzameling met de database naam, de naam van de verzameling en de doorvoer waarden die zijn opgegeven in het App.config-bestand. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Gegevens bulksgewijs bijwerken in Azure Cosmos DB

U kunt bestaande documenten bijwerken met behulp van de BulkUpdateAsync-API. In dit voor beeld stelt u het veld naam in op een nieuwe waarde en verwijdert u het veld Beschrijving uit de bestaande documenten. Zie [API-documentatie](/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor de volledige set ondersteunde update bewerkingen voor velden. 

1. Hiermee worden de update-items samen met bijbehorende veld Update bewerkingen gedefinieerd. In dit voor beeld gebruikt u SetUpdateOperation om het veld naam en UnsetUpdateOperation bij te werken, zodat het veld Beschrijving uit alle documenten wordt verwijderd. U kunt ook andere bewerkingen uitvoeren, zoals het ophogen van een document veld met een specifieke waarde, het pushen van specifieke waarden naar een matrix veld of het verwijderen van een specifieke waarde uit een matrix veld. Zie de [API-documentatie](/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor meer informatie over de verschillende methoden van de API voor bulksgewijs bijwerken.  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Roep de updateAll-API aan waarmee wille keurige documenten worden gegenereerd die vervolgens bulksgewijs worden geïmporteerd in een Azure Cosmos-container. U kunt de opdracht regel configuraties configureren om te worden door gegeven in het bestand CmdLineConfiguration. java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   De API voor bulksgewijs bijwerken accepteert een verzameling items die moeten worden bijgewerkt. Elk update-item geeft de lijst van veld Update bewerkingen aan die moeten worden uitgevoerd op een document dat wordt geïdentificeerd door een ID en een partitie sleutel waarde. Zie de [API-documentatie](/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor meer informatie:

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   De methode updateAll accepteert de volgende para meters:

   |**Parameter** |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  De maximale graad van gelijktijdigheid per partitie sleutel bereik. De standaard waarde is 20.  |
 
   **Definitie van antwoord object voor bulk import** Het resultaat van de API-aanroep voor Bulk Import bevat de volgende Get-methoden:

   |**Parameter** |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated ()  |   Het totale aantal documenten dat is bijgewerkt van de documenten die zijn geleverd aan de API-aanroep voor bulksgewijs bijwerken.      |
   |dubbele getTotalRequestUnitsConsumed () |  Het totale aantal aanvraag eenheden (RU) dat wordt gebruikt door de API-aanroep voor bulk updates.       |
   |Duration getTotalTimeTaken ()  |   De totale tijd die nodig is voor de API-aanroep voor bulk updates om de uitvoering te volt ooien.      |
   |Lijst met \<Exception> getErrors ()   |       Hiermee wordt de lijst met operationele of netwerk problemen met betrekking tot de update bewerking opgehaald.      |
   |Lijst met \<BulkUpdateFailure> getFailedUpdates ()   |       Hiermee wordt de lijst met updates opgehaald die niet kunnen worden voltooid, samen met de specifieke uitzonde ringen die het gevolg zijn van de fouten.|

3. Nadat u de toepassing voor bulk update gereed hebt, bouwt u het opdracht regel programma vanuit de bron met behulp van de opdracht ' MVN Cleanup package '. Met deze opdracht wordt een jar-bestand in de doelmap gegenereerd:  

   ```bash
   mvn clean package
   ```

4. Nadat de doel afhankelijkheden zijn gegenereerd, kunt u de toepassing bulksgewijze Update aanroepen met behulp van de volgende opdracht:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij het gebruik van de bibliotheek voor bulk-uitvoerder:

* Voor de beste prestaties voert u uw toepassing uit vanaf een virtuele machine van Azure in dezelfde regio als de schrijf regio van uw Cosmos DB-account.  
* Voor een hogere door Voer:  

   * Stel de heapgrootte van de JVM in op een groot genoeg nummer om te voor komen dat er geheugen problemen optreden bij het verwerken van grote hoeveel heden documenten. Voorgestelde Heap-grootte: Max (3 GB, 3 * sizeof (alle documenten die worden door gegeven aan de API voor bulk import in één batch)).  
   * Er is een voorbereidings tijd, omdat u een hogere door Voer krijgt bij het uitvoeren van bulk bewerkingen met een groot aantal documenten. Als u dus 10.000.000 documenten wilt importeren, is het uitvoeren van een Bulk Import tien keer per 10 grote hoeveelheid documenten die elk van de grootte 1.000.000 de voor keur heeft dan het uitvoeren van Bulk Import 100 100 keer 100.000 per onverpakt documenten.  

* Het is raadzaam om één DocumentBulkExecutor-object voor de hele toepassing te instantiëren binnen één virtuele machine die overeenkomt met een specifieke Azure Cosmos-container.  

* Omdat één bulk bewerking van een API een groot deel van de CPU-en netwerk-IO van de client computer verbruikt. Dit gebeurt door meerdere taken intern te starten, te voor komen dat er meerdere gelijktijdige taken binnen uw toepassings proces worden uitgevoerd voor het uitvoeren van API-aanroepen in bulk bewerkingen. Als één bulk bewerking van een API die op één virtuele machine wordt uitgevoerd, niet in staat is om de door Voer van de gehele container te gebruiken (als de door Voer van uw container > 1.000.000 RU/s), is het raadzaam om afzonderlijke virtuele machines te maken voor het gelijktijdig uitvoeren van API-aanroepen voor bulk bewerkingen.

    
## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over maven-pakket gegevens en release opmerkingen van de Java-bibliotheek voor bulk-uitvoerder[SDK](sql-api-sdk-bulk-executor-java.md).