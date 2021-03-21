---
title: Quickstart voor het toevoegen van functievlaggen aan .NET Framework-apps | Microsoft Docs | Microsoft Docs
description: Een quickstart voor het toevoegen van functievlaggen voor .NET Framework-apps en het beheren ervan in Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: alkemper
ms.openlocfilehash: 513c826e11ff9dfe6ea94349c67620da9d1bba48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932044"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Quickstart: Functievlaggen toevoegen aan een .NET Framework-app

In deze quickstart neemt u Azure App Configuration op in een .NET Framework-app om een end-to-end-implementatie van functiebeheer te maken. U kunt de App Configuration-service gebruiken om al uw functievlaggen centraal op te slaan en hun status te bepalen. 

De .NET Feature Management-bibliotheken breiden het framework uit met ondersteuning voor functiemarkeringen. Deze bibliotheken worden boven op het .NET-configuratiesysteem gebouwd. Ze integreren naadloos met App Configuration via de configuratieprovider voor .NET.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Functiebeheer** >  **+ Toevoegen** om een functievlag met de naam `Beta` toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Functievlag met de naam Beta inschakelen](media/add-beta-feature-flag.png)

    Laat `label` voor dit moment niet-gedefinieerd.

## <a name="create-a-net-console-app"></a>Een .NET Core-consoletoepassing maken

1. Start Visual Studio en selecteer **Bestand** > **Nieuw** > **Project**.

1. In **Een nieuw project maken**, filtert u op het projecttype **Console** en klikt u op **Console-app (.NET Framework)** . Klik op **Volgende**.

1. Voer in **Uw nieuwe project configureren** een projectnaam in. Selecteer onder **Framework** **.NET Framework 4.8** of hoger. Klik op **Create**.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Klik met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek op het tabblad **Bladeren** de volgende NuGet-pakketten op en voeg deze toe aan uw project. Als u deze niet kunt vinden, schakelt u het selectievakje **Prerelease insluiten** in.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Open *Program.cs* en voeg de volgende instructies toe:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. Werk de `Main`-methode bij om verbinding te maken met App Configuration, waarbij u de `UseFeatureFlags`-optie opgeeft, zodat er functievlaggen worden opgehaald. Vervolgens wordt een bericht weergegeven als de functievlag `Beta` is ingeschakeld.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele met de naam **ConnectionString** in en stel deze in op de toegangssleutel van uw App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, moet u de volgende opdracht uitvoeren:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Start Visual Studio opnieuw zodat de wijziging kan worden doorgevoerd. 

1. Druk op Ctrl + F5 om de consoletoepassing te bouwen en uit te voeren.

    ![App met functievlag ingeschakeld](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een functievlag in App Configuration gemaakt en dit gebruikt met een .NET Framework-console-app. Ga verder met de volgende zelfstudie als u wilt weten hoe u functievlaggen en andere configuratiewaarden dynamisch kunt bijwerken zonder de toepassing opnieuw te hoeven starten.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet.md)
