---
title: 'Snelstart: een .NET console-app bouwen om Azure Cosmos DB SQL-API-resources te beheren'
description: Meer informatie over hoe u een .NET console-app kunt maken voor het beheren van Azure Cosmos DB SQL-API-accountresources in deze Quickstart.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/21/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f25c8e2ca7abeae0a918825654c55256182189ae
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785356"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Quickstart: Een .NET console-app bouwen om Azure Cosmos DB SQL-API-resources te beheren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-clientbibliotheek voor .NET. Volg de stappen in dit document om het .NET-pakket te installeren, een app te bouwen en de voorbeeldcode voor basis CRUD-bewerkingen uit te proberen voor de gegevens die zijn opgeslagen in Azure Cosmos DB. 

Azure Cosmos DB is de snelle NoSQL-database van Microsoft, met open API's voor elke schaal. U kunt Azure Cosmos DB gebruiken om snel databases voor belangrijke/waardevolle documenten en grafieken te maken en doorzoeken. Gebruik de Azure Cosmos DB SQL API-clientbibliotheek voor .NET om:

* Een Azure Cosmos-database en een container te maken
* Voorbeeldgegevens toe te voegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

[API-referentiedocumentatie](/dotnet/api/microsoft.azure.cosmos) | [Bibliotheek broncode](https://github.com/Azure/azure-cosmos-dotnet-v3) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak een gratis abonnement](https://azure.microsoft.com/free/) of u kunt [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen. 
* De [.NET Core 2.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een Azure Cosmos-account maakt en een project maakt dat gebruikmaakt van Azure Cosmos DB SQL API-clientbibliotheek voor .NET om resources te beheren. Met de voorbeeldcode die in dit artikel wordt beschreven, maakt u een `FamilyDatabase`-database en familieleden (elk familielid is een item) binnen die database. Elk familielid heeft eigenschappen als `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. De eigenschap `LastName` wordt gebruikt als de partitiesleutel voor de container. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Een Azure Cosmos-account maken

Als u de optie [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) gebruikt om een Azure Cosmos-account te maken, moet u een Azure Cosmos DB-account van het type **SQL API** maken. Er is al een Azure Cosmos DB-testaccount voor u gemaakt. U hoeft het account niet expliciet te maken, dus u kunt deze sectie overslaan en doorgaan naar de volgende sectie.

Als u uw eigen Azure-abonnement hebt of een gratis abonnement hebt gemaakt, moet u expliciet een Azure Cosmos-account maken. Met de volgende code wordt een Azure Cosmos-account gemaakt met sessieconsistentie. Het account wordt gerepliceerd in `South Central US` en `North Central US`.  

U kunt Azure Cloud Shell gebruiken om het Azure Cosmos-account te maken. Azure Cloud Shell is een interactieve, geverifieerde en vanuit de browser toegankelijke shell voor het beheer van Azure-resources. Het biedt de flexibiliteit om de shell-ervaring te kiezen die het beste past bij de manier waarop u werkt, dan wel Bash of PowerShell. Kies voor deze quickstart de modus **Bash**. Voor Azure Cloud Shell hebt u ook een opslagaccount nodig. U kunt er een maken wanneer u hierom wordt gevraagd.

Selecteer de knop **Uitproberen** naast de volgende code, kies de modus **Bash**, selecteer **een opslagaccount maken** en meld u aan bij Cloud Shell. Kopieer vervolgens de volgende code en plak deze in Azure Cloud Shell en voer deze uit. De naam van het Azure Cosmos-account moet wereldwijd uniek zijn. Zorg ervoor dat u de `mysqlapicosmosdb`-waarde bijwerkt voordat u de opdracht uitvoert.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Het maken van het Azure Cosmos-account neemt enige tijd in beslag. Zodra de bewerking is voltooid, kunt u de bevestiging van de uitvoer bekijken. Nadat de opdracht is voltooid, meldt u zich aan bij de [Azure-portal](https://portal.azure.com/) en controleert u of het Azure Cosmos-account met de opgegeven naam bestaat. U kunt het Azure Cloud Shell-venster sluiten nadat de resource is gemaakt. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Een nieuwe .NET-app maken

Maak een nieuwe .NET-toepassing in uw favoriete editor of IDE. Open de Windows-opdrachtprompt of een Terminalvenster op uw lokale computer. Alle opdrachten in de volgende secties worden uitgevoerd vanaf de opdrachtprompt of terminal.  Voer de volgende nieuwe DotNet-opdracht uit om een nieuwe app te maken met de naam `todo`. Met de parameter --langVersion stelt u de eigenschap LangVersion in het gemaakte projectbestand in.

```console
dotnet new console --langVersion 7.1 -n todo
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```console
cd todo
dotnet build
```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Terwijl u zich nog in de toepassingsmap bevindt, installeert u de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de DotNet-opdracht pakket toevoegen.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieer uw Azure Cosmos-accountaanmeldingsgegevens vanuit de Azure-portal

De voorbeeldtoepassing moet de toegang tot uw Azure Cosmos-account verifiëren. Als u zich wilt verifiëren, moet u de aanmeldingsgegevens van het Azure Cosmos-account doorgeven aan de toepassing. U kunt de aanmeldingsgegevens van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deelvenster **Sleutels** en kopieer de **URI** en **PRIMAIRE SLEUTEL** van uw account. In de volgende stap voegt u de waarden voor de URI en sleutels toe aan een omgevingsvariabele.

### <a name="set-the-environment-variables"></a>Omgevingsvariabelen instellen

Nadat u de **URI** en **PRIMAIRE SLEUTEL** van uw account hebt gekopieerd, slaat u deze op in een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en voert u de volgende opdracht uit. Zorg ervoor dat de waarden `<Your_Azure_Cosmos_account_URI>` en `<Your_Azure_Cosmos_account_PRIMARY_KEY>` worden vervangen.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objectmodel

Voordat u begint met het compileren van de toepassing, kijken we naar de hiërarchie van resources in Azure Cosmos DB en het objectmodel dat wordt gebruikt voor het maken en openen van deze resources. De Azure Cosmos DB maakt resources in de volgende volgorde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie het artikel [werken met databases, containers en items in Azure Cosmos DB](account-databases-containers-items.md) voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om te communiceren met deze resources:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient): deze klasse biedt een logische weergave aan de clientzijde voor de Azure Cosmos DB-service. Het clientobject wordt gebruikt om aanvragen aan de service te configureren en uitvoeren.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync): deze methode maakt (indien niet aanwezig) of haalt (indien aanwezig) een database-resource op als een asynchrone bewerking. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync): deze methode maakt (indien niet aanwezig) of haalt (indien aanwezig) een container op als een asynchrone bewerking. U kunt de status code van het antwoord controleren om te bepalen of de container nieuw is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync): met deze methode maakt u een item in de container. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync): met deze methode maakt u een item in de container als deze nog niet bestaat of vervangt u het item als het al bestaat. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator): met deze methode maakt u een query voor items onder een container in een Azure Cosmos-database met behulp van een SQL-instructie met geparameteriseerde waarden. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync): hiermee verwijdert u de opgegeven database uit uw Azure Cosmos-account. Met de methode `DeleteAsync` verwijdert u alleen de database. Het verwijderen van het `Cosmosclient`-exemplaar moet afzonderlijk plaatsvinden (dit gebeurt in de methode DeleteDatabaseAndCleanupAsync. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Codevoorbeelden

Met de voorbeeldcode die in dit artikel wordt beschreven, wordt een familiedatabase gemaakt in Azure Cosmos DB. De familiedatabase bevat familiegegevens, zoals naam, adres, locatie, de gekoppelde ouders, kinderen en huisdieren. Voordat u de gegevens in uw Azure Cosmos-account vult, definieert u de eigenschappen van een familie-item. Maak een nieuwe klasse met de naam `Family.cs` op het hoofdniveau van uw voorbeeldtoepassing en voeg de volgende code toe:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Voeg de gebruiksinstructies toe en definieer het clientobject

Open in de projectmap het `Program.cs`-bestand in de editor en voeg de volgende instructies toe aan het begin van de toepassing:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Voeg aan het **Program.cs**-bestand code toe om de omgevingsvariabelen te lezen die u in de vorige stap hebt ingesteld. Definieer de objecten `CosmosClient`, `Database` en `Container`. Voeg vervolgens code toe aan de hoofdmethode waarmee de methode `GetStartedDemoAsync` wordt aangeroepen waar u resources van het Azure Cosmos-account beheert. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Een database maken 

Definieer de methode `CreateDatabaseAsync` binnen de klasse `program.cs`. Met deze methode maakt u de `FamilyDatabase` als deze nog niet bestaat.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Een container maken

Definieer de methode `CreateContainerAsync` binnen de klasse `program.cs`. Met deze methode maakt u de `FamilyContainer` als deze nog niet bestaat. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Een item maken

Maak een familie-item door de methode `AddItemsToContainerAsync` toe te voegen met de volgende code. U kunt de methoden `CreateItemAsync` of `UpsertItemAsync` gebruiken om een item te maken:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Query's uitvoeren op de items

Nadat u een item hebt ingevoegd, kunt u een query uitvoeren om de details van de familie 'Andersen' te verkrijgen. In de volgende code ziet u hoe u de query rechtstreeks kunt uitvoeren met behulp van de SQL-query. De SQL-query voor het ophalen van de details van de familie 'Anderson' is: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieer de methode `QueryItemsAsync` binnen de klasse `program.cs` en voeg de volgende code toe:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>De database verwijderen 

Ten slotte kunt u de database verwijderen waarmee de methode `DeleteDatabaseAndCleanupAsync` wordt toegevoegd met de volgende code:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>De CRUD-bewerkingen uitvoeren

Nadat u alle vereiste methoden hebt gedefinieerd, voert u deze uit in de methode `GetStartedDemoAsync`. Met de methode `DeleteDatabaseAndCleanupAsync` is een opmerking in deze code opgenomen omdat er geen resources worden weergegeven als deze methode wordt uitgevoerd. U kunt de opmerking opheffen nadat u hebt gecontroleerd of uw Azure Cosmos DB-resources zijn gemaakt in Azure Portal. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Nadat u alle vereiste methoden hebt toegevoegd, slaat u het bestand `Program.cs` op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens compileert u de toepassing en voert u deze uit om de Azure Cosmos DB-resources te maken. Zorg ervoor dat u een nieuw opdrachtpromptvenster opent en gebruik niet hetzelfde exemplaar dat u hebt gebruikt om de omgevingsvariabelen in te stellen. De omgevingsvariabelen zijn niet ingesteld in het huidige geopende venster. U moet een nieuwe opdrachtprompt openen om de updates te bekijken. 

```console
dotnet build
```

```console
dotnet run
```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U kunt zich ook aanmelden bij de Azure-portal en controleren of de resources zijn gemaakt:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

U kunt controleren of de gegevens zijn gemaakt door u aan te melden bij de Azure-portal en de vereiste items te bekijken in uw Azure Cosmos-account. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om het Azure Cosmos-account en de bijbehorende resourcegroep te verwijderen. In de volgende opdracht ziet u hoe u de resourcegroep verwijdert met behulp van Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos-account, een database en een container maakt met behulp van een .NET Core-app. U kunt nu aanvullende gegevens importeren in uw Azure Cosmos-account met behulp van de instructies in het volgende artikel. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
