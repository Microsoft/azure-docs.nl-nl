---
title: 'Zelf studie: dynamische configuratie gebruiken met push-vernieuwing in een .NET core-app'
titleSuffix: Azure App Configuration
description: In deze zelf studie leert u hoe u de configuratie gegevens voor .NET Core-Apps dynamisch kunt bijwerken met push vernieuwen
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 553c5081947ad784a8cdae6ad0eb92fc3e2a2c85
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982197"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Zelf studie: dynamische configuratie gebruiken met push-vernieuwing in een .NET core-app

De app-configuratie .NET core client library ondersteunt het bijwerken van configuratie op aanvraag zonder dat een toepassing opnieuw moet worden opgestart. Een toepassing kan worden geconfigureerd om wijzigingen in de app-configuratie te detecteren met behulp van een of beide van de volgende twee benaderingen.

1. Poll model: dit is het standaard gedrag dat polling gebruikt om wijzigingen in de configuratie te detecteren. Zodra de waarde in de cache van een instelling verloopt, wordt de volgende aanroep naar `TryRefreshAsync` `RefreshAsync` de server verzonden of een aanvraag naar het te verzenden om te controleren of de configuratie is gewijzigd en wordt de bijgewerkte configuratie zo nodig opgehaald.

1. Push model: dit maakt gebruik van [app-configuratie gebeurtenissen](./concept-app-configuration-event.md) om wijzigingen in de configuratie te detecteren. Zodra de configuratie van de app is ingesteld voor het verzenden van belang rijke wijzigings gebeurtenissen naar Azure Event Grid, kan de toepassing deze gebeurtenissen gebruiken om het totale aantal aanvragen te optimaliseren dat nodig is om de configuratie bij te werken. Toepassingen kunnen ervoor kiezen om zich rechtstreeks vanuit Event Grid aan te melden, of een van de [ondersteunde gebeurtenis-handlers](https://docs.microsoft.com/azure/event-grid/event-handlers) zoals een webhook, een Azure-functie of een service bus onderwerp.

Toepassingen kunnen ervoor kiezen om zich te abonneren op deze gebeurtenissen, hetzij rechtstreeks vanuit Event Grid, hetzij via een webhook, hetzij door het door sturen van gebeurtenissen naar Azure Service Bus. De Azure Service Bus SDK biedt een API voor het registreren van een bericht-handler die dit proces vereenvoudigt voor toepassingen die geen HTTP-eind punt hebben of waarvoor het gebeurtenis raster niet voortdurend moet worden doorzocht.

In deze zelf studie ziet u hoe u dynamische configuratie-updates in uw code kunt implementeren met behulp van push-vernieuwing. Dit is gebaseerd op de app die is geïntroduceerd in de quickstarts. Volg eerst [Een .NET Core-app maken met App Configuration](./quickstart-dotnet-core-app.md) voordat u verder gaat.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een abonnement instellen voor het verzenden van configuratie wijzigings gebeurtenissen van de app-configuratie naar een Service Bus onderwerp
> * Stel uw .NET core-app in om de configuratie bij te werken als reactie op wijzigingen in de app-configuratie.
> * De meest recente configuratie in uw toepassing gebruiken.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt uitvoeren, installeert u de [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Azure Service Bus onderwerp en-abonnement instellen

In deze zelf studie wordt gebruikgemaakt van de Service Bus-integratie voor Event Grid om de detectie van configuratie wijzigingen te vereenvoudigen voor toepassingen die de app-configuratie niet voortdurend willen controleren op wijzigingen. De Azure Service Bus SDK biedt een API voor het registreren van een bericht-handler die kan worden gebruikt om de configuratie bij te werken wanneer er wijzigingen worden gedetecteerd in de app-configuratie. Volg de stappen in de [Quick Start: gebruik de Azure Portal om een service bus onderwerp en een abonnement te maken](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal) om een service bus-naam ruimte, een onderwerp en een abonnement te maken.

Wanneer de resources zijn gemaakt, voegt u de volgende omgevings variabelen toe. Deze worden gebruikt voor het registreren van een gebeurtenis-handler voor configuratie wijzigingen in de toepassings code.

| Sleutel | Waarde |
|---|---|
| ServiceBusConnectionString | Verbindings reeks voor de service bus-naam ruimte |
| ServiceBusTopic | De naam van het onderwerp van de Service Bus |
| ServiceBusSubscription | De naam van het service bus-abonnement |

## <a name="set-up-event-subscription"></a>Gebeurtenis abonnement instellen

1. Open de configuratie bron van de app in de Azure Portal en klik vervolgens `+ Event Subscription` in het `Events` deel venster op.

    ![App-configuratie gebeurtenissen](./media/events-pane.png)

1. Voer een naam in voor de `Event Subscription` en `System Topic` .

    ![Gebeurtenisabonnement maken](./media/create-event-subscription.png)

1. Selecteer `Endpoint Type` als `Service Bus Topic` , kies het service bus onderwerp en klik vervolgens op aan `Confirm Selection` .

    ![Event Subscription Service Bus-eind punt](./media/event-subscription-servicebus-endpoint.png)

1. Klik op `Create` aan om het gebeurtenis abonnement te maken.

1. Klik `Event Subscriptions` in het `Events` deel venster om te valideren dat het abonnement is gemaakt.

    ![App-configuratie gebeurtenis abonnementen](./media/event-subscription-view.png)

> [!NOTE]
> Bij het abonneren op configuratie wijzigingen kunnen een of meer filters worden gebruikt om het aantal gebeurtenissen dat naar uw toepassing wordt verzonden, te verminderen. Deze kunnen worden geconfigureerd als [Event grid-abonnements filters](https://docs.microsoft.com/azure/event-grid/event-filtering) of [Service Bus-abonnements filters](https://docs.microsoft.com/azure/service-bus-messaging/topic-filters). U kunt bijvoorbeeld een abonnements filter gebruiken om alleen te abonneren op gebeurtenissen op wijzigingen in een sleutel die begint met een specifieke teken reeks.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Gebeurtenis-handler registreren om gegevens van de app-configuratie opnieuw te laden

Open *Program.cs* en werk het bestand bij met de volgende code.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

De methode [SetDirty](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) wordt gebruikt om de waarde in cache in te stellen voor sleutel waarden die zijn geregistreerd voor vernieuwen als gewijzigd. Dit zorgt ervoor dat de volgende keer `RefreshAsync` dat `TryRefreshAsync` de in de cache opgeslagen waarden worden aangeroepen of opnieuw worden gevalideerd met de app-configuratie en worden bijgewerkt als dat nodig is.

Er wordt een wille keurige vertraging toegevoegd voordat de waarde in de cache is gemarkeerd als vuil om mogelijke beperking te verminderen wanneer meerdere exemplaren tegelijkertijd worden vernieuwd. De standaard maximum vertraging voordat de waarde in de cache is gemarkeerd als vuil, is 30 seconden, maar kan worden overschreven door een optionele `TimeSpan` para meter door te geven aan de `SetDirty` methode.

> [!NOTE]
> Als u het aantal aanvragen voor de app-configuratie wilt beperken wanneer u push-vernieuwing gebruikt, is het belang rijk dat u aanroept `SetCacheExpiration(TimeSpan cacheExpiration)` met de juiste waarde voor de `cacheExpiration` para meter. Dit bepaalt de verval tijd van de cache voor pull-vernieuwing en kan worden gebruikt als een veiligheids netwerk wanneer er een probleem is met het gebeurtenis abonnement of het Service Bus-abonnement. De aanbevolen waarde is `TimeSpan.FromDays(30)`.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevings variabele met de naam **AppConfigurationConnectionString** in en stel deze in op de toegangs sleutel voor uw app-configuratie opslag. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Voer de volgende opdracht uit om de console-app te bouwen:

    ```console
     dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

    ```console
     dotnet run
    ```

    ![Push vernieuwen uitvoeren vóór update](./media/dotnet-core-app-pushrefresh-initial.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration - bijgewerkt |

1. Wacht 30 seconden totdat de gebeurtenis kan worden verwerkt en de configuratie moet worden bijgewerkt.

    ![Push vernieuwen uitvoeren na Updated](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw .NET Core-app ingeschakeld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
