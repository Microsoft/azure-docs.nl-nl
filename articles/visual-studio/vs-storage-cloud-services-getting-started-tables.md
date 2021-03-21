---
title: Aan de slag met tabel opslag met Visual Studio (Cloud Services)
description: Hoe u aan de slag gaat met Azure Table Storage in een Cloud service project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545773"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Table Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Table Storage in Visual Studio nadat u een Azure-opslag account in een Cloud Services-project hebt gemaakt of gerefereerd met behulp van het dialoog venster **verbonden services toevoegen** van Visual Studio. Met de bewerking **verbonden services toevoegen** installeert u de juiste NuGet-pakketten voor toegang tot Azure Storage in uw project en voegt u de Connection String voor het opslag account toe aan uw project configuratie bestanden.

Met de Azure Table Storage-service kunt u grote hoeveel heden gestructureerde gegevens opslaan. De service is een NoSQL-gegevens opslag die geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.

Als u aan de slag wilt gaan, moet u eerst een tabel maken in uw opslag account. We laten u zien hoe u een Azure-tabel in code maakt en hoe u ook eenvoudige tabel-en entiteits bewerkingen uitvoert, zoals het toevoegen, wijzigen, lezen en lezen van tabel entiteiten. De voor beelden zijn geschreven in C- \# code en gebruiken de [Microsoft Azure Storage-client bibliotheek voor .net](/previous-versions/azure/dn261237(v=azure.100)).

**Opmerking:** Sommige Api's die aanroepen naar Azure Storage, zijn asynchroon. Zie [asynchrone programmering met async en wacht](/previous-versions/hh191443(v=vs.140)) op voor meer informatie. In de onderstaande code wordt ervan uitgegaan dat er asynchrone programmeer methoden worden gebruikt.

* Zie [aan de slag met Azure Table Storage met behulp van .net](../cosmos-db/tutorial-develop-table-dotnet.md) voor meer informatie over het programmatisch bewerken van tabellen.
* Zie [opslag documentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.
* Raadpleeg de [documentatie van Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) voor algemene informatie over Azure Cloud Services.
* Zie [ASP.net](https://www.asp.net) voor meer informatie over het Program meren van ASP.NET-toepassingen.

## <a name="access-tables-in-code"></a>Access-tabellen in code
Voor toegang tot tabellen in Cloud service projecten moet u de volgende items toevoegen aan alle C#-bron bestanden die toegang hebben tot Azure-tabel opslag.

1. Zorg ervoor dat de naam ruimte declaraties boven in het C#-bestand de volgende **using** -instructies bevatten.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Een **Cloud Storage account** -object ophalen dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van de opslag connection string en het opslag account op te halen uit de Azure-service configuratie.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Gebruik alle bovenstaande code vóór de code in de volgende voor beelden.
   > 
   > 
3. Een **CloudTableClient** -object ophalen om te verwijzen naar de tabel objecten in uw opslag account.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Een **CloudTable** -referentie object ophalen om te verwijzen naar een specifieke tabel en entiteiten.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Een tabel in code maken
Als u de Azure-tabel wilt maken, voegt u een aanroep naar **CreateIfNotExistsAsync** toe aan de nadat u een **CloudTable** -object hebt opgehaald, zoals beschreven in de sectie ' Access tables in code '.

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel
Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert. Met de volgende code wordt een entiteits klasse gedefinieerd met de naam **CustomerEntity** die de voor naam van de klant als de rij-en achternaam gebruikt als de partitie sleutel.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Tabel bewerkingen waarbij entiteiten worden gebruikt, worden uitgevoerd met behulp van het **CloudTable** -object dat u eerder hebt gemaakt in ' Access tables in code '. Het **TableOperation** -object vertegenwoordigt de bewerking die moet worden uitgevoerd. In het volgende code voorbeeld ziet u hoe u een **CloudTable** -object en een **CustomerEntity** -object maakt. Om de bewerking voor te bereiden, wordt er een **TableOperation** gemaakt om de klant entiteit in de tabel in te voegen. Ten slotte wordt de bewerking uitgevoerd door het aanroepen van **CloudTable.ExecuteAsync**.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```


## <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen
U kunt in één schrijf bewerking meerdere entiteiten invoegen in een tabel. Het volgende code voorbeeld maakt twee entiteits objecten ("Jeff Smith" en "ben Smith"), voegt deze toe aan een **TableBatchOperation** -object met de methode Insert en start de bewerking door het aanroepen van **CloudTable.ExecuteBatchAsync**.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen
Gebruik een **TableQuery** -object om een tabel voor alle entiteiten in een partitie op te vragen. Het volgende codevoorbeeld geeft een filter voor entiteiten waarbij 'Smith' de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);

return View();
```


## <a name="get-a-single-entity"></a>Eén entiteit ophalen
U kunt een query schrijven om één specifieke entiteit te verkrijgen. De volgende code gebruikt een **TableOperation** -object om een klant met de naam ' ben Smith ' op te geven. Deze methode retourneert slechts één entiteit, in plaats van een verzameling, en de geretourneerde waarde in **TableResult. Result** is een **CustomerEntity** -object. Het opgeven van zowel de partitie-als de rijwaarden in een query is de snelste manier om één entiteit uit de **tabel** service op te halen.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen
U kunt een entiteit verwijderen nadat u deze hebt gevonden. De volgende code zoekt naar een klant entiteit met de naam "ben Smith" en als deze wordt gevonden, wordt deze verwijderd.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    await peopleTable.ExecuteAsync(deleteOperation);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]