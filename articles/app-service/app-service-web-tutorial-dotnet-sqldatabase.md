---
title: 'Zelfstudie: ASP.NET-app met Azure SQL Database'
description: Informatie over het implementeren van een C# ASP.NET-app naar Azure en Azure SQL Database
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605974"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Zelfstudie: Een ASP.NET-app implementeren in Azure met Azure SQL Database

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelfstudie leert u hoe u een gegevensgestuurde ASP.NET-app implementeert in App Service en de app verbindt met [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Wanneer u klaar bent, hebt u een ASP.NET-app die wordt uitgevoerd in Azure en is verbonden met SQL Database.

![Gepubliceerde ASP.NET-toepassing in Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een database maken in Azure SQL Database
> * Een ASP.NET-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken vanaf Azure naar uw terminal streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload** installeren.

Als u Visual Studio al hebt geïnstalleerd, voegt u de workloads toe in Visual Studio door te klikken op **Hulpprogramma's** > **Hulpprogramma's en functies ophalen**.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

1. [Download het voorbeeldproject](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Extraheer (uitpakken) het *dotnet-sqldb-zelfstudie-master.zip*-bestand.

Het voorbeeldproject bevat een eenvoudige [ASP.NET MVC](https://www.asp.net/mvc)-CRUD-app (create-read-update-delete) die gebruikmaakt van [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>De app uitvoeren

1. Open het bestand *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* in Visual Studio.

1. Typ `Ctrl+F5` voor het uitvoeren van de app zonder foutopsporing. De app wordt weergegeven in de standaardbrowser.

1. Selecteer de koppeling **Nieuwe maken** en maak enkele *taakitems*.

    ![Het dialoogvenster Nieuw ASP.NET-project](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Test de links **Bewerken**, **Details** en **Verwijderen**.

De app maakt gebruik van een databasecontext om met de database te verbinden. In dit voorbeeld gebruikt de databasecontext een verbindingsreeks met de naam `MyDbConnection`. De verbindingsreeks waarnaar wordt verwezen in het bestand *Models/MyDatabaseContext.cs* is ingesteld in het bestand *Web.config*. De naam van de tekenreeks wordt verderop in de zelfstudie gebruikt om de Azure-app te verbinden met een Azure SQL Database.

## <a name="publish-aspnet-application-to-azure"></a>De ASP.NET-toepassing publiceren in Azure

1. Klik in de **Solution Explorer** met de rechtermuisknop op uw project **DotNetAppSqlDb** en selecteer **Publiceren**.

    ![Publiceren vanuit Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Selecteer **Azure** als doel en klik op **volgende**.

1. Zorg ervoor dat **Azure app service (Windows)** is geselecteerd en klik op **volgende**.

#### <a name="sign-in-and-add-an-app"></a>Aanmelden en een app toevoegen

1. Klik in het dialoog venster **publiceren** op **een account toevoegen** in de vervolg keuzelijst account beheerder.

1. Meld u aan bij uw Azure-abonnement. Als u al bent aangemeld bij een Microsoft-account, moet u ervoor zorgen dat dit account uw Azure-abonnement bevat. Als het aangemelde Microsoft-account niet uw Azure-abonnement bevat, klikt u erop om het juiste account toe te voegen.

1. Klik in het deel venster **instanties van app service** op **+** .

    ![Aanmelden bij Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Naam van web-app configureren

U kunt de naam van de gegenereerde web-app houden of wijzigen in een andere unieke naam (geldige tekens zijn `a-z`, `0-9` en `-`). De naam van de web-app wordt gebruikt als onderdeel van de standaard-URL voor uw app (`<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam is van uw web-app). De naam van de web-app moet uniek zijn in alle apps in Azure.

> [!NOTE]
> Selecteer nog geen **maken** .

![Het dialoogvenster App Service maken](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Klik naast **Resourcegroep** op **Nieuw**.

   ![Klik naast Resourcegroep op Nieuw.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Noem de resourcegroep **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Klik naast **hosting plan** op **Nieuw**.

1. Configureer in het dialoog venster **app service plan configureren** het nieuwe app service plan met de volgende instellingen en klik op **OK**:

   | Instelling  | Voorgestelde waarde | Voor meer informatie |
   | ----------------- | ------------ | ----|
   |**App Service Plan**| myAppServicePlan | [App Service-abonnementen](../app-service/overview-hosting-plans.md) |
   |**Locatie**| Europa -west | [Azure-regio's](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Grootte**| Gratis | [Prijscategorieën](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Een App Service-plan maken](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Klik op **maken** en wacht tot de Azure-resources zijn gemaakt.

1. In het dialoogvenster **Publiceren** ziet u de resources die u hebt geconfigureerd. Klik op **Voltooien**.

   ![de resources die u hebt gemaakt](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Een server en database maken

Voordat u een database maakt, hebt u een [logische SQL-server](../azure-sql/database/logical-servers.md) nodig. Een logische SQL-server is een logische omgeving met een groep met databases die worden beheerd als groep.

1. Schuif in het dialoog venster **publiceren** omlaag naar de sectie **service afhankelijkheden** . Klik naast **SQL Server Data Base** op **configureren**.

   ![SQL Database afhankelijkheid configureren](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Selecteer **Azure SQL database** en klik op **volgende**.

1. Klik in het dialoog venster **Azure SQL database configureren** op **+** .

1. Klik naast **database server** op **Nieuw**.

   Er wordt een server naam gegenereerd. Deze naam wordt gebruikt als onderdeel van de standaard-URL voor de server `<server_name>.database.windows.net`. Deze moet uniek zijn op alle servers in Azure SQL. U kunt de servernaam wijzigen, maar bewaar voor deze zelfstudie de gegenereerde waarde.

1. Voeg een administrator-gebruikersnaam en een wachtwoord toe. Zie [Wachtwoordbeleid](/sql/relational-databases/security/password-policy) voor de vereisten voor wachtwoordcomplexiteit.

   Onthoud deze gebruikersnaam en dit wachtwoord. U hebt deze later nodig om de server te beheren.

   ![Server maken](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Hoewel u uw wachtwoord in de verbindingsreeksen wordt gemaskeerd (in Visual Studio en ook in App Service), vergroot het feit dat het ergens wordt bijgehouden de kwetsbaarheid van uw app voor aanvallen. App Service kan [beheerde service-identiteiten](overview-managed-identity.md) gebruiken om dit risico te elimineren door het bijhouden van geheimen in uw code of app-configuratie geheel onnodig te maken. Zie voor meer informatie [Volgende stappen](#next-steps).

1. Klik op **OK**.

1. In het dialoog venster **Azure SQL database** behoudt u de standaard naam van de gegenereerde **Data Base**. Selecteer **maken** en wacht tot de database resources zijn gemaakt.

    ![Database configureren](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Database verbinding configureren

1. Klik op **volgende** wanneer de wizard klaar is met het maken van de database resources.

1. Typ _MyDbConnection_ in de naam van de **Data Base Connection String**. Deze naam moet overeenkomen met de verbindingsreeks waarnaar wordt verwezen in _Models/MyDatabaseContext.cs_.

1. Typ in de **gebruikers naam van de database verbinding** en het wacht woord voor de **database verbinding** de gebruikers naam en het wacht woord van de beheerder die u hebt gebruikt in [een server maken](#create-a-server-and-database).

1. Zorg ervoor dat **Azure-app instellingen** is geselecteerd en klik op **volt ooien**.

    ![De databaseverbindingsreeks configureren](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Wacht totdat de configuratie wizard is voltooid en klik op **sluiten**.

#### <a name="deploy-your-aspnet-app"></a>Uw ASP.NET-app implementeren

1. Blader in het tabblad **publiceren** naar de bovenkant en klik op **publiceren**. Zodra de ASP.NET-app is geïmplementeerd in Azure. De standaardbrowser wordt gestart met de URL van de geïmplementeerde app.

1. Voeg enkele taakitems toe.

    ![Gepubliceerde ASP.NET-toepassing in Azure-app](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Gefeliciteerd! Uw gegevensgestuurde ASP.NET-toepassing wordt live in Azure App Service uitgevoerd.

## <a name="access-the-database-locally"></a>Lokale toegang tot de database

Met Visual Studio kunt u eenvoudig uw nieuwe data base in azure verkennen en beheren in de **SQL Server-objectverkenner**. De nieuwe data base heeft de firewall al geopend in de App Service-app die u hebt gemaakt, maar om toegang te krijgen vanaf uw lokale computer (bijvoorbeeld vanuit Visual Studio), moet u een firewall openen voor het open bare IP-adres van uw lokale computer. Als uw Internet serviceprovider uw open bare IP-adres wijzigt, moet u de firewall opnieuw configureren voor toegang tot de Azure-data base.

#### <a name="create-a-database-connection"></a>Een databaseverbinding maken

1. Vanuit het **Weergave**-menu selecteert u **SQL Server-objectverkenner**.

1. Aan de bovenkant van **SQL Server-objectverkenner**, klikt u op de knop **SQL Server toevoegen**.

#### <a name="configure-the-database-connection"></a>Verbinding met de database configureren

1. Vouw in het dialoogvenster **Verbinden** het **Azure**-knooppunt uit. Al uw SQL Database-exemplaren in Azure worden hier weergegeven.

1. Selecteer de database die u eerder hebt gemaakt. De verbinding die u eerder hebt gemaakt, wordt automatisch onderaan ingevuld.

1. Typ het wachtwoord van de databasebeheerder dat u eerder hebt gemaakt en klik op **Verbinden**.

    ![Databaseverbinding vanuit Visual Studio configureren](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Clientverbinding vanaf uw computer toestaan

Het dialoogvenster **Een nieuwe firewallregel maken**. Een server staat standaard alleen verbindingen naar de bijbehorende databases toe vanuit Azure-services, zoals uw Azure-app. Maak een firewallregel op serverniveau om buiten Azure verbinding te maken met uw database. De firewallregel staat het openbare IP-adres van uw lokale computer toe.

In het dialoogvenster is het openbare IP-adres van uw computer al ingevuld.

1. Zorg ervoor dat **Mijn client-IP toevoegen** is geselecteerd en klik op **OK**.

    ![Firewallregel maken](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Zodra de Visual Studio klaar is met het maken van de firewallinstelling voor uw exemplaar van SQL Database, wordt uw verbinding weergegeven in **SQL Server-objectverkenner**.

    Hier kunt u de meest voorkomende databasebewerkingen uitvoeren, zoals het uitvoeren van query's, het maken van weergaven en opgeslagen procedures en meer.

1. Vouw uw verbinding uit > **Databases** >  **&lt;uw database >**  > **Tabellen**. Klik met de rechtermuisknop op de `Todoes`-tabel en selecteer **Gegevens weergeven**.

    ![SQL Database-objecten verkennen](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>De app bijwerken met Code First Migrations

U kunt de vertrouwde hulpprogramma's in Visual Studio gebruiken om uw database en app in Azure bij te werken. In deze stap kunt u met Code First Migrations in Entity Framework een wijziging aanbrengen in uw databaseschema en deze publiceren naar Azure.

Zie voor meer informatie over het gebruik van Entity Framework Code First Migrations, [Aan de slag met Entity Framework 6 Code First met MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

#### <a name="update-your-data-model"></a>Gegevensmodel bijwerken

Open _Models\Todo.cs_ in de code-editor. Voeg de volgende eigenschap toe aan de klasse `ToDo`:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Code First Migrations lokaal uitvoeren

Voer enkele opdrachten uit om de lokale database bij te werken.

1. Klik in het menu **Hulpprogramma’s** op **NuGet Package Manager** > **Package Manager Console**.

1. Schakel in het Package Manager Console-venster Code First Migrations in:

    ```powershell
    Enable-Migrations
    ```
    
1. Voeg een migratie toe:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Werk de lokale database bij:

    ```powershell
    Update-Database
    ```
    
1. Typ `Ctrl+F5` om de app uit te voeren. Test de links voor bewerkingen, details, en maken.

Als de toepassing wordt geladen zonder fouten, is Code First Migrations voltooid. Uw pagina ziet er echter nog steeds hetzelfde uit omdat uw toepassingslogica deze nieuwe eigenschap niet nog gebruikt.

#### <a name="use-the-new-property"></a>Nieuwe eigenschap gebruiken

Breng enkele wijzigingen aan de code aan zodat de eigenschap `Done` kan worden gebruikt. Om deze zelfstudie eenvoudig te houden, wijzigt u eerst de weergaven `Index` en `Create` om de eigenschap in actie te zien.

1. Open _Controllers\TodosController.cs_.

1. Zoek de methode `Create()` in regel 52 en voeg `Done` toe aan de lijst met eigenschappen in het kenmerk `Bind`. Als u klaar bent, ziet uw methode `Create()` er uit als de onderstaande code:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Open _Views\Todos\Create.cshtml_.

1. In de Razor-code zou u een `<div class="form-group">`-element moeten zien dat `model.Description` gebruikt en vervolgens een ander `<div class="form-group">`-element voor `model.CreatedDate`. Voeg direct na deze twee elementen een ander `<div class="form-group">`-element voor `model.Done` toe:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Open _Views\Todos\Index.cshtml_.

1. Zoek het lege element `<th></th>`. Vlak boven dit element voegt u de volgende Razor-code toe:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Zoek het `<td>`-element dat de helpermethoden `Html.ActionLink()` bevat. Voeg _boven_ dit `<td>` een ander `<td>`-element toe met de volgende Razor-code:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Meer hebt u niet nodig om de wijzigingen in de weergaven `Index` en `Create` te zien.

1. Typ `Ctrl+F5` om de app uit te voeren.

U kunt nu een taakitem toevoegen en **Gereed** aanvinken. Het moet nu als een voltooid taakitem worden weergegeven op uw startpagina. In de weergave `Edit` wordt het veld `Done` niet getoond omdat u de weergave `Edit` niet hebt gewijzigd.

#### <a name="enable-code-first-migrations-in-azure"></a>Code First Migrations in Azure uitvoeren

Nu het wijzigen van uw code werkt, met inbegrip van de databasemigratie, moet u deze publiceren naar uw Azure-app en ook uw SQL Database bijwerken met Code First Migrations.

1. Klik net als voorheen met de rechtermuisknop op uw project en selecteer **Publiceren**.

1. Klik op **meer acties**  >  **bewerken** om de publicatie-instellingen te openen.

    ![Publicatie-instellingen openen](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. Selecteer in de vervolg keuzelijst **MyDatabaseContext** de database verbinding voor uw Azure SQL database.

1. Selecteer **Code First-migraties uitvoeren (wordt uitgevoerd bij de start van toepassing)** , klik vervolgens op **Opslaan**.

    ![Code First Migrations inschakelen in Azure-app](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Uw wijzigingen publiceren

Nu dat u Code First Migrations in uw Azure-app hebt ingeschakeld, moet u uw codewijzigingen publiceren.

1. Klik op de publicatiepagina op **Publiceren**.

1. Probeer opnieuw taakitems toe te voegen en selecteer **Gereed**, dan moeten ze worden weergegeven in uw startpagina als een voltooid item.

    ![Azure-app na Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Alle bestaande taakitems worden nog steeds weergegeven. Als u de ASP.NET Core-toepassing opnieuw publiceert, blijven bestaande gegevens in SQL Database behouden. En met Code First Migrations wordt alleen het gegevensschema gewijzigd. De bestaande gegevens blijven ongewijzigd.

## <a name="stream-application-logs"></a>Toepassingslogboeken streamen

U kunt traceringsberichten rechtstreeks vanuit uw Azure-app streamen met Visual Studio.

Open _Controllers\TodosController.cs_.

Elke actie begint met een `Trace.WriteLine()`-methode. Deze code wordt toegevoegd om te tonen hoe u traceringsberichten toevoegt aan uw Azure-app.

#### <a name="enable-log-streaming"></a>Logboekstreaming activeren

1. Selecteer in het menu **weer gave** de optie **Cloud Explorer**.

1. Vouw in **Cloud Explorer** het Azure-abonnement uit met uw app en vouw **app service** uit.

1. Klik met de rechtermuisknop op uw Azure-app en selecteer **Streaminglogboeken bekijken**.

    ![Logboekstreaming activeren](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    De logboeken worden nu gestreamd naar het venster **Uitvoer**.

    ![Logboekstreaming in het venster Uitvoer](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    U ziet echter nog geen traceerberichten. Wanneer u voor het eerst **Streaminglogboeken bekijken** selecteert, stelt uw Azure-app namelijk eerst het traceringsniveau op `Error` in. Dit legt alleen foutgebeurtenissen vast (met de methode `Trace.TraceError()`).

#### <a name="change-trace-levels"></a>Traceringsniveaus wijzigen

1. Als u de tracerings niveaus wilt wijzigen om andere tracerings berichten uit te voeren, gaat u terug naar **Cloud Explorer**.

1. Klik met de rechter muisknop op uw app en selecteer **openen in portal**.

1. Selecteer op de pagina Portal beheer voor uw app in het linkermenu **app service logboeken**.

1. Onder **toepassings Logboeken (bestands systeem)** selecteert u **uitgebreid** in **niveau**. Klik op **Opslaan**.

    ![Wijzig het traceringsniveau naar Uitgebreid](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > U kunt experimenteren met verschillende traceringsniveaus om te zien welke typen berichten worden weergegeven voor elk niveau. Het niveau **Informatie** omvat bijvoorbeeld alle logboeken die zijn gemaakt voor `Trace.TraceInformation()`, `Trace.TraceWarning()` en `Trace.TraceError()`, maar niet de logboeken die zijn gemaakt door `Trace.WriteLine()`.

1. Navigeer in uw browser opnieuw naar uw app op *http://&lt;uw app-naam>.azurewebsites.net*, probeer vervolgens rond te klikken in de lijst met taken in Azure. Nu worden de traceringsberichten gestreamd naar het venster **Uitvoer** in Visual Studio.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Logboekstreaming stoppen

Om de service logboekstreaming te stoppen, klikt u op de knop **Bewaking stoppen** in het venster **Uitvoer**.

![Logboekstreaming stoppen](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Uw Azure-app beheren

Ga naar [Azure Portal](https://portal.azure.com) om de web-app te beheren. Zoek en selecteer **App Services**.

![Zoeken naar Azure App Services](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Selecteer de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

U bevindt zich op de pagina van uw app.

In de portal wordt standaard de pagina **Overzicht** getoond. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![App Service-pagina in Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Een database maken in Azure SQL Database
> * Een ASP.NET-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken vanaf Azure naar uw terminal streamen
> * De app in Azure Portal beheren

Ga naar de volgende zelfstudie om te ontdekken hoe u eenvoudig de beveiliging van uw verbinding met Azure SQL Database kunt verbeteren.

> [!div class="nextstepaction"]
> [Veilige toegang tot SQL Database met behulp van beheerde identiteiten voor Azure-resources](app-service-web-tutorial-connect-msi.md)

Meer resources:

> [!div class="nextstepaction"]
> [ASP.NET configureren](configure-language-dotnet-framework.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)