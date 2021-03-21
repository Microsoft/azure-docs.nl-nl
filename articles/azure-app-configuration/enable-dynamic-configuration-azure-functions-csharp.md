---
title: Zelfstudie voor het gebruik van dynamische configuratie van Azure App Configuration in een Azure Functions-app | Microsoft Docs
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor Azure Functions-apps dynamisch bijwerkt.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963554"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een Azure Functions-app

De .NET-configuratieprovider van App Configuration ondersteunt het dynamisch cachen en vernieuwen van een configuratie op basis van toepassingsactiviteit. In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. We maken gebruik van de Azure Functions-app die is geïntroduceerd in de quickstarts. Voordat u verdergaat, moet u eerst de quickstart [Een Azure Functions-app maken met Azure App Configuration](./quickstart-azure-functions-csharp.md) voltooien als u dat nog niet hebt gedaan.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw Azure Functions-app instellen voor het bijwerken van de configuratie als reactie op wijzigingen in een archief van App Configuration.
> * De nieuwste configuratie injecteren in uw Azure Functions-aanroepen.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de workload **Azure development**
- [Hulpprogramma's van Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Neem de quickstart [Een Azure Functions-app maken met Azure App Configuration](./quickstart-azure-functions-csharp.md) door voordat u verdergaat.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Open *Startup.cs* en werk de methode `ConfigureAppConfiguration` bij. 

   De methode `ConfigureRefresh` registreert een instelling die moet worden gecontroleerd op wijzigingen wanneer een vernieuwing wordt geactiveerd binnen de toepassing. U gaat dit in een latere stap doen wanneer u `_configurationRefresher.TryRefreshAsync()` toevoegt. Met de parameter `refreshAll` wordt de App Configuration-provider geïnstrueerd de volledige configuratie opnieuw te laden wanneer een wijziging wordt gedetecteerd in de geregistreerde instelling.

    Alle instellingen die voor vernieuwen zijn geregistreerd, hebben een standaard cachetijd van 30 seconden. Het kan worden bijgewerkt door de methode `AzureAppConfigurationRefreshOptions.SetCacheExpiration` aan te roepen.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Wanneer u meerdere sleutelwaarden in App Configuration bijwerkt, is het de bedoeling dat uw toepassing de configuratie niet opnieuw laadt voordat alle wijzigingen worden aangebracht. U kunt een **Sentinel**-sleutel registreren en deze alleen bijwerken wanneer alle andere configuratiewijzigingen zijn voltooid. Dit helpt de consistentie van de configuratie in uw toepassing te garanderen.

2. Werk de methode `Configure` bij om Azure App Configuration-services beschikbaar te maken via afhankelijkheidsinjectie.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Open *Function1.cs* en voeg de volgende naamruimten toe.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Werk de constructor bij om het exemplaar van `IConfigurationRefresherProvider` te verkrijgen via afhankelijkheidsinjectie, van waaruit u het exemplaar van `IConfigurationRefresher` kunt verkrijgen.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Werk de methode `Run` bij en geef aan het begin van de Functions-aanroep aan dat de configuratie moet worden bijgewerkt met de methode `TryRefreshAsync`. Er gebeurt niets (no-op) als de vervaltijd van de cache niet wordt bereikt. Verwijder de operator `await` als u de configuratie liever wilt vernieuwen zonder dat de huidige aanroep van Functions wordt geblokkeerd. In dat geval krijgen latere aanroepen van Functions een bijgewerkte waarde.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>De functie lokaal testen

1. Stel een omgevingsvariabele met de naam **ConnectionString** in en stel deze in op de toegangssleutel van het App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

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

2. Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om **Azure Functions Core-hulpprogramma's (CLI)** te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de afbeelding hieronder ziet u de reactie in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer het App Configuration-archief dat u in de quickstart hebt gemaakt.

6. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutel bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration - bijgewerkt |

   Maak vervolgens de Sentinel-sleutel of wijzig de waarde ervan als deze al bestaat, bijvoorbeeld,

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. Vernieuw de browser een paar keer. Wanneer de cache-instelling na 30 seconden verloopt, ziet u op de pagina de reactie van de Functions-aanroep met de bijgewerkte waarde.

    ![Functions-app lokaal bijwerken](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> De voorbeeldcode in deze zelfstudie kan worden gedownload van de [App Configuration-opslagplaats op GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw Azure Functions-app ingesteld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
