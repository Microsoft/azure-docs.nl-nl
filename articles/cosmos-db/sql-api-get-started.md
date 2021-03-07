---
title: 'Zelfstudie: Een .NET-console-app bouwen om gegevens te beheren in het Azure Cosmos DB SQL-API-account'
description: 'Zelfstudie: Meer informatie over het maken van Azure Cosmos DB SQL-API-resources met behulp van een C#-consoletoepassing.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ece9ed5cb6eb6ea706db1ed615a64d001f2b17c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423948"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Zelfstudie: Een .NET-console-app bouwen om gegevens te beheren in het Azure Cosmos DB SQL-API-account
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Welkom bij de zelfstudie Aan de slag met de Azure Cosmos DB SQL-API. Wanneer u deze zelfstudie hebt voltooid, beschikt u over een consoletoepassing waarmee u Azure Cosmos DB-resources kunt maken en er query's op kunt uitvoeren.

In deze zelfstudie wordt gebruikgemaakt van versie 3.0 of later van de [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). U kunt werken met [.NET Framework of .NET Core](https://dotnet.microsoft.com/download).

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken en verbinden
> * Uw project configureren in Visual Studio
> * Een database en een container maken
> * Items toevoegen aan de container
> * Een query uitvoeren op de container
> * De ‘maken, lezen, bijwerken en verwijderen’ (CRUD)-bewerkingen uitvoeren op het item
> * De database verwijderen

Hebt u geen tijd? Geen probleem. De volledige oplossing is beschikbaar via [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Ga naar de sectie [De volledige zelfstudieoplossing ophalen](#GetSolution) voor beknopte instructies.

Tijd om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: Maak een Azure Cosmos DB-account

Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt dat u wilt gebruiken, kunt u deze sectie overslaan. Om de Azure Cosmos DB-emulator te gebruiken, volgt u de stappen in [Azure Cosmos DB-emulator](local-emulator.md) om de emulator in te stellen. Ga vervolgens verder met [stap 2: uw Visual Studio-project instellen](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Stap 2: uw Visual Studio-project instellen

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** voor C# en selecteer vervolgens **Volgende**.
1. Geef uw project de naam *CosmosGettingStartedTutorial* en selecteer **Maken**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Uw project configureren":::

1. Klik in **Solution Explorer** met de rechtermuisknop op uw nieuwe consoletoepassing. Deze bevindt zich onder uw Visual Studio-oplossing. Selecteer op **Manage NuGet Packages**.
1. Selecteer **Bladeren** en zoek naar *Microsoft.Azure.Cosmos* in het **NuGet Package Manager**. Kies **Microsoft.Azure.Cosmos** en selecteer **Installeren**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="NuGet voor Azure Cosmos DB-client-SDK installeren":::

   De pakket-id voor de Azure Cosmos DB SQL-API-clientbibliotheek is [Microsoft Azure Cosmos DB-clientbibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Goed gedaan. De configuratie is voltooid en u kunt nu aan de slag met het schrijven van code. Raadpleeg [Een .NET-console-App ontwikkelen met behulp van Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) voor het voltooide project van deze zelfstudie.

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Stap 3: Verbinding maken met een Azure Cosmos DB-account

1. Vervang de verwijzingen aan het begin van de C#-toepassing in het bestand *Program.cs* door de volgende verwijzingen:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Voeg deze constanten en variabelen toe in de klasse `Program`.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Als u bekend bent met de vorige versie van de .NET SDK, komen de termen *verzameling* en *document* u misschien bekend voor. Azure Cosmos DB biedt ondersteuning voor meerdere API-modellen. Daarom worden in versie 3.0 van de .NET SDK de generieke termen *container* en *item* gebruikt. Een *container* kan een verzameling, een graaf of een tabel zijn. Een *item* kan een document, rand/hoekpunt of rij zijn en is de inhoud binnen een container. Raadpleeg [Werken met databases, containers en items in Azure Cosmos DB](account-databases-containers-items.md) voor meer informatie.

1. Open de [Azure Portal](https://portal.azure.com). Vind uw Azure Cosmos DB-account en selecteer vervolgens **Sleutels**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Haal de Azure Cosmos DB-sleutels uit de Azure-portal op":::

1. Vervang `<your endpoint URL>` in *Program.cs* door de waarde van **URI**. Vervang `<your primary key>` door de waarde van de **HOOFDSLEUTEL**.

1. Voeg onder de methode **Main** de nieuwe asynchrone taak met de naam **GetStartedDemoAsync** toe, waarmee de nieuwe `CosmosClient` wordt geïnstantieerd.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    We gebruiken **GetStartedDemoAsync** als het toegangspunt dat de methoden aanroept die gebruikmaken van Azure Cosmos DB-resources.

1. Voeg de volgende code toe om de asynchrone taak **GetStartedDemoAsync** uit te voeren vanuit de methode **Main**. Uitzonderingen worden door de methode **Main** onderschept en naar de console geschreven.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecteer F5 om de toepassing uit te voeren.

    Het bericht wordt weergegeven in de console:  **Einde van de demo, druk op een willekeurige toets om af te sluiten.** Dit bericht bevestigt dat uw toepassing een verbinding heeft gemaakt met Azure Cosmos DB. U kunt vervolgens het consolevenster sluiten.

Gefeliciteerd! U hebt nu verbinding gemaakt met een Azure Cosmos DB-account.

## <a name="step-4-create-a-database"></a>Stap 4: Een database maken

Een database is de logische container voor items die zijn gepartitioneerd in containers. De methode `CreateDatabaseIfNotExistsAsync` of `CreateDatabaseAsync` van de klasse [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) kan een database maken.

1. Kopieer en plak de methode `CreateDatabaseAsync` onder uw methode `GetStartedDemoAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` maakt een nieuwe database gemaakt met id `FamilyDatabase` als deze nog niet bestaat, met de id die is opgegeven vanuit het veld `databaseId`.

1. Kopieer de onderstaande code en plak deze waar u de CosmosClient hebt geïnstantieerd om de methode **CreateDatabaseAsync** aan te roepen die u zojuist hebt toegevoegd.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Uw *Program.cs* moet er als volgt uitzien, met uw eindpunt en primaire sleutel ingevuld.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

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
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

   > [!NOTE]
   > Als u de foutmelding ‘503 - Service niet beschikbaar’ ziet, kan het zijn dat de vereiste [poorten](sql-sdk-connection-modes.md#service-port-ranges) voor de directe verbindingsmodus worden geblokkeerd door een firewall. Om dit probleem op te lossen, opent u de vereiste poorten of gebruikt u de verbinding in gatewaymodus zoals weergegeven in de volgende code:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gefeliciteerd! U hebt een Azure Cosmos-database gemaakt.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Stap 5: Een container maken

> [!WARNING]
> De methode `CreateContainerIfNotExistsAsync` maakt een nieuwe container met prijsimplicaties. Zie onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.
>
>

Een container kan worden gemaakt met behulp van de methode [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) of [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) in de klasse `CosmosDatabase`. Een container bestaat uit items (in het geval van de SQL-API zijn dit JSON-documenten) en bijbehorende toepassingslogica, in bijvoorbeeld Javascript, zoals opgeslagen procedures, door de gebruiker gedefinieerde functies, en triggers.

1. Kopieer en plak de methode `CreateContainerAsync` onder uw methode `CreateDatabaseAsync`. `CreateContainerAsync`  maakt een nieuwe container met de id `FamilyContainer` als deze nog niet bestaat, met behulp van de id die is opgegeven in het veld `containerId` gepartitioneerd door eigenschap `LastName`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Kopieer de onderstaande code en plak deze waar u de CosmosClient hebt geïnstantieerd om de methode **CreateContainer** aan te roepen die u zojuist hebt toegevoegd.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-container gemaakt.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Stap 6: Items toevoegen aan de container

Met de methode [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) van de klasse `CosmosContainer` kan een item worden gemaakt. Als u de SQL-API gebruikt, worden items als documenten geprojecteerd. Deze zijn door de gebruiker gedefinieerde willekeurige JSON-inhoud. U kunt nu een item invoegen in uw Azure Cosmos-container.

Eerst maakt u de klasse `Family`, die aangeeft welke objecten worden opgeslagen in Azure Cosmos DB in dit voorbeeld. We maken ook subklassen `Parent`, `Child`, `Pet`, `Address` die in `Family` worden gebruikt. Het item moet een `Id`-eigenschap bevatten dat is geserialiseerd als `id` in JSON.

1. Selecteer Ctrl+Shift+A om **Nieuw item toevoegen** te openen. Voeg een nieuwe klasse `Family.cs` toe aan uw project.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Schermopname van het toevoegen van een nieuwe Family.cs-klasse aan het project":::

1. Kopieer en plak de klasse `Family`, de `Parent`, de `Child`, de `Pet`en `Address` in `Family.cs`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Voeg in *Program.cs* de methode `AddItemsToContainerAsync` toe na uw methode `CreateContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    De code controleert de code of er niet al een item met dezelfde id bestaat. U voegt twee items in, één voor de *familie Andersen* en één voor de *familie Wakefield*.

1. Voeg een aanroep naar `AddItemsToContainerAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt twee Azure Cosmos-items gemaakt.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Stap 7: Query's uitvoeren op Azure Cosmos DB-resources

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's voor de JSON-documenten die in elke container zijn opgeslagen. Zie [Aan de slag met SQL-query’s](./sql-query-getting-started.md) voor meer informatie. In de volgende voorbeeldcode ziet u hoe u een query uitvoert voor de items die u in de vorige stap hebt ingevoegd.

1. Kopieer en plak de methode `QueryItemsAsync` achter uw methode `AddItemsToContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Voeg een aanroep naar ``QueryItemsAsync`` toe in de methode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een query op een Azure Cosmos-container uitgevoerd.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Stap 8: Een JSON-item vervangen

Nu gaat u een item in Azure Cosmos DB bijwerken. U wijzigt de eigenschap `IsRegistered` van de `Family` en de `Grade` van een van de kinderen.

1. Kopieer en plak de methode `ReplaceFamilyItemAsync` achter uw methode `QueryItemsAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Voeg een aanroep naar `ReplaceFamilyItemAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-item vervangen.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Stap 9: Item verwijderen

Nu gaat u een item verwijderen uit Azure Cosmos DB.

1. Kopieer en plak de methode `DeleteFamilyItemAsync` achter uw methode `ReplaceFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Voeg een aanroep naar `DeleteFamilyItemAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt succesvol een Azure Cosmos-item verwijdert.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Stap 10: De database verwijderen

Nu gaat u de database verwijderen. Als u de gemaakte database verwijdert, worden de database en alle onderliggende resources verwijderd. De resources omvatten containers, items en eventuele opgeslagen procedures, door de gebruiker gedefinieerde functies en triggers. We verwijderen ook de instantie `CosmosClient`.

1. Kopieer en plak de methode `DeleteDatabaseAndCleanupAsync` achter uw methode `DeleteFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Voeg een aanroep naar ``DeleteDatabaseAndCleanupAsync`` toe in de methode ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt succesvol een Azure Cosmos-database verwijdert.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Stap 11: Uw C#-consoletoepassing volledig uitvoeren

Druk in Visual Studio op F5 en compileer en voer de toepassing uit in de foutopsporingsmodus.

Als het goed is, wordt de uitvoer van de volledige app in een consolevenster weergegeven. In de uitvoer ziet u de resultaten van de query's die we hebben toegevoegd. Deze moet overeenkomen met de onderstaande voorbeeldtekst.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gefeliciteerd! U hebt de zelfstudie voltooid en beschikt nu over een werkende C#-consoletoepassing.

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> De volledige zelfstudieoplossing ophalen

Als u geen tijd hebt gehad om de stappen in deze zelfstudie uit te voeren of als u alleen de codevoorbeelden wilt downloaden, kunt u deze downloaden.

Om de oplossing `GetStarted` te bouwen, zijn de volgende vereisten nodig:

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een [Azure Cosmos DB-account][cosmos-db-create-account].
* De [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-oplossing die beschikbaar is via GitHub.

Als u de verwijzingen naar de Azure Cosmos DB .NET-SDK in Visual Studio wilt herstellen, klikt u in **Solution Explorer** met de rechtermuisknop op de oplossing en selecteert u vervolgens **NuGet-pakketten herstellen**. Werk vervolgens in het bestand *App. config* de waarden `EndPointUri` en `PrimaryKey` bij zoals beschreven in [Stap 3: verbinding maken met een Azure Cosmos DB-account](#Connect).

Dat is alles, bouw nu de oplossing. Succes!

## <a name="next-steps"></a>Volgende stappen

* Wilt u een complexere ASP.NET MVC-zelfstudie? Zie [Zelfstudie: een ASP.NET Core MVC-webtoepassing ontwikkelen met Azure Cosmos DB met behulp van de .NET SDK](sql-api-dotnet-application.md).
* Wilt u de schaal en prestaties testen met Azure Cosmos DB? Raadpleeg [Performance and Scale Testing with Azure Cosmos DB (Prestaties en schaal testen met Azure Cosmos DB)](performance-testing.md).
* Raadpleeg voor meer informatie over het bewaken van Azure Cosmos DB aanvragen, het gebruik en de opslag [Prestaties en metrische gegevens van de opslag in Azure Cosmos DB bewaken](./monitor-cosmos-db.md).
* Query's uitvoeren op onze voorbeeldgegevensset in de [Testomgeving voor query's](https://www.documentdb.com/sql/demo).
* Zie [Welkom bij Azure Cosmos DB](./introduction.md) voor meer informatie over Azure Cosmos DB.

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account