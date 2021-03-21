---
title: Quickstart voor de serverloze Azure SignalR Service - C#
description: Een quickstart waarin u leert hoe u de service Azure SignalR en Azure Functions gebruikt om een chatruimte te maken met behulp van C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: 1f28058a766144ada3326b3ee4ce09ca503d1896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94873876"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Quickstart: Een chatruimte maken met Azure Functions en SignalR Service met behulp van C\#

Met de service Azure SignalR kunt u eenvoudig realtimefunctionaliteit toevoegen aan een toepassing. Azure Functions is een serverloos platform waarmee u code kunt uitvoeren zonder een infrastructuur te beheren. In deze quickstart leert u hoe u de service SignalR en Functions gebruikt om een serverloze, realtimechattoepassing te bouwen.

## <a name="prerequisites"></a>Vereisten

Als u Visual Studio 2019 nog niet hebt geïnstalleerd, kunt u het downloaden en de **gratis** [Community Edition van Visual Studio 2019](https://www.visualstudio.com/downloads) gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

U kunt deze zelfstudie ook uitvoeren op de opdrachtregel (macOS, Windows of Linux) met behulp van de [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), de [.NET Core SDK](https://dotnet.microsoft.com/download) en uw favoriete code-editor.

Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/dotnet) voordat u begint.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

## <a name="configure-and-run-the-azure-function-app"></a>De Azure-functie-app configureren en uitvoeren

1. Start Visual Studio (of een andere code-editor) en open de oplossing in de map *src/chat/csharp* van de gekloonde opslagplaats.

1. Controleer in de browser waarin de Azure-portal is geopend, of het service-exemplaar van SignalR dat u eerder hebt geïmplementeerd, is gemaakt. Hiervoor typt u de naam van het exemplaar in het zoekvak boven in de portal. Selecteer het exemplaar om het te openen.

    ![Het service-exemplaar van SignalR zoeken](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

1. Terug in Visual Studio wijzigt u in **Solution Explorer** de naam van *local.settings.sample.json* in *local.settings.json*.

1. Plak in *local.settings.json* de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Open *Functions.cs*. Deze functie-app bevat twee met HTTP geactiveerde functies:

    - **GetSignalRInfo**: gebruikt de invoergegevensbinding `SignalRConnectionInfo` om geldige verbindingsgegevens te genereren en te retourneren.
    - **SendMessage**: ontvangt een chatbericht in de aanvraagbody en gebruikt de uitvoergegevensbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Gebruik een van de volgende opties om de Azure Functie-app lokaal te starten.

    - **Visual Studio**: Selecteer in het menu *Fouten opsporen* de optie *Foutopsporing starten* om de toepassing uit te voeren.

        ![Fouten in de toepassing opsporen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Opdrachtregel**: Voer de volgende opdracht uit om de functiehost te starten.

        ```bash
        func start
        ```
Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een serverloze, realtimetoepassing in Visual Studio Code gebouwd en uitgevoerd. Nu volgt meer informatie over het ontwikkelen en implementeren van Azure Functions met behulp van Visual Studio Code.

> [!div class="nextstepaction"]
> [Azure Functions ontwikkelen met Visual Studio](../azure-functions/functions-develop-vs.md)

