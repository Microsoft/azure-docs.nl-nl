---
title: Azure Table Storage of de Azure Cosmos DB Table-API van Java gebruiken
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API van Java.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: a5da5e1717f897d2236fd73f0fff525e157f7a0e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093686"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Azure Table Storage of de Azure Cosmos DB Table-API van Java gebruiken

[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Dit artikel laat zien hoe u tabellen maakt, uw gegevens opslaat en CRUD-bewerkingen op de gegevens uitvoert. Kies de Azure Table service of de Azure Cosmos DB Table-API. De voorbeelden zijn geschreven in Java en maken gebruik van de [Azure Storage SDK v8 voor Java][Azure Storage SDK for Java]. De volgende scenario's worden behandeld: het **maken**, **in een lijst opnemen** en **verwijderen** van tabellen, alsmede het **invoegen**, **uitvoeren van query's**, **aanpassen** en **verwijderen** van entiteiten in een tabel. Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over tabellen.

> [!IMPORTANT]
> De laatste versie van de Azure Storage SDK die ondersteuning biedt voor Table Storage is [V8][Azure Storage SDK for Java]. Binnenkort is een nieuwe versie van de Table Storage SDK voor Java beschikbaar.

> [!NOTE]
> Er is een SDK beschikbaar voor ontwikkelaars die Azure Storage op Android-apparaten gebruiken. Raadpleeg de [Azure Storage SDK voor Android][Azure Storage SDK for Android] voor meer informatie.
>

## <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Een Azure-opslagaccount maken**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB-account maken**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

In deze handleiding gebruikt u opslagfuncties die u lokaal kunt uitvoeren in een Java-toepassing, of in code die wordt uitgevoerd in een webrol of werkrol in Azure.

Als u de voorbeelden in dit artikel wilt gebruiken, installeert u de Java Development Kit (JDK) en maakt u vervolgens een Azure-opslagaccount of Azure Cosmos DB-account in uw Azure-abonnement. Zodra u dit hebt gedaan, controleert u of uw ontwikkelingssysteem voldoet aan de minimumvereisten en afhankelijkheden die worden genoemd in de GitHub-opslagplaats [Azure Storage SDK voor Java][Azure Storage SDK for Java]. Indien uw systeem aan die vereisten voldoet, kunt u de instructies volgen om de Azure Storage-bibliotheken voor Java vanuit die opslagplaats te downloaden en op uw systeem te installeren. Nadat u die taken hebt voltooid, kunt u een Java-toepassing maken waarin de voorbeelden in dit artikel worden gebruikt.

## <a name="configure-your-application-to-access-table-storage"></a>Uw toepassing configureren voor toegang tot tabelopslag

Voeg bovenaan het Java-bestand de volgende importinstructies toe waar u Azure Storage-API's of de Azure Cosmos DB Table-API wilt gebruiken voor toegang tot tabellen:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-your-connection-string"></a>Uw verbindingsreeks toevoegen

U kunt verbinding maken met het Azure Storage-account of het Azure Cosmos DB Table-API-account. Haal de verbindingsreeks op voor het soort account dat u gebruikt.

### <a name="add-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks toevoegen

Een Azure-opslagclient gebruikt een opslagverbindingstekenreeks om eindpunten en referenties voor toegang tot gegevensbeheerservices op te slaan. Bij uitvoering in een clienttoepassing moet u de opslagverbindingstekenreeks opgeven in de volgende indeling, met de naam van uw opslagaccount en de primaire toegangssleutel voor het opslagaccount dat in [Azure Portal](https://portal.azure.com) wordt vermeld voor de waarden **AccountName** en **AccountKey**. 

In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Een Azure Cosmos DB Table-API-verbindingstekenreeks toevoegen

Een Azure Cosmos DB-account gebruikt een verbindingstekenreeks voor het opslaan van de tabeleindpunt en uw referenties. Bij uitvoering in een clienttoepassing moet u de Azure Cosmos DB-verbindingstekenreeks opgeven in de volgende indeling, met de naam van uw Azure Cosmos DB-account en de primaire toegangssleutel voor het account dat in [Azure Portal](https://portal.azure.com) wordt vermeld voor de waarden **AccountName** en **AccountKey**. 

In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de Azure Cosmos DB-verbindingstekenreeks:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

In een toepassing die wordt uitgevoerd binnen een rol in Azure kunt u deze tekenreeks opslaan in het serviceconfiguratiebestand *ServiceConfiguration.cscfg* en u kunt deze openen met behulp van een aanroep naar de **RoleEnvironment.getConfigurationSettings**-methode. Hier volgt een voorbeeld van het ophalen van de verbindingstekenreeks van een **Setting**-element met de naam *StorageConnectionString* in het serviceconfiguratiebestand:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

U kunt uw verbindingstekenreeks ook opslaan in het bestand config.properties van uw project:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.

## <a name="create-a-table"></a>Een tabel maken

Met een `CloudTableClient`-object kunt u referentieobjecten ophalen voor tabellen en entiteiten. Met de volgende code maakt u een `CloudTableClient`-object dat wordt gebruikt om een nieuw `CloudTable`-object te maken dat de tabel 'mensen' vertegenwoordigt. 

> [!NOTE]
> Er zijn andere manieren om `CloudStorageAccount`-objecten te maken. Raadpleeg `CloudStorageAccount` in de [Azure Storage Client SDK-referentie] voor meer informatie.
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>De tabellen vermelden

Als u een lijst met tabellen wilt weergeven, roept u de methode **CloudTableClient.listTables()** aan om een lijst met tabelnamen op te halen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Entiteiten worden aan Java-objecten toegewezen met een aangepaste klasse waarmee `TableEntity` wordt geïmplementeerd. Voor het gemak implementeert de klasse `TableServiceEntity` `TableEntity` en wordt reflectie gebruikt om eigenschappen toe te wijzen aan de ophaal- en installatiemethoden die voor de eigenschappen zijn benoemd. Als u een entiteit wilt toevoegen aan een tabel, maakt u eerst een klasse die de eigenschappen van uw entiteit definieert. Met de volgende code definieert u een entiteitsklasse die de voornaam van de klant als de rijsleutel en de achternaam als de partitiesleutel gebruikt. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Voor entiteiten met dezelfde partitiesleutel kunnen sneller query’s worden uitgevoerd dan voor entiteiten met verschillende partitiesleutels.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Tabelbewerkingen waarbij entiteiten betrokken zijn, hebben een `TableOperation`-object nodig. Dit object definieert de bewerking die op een entiteit moet worden uitgevoerd. Dit kan worden uitgevoerd met een `CloudTable`-object. Met de volgende code maakt u een nieuwe instantie van de klasse `CustomerEntity`, waarbij een aantal klantgegevens worden opgeslagen. Vervolgens wordt met de code `TableOperation` aangeroepen om een `TableOperation`-object te maken om een entiteit in te voegen in een tabel. Hier wordt de nieuwe `CustomerEntity` aan gekoppeld. Als laatste wordt met de code de methode `execute` aangeroepen op het `CloudTable`-object, waarbij de tabel 'mensen' en de nieuwe `TableOperation` worden gespecificeerd. Vervolgens wordt een aanvraag naar de opslagservice verzonden om de nieuwe klantentiteit in de tabel 'mensen' in te voegen, of om de entiteit te vervangen als deze al bestaat.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen

U kunt in één schrijfbewerking een batch entiteiten invoegen in de tabelservice. Met de volgende code maakt u een `TableBatchOperation`-object en voegt hier vervolgens drie invoegbewerkingen aan toe. Elke invoegbewerking wordt toegevoegd door het maken van een nieuw entiteitsobject, de waarden ervan in te stellen en vervolgens de methode `insert` aan te roepen op het `TableBatchOperation`-object om de entiteit aan een nieuwe invoegbewerking te koppelen. Vervolgens roept u met de code `execute` aan op het `CloudTable`-object, waarbij de tabel 'mensen' en het `TableBatchOperation`-object worden gespecificeerd. Zo wordt de batch met tabelbewerkingen in één aanvraag verzonden naar de opslagservice.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Een aantal zaken die u moet weten over batchbewerkingen:

* U kunt maximaal 100 bewerkingen voor invoegen, verwijderen, samenvoegen, vervangen, invoegen of samenvoegen, en invoegen of vervangen in elke willekeurige combinatie in één batch uitvoeren.
* Een batchbewerking kan een ophaalbewerking hebben als dit de enige bewerking in de batch is.
* Alle entiteiten in een batchbewerking moeten dezelfde partitiesleutel hebben.
* Een batchbewerking is beperkt tot een gegevensnettolading van 4 MB.

## <a name="retrieve-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Gebruik een `TableQuery` om een tabel met entiteiten in een partitie op te vragen. Roep `TableQuery.from` aan om een query op een specifieke tabel te maken waarmee een opgegeven resultaattype wordt geretourneerd. Met de volgende code geeft u een filter op voor entiteiten waarbij 'Smith' de partitiesleutel is. `TableQuery.generateFilterCondition` is een helpermethode voor het maken van filters voor query's. Roep `where` aan op de referentie die door de methode `TableQuery.from` is geretourneerd om het filter op de query toe te passen. Wanneer de query wordt uitgevoerd met een aanroep van `execute` op het `CloudTable`-object, wordt een `Iterator` met het resultaattype `CustomerEntity` geretourneerd. U kunt vervolgens de geretourneerde `Iterator` in een 'ForEach'-lus gebruiken om de resultaten te gebruiken. In deze code worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Een bereik van entiteiten in een partitie ophalen

Als u geen query wilt uitvoeren op alle entiteiten in een partitie, kunt u een bereik opgeven door vergelijkingsoperators in een filter te gebruiken. Met de volgende code maakt u gebruik van twee filters om alle entiteiten met de partitie 'Smith' op te halen waarbij de rijsleutel (voornaam) begint met een letter die in het alfabet vóór de 'E' komt. Vervolgens worden de queryresultaten afgedrukt. Als u de entiteiten wilt gebruiken die aan de tabel zijn toegevoegd in het gedeelte over het invoegen van batches in deze handleiding, worden deze keer slechts twee entiteiten geretourneerd (Ben en Denise Smith); Jeff Smith wordt niet vermeld.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Eén entiteit ophalen

U kunt een query schrijven om één specifieke entiteit op te halen. Met de volgende code roept u `TableOperation.retrieve` aan met partitiesleutel- en rijsleutelparameters voor het opgeven van de klant ‘Jeff Smith’, in plaats van een `Table Query` te maken en filters te gebruiken om dezelfde actie uit te voeren. Wanneer de ophaalbewerking wordt uitgevoerd, wordt slechts één entiteit geretourneerd in plaats van een hele verzameling. De `getResultAsType`-methode wijst het resultaat toe aan het type van het toepassingsdoel, een `CustomerEntity`-object. Als dit type niet compatibel is met het type dat voor de query is opgegeven, wordt een uitzondering opgegeven. Er wordt een waarde van null geretourneerd als geen enkele entiteit een exacte combinatie van partitie en rijsleutel heeft. Het opgeven van zowel partitie- als rijsleutels in een query is de snelste manier om één entiteit op te halen uit de Tabelservice.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Een entiteit aanpassen

Als u een entiteit wilt aanpassen, haalt u deze op uit de tabelservice, wijzigt u het entiteitsobject en slaat u de wijzigingen weer op in de tabelservice met een vervangings- of samenvoegingsbewerking. De volgende code wijzigt het telefoonnummer van een bestaande klant. In plaats van **TableOperation.insert** op te roepen, zoals we deden voor een invoegbewerking, roept u met deze code **TableOperation.replace** aan. Met de methode **CloudTable.execute** roept u de tabelservice aan en de entiteit wordt vervangen, tenzij deze in de tussentijd door een andere toepassing is gewijzigd sinds deze toepassing de entiteit heeft opgehaald. Wanneer dat gebeurt, wordt een uitzondering opgegeven en moet de entiteit opnieuw worden opgehaald, aangepast en opgeslagen. Dit herhalingspatroon voor optimistische gelijktijdige uitvoering komt vaker voor in een gedistribueerd opslagsysteem.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Een query uitvoeren op een subset van entiteitseigenschappen

Met een query naar een tabel kunnen slechts enkele eigenschappen van een entiteit worden opgehaald. Deze methode, projectie genoemd, verbruikt minder bandbreedte en kan de queryprestaties verbeteren, vooral bij grote entiteiten. Met de query in de volgende code gebruikt u de methode `select` om alleen de e-mailadressen van de entiteiten in de tabel te retourneren. De resultaten worden geprojecteerd in een verzameling van `String` met een `Entity Resolver`. Deze voert de typeconversie uit op de entiteiten die van de server worden geretourneerd. U vindt meer informatie over projectie in [Azure Tables: Introductie tot upsert en queryprojectie][Azure Tables: Introductie tot upsert en queryprojectie]. Projectie wordt niet ondersteund in de emulator van de lokale opslag. Deze code wordt dus alleen uitgevoerd als u een account gebruikt in de tabelservice.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Een entiteit invoegen of vervangen

Vaak zult u een entiteit aan een tabel willen toevoegen zonder te weten of deze entiteit al in de tabel bestaat. Met een bewerking voor invoegen of vervangen, kunt u één aanvraag maken waarmee de entiteit wordt ingevoegd als deze niet bestaat of wordt vervangen als er al een bestaat. Op basis van de vorige voorbeelden voegt u met de volgende code de entiteit voor ‘Walter Harp’ in of vervangt u met de code deze entiteit. Na het maken van een nieuwe entiteit roept u met deze code de methode **TableOperation.insertOrReplace** aan. Met deze code roept u vervolgens **execute** op het **CloudTable**-object aan met de tabel en de tabelbewerking voor invoegen of vervangen als parameters. Als u slechts een deel van een entiteit wilt bijwerken, kunt u de methode **TableOperation.insertOrMerge** gebruiken. Invoegen of vervangen wordt niet ondersteund in de emulator van de lokale opslag. Deze code wordt dus alleen uitgevoerd als u een account gebruikt in de tabelservice. U leest meer over invoegen of vervangen en invoegen of samenvoegen in het artikel [Azure Tables: Introductie tot upsert en queryprojectie][Azure Tables: Introductie tot upsert en queryprojectie].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit gemakkelijk verwijderen nadat u deze hebt opgehaald. Nadat de entiteit is opgehaald, roept u `TableOperation.delete` aan met de entiteit die u wilt verwijderen. Roep vervolgens `execute` aan op het `CloudTable`-object. Met de volgende code wordt een klantentiteit opgehaald en verwijderd.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Een tabel verwijderen

Ten slotte wordt met de volgende code een tabel uit een opslagaccount verwijderd. Gedurende ongeveer 40 seconden nadat u een tabel hebt verwijderd, kunt u deze niet opnieuw maken. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
* [Azure Storage SDK voor Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referentie][Azure Storage Client SDK Reference]
* [Azure Storage REST API][Azure Storage REST API]
* [Blog van het Azure Storage-team][Azure Storage Team Blog]

Voor meer informatie gaat u naar [Azure voor Java-ontwikkelaars](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/Azure/azure-storage-java/tree/v8.6.5
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referentie]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/