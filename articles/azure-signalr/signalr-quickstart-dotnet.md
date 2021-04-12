---
title: Ontwikkelen met ASP.NET - de Azure SignalR Service
description: Een quickstart waarin u leert hoe u met de Azure SignalR Service een chatruimte maakt met het ASP.NET-framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: c39ef505b0cea0ad0c03b81683db8441077cd0d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874539"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Quickstart: Een chatruimte maken met ASP.NET en SignalR Service

Azure SignalR Service is gebaseerd op [SignalR voor ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1). Dit is **niet** 100% compatibel met ASP.NET SignalR. Met Azure SignalR Service wordt het ASP.NET SignalR-gegevensprotocol opnieuw geïmplementeerd op basis van de meest recente ASP.NET Core-technologieën. Wanneer u Azure SignalR Service gebruikt voor ASP.NET SignalR, worden een aantal ASP.NET SignalR-functies niet langer ondersteund; met Azure SignalR worden berichten bijvoorbeeld niet opnieuw afgespeeld wanneer de client weer verbinding maakt. Ook worden Forever Frame-transport en JSONP niet ondersteund. Een aantal codewijzigingen en de juiste versie van afhankelijke bibliotheken zijn benodigd om ervoor te zorgen dat de ASP.NET SignalR-toepassing werkt met SignalR Service.

Raadpleeg het [document met versieverschillen](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) voor een complete lijst met functievergelijkingen tussen ASP.NET SignalR en ASP.NET Core SignalR.

In deze quickstart leert u hoe u aan de slag kunt gaan met ASP.NET en Azure SignalR Service voor een vergelijkbare [Chatroom-toepassing](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

De modus *Serverloos* wordt niet ondersteund voor ASP.NET SignalR-toepassingen. Gebruik altijd *Standaard* of *Klassiek* voor het Azure SignalR Service-exemplaar.

U kunt de Azure-resources die in deze quickstart worden gebruikt ook maken met [Een SignalR Service-script maken](scripts/signalr-cli-create-service.md).

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Terwijl de service wordt geïmplementeerd, gaan we met code werken. Kloon de [voorbeeld-app vanuit GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), stel de SignalR Service-verbindingsreeks in en voer de toepassing lokaal uit.

1. Open een nieuw git-terminalvenster. Ga naar een map waarin u het voorbeeldproject wilt klonen.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Chatroom-web-app

1. Start Visual Studio en open de oplossing in de map *aspnet-samples/ChatRoom/* van de gekloonde opslagplaats.

1. Zoek en selecteer het exemplaar dat u hebt gemaakt in de browser waarin Azure Portal is geopend.

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

1. Stel nu in het bestand *web.config* de verbindingsreeks in.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. In *Startup.cs* moet u niet `MapSignalR()` maar `MapAzureSignalR({YourApplicationName})` aanroepen en de verbindingsreeks doorgeven om de toepassing verbinding te laten maken met de service in plaats van SignalR zelf te hosten. Vervang `{YourApplicationName}` door de naam van uw toepassing. Deze naam is een unieke naam waarmee u deze toepassing kunt onderscheiden van uw andere toepassingen. U kunt `this.GetType().FullName` als de waarde gebruiken.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    U moet ook verwijzen naar de service-SDK voordat u deze API's gebruikt. Open **Extra > NuGet-pakketbeheer > Package Manager-console** en voer de volgende opdracht uit:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Met uitzondering van deze wijzigingen blijft de rest onveranderd; u kunt gebruik blijven maken van de hubinterface die u al kent om bedrijfslogica te schrijven.

    > [!NOTE]
    > In de implementatie wordt een eindpunt `/signalr/negotiate` weergegeven voor onderhandeling door de Azure SignalR Service-SDK. Er wordt een speciaal onderhandelingsantwoord geretourneerd wanneer clients verbinding proberen te maken en clients proberen door te sturen naar het service-eindpunt dat in de verbindingsreeks is gedefinieerd.

1. Druk op <kbd>F5</kbd> om het project uit te voeren in de foutopsporingsmodus. U kunt zien dat de toepassing lokaal wordt uitgevoerd. In plaats van een SignalR-runtime door de toepassing zelf te laten hosten, wordt nu verbinding gemaakt met de Azure SignalR Service.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.

Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.

Typ de naam van de resourcegroep in het tekstvak **Filteren op naam...** . In de instructies voor deze snelstart is een resourcegroep met de naam *SignalRTestResources* gebruikt. Klik in de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

![Verwijderen](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe Azure SignalR Service-resource gemaakt en deze gebruikt voor een ASP.NET-web-app. Hierna leert u hoe u realtime toepassingen ontwikkelt met behulp van Azure SignalR Service met ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR Service met ASP.NET Core](./signalr-quickstart-dotnet-core.md)
