---
title: Externe bewakings oplossing implementeren lokaal-Visual Studio code-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u de oplossing voor externe controle kunt implementeren op uw lokale machine met Visual Studio code voor testen en ontwikkeling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024177"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>De externe bewaking Solution Accelerator Local-Visual Studio code implementeren

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel wordt beschreven hoe u de oplossing voor externe controle op uw lokale computer implementeert voor testen en ontwikkeling. U leert hoe u de micro Services in Visual Studio code kunt uitvoeren. Een lokale implementatie van micro Services maakt gebruik van de volgende Cloud Services: IoT Hub, Cosmos DB, Azure streaming Analytics en Azure Time Series Insights.

## <a name="prerequisites"></a>Vereisten

Als u de Azure-Services wilt implementeren die worden gebruikt door de oplossings versneller voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine instellen

Als u de lokale implementatie wilt volt ooien, moet u de volgende hulpprogram ma's installeren op uw lokale ontwikkel computer:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [De C#-extensie van VS code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js V8](https://nodejs.org/) : deze software is een vereiste voor de pc's cli die de scripts gebruiken om Azure-resources te maken. Gebruik Node.js V10 toevoegen niet

> [!NOTE]
> Visual Studio code is beschikbaar voor Windows, Mac en Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>De micro Services uitvoeren

In deze sectie voert u de externe bewakings service uit. U voert de webgebruikersinterface zelf uit, de Device simulatie service in docker en de micro Services in Visual Studio code.

### <a name="build-the-code"></a>De code bouwen

Navigeer naar Azure-IOT-PCs-Remote-Monitoring-dotnet\services in de opdracht prompt en voer de volgende opdrachten uit om de code te bouwen.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Alle andere micro Services op de lokale computer implementeren

De volgende stappen laten zien hoe u micro Services voor externe controle in Visual Studio code uitvoert:

1. Start Visual Studio Code.
1. Open in VS code de map **Azure-IOT-pc's-externe bewaking-DotNet** .
1. Maak een nieuwe map met de naam **. vscode** in de map **Azure-IOT-PCs-externe bewaking-DotNet** .
1. Kopieer de bestanden **launch.jsop** en **tasks.jsop** van services\scripts\local\launch\idesettings\vscode naar de map **. vscode** die u zojuist hebt gemaakt.
1. Open het **deel venster fout opsporing** in VS code en voer de configuratie **alle micro Services uitvoeren** uit. Deze configuratie voert de Device simulatie micro service uit in docker en voert de andere micro services uit in het fout opsporingsprogramma.

De uitvoer van het uitvoeren van **alle microsoervices** in de console fout opsporing ziet er ongeveer als volgt uit:

[![Implementeren-lokale-micro Services](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>De Web-UI uitvoeren

In deze stap start u de Web-UI. Ga naar de map **Azure-IOT-PCs-Remote-Monitoring-dotnet\webui** in uw lokale kopie en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer het starten is voltooid, wordt in uw browser de pagina **http: \/ /localhost: 3000/dash board** weer gegeven. De fouten op deze pagina worden verwacht. Als u de toepassing zonder fouten wilt weer geven, voert u de volgende stap uit.

### <a name="configure-and-run-nginx"></a>NGINX configureren en uitvoeren

Stel een reverse-proxy server in om de webtoepassing en micro services die worden uitgevoerd op uw lokale computer te koppelen:

* Kopieer het bestand **nginx. conf** van de map **webui\scripts\localhost** naar de installatiemap van **nginx\conf** .
* Voer **nginx** uit.

Zie [nginx voor Windows](https://nginx.org/en/docs/windows.html)voor meer informatie over het uitvoeren van **nginx**.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dash board

Om toegang te krijgen tot het dash board van de oplossing voor externe controle, gaat u naar http: \/ /localhost: 9000 in uw browser.

## <a name="clean-up"></a>Opschonen

Om onnodige kosten te voor komen, kunt u, wanneer u klaar bent met testen, de Cloud Services verwijderen uit uw Azure-abonnement. Als u de services wilt verwijderen, gaat u naar de [Azure Portal](https://ms.portal.azure.com) en verwijdert u de resource groep waarin het script **Start. cmd** is gemaakt.

U kunt ook de lokale kopie van de externe bewakings opslagplaats verwijderen die is gemaakt bij het klonen van de bron code van GitHub.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor controle op afstand hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het dash board van de oplossing](quickstart-remote-monitoring-deploy.md).
