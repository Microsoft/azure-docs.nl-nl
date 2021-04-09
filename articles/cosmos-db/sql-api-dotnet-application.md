---
title: Zelfstudie over de ASP.NET Core MVC-Web-app met Azure Cosmos DB
description: Dit is een zelfstudie voor ASP.NET Core MVC om een MVC-webtoepassing met Azure Cosmos DB te maken. JSON opslaan en gegevens benaderen via een takenlijst-app die wordt gehost op Azure App Service - Stapsgewijze zelfstudie voor ASP NET Core MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010346"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Zelfstudie: Een ASP.NET Core MVC-webtoepassing ontwikkelen met Azure Cosmos DB met behulp van de .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

In deze zelfstudie wordt uitgelegd hoe u Azure Cosmos DB gebruikt om gegevens uit een ASP.NET MVC-toepassing die wordt gehost op Azure op te slaan en te openen. In deze zelfstudie gebruikt u de .NET SDK v3. De volgende afbeelding toont de webpagina die u gaat bouwen met behulp van het voorbeeld in dit artikel:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Schermopname van de takenlijst MVC-webtoepassing die is gemaakt met deze zelfstudie - Stapsgewijze zelfstudie voor ASP NET Core MVC":::

Als u geen tijd hebt om de zelfstudie te voltooien, kunt u het volledige voorbeeldproject uit [GitHub][GitHub] downloaden.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken
> * Een ASP.NET Core MVC-app maken
> * De app met Azure Cosmos DB verbinden
> * De ‘maken, lezen, bijwerken en verwijderen’ (CRUD)-bewerkingen uitvoeren op de gegevens

> [!TIP]
> Voor deze zelfstudie wordt ervan uitgegaan dat u ervaring hebt met ASP.NET Core MVC en Azure App Service. Als u niet bekend met ASP.NET Core of de [vereiste hulpprogramma's](#prerequisites) bent, raden we u aan het volledige voorbeeldproject uit [GitHub][GitHub] te downloaden, de vereiste NuGet-pakketten toe te voegen en het project uit te voeren. Zodra u het project gaat maken, kunt u dit artikel lezen voor meer informatie over de code in de context van het project.

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel uitvoert, moet u zorgen dat u beschikt over de volgende resources:

* Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alle schermopnamen in dit artikel zijn van Microsoft Visual Studio Community 2019. Als u een andere versie gebruikt, komen uw schermen en opties mogelijk niet geheel overeen. De oplossing zou moeten werken als u aan de vereisten voldoet.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Stap 1: Een Azure Cosmos-account maken

Begin met het maken van een Azure Cosmos-account. Als u al een Azure Cosmos DB SQL API-account hebt of de Azure Cosmos DB-emulator gebruikt, kunt u direct doorgaan naar [Stap 2: nieuwe ASP.NET MVC-toepassing maken](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

In het volgende gedeelte maakt u een nieuwe ASP.NET Core MVC-toepassing.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Stap 2: Een nieuwe ASP.NET Core MVC-toepassing maken

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Zoek en selecteer **ASP.NET Core-webtoepassing** in **Een nieuw project maken** voor C#. Selecteer **Volgende** om door te gaan.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Nieuw project voor ASP.NET Core-webtoepassing maken":::

1. In **Uw nieuwe project configureren**, geeft u het project de naam *TODO* en selecteert u **Maken**.

1. Kies in **Een nieuwe ASP.NET Core-webtoepassing maken** de optie **Webtoepassing (model-weergave-controller)** . Selecteer **Maken** om door te gaan.

   Visual Studio maakt een lege MVC-toepassing.

1. Selecteer **Fouten opsporen** > **Foutopsporing starten** of F5 om uw ASP.NET-toepassing lokaal uit te voeren.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Stap 3: Azure Cosmos DB NuGet-pakket aan het project toevoegen

Nu we de meeste code voor de ASP.NET Core MVC-framework hebben die nodig is voor deze oplossing, voegen we de NuGet-pakketten toe die zijn vereist voor het verbinden met Azure Cosmos DB.

1. Klik in **Solution Explorer** met de rechtermuisknop op uw project en klik op **NuGet-pakketten beheren**.

1. Zoek en selecteer **Microsoft.Azure.Cosmos** in het **NuGet Package Manager**. Selecteer **Installeren**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="NuGet-pakket installeren":::

   Visual Studio downloadt en installeert het Azure Cosmos DB-pakket en de afhankelijkheden.

   U kunt ook de **console voor Pakketbeheer** gebruiken om het NuGet-pakket te installeren. Om dit te doen, selecteert u **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**. Typ achter de prompt de volgende opdracht:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Stap 4: ASP.NET Core MVC-toepassing instellen

U kunt nu de modellen, weergaven en controllers toevoegen aan deze MVC-toepassing.

### <a name="add-a-model"></a><a name="add-a-model"></a> Een model toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Modellen** en selecteer achtereenvolgens **Toevoegen** > **Klasse**.

1. Noem in **Nieuw item toevoegen** uw nieuwe klasse *Item.cs* en selecteer **Toevoegen**.

1. Vervang de inhoud van de klasse *Item.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB gebruikt JSON om gegevens te verplaatsen en op te slaan. U kunt het kenmerk `JsonProperty` gebruiken om te bepalen hoe JSON objecten serialiseert en deserialiseert. De klasse `Item` demonstreert het kenmerk `JsonProperty`. Met deze code bepaalt u de indeling van de eigenschapsnaam die naar JSON gaat. De naam van de .NET-eigenschap `Completed` wordt ook gewijzigd.

### <a name="add-views"></a><a name="add-views"></a>Weergaven toevoegen

Nu gaan we de volgende weergaven toevoegen.

* Een weergave voor het maken van items
* Een weergave voor het verwijderen van items
* Een weergave voor het ophalen van itemdetails
* Een weergave voor het bewerken van items
* Een weergave voor het vermelden van alle items

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Weergave voor het maken van items

1. Klik met de rechtermuisknop in **Solution Explorer** op de map **Views** en selecteer **Toevoegen** > **Nieuw map**. Geef een naam op voor de map *Item*.

1. Klik met de rechtermuisknop op de lege map **Item** en selecteer vervolgens **Toevoegen** > **Weergave**.

1. Breng de volgende wijzigingen aan in **MVC-weergave toevoegen**:

   * Voer *Maken* in bij **Naam weergeven**.
   * Selecteer **Maken** in **Sjabloon**.
   * Selecteer in **Modelklasse** de optie **Item(todo.Models)** .
   * Selecteer **Een indelingspagina gebruiken** en voer *~/Views/Shared/_Layout. cshtml* in.
   * Selecteer **Toevoegen**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Schermopname van het dialoogvenster MVC-weergave toevoegen":::

1. Vervolgens selecteert u **Toevoegen** en laat u Visual Studio een nieuwe sjabloonweergave maken. Vervang de code in het gegenereerde bestand door de volgende inhoud:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Weergave voor het verwijderen van items

1. Klik vanuit de **Solution Explorer** met de rechtermuisknop opnieuw op de map **Item** en selecteer **Toevoegen** > **Weergave**.

1. Breng de volgende wijzigingen aan in **MVC-weergave toevoegen**:

   * Typ in het vak **Weergavenaam**: *Verwijderen*.
   * Selecteer in het vak **Sjabloon** de optie **Verwijderen**.
   * Selecteer in het vak **Modelklasse** de optie **Item (todo.Models)** .
   * Selecteer **Een indelingspagina gebruiken** en voer *~/Views/Shared/_Layout. cshtml* in.
   * Selecteer **Toevoegen**.

1. Vervolgens selecteert u **Toevoegen** en laat u Visual Studio een nieuwe sjabloonweergave maken. Vervang de code in het gegenereerde bestand door de volgende inhoud:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Weergave voor het ophalen van itemdetails

1. Klik in **Solution Explorer** met de rechtermuisknop opnieuw op de map **Item** en selecteer **Toevoegen** > **Weergave**.

1. Geef de volgende waarden op in **MVC-weergave toevoegen**:

   * Voer in **Weergavenaam** in: *Details*.
   * Selecteer in **Sjabloon** de optie **Details**.
   * Selecteer in **Modelklasse** de optie **Item(todo.Models)** .
   * Selecteer **Een indelingspagina gebruiken** en voer *~/Views/Shared/_Layout. cshtml* in.

1. Vervolgens selecteert u **Toevoegen** en laat u Visual Studio een nieuwe sjabloonweergave maken. Vervang de code in het gegenereerde bestand door de volgende inhoud:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Een weergave voor het bewerken van items toevoegen

1. Klik vanuit de **Solution Explorer** met de rechtermuisknop opnieuw op de map **Item** en selecteer **Toevoegen** > **Weergave**.

1. Breng de volgende wijzigingen aan in **MVC-weergave toevoegen**:

   * Typ in het vak **Weergavenaam***Bewerken*.
   * Selecteer in het vak **Sjabloon** de optie **Bewerken**.
   * Selecteer in het vak **Modelklasse** de optie **Item (todo.Models)** .
   * Selecteer **Een indelingspagina gebruiken** en voer *~/Views/Shared/_Layout. cshtml* in.
   * Selecteer **Toevoegen**.

1. Vervolgens selecteert u **Toevoegen** en laat u Visual Studio een nieuwe sjabloonweergave maken. Vervang de code in het gegenereerde bestand door de volgende inhoud:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Weergave voor het vermelden van alle items

En tot slot voert u de volgende stappen uit om een weergave toe te voegen om alle items op te halen:

1. Klik vanuit de **Solution Explorer** met de rechtermuisknop opnieuw op de map **Item** en selecteer **Toevoegen** > **Weergave**.

1. Breng de volgende wijzigingen aan in **MVC-weergave toevoegen**:

   * In het vak **Weergavenaam** typt u *Index*.
   * Selecteer in het vak **Sjabloon** de optie **Lijst**.
   * Selecteer in het vak **Modelklasse** de optie **Item (todo.Models)** .
   * Selecteer **Een indelingspagina gebruiken** en voer *~/Views/Shared/_Layout. cshtml* in.
   * Selecteer **Toevoegen**.

1. Vervolgens selecteert u **Toevoegen** en laat u Visual Studio een nieuwe sjabloonweergave maken. Vervang de code in het gegenereerde bestand door de volgende inhoud:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Zodra u deze stappen hebt voltooid, sluit u alle *cshtml*-documenten in Visual Studio.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Services declareren en initialiseren

Allereerst voegen we een klasse toe die de logica bevat voor de verbinding met en het gebruik van Azure Cosmos DB. Voor deze zelfstudie kapselen we deze logica in een klasse genaamd `CosmosDbService` en een interface met de naam `ICosmosDbService`. Deze service voert de CRUD-bewerkingen uit. Ook worden de feedbewerkingen gelezen, zoals het weergeven van onvolledige items, het maken, bewerken en verwijderen van de items.

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Toevoegen** > **Nieuwe map**. Geef een naam op voor de map *Services*.

1. Klik met de rechtermuisknop op de map **Services** en selecteer **Toevoegen** > **Klasse**. Noem de nieuwe klasse *CosmoDbService* en selecteer **Toevoegen**.

1. Vervang de inhoud van *CosmoDbService* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klik met de rechtermuisknop op de map **Services** en selecteer **Toevoegen** > **Klasse**. Noem de nieuwe klasse *ICosmoDbService* en selecteer **Toevoegen**.

1. Voeg de volgende code toe aan klasse *ICosmosDbService*:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Open het bestand *Startup.cs* in uw oplossing en voeg de volgende methode toe **InitializeCosmosClientInstanceAsync**, waarmee de configuratie wordt gelezen en de client wordt geïnitialiseerd.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. Vervang op hetzelfde bestand de methode `ConfigureServices` door:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   De code in deze stap initialiseert de client op basis van de configuratie als een singleton-exemplaar dat moet worden geïnjecteerd door [Afhankelijkheidsinjectie in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).

   En zorg ervoor dat u de standaard MVC-controller wijzigt naar `Item` door de routes te bewerken in de methode `Configure` van hetzelfde bestand:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Definieer de configuratie in het *bestand appSettings. json* van het project zoals wordt weer gegeven in het volgende codefragment:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Een controller toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Controllers** en selecteer **Toevoegen** > **Controller**.

1. Selecteer in **Scaffold toevoegen** de optie **MVC-controller: leeg** en selecteer **Toevoegen**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="MVC-controller selecteren - Leeg in Scaffold toevoegen":::

1. Noem uw nieuwe controller *ItemController*.

1. Vervang de inhoud van *ItemController.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Het kenmerk **ValidateAntiForgeryToken** wordt hier gebruikt om deze toepassing te beschermen tegen aanvallen via aanvraagvervalsing op meerdere sites. Uw weergaven werken ook met dit anti-vervalsingstoken. Raadpleeg [Voorkomen van aanvraagvervalsing op meerdere sites (CSRF) in ASP.NET MVC-toepassing][Preventing Cross-Site Request Forgery] voor meer informatie en voorbeelden. De broncode op [GitHub][GitHub] beschikt over de volledige implementatie.

We gebruiken ook het kenmerk **Bind** voor de methodeparameter om u te beschermen tegen over-postingaanvallen. Zie [Zelfstudie: Implementeer CRUD-functionaliteit met de Entity Framework in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Stap 5: De toepassing lokaal uitvoeren

Volg deze stappen als u de toepassing wilt testen op een lokale computer:

1. Druk in Visual Studio op F5 om de toepassing in de foutopsporingsmodus op te bouwen. De toepassing wordt opgebouwd en wordt er een browser gestart met het lege rasterpagina dat we eerder hebben gezien:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Schermopname van de takenlijstwebtoepassing die is gemaakt met deze zelfstudie":::
   
   Als de toepassing in plaats daarvan op de startpagina wordt geopend, voegt u `/Item` toe aan de URL.

1. Selecteer de koppeling **Nieuw maken** en voeg waarden toe aan de velden **Naam** en **Beschrijving**. Zorg ervoor dat het selectievakje **Voltooid** is uitgeschakeld. Als u deze optie selecteert, voegt de app het nieuwe item toe met de status voltooid. Het item wordt niet meer weergegeven in de eerste lijst.

1. Selecteer **Maken**. Met de app keert u terug naar de weergave **Index** en wordt uw item in de lijst weergegeven. U kunt eventueel nog enkele items aan uw **Takenlijst** toevoegen.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Schermopname van de weergave Index":::
  
1. Selecteer **Bewerken** naast een **Item** in de lijst. De app opent de weergave **Bewerken** waar u de eigenschappen van uw object kunt bijwerken, inclusief de vlag **Voltooid**. Als u **Voltooid** selecteert en **Opslaan** selecteert, wordt in de app het **Item** weergegeven als voltooid in de lijst.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Schermopname van de weergave Index met het selectievakje Voltooid ingeschakeld":::

1. Controleer de status van de gegevens in de Azure Cosmos DB-service met [Cosmos Explorer](https://cosmos.azure.com) of de Data Explorer van de Azure Cosmos DB-emulator.

1. Zodra u de app hebt getest, selecteert u Ctrl + F5 om de foutopsporing voor de app te stoppen. U kunt de app nu implementeren.

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Stap 6: De toepassing implementeren

Nu de volledige toepassing correct werkt met Azure Cosmos DB, kunt u de web-app implementeren in Azure App Service.  

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren** om de toepassing te publiceren.

1. Selecteer **App Service** in **Kies een publicatiedoel**.

1. Als u een bestaand App Service-profiel wilt gebruiken, kiest u **Bestaande selecteren** en selecteert u **Publiceren**.

1. Selecteer in **App Service** een **Abonnement**. Sorteer met de filter **Weergave** op resourcegroep of resourcetype.

1. Zoek uw profiel en selecteer vervolgens **OK**. Zoek vervolgens de vereiste Azure App Service en selecteer **OK**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Dialoogvenster App Service in Visual Studio":::

Een andere optie is het maken van een nieuw profiel:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**, net als in de vorige procedure.
  
1. Selecteer **App Service** in **Kies een publicatiedoel**.

1. Selecteer in **Kies een publicatiedoel** **Nieuwe maken** en selecteer **Publiceren**.

1. Voer in **App Service** de naam van uw webtoepassing en het abonnement, de resourcegroep en het hostingabonnement in en selecteer vervolgens **Maken**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Dialoogvenster App-service maken in Visual Studio":::

Over een paar seconden zal Visual Studio uw webtoepassing publiceren en een browser starten waarin u kunt zien hoe uw project in Azure wordt uitgevoerd!

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een ASP.NET Core MVC-webtoepassing bouwt. Uw toepassing kan toegang krijgen tot gegevens die zijn opgeslagen in Azure Cosmos DB. U kunt nu doorgaan met deze resources:

* [Partitionering in Azure Cosmos DB](./partitioning-overview.md)
* [Aan de slag met SQL-query’s](./sql-query-getting-started.md)
* [Meer informatie over het modelleren en partitioneren van gegevens in Azure Cosmos DB aan de hand van een praktijkvoorbeeld](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
