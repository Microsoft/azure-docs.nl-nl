---
title: Azure App Configuration integreren met behulp van een pijplijn voor continue integratie en levering
description: Leer hoe u continue integratie en levering kunt implementeren met behulp van Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: 3a4d171f0e3225db195c5c2b71ca99a3386e3a36
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979841"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integreren met een CI/CD-pijplijn

In dit artikel wordt uitgelegd hoe u gegevens uit Azure App Configuration kunt gebruiken in een implementatiesysteem voor continue integratie en levering.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>App Configuration gebruiken in uw Azure DevOps Pipeline

Als u beschikt over een Azure DevOps Pipeline, kunt u sleutel-waarden ophalen uit App Configuration, en deze instellen als taakvariabelen. De [Azure App Configuration DevOps-extensie](https://go.microsoft.com/fwlink/?linkid=2091063) is een invoegtoepassingsmodule die deze functionaliteit biedt. Volg de bijbehorende instructies voor het gebruik van de extensie in een build- of releasetaakreeks.

## <a name="deploy-app-configuration-data-with-your-application"></a>App Configuration-gegevens implementeren met uw toepassing

Het uitvoeren van de toepassing kan mislukken als de toepassing afhankelijk is van Azure App Configuration en deze niet kan bereiken. Verbeter de tolerantie van de toepassing door configuratiegegevens te verpakken in een bestand dat met de toepassing wordt geïmplementeerd, en dat lokaal wordt geladen tijdens het opstarten van de toepassing. Deze aanpak garandeert dat de toepassing standaardinstellingswaarden heeft bij het opstarten. Deze waarden worden overschreven door nieuwere wijzigingen in een App Configuration-archief wanneer deze beschikbaar zijn.

Met de functie [Exporteren](./howto-import-export-data.md#export-data) van Azure App Configuration kunt u het ophalen van de huidige configuratiegegevens als één bestand automatiseren. U kunt dit bestand insluiten in een build- of implementatiestap in de CI/CD-pijplijn (continue integratie en continue implementatie).

In het volgende voorbeeld ziet u hoe u App Configuration-gegevens kunt opnemen als stap voor het bouwen van de web-app die is geïntroduceerd in de quickstarts. Volg eerst [Een ASP.NET Core-app maken met App Configuration](./quickstart-aspnet-core-app.md) voordat u verder gaat.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

### <a name="prerequisites"></a>Vereisten

Als u lokaal bouwt, downloadt en installeert u de [Azure CLI](/cli/azure/install-azure-cli) als u dit nog niet hebt gedaan.

Als u een cloud-build gebruikt, bijvoorbeeld met Azure DevOps, zorgt u ervoor dat de [Azure CLI](/cli/azure/install-azure-cli) is geïnstalleerd in het buildsysteem.

### <a name="export-an-app-configuration-store"></a>Een App Configuration-archief exporteren

1. Open het *.csproj*-bestand, en voeg het volgende script toe:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Open *Program.cs* en werk de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `config.AddJsonFile()` aan te roepen.  Voeg ook de naamruimte `System.Reflection` toe.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele in met de naam **ConnectionString** en stel deze in op de toegangssleutel van het App Configuration-archief. 
    Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```console
     dotnet build
    ```

3. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```console
     dotnet run
    ```

4. Open een browservenster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure App Configuration-gegevens geëxporteerd die moeten worden gebruikt in een implementatiepijplijn. Voor meer informatie over het gebruik van App Configuration gaat u verder naar de Azure CLI-voorbeelden.

> [!div class="nextstepaction"]
> [Azure-CLI](/cli/azure/appconfig)
