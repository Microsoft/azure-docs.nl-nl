---
title: Quickstart voor de serverloze Azure SignalR Service - Python
description: Een quickstart waarin u leert hoe u de service Azure SignalR en Azure Functions gebruikt om een chatruimte te maken met behulp van Python.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: aaaf9011d38e7ec02e83db63757c434329b835e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960288"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Quickstart: Een chatruimte maken met Azure Functions en SignalR Service met behulp van Python

Met de service Azure SignalR kunt u eenvoudig realtimefunctionaliteit toevoegen aan een toepassing. Azure Functions is een serverloos platform waarmee u code kunt uitvoeren zonder een infrastructuur te beheren. In deze quickstart leert u hoe u de service SignalR en Functions gebruikt om een serverloze, realtimechattoepassing te bouwen.

## <a name="prerequisites"></a>Vereisten

Deze quickstart kan worden uitgevoerd op macOS, Windows of Linux.

Zorg ervoor dat u een code-editor hebt geïnstalleerd, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).

Installeer [Azure Functions Core Tools (versie 2.7.1505 of hoger)](https://github.com/Azure/azure-functions-core-tools#installing) om lokaal Azure Function-apps in Python uit te voeren.

Voor Azure Functions is [Python 3.6 of 3.7](https://www.python.org/downloads/) vereist.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>De Azure-functie-app configureren en uitvoeren

1. Controleer in de browser waarin de Azure-portal is geopend, of het service-exemplaar van SignalR dat u eerder hebt geïmplementeerd, is gemaakt. Hiervoor typt u de naam van het exemplaar in het zoekvak boven in de portal. Selecteer het exemplaar om het te openen.

    ![Het service-exemplaar van SignalR zoeken](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

    ![Selecteer en kopieer de primaire verbindingsreeks.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Open in de code-editor de map *src/chat/python* in de gekloonde opslagplaats.

1. Als u Python-functies lokaal wilt ontwikkelen en testen, moet u in een Python 3.6- of 3.7-omgeving werken. Voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv` te maken.

    **Linux of macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. Plak in **local.settings.json** de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Python-functies zijn georganiseerd in mappen. In elke map bevinden zich twee bestanden: *function.json* definieert de bindingen die worden gebruikt in de functie, en *\_\_init\_\_.py* is de hoofdtekst van de functie. Deze functie-app bevat twee met HTTP geactiveerde functies:

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **messages**: ontvangt een chatbericht in de hoofdtekst van de aanvraag en gebruikt de uitvoerbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Zorg er in de terminal met de geactiveerde virtuele omgeving voor dat u zich in de map *src/chat/python* bevindt. Installeer de vereiste Python-pakketten met behulp van PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Voer de functie-app uit.

    ```bash
    func start
    ```

    ![Functie-app uitvoeren](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een serverloze, realtime toepassing in VS Code gebouwd en uitgevoerd. Nu volgt meer informatie over het implementeren van Azure Functions vanuit VS Code.

> [!div class="nextstepaction"]
> [Azure Functions met VS Code implementeren](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

