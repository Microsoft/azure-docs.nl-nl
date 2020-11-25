---
title: 'Quickstart: functievlaggen toevoegen aan ASP.NET Core'
description: U kunt functievlaggen toevoegen aan ASP.NET Core-apps en deze beheren met Azure App Configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: 88481346f22176b8e307b53774b42d753838f90b
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554819"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Quickstart: functievlaggen toevoegen aan een ASP.NET Core-app

In deze quickstart maakt u een end-to-end-implementatie van functiebeheer in een ASP.NET Core-app met behulp van Azure App Configuration. U gebruikt de App Configuration-service om al uw functievlaggen centraal op te slaan en hun status te bepalen. 

De functiebeheerbibliotheken van .NET Core breiden het framework uit met uitgebreide ondersteuning voor functievlaggen. Deze bibliotheken worden boven op het .NET Core-configuratiesysteem gebouwd. Ze kunnen naadloos worden geïntegreerd met App Configuration via de configuratieprovider voor .NET Core.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
* [.NET Core-SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Selecteer **Bewerkingen** > **Functiebeheerder** > **Toevoegen** om een functievlag met de naam *Beta* toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Functievlag met de naam Beta inschakelen](media/add-beta-feature-flag.png)

    Laat **Label** nog leeg. Selecteer **Toepassen** om de nieuwe functievlag op te slaan.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [opdrachtregelinterface (CLI) van .NET Core](/dotnet/core/tools) om een nieuw project van ASP.NET Core MVC te maken. Het voordeel van de CLI van .NET Core ten opzichte van Visual Studio is dat de CLI van .NET Core beschikbaar is voor Windows, macOS en Linux.

Voer de volgende opdracht uit om een ASP.NET Core MVC-project te maken in een nieuwe *TestFeatureFlags*-map:

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Installeer de NuGet-pakketten [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) en [Microsoft.FeatureManagement.AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) door de volgende opdrachten uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Voer de volgende opdracht uit in dezelfde map als waar het *.csproj*-bestand zich bevindt. De opdracht maakt gebruik van Secret Manager voor het opslaan van een geheim met de naam `ConnectionStrings:AppConfig`, waarin de verbindingstekenreeks voor uw App Configuration-opslagplaats wordt opgeslagen. Vervang de tijdelijke aanduiding `<your_connection_string>` door de verbindingstekenreeks van uw App Configuration-opslagplaats. U vindt de verbindingsreeks onder **Toegangssleutels** in Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    Secret Manager wordt alleen gebruikt om de web-app lokaal te testen. Wanneer de app is geïmplementeerd naar [Azure App Service](https://azure.microsoft.com/services/app-service/web), gebruikt u een toepassingsinstelling **Verbindingsreeksen** in App Service in plaats van Secret Manager om de verbindingsreeks op te slaan.

    Open dit geheim met behulp van de .NET Core-configuratie-API. Een dubbele punt (`:`) werkt in de configuratienaam met de configuratie-API op alle ondersteunde platforms. Zie [Configuratiesleutels en -waarden](/aspnet/core/fundamentals/configuration#configuration-keys-and-values) voor meer informatie.

1. Werk in *Program.cs* de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `AddAzureAppConfiguration` aan te roepen.

    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.x. Selecteer de juiste syntaxis op basis van uw omgeving.

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Door de voorgaande wijziging is de [configuratieprovider voor App Configuration](https://go.microsoft.com/fwlink/?linkid=2074664) geregistreerd bij de .NET Core-Configuratie-API.

1. Voeg in *Startup.cs* verwijzingen toe naar functiebeheer van .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Werk de methode `Startup.ConfigureServices` bij om ondersteuning voor functievlaggen toe te voegen door de methode `AddFeatureManagement` aan te roepen. U kunt eventueel ook een filter toevoegen dat moet worden gebruikt met functievlaggen door `AddFeatureFilter<FilterType>()` aan te roepen:

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Voeg een *MyFeatureFlags.cs*-bestand toe aan de hoofdmap van het project met de volgende code:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Voeg een *BetaController.cs*-bestand toe aan de map *Controllers* met de volgende code:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. In *Views/_ViewImports.cshtml* moet u het hulpprogramma voor functiebeheer registreren met behulp van een `@addTagHelper`-instructie:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Met de voorgaande code kan de `<feature>` Tag Helper worden gebruikt in de *.cshtml*-bestanden van het project.

1. Open *_Layout.cshtml* in de map *Weergaven*\\*Gedeeld*. Zoek de streepjescode `<nav>` onder `<body>` > `<header>`. Voeg een nieuwe tag `<feature>` in tussen de navigatiebalkitems *Start* en *Privacy*, zoals wordt weergegeven in de gemarkeerde regels hieronder.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="13-17":::

1. Maak een map *Views/Beta* en een *Index.cshtml*-bestand met de volgende opmaak:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Open een browservenster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost. Als u werkt in Azure Cloud Shell, selecteert u de knop **Web-preview** gevolgd door **Configureren**. Selecteer poort 5000 wanneer hierom wordt gevraagd.

    ![De knop Web-preview zoeken](./media/quickstarts/cloud-shell-web-preview.png)

    In uw browser wordt een pagina weergegeven die vergelijkbaar is met de onderstaande afbeelding.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="Lokale snelstart-app voor wijziging" border="true":::

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Functiebeheer**. 

1. Schakel de vlag *Bèta* in door het selectievakje onder **Ingeschakeld** aan te vinken.

1. Ga terug naar de opdrachtshell. Annuleer het actieve `dotnet`-proces door op <kbd>CTRL + C</kbd>te drukken. Start de app opnieuw op met `dotnet run`.

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="Lokale snelstart-app na wijziging" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt en dit gebruikt om functies in een ASP.NET Core-web-app te beheren via de [functiebeheerbibliotheken](https://go.microsoft.com/fwlink/?linkid=2074664).

* Meer informatie over [functiebeheer](./concept-feature-management.md).
* [Functievlaggen beheren](./manage-feature-flags.md).
* [Functievlaggen gebruiken in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
* [Dynamische configuratie in een ASP.NET Core-app gebruiken](./enable-dynamic-configuration-aspnet-core.md)
