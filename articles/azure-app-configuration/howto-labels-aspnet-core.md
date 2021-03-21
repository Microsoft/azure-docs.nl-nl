---
title: Configuratie per omgeving gebruiken
titleSuffix: Azure App Configuration
description: Gebruik labels om de configuratie waarden per omgeving te bieden.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929613"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Labels gebruiken om configuraties voor verschillende omgevingen in te scha kelen

Veel toepassingen moeten verschillende configuraties gebruiken voor verschillende omgevingen. Stel dat een toepassing een configuratie waarde heeft die het connection string definieert dat moet worden gebruikt voor de back-end-data base. De ontwikkel aars van toepassingen gebruiken een andere data base dan die welke wordt gebruikt voor productie. De data base connection string die door de toepassing wordt gebruikt, moet worden gewijzigd wanneer de toepassing wordt verplaatst van ontwikkeling naar productie.

In Azure-app configuratie kunt u *labels* gebruiken om verschillende waarden voor dezelfde sleutel te definiëren. U kunt bijvoorbeeld één sleutel definiëren met verschillende waarden voor ontwikkeling en productie. U kunt opgeven welk label moet worden geladen bij het maken van verbinding met de app-configuratie.

Als u deze functionaliteit wilt demonstreren, wijzigt u de web-app die u hebt gemaakt in [Quick Start: een ASP.net core-app maken met Azure-app configuratie](./quickstart-aspnet-core-app.md) voor het gebruik van verschillende configuratie-instellingen voor ontwikkeling en productie. Voltooi de Snelstartgids voordat u doorgaat.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Geef een label op bij het toevoegen van een configuratie waarde

Ga in het Azure Portal naar **Configuration Explorer** en zoek de *TestApp: Settings: FontColor* -sleutel die u in de Snelstartgids hebt gemaakt. Selecteer het context menu en selecteer vervolgens **waarde toevoegen**.

> [!div class="mx-imgBorder"]
> ![Menu-item waarde toevoegen](media/labels-add-value.png)

Voer in het scherm **waarde toevoegen** de **waarde** **rood** en een **Label** in voor **ontwikkeling**. Laat het **inhouds type** leeg. Selecteer **Toepassen**.

## <a name="load-configuration-values-with-a-specified-label"></a>Configuratie waarden met een opgegeven label laden

Azure-app configuratie laadt standaard alleen configuratie waarden zonder label. Als u labels voor uw configuratie waarden hebt gedefinieerd, wilt u de labels opgeven die moeten worden gebruikt om verbinding te maken met de app-configuratie.

In de vorige sectie hebt u een andere configuratie waarde voor de ontwikkel omgeving gemaakt. U gebruikt de `HostingEnvironment.EnvironmentName` variabele om dynamisch te bepalen in welke omgeving de app momenteel wordt uitgevoerd. Zie [meerdere omgevingen gebruiken in ASP.net core](/aspnet/core/fundamentals/environments)voor meer informatie.

Laad configuratie waarden met het label dat overeenkomt met de huidige omgeving door de naam van de omgeving door te geven aan de `Select` methode:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Het voor gaande code fragment laadt de app-configuratie connection string van een omgevings variabele met de naam `AppConfigConnectionString` . Zorg ervoor dat deze omgevings variabele juist is ingesteld.

De `Select` methode wordt twee keer aangeroepen. De eerste keer dat er configuratie waarden worden geladen zonder label. Vervolgens worden configuratie waarden geladen met het label dat overeenkomt met de huidige omgeving. Deze specifieke waarden voor de omgeving overschrijven alle bijbehorende waarden zonder label. U hoeft geen specifieke waarden voor elke sleutel te definiëren. Als een sleutel geen waarde heeft met een label dat overeenkomt met de huidige omgeving, wordt de waarde zonder label gebruikt.

## <a name="test-in-different-environments"></a>Testen in verschillende omgevingen

Open het `launchSettings.json` bestand in de `Properties` map. Zoek de `config` vermelding onder `profiles` . Stel in de `environmentVariables` sectie de `ASPNETCORE_ENVIRONMENT` variabele in op `Production` .

Stel uw toepassing samen met de nieuwe waarden en voer deze uit.

```dotnetcli
dotnet build
dotnet run
```

Gebruik een webbrowser om naar te gaan `http://localhost:5000` . U ziet dat de tekst kleur zwart is.

![Webtoepassing uitgevoerd met productie configuratie](media/labels-website-prod.png)

Update `launchSettings.json` om de `ASPNETCORE_ENVIRONMENT` variabele in te stellen op `Development` . Voer `dotnet run` opnieuw uit. 

U ziet dat de tekst kleur nu rood is. Dit komt doordat de toepassing nu gebruikmaakt van de waarde van `TestApp:Settings:FontColor` met het `Development` label. Alle andere configuratie waarden blijven hetzelfde als de productie waarden.

![Webtoepassing die wordt uitgevoerd met de ontwikkelings configuratie](media/labels-website-dev.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie in ASP.NET Core](/aspnet/core/fundamentals/configuration/)
