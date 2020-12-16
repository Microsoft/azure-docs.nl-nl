---
title: Quickstart voor Azure-app-configuratie met Azure Functions | Microsoft Docs
description: Maak in deze quickstart een Azure Functions-app met Azure App Configuration en C#. Maak een App Configuration-archief en maak er verbinding mee. Test de functie lokaal.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 4197891949062123042736e578cfbcc5def4e1f9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930786"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Quickstart: Een Azure Functions-app maken met Azure App Configuration

In deze quickstart neemt u de service Azure App Configuration op in een Azure Functions-app om opslag en beheer van al uw toepassingsinstellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de workload **Azure Development**.
- [Hulpprogramma's van Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** >  **+ Maken** > **Sleutel-waarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg.

8. Selecteer **Toepassen**.

## <a name="create-a-functions-app"></a>Een Functions-app maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Klik met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek op het tabblad **Bladeren** naar het NuGet-pakket `Microsoft.Extensions.Configuration.AzureAppConfiguration` en voeg het toe aan uw project. Als u het pakket niet kunt vinden, schakelt u het selectievakje **Prerelease insluiten** in.

2. Open *Function1.cs* en voeg de naamruimten van de .NET Core-configuratie en de configuratieprovider App Configuration toe.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Voeg een `static`-eigenschap met de naam `Configuration` toe om een singleton-exemplaar van `IConfiguration` te maken. Voeg vervolgens een `static`-constructor toe om verbinding te maken met App Configuration door `AddAzureAppConfiguration()` aan te roepen. Hiermee wordt de configuratie eenmaal geladen bij het opstarten van de toepassing. Hetzelfde configuratie-exemplaar wordt later gebruikt voor alle Functions-aanroepen.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Werk de methode `Run` bij om waarden te lezen uit de configuratie.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>De functie lokaal testen

1. Stel een omgevingsvariabele in met de naam **ConnectionString** en stel deze in op de toegangssleutel van het App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om **Azure Functions Core-hulpprogramma's (CLI)** te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de afbeelding hieronder ziet u de reactie in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt en dit via de [provider App Configuration](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) gebruikt met een Azure Functions-app. Ga door naar de volgende zelfstudie voor meer informatie over het configureren van uw Azure Functions-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-azure-functions-csharp.md)