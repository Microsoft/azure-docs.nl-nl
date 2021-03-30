---
title: 'Quickstart: Table-API met .NET - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096934"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Quickstart: Een Table-API-app bouwen met .NET SDK en Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Deze quickstart laat zien hoe u .NET en de [Table-API](table-introduction.md) van Azure Cosmos DB gebruikt voor het compileren van een app door een voorbeeld uit GitHub te klonen. In deze quickstart ziet u ook hoe u een Azure Cosmos DB-account maakt en hoe u Data Explorer gebruikt om tabellen en entiteiten te maken in Azure Portal op internet.

## <a name="prerequisites"></a>Vereisten

Als u Visual Studio 2019 nog niet hebt geïnstalleerd, kunt u het downloaden en de **gratis** [Community Edition van Visual Studio 2019](https://www.visualstudio.com/downloads/) gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Een tabel toevoegen

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Table-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

   ```bash
   md "C:\git-samples"
   ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

   ```bash
   cd "C:\git-samples"
   ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> Zie het artikel [Voorbeeld van Cosmos DB Table-API](./tutorial-develop-table-dotnet.md) voor een gedetailleerd overzicht van soortgelijke code.

## <a name="open-the-sample-application-in-visual-studio"></a>De voorbeeldtoepassing openen in Visual Studio

1. In Visual Studio opent u het menu **Bestand**, kiest u **Openen** en vervolgens **Project/oplossing**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="De oplossing openen"::: 

2. Navigeer naar de map waarin u de voorbeeldtoepassing hebt gekloond en open het bestand TableStorage.sln.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar de sectie [De verbindingsreeks bijwerken](#update-your-connection-string) van dit document.

* De volgende code laat zien hoe u een tabel maakt in de Azure Storage:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* De volgende code laat zien hoe u gegevens invoegt in de tabel:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* De volgende code laat zien hoe u gegevens uit de tabel opvraagt:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* De volgende code laat zien hoe u gegevens uit de tabel verwijdert:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Klik in [Azure Portal](https://portal.azure.com/) op **Verbindingsreeks**. Gebruik de kopieerknop aan de rechterkant van het venster om de **PRIMARY CONNECTION STRING** te kopiëren.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="De PRIMARY CONNECTION STRING in het deelvenster Verbindingsreeks weergeven en kopiëren":::

2. Open het bestand **Settings.json** in Visual Studio. 

3. Plak de **PRIMARY CONNECTION STRING** vanuit de portal in de waarde StorageConnectionString. Plak de tekenreeks tussen de aanhalingstekens.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Druk op CTRL + S om het bestand **Settings.json** op te slaan.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>De app compileren en implementeren

1. Klik in Visual Studio met de rechtermuisknop op het project **CosmosTableSamples** in **Solution Explorer** en klik vervolgens op **NuGet-pakketten beheren**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="NuGet-pakketten beheren":::

2. Typ in het NuGet-vak **Bladeren** Microsoft.Azure.Cosmos.Table. Daarmee wordt de clientbibliotheek Cosmos DB-tabel API gevonden. Houd er rekening mee dat deze bibliotheek momenteel alleen beschikbaar is voor .NET Framework en .NET Standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Tabblad NuGet bladeren":::

3. Klik op **Installeren** om de bibliotheek **Microsoft.Azure.Cosmos.Table** te installeren. Hiermee worden het pakket voor de Azure Cosmos DB-tabel API en alle daarvoor vereiste onderdelen geïnstalleerd.

4. Wanneer u de hele app uitvoert, worden er voorbeeldgegevens in de tabelentiteit ingevoegd en aan het eind verwijderd, zodat er geen ingevoegd gegevens worden weergegeven als u het hele voorbeeld uitvoert. U kunt echter een aantal onderbrekingspunten invoegen om de gegevens weer te geven. Open het bestand BasicSamples.cs en klik met de rechtermuisknop op regel 52, selecteer **Onderbrekingspunt** en selecteer daarna **Onderbrekingspunt invoegen**. Voeg nog een onderbrekingspunt in op regel 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Een onderbrekingspunt toevoegen"::: 

5. Druk op F5 om de toepassing uit te voeren. In het consolevenster wordt de naam van de nieuwe tabeldatabase (in dit geval demoa13b1) weergegeven in Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Console-uitvoer":::

   Als u het eerste onderbrekingspunt bereikt, gaat u terug naar de Data Explorer in Azure Portal. Klik op de knop **Vernieuwen**, vouw de demo*-tabel uit en klik op **Entiteiten**. Het tabblad **Entiteiten** aan de rechterkant geeft de nieuwe entiteit weer die is toegevoegd voor Walter Harp. U ziet dat het telefoonnummer voor de nieuwe entiteit 425-555-0101 is.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Nieuwe entiteit":::
    
   Als er een foutbericht wordt weergegeven met de tekst dat het Settings.json-bestand niet gevonden is bij het uitvoeren van het project, kunt u dit oplossen door de volgende XML-vermelding toe te voegen aan de projectinstellingen. Klik met de rechtermuisknop op CosmosTableSamples, selecteer Bewerken CosmosTableSamples.csproj en voeg de volgende itemGroup toe: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Sluit het tabblad **Entiteiten** in Data Explorer.
    
7. Druk op F5 om de app uit te voeren tot het volgende onderbrekingspunt wordt bereikt. 

    Wanneer u het onderbrekingspunt bereikt, gaat u terug naar Azure Portal en klikt u opnieuw op **Entiteiten** om het tabblad **Entiteiten** te openen. U ziet dat het telefoonnummer is bijgewerkt naar 425-555-0105.

8. Druk op F5 om de app uit te voeren. 
 
   De app voegt entiteiten toe voor gebruik in een geavanceerde voorbeeld-app die momenteel niet door de tabel-API wordt ondersteund. De app verwijdert vervolgens de tabel die door de voorbeeld-app is gemaakt.

9. Druk in het consolevenster op Enter om te stoppen met de uitvoering van de app. 
  

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account kunt maken, hebt u een tabel gemaakt met de Data Explorer en hebt u een app uitgevoerd.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)