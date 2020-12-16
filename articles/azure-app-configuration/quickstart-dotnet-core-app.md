---
title: Quickstart voor Azure App Configuration met .NET Core | Microsoft Docs
description: In deze quickstart maakt u een .NET Core-app met Azure App Configuration om opslag en beheer van toepassingsinstellingen gescheiden van uw code te centraliseren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 1a5e756e05543d9f19775f4e2d1abcee82794f9e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932061"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Quickstart: Een .NET Core-app maken met App Configuration

In deze quickstart neemt u Azure App Configuration op in een .NET Core-console-app om opslag en beheer van toepassingsinstellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download): ook beschikbaar in de [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** > **Maken** > **Sleutel-waarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg.

8. Selecteer **Toepassen**.

## <a name="create-a-net-core-console-app"></a>Een .NET Core-console-app maken

U gebruikt de [opdrachtregelinterface (CLI) van .NET Core](/dotnet/core/tools/) om een nieuw web-app-project van .NET Core Console te maken. Het voordeel van de CLI van .NET Core ten opzichte van Visual Studio is dat de interface beschikbaar is voor Windows, macOS en Linux.  U kunt ook de vooraf geïnstalleerde hulpprogramma's gebruiken die beschikbaar zijn in de [Azure Cloud Shell](https://shell.azure.com).

1. Maak een nieuwe map voor uw project.

2. Voer in de nieuwe map de volgende opdracht uit om een nieuw console-app-project van NET Core te maken:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Voeg een verwijzing toe naar het NuGet-pakket `Microsoft.Extensions.Configuration.AzureAppConfiguration` door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

3. Open *Program.cs* en voeg een verwijzing toe naar de configuratieprovider van .NET Core App Configuration.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Werk de methode `Main` bij voor het gebruik van App Configuration door de methode `builder.AddAzureAppConfiguration()` aan te roepen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele met de naam **ConnectionString** in en stel deze in op de toegangssleutel van het App Configuration-archief. Voer op de opdrachtregel de volgende opdracht uit:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Start de opdrachtprompt opnieuw op om de wijziging door te voeren. Druk de waarde van de omgevingsvariabele af om te controleren of deze correct is ingesteld.

2. Voer de volgende opdracht uit om de console-app te bouwen:

    ```dotnetcli
    dotnet build
    ```

3. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt en via de [App Configuration-provider](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) gebruikt met een .NET Core-console-app. Ga door naar de volgende zelfstudie voor meer informatie over het configureren van uw .NET Core-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet-core.md)