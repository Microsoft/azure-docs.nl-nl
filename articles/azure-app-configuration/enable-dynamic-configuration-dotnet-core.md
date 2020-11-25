---
title: 'Zelfstudie: Dynamische configuratie in een .Net Core-app gebruiken'
titleSuffix: Azure App Configuration
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor .NET Core-apps dynamisch bijwerkt
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: aebe7772c673162d60f35d6a81725ba1452d16a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012353"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Zelfstudie: Dynamische configuratie in een .Net Core-app gebruiken

De App Configuration .NET Core-clientbibliotheek biedt ondersteuning voor het bijwerken van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw hoeft te worden opgestart. Dit kan worden geïmplementeerd door eerst een exemplaar van `IConfigurationRefresher` op te halen uit de opties voor de configuratieprovider en vervolgens `TryRefreshAsync` aan te roepen voor dat exemplaar, waar dan ook in uw code.

Om de instellingen actueel te houden en te veel aanroepen naar de configuratieopslag te voorkomen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwingsbewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratieopslag. De standaardvervaltijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden gewijzigd.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Dit is gebaseerd op de app die is geïntroduceerd in de quickstarts. Volg eerst [Een .NET Core-app maken met App Configuration](./quickstart-dotnet-core-app.md) voordat u verder gaat.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw .NET Core-app instellen voor het bijwerken van de configuratie als reactie op wijzigingen in een app-configuratiearchief.
> * De meest recente configuratie in uw toepassing gebruiken.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt uitvoeren, installeert u de [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

Open *Program.cs* en werk het bestand bij om een verwijzing naar de `System.Threading.Tasks`-naamruimte toe te voegen, om de vernieuwingsconfiguratie op te geven in de methode `AddAzureAppConfiguration` en om handmatige vernieuwing te activeren met de methode `TryRefreshAsync`.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

De methode `ConfigureRefresh` wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratiegegevens met het app-configuratiearchief wanneer een vernieuwingsbewerking wordt geactiveerd. Een exemplaar van `IConfigurationRefresher` kan worden opgehaald door de methode `GetRefresher` aan te roepen voor de opties die aan de methode `AddAzureAppConfiguration` zijn gegeven, en de methode `TryRefreshAsync` in dit exemplaar kan worden gebruikt om een vernieuwingsbewerking overal in uw code te activeren.
    
> [!NOTE]
> De standaardwaarde voor de vervaltijd van de cache voor een configuratie-instelling is 30 seconden, maar kan worden gewijzigd door de methode `SetCacheExpiration` aan te roepen voor de initialisatiefunctie voor opties die als een argument aan de methode `ConfigureRefresh` zijn doorgegeven.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele in met de naam **ConnectionString** en stel deze in op de toegangssleutel van het App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

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

1. Voer de volgende opdracht uit om de console-app te bouwen:

    ```console
     dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

    ```console
     dotnet run
    ```

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/dotnet-core-app-run.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration - bijgewerkt |

1. Druk op Enter om een vernieuwing te activeren en de bijgewerkte waarde af te drukken naar de opdrachtprompt of het PowerShell-venster.

    ![Quickstart voor het lokaal vernieuwen van een app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Omdat de vervaltijd van de cache is ingesteld op tien seconden met behulp van de methode `SetCacheExpiration` tijdens het opgeven van de configuratie voor de vernieuwingsbewerking, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste tien seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw .NET Core-app ingeschakeld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
