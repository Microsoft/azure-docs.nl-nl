---
title: 'Azure Cosmos DB: Een taken-app ontwikkelen met Xamarin'
description: Biedt een Xamarin-voorbeeldcode die u kunt gebruiken om verbinding te maken met en query's uit te voeren op Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: f02a0673a3fae33f935ae9a9cbf94f101b25c39f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449780"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Quickstart: een taken-app maken met Xamarin met behulp van een SQL API-account van Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB.

> [!NOTE]
> Voorbeeldcode voor een hele canonieke voorbeeldapp in Xamarin, met meerdere Azure-producten, waaronder CosmosDB, zijn te vinden [hier](https://github.com/xamarinhq/app-geocontacts) op GitHub. Deze app demonstreert de weergave van geografisch verspreide contactpersonen en de mogelijkheid om toe te staan dat deze contactpersonen hun locatie wijzigen.

Deze snelstart laat zien hoe u een SQL API-account van Azure Cosmos DB, een documentdatabase en een container kunt maken met behulp van de Azure-portal. U maakt en implementeert vervolgens een mobiele app voor takenlijstjes op de [SQL .NET API](sql-api-sdk-dotnet.md) en [Xamarin](/xamarin/) met behulp van [Xamarin.Forms](/xamarin/) en het [MVVM-architectuurpatroon](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Taken-app in Xamarin uitgevoerd op iOS":::

## <a name="prerequisites"></a>Vereisten

Als u Windows gebruikt en Visual Studio 2019 nog niet hebt geïnstalleerd, kunt u het downloaden en de **gratis** [Community Edition van Visual Studio 2019](https://www.visualstudio.com/downloads/) gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** en **Mobile Development met .NET**-workloads inschakelt tijdens de installatie van Visual Studio.

Als u een Mac gebruikt, kunt u **gratis** [Visual Studio voor Mac](https://www.visualstudio.com/vs/mac/) downloaden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaat u de SQL API-app voor Xamarin klonen vanuit GitHub, de code controleren, de API-sleutels ophalen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Open in Visual Studio **C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Uw API-sleutels ophalen

Ga terug naar Azure Portal om de API-sleutelgegevens op te halen en deze in de app te kopiëren.

1. Klik in [Azure Portal](https://portal.azure.com/), in uw SQL API-account voor Azure Cosmos DB, in het linker navigatiegedeelte op **Sleutels** en klik vervolgens op **Sleutels voor lezen/schrijven**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de URI en Primaire sleutel in het bestand APIKeys.cs te kopiëren.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Een toegangssleutel weergeven en kopiëren in Azure Portal, blade Sleutels":::

2. Open in Visual Studio **ToDoItems.Core/Helpers/APIKeys.cs**.

3. Gebruik in Azure Portal de kopieerknop om de **URI**-waarde te kopiëren, en maak deze de waarde van de variabele `CosmosEndpointUrl` in APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure Portal]";
    ```

4. Gebruik in Azure Portal de kopieerknop om de waarde van **PRIMAIRE SLEUTEL** te kopiëren, en maak deze de waarde van de `Cosmos Auth Key` in APIKeys.cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure Portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>De code bekijken

Deze oplossing laat zien hoe u een taken-app kunt maken met behulp van de SQL API van Azure Cosmos DB en Xamarin.Forms. De app bevat twee tabbladen. Het eerste tabblad bevat een lijst met taken die nog niet zijn voltooid. Het tweede tabblad geeft een lijst met voltooide taken weer. U kunt niet alleen niet-voltooide taken in het eerste tabblad bekijken, maar ook nieuwe taken toevoegen, bestaande taken bewerken en taken als voltooid markeren.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="JSON-gegevens kopiëren en op Opslaan klikken in Data Explorer in Azure Portal":::

De code in de takenoplossing bevat:

* **ToDoItems.Core**
   * Dit is een standaard .NET-project met een Xamarin.Forms-project en een logische code voor gedeelde toepassingen die taken in Azure Cosmos DB bijhoudt.
* **ToDoItems.Android**
  * Dit project bevat de Android-app.
* **ToDoItems.iOS**
  * Dit project bevat de iOS-app.

Laten we nog eens kort bekijken hoe de app met Azure Cosmos DB communiceert.

* Het NuGet-pakket [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) moet aan alle projecten worden toegevoegd.
* De klasse `ToDoItem` in de map **ToDoItems.Core/Models** modelleert de documenten in de hierboven gemaakte container **Items**. Houd er rekening mee dat de namen van eigenschappen hoofdlettergevoelig zijn.
* De klasse `CosmosDBService` in de map **ToDoItems.Core/Services** kapselt de communicatie met Azure Cosmos DB in.
* Binnen de klasse `CosmosDBService` bevindt zich een variabele van het type `DocumentClient`. De `DocumentClient` wordt gebruikt om aanvragen te configureren en uit te voeren op het Azure Cosmos DB-account en wordt geïnstantieerd:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Tijdens het uitvoeren van query's op een container voor documenten wordt de methode `DocumentClient.CreateDocumentQuery<T>` gebruikt, zoals hier wordt weergegeven in de functie `CosmosDBService.GetToDoItems`:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    De `CreateDocumentQuery<T>` maakt gebruikt van een URI die verwijst naar de container die in de vorige sectie is gemaakt. U kunt ook LINQ-operators opgeven, zoals een `Where`-component. In dit geval worden alleen taken die niet zijn voltooid, geretourneerd.

    De functie `CreateDocumentQuery<T>` wordt synchroon uitgevoerd en retourneert een `IQueryable<T>`. De methode `AsDocumentQuery` zet de `IQueryable<T>` echter om naar een `IDocumentQuery<T>`-object dat asynchroon kan worden uitgevoerd. Zo wordt de gebruikersinterface-thread voor mobiele toepassingen niet geblokkeerd.

    De functie `IDocumentQuery<T>.ExecuteNextAsync<T>` haalt de pagina met resultaten op van Azure Cosmos DB en `HasMoreResults` controleert of er nog extra resultaten moeten worden geretourneerd.

> [!TIP]
> Verschillende functies die op Azure Cosmos-containers en -documenten worden uitgevoerd, gebruiken een URI als een parameter die het adres van de container of het document opgeeft. Deze URI is opgesteld met de klasse `URIFactory`. URI's voor databases, containers en documenten kunnen allemaal met deze klasse worden gemaakt.

* De functie `ComsmosDBService.InsertToDoItem` laat zien hoe u een nieuw document invoegt:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Zowel de item-URI als het item dat moet worden ingevoegd, zijn opgegeven.

* De functie `CosmosDBService.UpdateToDoItem` laat zien hoe u een bestaand document door een nieuwe vervangt:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Hier is een nieuwe URI nodig als unieke ID voor het document dat moet worden vervangen. Deze wordt verkregen door `UriFactory.CreateDocumentUri` te gebruiken en de namen van de database en container en de id van het document eraan door te geven.

    De `DocumentClient.ReplaceDocumentAsync` vervangt het document dat door de URI is geïdentificeerd door het document dat als parameter is opgegeven.

* Het verwijderen van een item wordt getoond met de functie `CosmosDBService.DeleteToDoItem`:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Let op de unieke document-URI die wordt gemaakt en doorgegeven aan de functie `DocumentClient.DeleteDocumentAsync`.

## <a name="run-the-app"></a>De app uitvoeren

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

Met de volgende stappen wordt gedemonstreerd hoe u de app uitvoert met behulp van het Visual Studio voor Mac-foutopsporingsprogramma.

> [!NOTE]
> Het gebruik van de Android-app is precies hetzelfde; eventuele verschillen worden in de onderstaande stappen aangegeven. Als u fouten wilt opsporen met Visual Studio in Windows, bekijkt u de documentatie voor [iOS](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) of [Android](/xamarin/android/deploy-test/debugging/).

1. Selecteer eerst het doelplatform door op de gemarkeerde vervolgkeuzelijst te klikken en ToDoItems.iOS te selecteren voor iOS of ToDoItems.Android te selecteren voor Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Een platform kiezen voor foutopsporing in Visual Studio voor Mac":::

2. Druk op cmd + Enter of klik op de afspeelknop om de foutopsporing te starten in de app.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Foutopsporing starten in Visual Studio voor Mac":::

3. Als de iOS-simulator of Android-emulator klaar is met opstarten, geeft de app twee tabbladen weer, onderaan het scherm voor iOS en bovenaan het scherm voor Android. Het eerste geeft taken weer die niet zijn voltooid, het tweede taken die wel zijn voltooid.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Startscherm van taken-app":::

4. Als u een taak op iOS wilt voltooien, schuift u deze naar links en tikt u op de knop **Voltooien**. Als u een taak op Android wilt voltooien, houdt u het item lang ingedrukt en tikt u op de knop Voltooien.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Een taak voltooien":::

5. Als u een taak wilt bewerken, tikt u op het item. Er wordt een nieuw scherm weergegeven waarop u nieuwe waarden kunt invoeren. Door op de knop Opslaan te tikken,worden de wijzigingen in Azure Cosmos DB opgeslagen.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Taak bewerken":::

6. Als u een taak wilt toevoegen, tikt u in de rechterbovenhoek van het startscherm op de knop **Toevoegen**. Er wordt een nieuwe, lege pagina voor bewerken weergegeven.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Taak toevoegen":::

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account kunt maken en hebt u een container gemaakt met de Data Explorer en een Xamarin-app ontwikkeld en geïmplementeerd. Nu kunt u aanvullende gegevens in uw Azure Cosmos-account importeren.

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
