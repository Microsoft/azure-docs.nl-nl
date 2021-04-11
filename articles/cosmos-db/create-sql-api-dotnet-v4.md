---
title: Azure Cosmos DB SQL API-resources beheren met .NET V4 SDK
description: Quickstart voor het bouwen van een console-app met behulp van .NET V4 SDK om Azure Cosmos DB SQL API-accountresources te beheren.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8afa0ec51f86bd77e5539840f3419eaff89171f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107190"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Quick Start: een console-app bouwen met behulp van de .NET v4 SDK (preview) voor het beheren van Azure Cosmos DB SQL-API-account resources.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-clientbibliotheek voor .NET. Volg de stappen in dit document om het .NET V4-pakket (Azure.Cosmos) te installeren, een app te bouwen en de voorbeeldcode voor basis CRUD-bewerkingen uit te proberen voor de gegevens die zijn opgeslagen in Azure Cosmos DB.

> [!IMPORTANT]
> De .NET v4 SDK voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure Cosmos DB is de snelle NoSQL-database van Microsoft, met open API's voor elke schaal. U kunt Azure Cosmos DB gebruiken om snel databases voor belangrijke/waardevolle documenten en grafieken te maken en doorzoeken. Gebruik de Azure Cosmos DB SQL API-clientbibliotheek voor .NET om:

* Een Azure Cosmos-database en een container te maken
* Voorbeeldgegevens toe te voegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

[Broncode van de bibliotheek](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak een gratis abonnement](https://azure.microsoft.com/free/) of u kunt [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U kunt controleren welke versie beschikbaar is in uw omgeving door `dotnet --version` uit te voeren.

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

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

   ```bash
   cd todo
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Terwijl u zich nog in de toepassingsmap bevindt, installeert u de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de DotNet-opdracht pakket toevoegen.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieer uw Azure Cosmos-accountaanmeldingsgegevens vanuit de Azure-portal

De voorbeeldtoepassing moet de toegang tot uw Azure Cosmos-account verifiëren. Als u zich wilt verifiëren, moet u de aanmeldingsgegevens van het Azure Cosmos-account doorgeven aan de toepassing. U kunt de aanmeldingsgegevens van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deelvenster **Sleutels** en kopieer de **URI** en **PRIMAIRE SLEUTEL** van uw account. In de volgende stap voegt u de waarden voor de URI en sleutels toe aan een omgevingsvariabele.

## <a name="object-model"></a><a id="object-model"></a>Objectmodel

Voordat u begint met het compileren van de toepassing, kijken we naar de hiërarchie van resources in Azure Cosmos DB en het objectmodel dat wordt gebruikt voor het maken en openen van deze resources. De Azure Cosmos DB maakt resources in de volgende volgorde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie het artikel [werken met databases, containers en items in Azure Cosmos DB](account-databases-containers-items.md) voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om te communiceren met deze resources:

* CosmosClient: deze klasse biedt een logische weergave aan de clientzijde voor de Azure Cosmos DB-service. Het clientobject wordt gebruikt om aanvragen aan de service te configureren en uitvoeren.
* CreateDatabaseIfNotExistsAsync: deze methode maakt (indien niet aanwezig) of haalt (indien aanwezig) een database-resource op als een asynchrone bewerking. 
* CreateContainerIfNotExistsAsync: deze methode maakt (indien niet aanwezig) of haalt (indien aanwezig) een container op als een asynchrone bewerking. U kunt de statuscode van het antwoord controleren om te bepalen of de container nieuw is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* CreateItemAsync: met deze methode maakt u een item in de container.
* UpsertItemAsync: met deze methode maakt u een item in de container als deze nog niet bestaat of vervangt u het item als het al bestaat. 
* GetItemQueryIterator: met deze methode maakt u een query voor items onder een container in een Azure Cosmos-database met behulp van een SQL-instructie met geparameteriseerde waarden. 
* DeleteAsync: hiermee verwijdert u de opgegeven database uit uw Azure Cosmos-account. Met de methode `DeleteAsync` verwijdert u alleen de database.

 ## <a name="code-examples"></a><a id="code-examples"></a>Codevoorbeelden

Met de voorbeeldcode die in dit artikel wordt beschreven, wordt een familiedatabase gemaakt in Azure Cosmos DB. De familiedatabase bevat familiegegevens, zoals naam, adres, locatie, de gekoppelde ouders, kinderen en huisdieren. Voordat u de gegevens in uw Azure Cosmos-account vult, definieert u de eigenschappen van een familie-item. Maak een nieuwe klasse met de naam `Family.cs` op het hoofdniveau van uw voorbeeldtoepassing en voeg de volgende code toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Voeg de gebruiksinstructies toe en definieer het clientobject

Open in de projectmap het `Program.cs`-bestand in de editor en voeg de volgende instructies toe aan het begin van de toepassing:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Voeg in de `Program`-klasse de volgende globale variabelen toe: Dit zijn onder andere de eindpunt- en autorisatiesleutels, de naam van de database en de container die u gaat maken. Zorg ervoor dat u de waarden voor het eindpunt en de autorisatiesleutels vervangt naar aanleiding van uw omgeving. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Vervang ten slotte de methode `Main`:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Een database maken 

Definieer de methode `CreateDatabaseAsync` binnen de klasse `program.cs`. Met deze methode maakt u de `FamilyDatabase` als deze nog niet bestaat.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Een container maken

Definieer de methode `CreateContainerAsync` binnen de klasse `Program`. Met deze methode maakt u de `FamilyContainer` als deze nog niet bestaat. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Een item maken

Maak een familie-item door de methode `AddItemsToContainerAsync` toe te voegen met de volgende code. U kunt de methoden `CreateItemAsync` of `UpsertItemAsync` gebruiken om een item te maken:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Query's uitvoeren op de items

Nadat u een item hebt ingevoegd, kunt u een query uitvoeren om de details van de familie 'Andersen' te verkrijgen. In de volgende code ziet u hoe u de query rechtstreeks kunt uitvoeren met behulp van de SQL-query. De SQL-query voor het ophalen van de details van de familie 'Anderson' is: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieer de methode `QueryItemsAsync` binnen de klasse `Program` en voeg de volgende code toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Een item vervangen 

Lees een familie-item en werk het vervolgens bij door de methode `ReplaceFamilyItemAsync` toe te voegen met de volgende code.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Een item verwijderen 

Verwijder een familie-item door de methode `DeleteFamilyItemAsync` toe te voegen met de volgende code.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>De database verwijderen 

Ten slotte kunt u de database verwijderen waarmee de methode `DeleteDatabaseAndCleanupAsync` wordt toegevoegd met de volgende code:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Nadat u alle vereiste methoden hebt toegevoegd, slaat u het bestand `Program` op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens compileert u de toepassing en voert u deze uit om de Azure Cosmos DB-resources te maken.

   ```bash
   dotnet run
   ```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U kunt zich ook aanmelden bij de Azure-portal en controleren of de resources zijn gemaakt:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

U kunt controleren of de gegevens zijn gemaakt door u aan te melden bij de Azure-portal en de vereiste items te bekijken in uw Azure Cosmos-account. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om het Azure Cosmos-account en de bijbehorende resourcegroep te verwijderen. In de volgende opdracht ziet u hoe u de resourcegroep verwijdert met behulp van Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos-account, een database en een container maakt met behulp van een .NET Core-app. U kunt nu aanvullende gegevens importeren in uw Azure Cosmos-account met de instructies in het volgende artikel. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
