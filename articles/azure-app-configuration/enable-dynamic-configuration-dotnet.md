---
title: '.NET Framework-zelfstudie: dynamische configuratie in Azure App Configuration'
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor .NET Framework-apps dynamisch kunt bijwerken met behulp van Azure App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932758"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een .NET-framework-app

De App Configuration .NET-clientbibliotheek biedt ondersteuning voor het bijwerken van een set configuratie-instellingen op aanvraag, zonder dat een toepassing opnieuw hoeft te worden opgestart. Dit kan worden geïmplementeerd door eerst een exemplaar van `IConfigurationRefresher` op te halen uit de opties voor de configuratieprovider en vervolgens `TryRefreshAsync` aan te roepen voor dat exemplaar, waar dan ook in uw code.

Om de instellingen actueel te houden en te veel aanroepen naar de configuratieopslag te voorkomen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwingsbewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratieopslag. De standaardvervaltijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden gewijzigd.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Dit is gebaseerd op de app die is geïntroduceerd in de quickstarts. Volg eerst [Een .NET Framework-app maken met App Configuration](./quickstart-dotnet-app.md) voordat u verder gaat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De .NET Framework-app instellen om de bijbehorende configuratie bij te werken als reactie op wijzigingen in een App Configuration-archief.
> * De meest recente configuratie opnemen in uw toepassing.
## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 of hoger](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** >  **+ Maken** > **Sleutel-waarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg.

8. Selecteer **Toepassen**.

## <a name="create-a-net-framework-console-app"></a>Een .NET Framework-console-app maken

1. Start Visual Studio en selecteer **Bestand** > **Nieuw** > **Project**.

1. In **Een nieuw project maken**, filtert u op het projecttype **Console** en klikt u op **Console-app (.NET Framework)** . Klik op **Volgende**.

1. Voer in **Uw nieuwe project configureren** een projectnaam in. Selecteer onder **Framework** **.NET Framework 4.7.1** of hoger. Klik op **Maken**.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie
1. Klik met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek op het tabblad **Bladeren** het NuGet-pakket *Microsoft.Extensions.Configuration.AzureAppConfiguration* voor uw project en voeg dit toe. Als u het pakket niet kunt vinden, schakelt u het selectievakje **Prerelease insluiten** in.

1. Open *Program.cs* en voeg een verwijzing naar de .NET Core App Configuration-provider toe.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Voeg twee variabelen toe om aan de configuratie gerelateerde objecten op te slaan.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Werk de methode `Main` bij om verbinding te maken met App Configuration met de opgegeven vernieuwingsopties.

    ```csharp
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
    ```
    De methode `ConfigureRefresh` wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratiegegevens met het app-configuratiearchief wanneer een vernieuwingsbewerking wordt geactiveerd. Een exemplaar van `IConfigurationRefresher` kan worden opgehaald door de methode `GetRefresher` aan te roepen voor de opties die aan de methode `AddAzureAppConfiguration` zijn gegeven, en de methode `TryRefreshAsync` in dit exemplaar kan worden gebruikt om een vernieuwingsbewerking overal in uw code te activeren.

    > [!NOTE]
    > De standaardwaarde voor de vervaltijd van de cache voor een configuratie-instelling is 30 seconden, maar kan worden gewijzigd door de methode `SetCacheExpiration` aan te roepen voor de initialisatiefunctie voor opties die als een argument aan de methode `ConfigureRefresh` zijn doorgegeven.

1. Voeg een methode toe met de naam `PrintMessage()` waarmee het handmatig vernieuwen van de configuratiegegevens uit App Configuration wordt geactiveerd.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele in met de naam **ConnectionString** en stel deze in op de toegangssleutel van het App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Start Visual Studio opnieuw zodat de wijziging kan worden doorgevoerd. 

1. Druk op Ctrl + F5 om de consoletoepassing te bouwen en uit te voeren.

    ![App lokaal starten](./media/dotnet-app-run.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration - bijgewerkt |

1. Druk, als u weer in de actieve toepassing bent, op Enter om een vernieuwing te activeren en de bijgewerkte waarde af te drukken naar de opdrachtprompt of het PowerShell-venster.

    ![App lokaal vernieuwen](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Omdat de vervaltijd van de cache is ingesteld op tien seconden met behulp van de methode `SetCacheExpiration` tijdens het opgeven van de configuratie voor de vernieuwingsbewerking, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste tien seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw .NET Framework-app ingeschakeld om de configuratie-instellingen uit App Configuration dynamisch te vernieuwen. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
