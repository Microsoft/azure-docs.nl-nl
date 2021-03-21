---
title: Gegevens bulksgewijs importeren in Azure Cosmos DB SQL API-account met behulp van de .NET SDK
description: Meer informatie over het importeren of opnemen van gegevens in Azure Cosmos DB door het bouwen van een .NET-consoletoepassing waarmee de ingerichte doorvoer (RU/s) die nodig is voor het importeren van gegevens, wordt geoptimaliseerd
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563603"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Gegevens bulksgewijs importeren in Azure Cosmos DB SQL API-account met behulp van de .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze zelfstudie laat zien hoe u een .NET-consoletoepassing bouwt waarmee de ingerichte doorvoer (RU/s) die nodig is voor het importeren van gegevens in Azure Cosmos DB, wordt geoptimaliseerd. In dit artikel leest u gegevens uit een voorbeeldgegevensbron en importeert u deze in een Azure Cosmos-container.
In deze zelfstudie wordt gebruikgemaakt van [versie 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) van de Azure Cosmos DB .NET SDK. Deze is gericht op .NET Framework of .NET Core.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account waarvoor bulkondersteuning is ingeschakeld
> * Een gegevensimport uitvoeren met gelijktijdige maakbewerkingen

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel uitvoert, moet u zorgen dat u beschikt over de volgende resources:

* Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U kunt controleren welke versie beschikbaar is in uw omgeving door `dotnet --version` uit te voeren.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: Maak een Azure Cosmos DB-account

[Maak een Azure Cosmos DB SQL API-account](create-cosmosdb-resources-portal.md) vanuit Azure Portal. U kunt het account ook maken met behulp van [Azure Cosmos DB Emulator](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Stap 2: Uw .NET-project instellen

Open de Windows-opdrachtprompt of een Terminalvenster op uw lokale computer. Alle opdrachten in de volgende secties worden uitgevoerd vanaf de opdrachtprompt of terminal. Voer de volgende nieuwe DotNet-opdracht uit om een nieuwe app te maken met de naam *bulk-import-demo*. Met de parameter `--langVersion` stelt u de eigenschap *LangVersion* in het gemaakte projectbestand in.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Stap 3: Het Azure Cosmos DB-pakket toevoegen

Terwijl u zich nog in de toepassingsmap bevindt, installeert u de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de DotNet-opdracht pakket toevoegen.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Stap 4: De aanmeldingsgegevens van het Azure Cosmos-account weergeven

De voorbeeldtoepassing moet de toegang tot uw Azure Cosmos-account verifiëren. Als u zich wilt verifiëren, moet u de aanmeldingsgegevens van het Azure Cosmos-account doorgeven aan de toepassing. U kunt de aanmeldingsgegevens van het opslagaccount weergeven door de volgende stappen te volgen:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1.  Navigeer naar uw Azure Cosmos-account.
1.  Open het deelvenster **Sleutels** en kopieer de **URI** en **PRIMAIRE SLEUTEL** van uw account.

Als u Azure Cosmos DB Emulator gebruikt, raadpleegt u [dit artikel om de aanmeldingsgegevens voor de emulator](local-emulator.md#authenticate-requests) op te halen.

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Stap 5: Het CosmosClient-object initialiseren met ondersteuning voor bulkuitvoering

Open het gegenereerde `Program.cs`-bestand in een code-editor. U maakt een nieuw exemplaar van CosmosClient waarvoor bulkuitvoering is ingeschakeld en gebruikt dit om bewerkingen uit te voeren in Azure Cosmos DB. 

Laten we beginnen met het overschrijven van de standaardmethode `Main` en het definiëren van de globale variabelen. Deze globale variabelen bevatten de eindpunt- en autorisatiesleutels, de naam van de database, de container die u gaat maken en het aantal items dat u in bulk wilt invoegen. Zorg ervoor dat u de waarden voor de eindpunt-URL en de autorisatiesleutel vervangt naar aanleiding van uw omgeving. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Voeg in de methode `Main` de volgende code toe om het CosmosClient-object te initialiseren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Nadat de bulkuitvoering is ingeschakeld, worden in de CosmosClient intern gelijktijdige bewerkingen gegroepeerd in afzonderlijke serviceaanroepen. Op deze manier wordt het gebruik van de doorvoer geoptimaliseerd door serviceaanroepen over partities te verdelen en uiteindelijk afzonderlijke resultaten toe te wijzen aan de oorspronkelijke oproepende functies.

U kunt vervolgens een container maken om alle items op te slaan.  Definieer `/pk` als de partitiesleutel, 50.000 RU/s als ingerichte doorvoer en een aangepast indexeringsbeleid waarmee alle velden worden uitgesloten om de schrijfdoorvoer te optimaliseren. Voeg de volgende code toe na de CosmosClient-initialisatie-instructie:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Stap 6: Een lijst met gelijktijdige taken vullen

Als u de ondersteuning voor bulkuitvoering wilt benutten, maakt u een lijst met asynchrone taken op basis van de gegevensbron en de bewerkingen die u wilt uitvoeren, en gebruikt u `Task.WhenAll` om deze gelijktijdig uit te voeren.
Laten we beginnen met pseudogegevens om een lijst met items op basis van ons gegevensmodel te genereren. In een echte toepassing zijn de items afkomstig uit de gewenste gegevensbron.

Voeg eerst het pseudopakket aan de oplossing toe met behulp van de DotNet-opdracht pakket toevoegen.

   ```bash
   dotnet add package Bogus
   ```

Definieer de definitie van de items die u wilt opslaan. U moet de klasse `Item` in het bestand `Program.cs` definiëren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Maak vervolgens een Help-functie binnen de klasse `Program`. Met deze Help-functie wordt het gedefinieerde aantal in te voegen items opgehaald en worden willekeurige gegevens gegenereerd:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Gebruik de Help-functie om een lijst met documenten te initialiseren waarmee u kunt werken:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Gebruik vervolgens de lijst met documenten om gelijktijdige taken te maken en de taken lijst in te vullen om de items in de container in te voegen. Als u deze bewerking wilt uitvoeren, voegt u de volgende code toe aan de klasse `Program`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Al deze gelijktijdige puntbewerkingen worden samen uitgevoerd (in bulk), zoals beschreven in de inleidende sectie.

## <a name="step-7-run-the-sample"></a>Stap 7: De voorbeeldtoepassing uitvoeren

Als u de voorbeeldtoepassing wilt uitvoeren, kunt u dit gewoon doen met de `dotnet`-opdracht:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>De volledige voorbeeldtoepassing ophalen

Als u geen tijd hebt gehad om de stappen in deze zelfstudie uit te voeren of als u alleen de codevoorbeelden wilt downloaden, kunt u deze ophalen van [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Nadat het project is gekloond, moet u de gewenste referenties in [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25) bijwerken.

De voorbeeldtoepassing kan worden uitgevoerd door naar de map van de opslagplaats te gaan en met behulp van `dotnet`:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account waarvoor bulkondersteuning is ingeschakeld
> * Een gegevensimport uitvoeren met gelijktijdige maakbewerkingen

U kunt nu verdergaan met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Query's uitvoeren in Azure Cosmos DB met behulp van de SQL-API](tutorial-query-sql-api.md)