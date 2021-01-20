---
title: Quickstart voor Azure-app-configuratie met ASP.NET Core | Microsoft Docs
description: Maak een ASP.NET Core-toepassing met Azure App Configuration om de opslag en het beheer van toepassingsinstellingen te centraliseren een ASP.NET Core-toepassing.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: d0e564b98ab6331677451afd32a0848e7f5594dd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598748"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Quickstart: Een ASP.NET Core-app maken met Azure-app-configuratie

In deze quickstart gebruikt u Azure App Configuration om de opslag en het beheer van toepassingsinstellingen voor een ASP.NET Core-app te centraliseren. ASP.NET Core maakt één configuratieobject met sleutels en waarden op basis van instellingen van een of meer gegevensbronnen die worden opgegeven door een app. Deze gegevensbronnen worden *configuratieproviders* genoemd. Omdat de .NET Core-client van App Configuration wordt geïmplementeerd als configuratieprovider, wordt de service als elke andere gegevensbron weergegeven.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
* [.NET Core-SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de opdrachtregelinstructies in dit artikel kunt uitvoeren. Deze heeft algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd, met inbegrip van de .NET Core SDK. Als u bent aangemeld bij uw Azure-abonnement, start u [Azure Cloud Shell](https://shell.azure.com) vanaf shell.azure.com. Meer informatie over Azure Cloud Shell vindt u door [onze documentatie te lezen](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Bewerkingen** > **Configuratieverkenner** > **Maken** > **Sleutel-waarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel                                | Waarde                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Gegevens van Azure App Configuration* |

    Laat **Label** en **Inhoudstype** nog even leeg. Selecteer **Toepassen**.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [opdrachtregelinterface (CLI) van .NET Core](/dotnet/core/tools) om een nieuw project van ASP.NET Core MVC te maken. De [Azure Cloud Shell](https://shell.azure.com) biedt u deze hulpprogramma's. Ze zijn ook beschikbaar op het Windows-, macOS- en Linux-platform.

Voer de volgende opdracht uit om een ASP.NET Core MVC-project te maken in een nieuwe *TestAppConfig*-map:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Verbinding maken met het App Configuration-archief

1. Voer de volgende opdracht uit om een verwijzing van een NuGet-pakket voor [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) te maken:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Voer de volgende opdracht uit in dezelfde map als waar het *.csproj*-bestand zich bevindt. De opdracht maakt gebruik van Secret Manager voor het opslaan van een geheim met de naam `ConnectionStrings:AppConfig`, waarin de verbindingstekenreeks voor uw App Configuration-opslagplaats wordt opgeslagen. Vervang de tijdelijke aanduiding `<your_connection_string>` door de verbindingstekenreeks van uw App Configuration-opslagplaats. U vindt de verbindingsreeks onder **Toegangssleutels** in Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Bij sommige shells wordt de verbindingsreeks afgekapt, tenzij deze tussen aanhalingstekens staat. Zorg ervoor dat de uitvoer van de `dotnet user-secrets list`-opdracht de volledige verbindingsreeks toont. Als dat niet het geval is, voert u de opdracht opnieuw uit en plaatst u de verbindingsreeks tussen aanhalingstekens.

    Secret Manager wordt alleen gebruikt om de web-app lokaal te testen. Wanneer de app is geïmplementeerd naar [Azure App Service](https://azure.microsoft.com/services/app-service/web), gebruikt u een toepassingsinstelling **Verbindingsreeksen** in App Service in plaats van Secret Manager om de verbindingsreeks op te slaan.

    Open dit geheim met behulp van de .NET Core-configuratie-API. Een dubbele punt (`:`) werkt in de configuratienaam met de configuratie-API op alle ondersteunde platforms. Zie [Configuratiesleutels en -waarden](/aspnet/core/fundamentals/configuration#configuration-keys-and-values) voor meer informatie.

1. Voeg in *Program.cs* een verwijzing toe naar de naamruimte van de .NET Core Configuration-API:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Werk de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `AddAzureAppConfiguration` aan te roepen.

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
                    config.AddAzureAppConfiguration(connection);
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
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Door de voorgaande wijziging is de [configuratieprovider voor App Configuration](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) geregistreerd bij de .NET Core-Configuratie-API.

## <a name="read-from-the-app-configuration-store"></a>Uitlezen uit het App Configuration-archief

Voer de volgende stappen uit om waarden die in het App Configuration-archief zijn opgeslagen, uit te lezen en weer te geven. The .NET Core Configuration-API wordt gebruikt om toegang tot het archief te krijgen. De waarden van de sleutels worden weergegeven in Razor-syntaxis.

Open *\<app root>/Views/Home/Index.cshtml* en vervang de inhoud door de volgende code:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

In de voorgaande code worden de sleutels van het App Configuration-archief als volgt gebruikt:

* De waarde van de sleutel `TestApp:Settings:BackgroundColor` wordt toegewezen aan de CSS-eigenschap `background-color`.
* De waarde van de sleutel `TestApp:Settings:FontColor` wordt toegewezen aan de CSS-eigenschap `color`.
* De waarde van de sleutel `TestApp:Settings:FontSize` wordt toegewezen aan de CSS-eigenschap `font-size`.
* De waarde van de sleutel `TestApp:Settings:Message` wordt weergegeven als kop.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt compileren met de .NET Core CLI, gaat u naar de hoofdmap van uw project. Voer de volgende opdracht uit in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat het compileren is voltooid, gebruikt u de volgende opdracht om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Als u op uw lokale computer werkt, gebruikt u een browser om naar `http://localhost:5000` te navigeren. Dit adres is de standaard-URL voor de lokaal gehoste web-app. Als u werkt in Azure Cloud Shell, selecteert u de knop **Web-preview** gevolgd door **Configureren**.

    ![De knop Web-preview zoeken](./media/quickstarts/cloud-shell-web-preview.png)

    Wanneer u wordt gevraagd om de poort voor de preview te configureren, voert u *5000* in en selecteert u **Openen en bladeren**. Op de webpagina staat Gegevens van Azure App Configuration.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* Een nieuw App Configuration-archief ingericht
* De .NET Core-configuratieprovider van het App Configuration-archief geregistreerd
* De sleutels van het App Configuration-archief uitgelezen met de configuratieprovider
* De waarden van de sleutels van het App Configuration-archief weergegeven in Razor-syntaxis

Ga door naar de volgende zelfstudie voor meer informatie over het configureren van uw ASP.NET Core-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-aspnet-core.md)