---
title: Externe bewakings oplossing implementeren lokaal-VS IDE-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u de oplossing voor externe controle kunt implementeren op uw lokale computer met behulp van Visual Studio voor testen en ontwikkeling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024143"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>De externe controle oplossings versneller implementeren lokaal-Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel wordt beschreven hoe u de oplossing voor externe controle op uw lokale computer implementeert voor testen en ontwikkeling. U leert hoe u de micro Services in Visual Studio uitvoert. Een lokale implementatie van micro Services maakt gebruik van de volgende Cloud Services: IoT Hub, Cosmos DB, Azure streaming Analytics en Azure Time Series Insights Services in de Cloud.

Als u de oplossings versneller voor externe controle in docker op uw lokale computer wilt uitvoeren, raadpleegt u [de externe controle oplossings versneller lokaal-docker implementeren](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-Services wilt implementeren die worden gebruikt door de oplossings versneller voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine instellen

Als u de lokale implementatie wilt volt ooien, moet u de volgende hulpprogram ma's installeren op uw lokale ontwikkel computer:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/) : deze software is een vereiste voor de pc's cli die de scripts gebruiken om Azure-resources te maken. Gebruik Node.js V10 toevoegen niet.

> [!NOTE]
> Visual Studio is beschikbaar voor Windows en Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>De micro Services uitvoeren

In deze sectie voert u de externe bewakings service uit. U voert de Web-UI zelf uit, de Device simulatie service in docker en de micro Services in Visual Studio.

### <a name="run-the-device-simulation-service"></a>De Device simulatie service uitvoeren

Open een nieuw opdracht prompt venster om er zeker van te zijn dat u toegang hebt tot de omgevings variabelen die zijn ingesteld door het script **Start. cmd** in de vorige sectie.

Voer de volgende opdracht uit om de docker-container voor de Device simulatie service te starten. De service simuleert apparaten voor de oplossing voor externe controle.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Alle andere micro Services op de lokale computer implementeren

De volgende stappen laten zien hoe u de micro Services voor externe controle uitvoert in Visual Studio:

1. Start Visual Studio.
1. Open de oplossing **Remote-monitoring. SLN** in de map **Services** in uw lokale kopie van de opslag plaats.
1. Klik in **Solution Explorer** met de rechter muisknop op de oplossing en klik op **Eigenschappen**.
1. Selecteer **algemene eigenschappen > project voor opstarten**.
1. Selecteer **meerdere opstart projecten** en stel **actie** in die moet worden **gestart** voor de volgende projecten:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Klik op **OK** om uw keuzes op te slaan.
1. Klik op **fouten opsporen > fout opsporing starten** om de webservices op de lokale computer te bouwen en uit te voeren.

Elke webservice opent een opdracht prompt en webbrowser venster. Bij de opdracht prompt ziet u uitvoer van de actieve service en kunt u in het browser venster de status bewaken. Sluit de opdracht prompts of webpagina's niet. met deze actie wordt de webservice gestopt.

### <a name="start-the-stream-analytics-job"></a>De Stream Analytics-taak starten

Volg deze stappen om de Stream Analytics taak te starten:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Navigeer naar de **resource groep** die is gemaakt voor uw oplossing. De naam van de resource groep is de naam die u voor uw oplossing hebt gekozen toen u het script **Start. cmd** hebt uitgevoerd.
1. Klik op de **taak stream Analytics** in de lijst met resources.
1. Klik op de pagina **overzicht** van stream Analytics op de knop **starten** . Klik vervolgens op **Start** om de taak nu te starten.

### <a name="run-the-web-ui"></a>De Web-UI uitvoeren

In deze stap start u de Web-UI. Open een nieuw opdracht prompt venster om er zeker van te zijn dat u toegang hebt tot de omgevings variabelen die zijn ingesteld door het script **Start. cmd** . Ga naar de map **webui** in uw lokale kopie van de opslag plaats en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer het starten is voltooid, wordt in uw browser de pagina **http: \/ /localhost: 3000/dash board** weer gegeven. De fouten op deze pagina worden verwacht. Als u de toepassing zonder fouten wilt weer geven, voert u de volgende stap uit.

### <a name="configure-and-run-nginx"></a>NGINX configureren en uitvoeren

Stel een reverse-proxy server in om de webtoepassing en micro services die worden uitgevoerd op uw lokale computer te koppelen:

* Kopieer het bestand **nginx. conf** vanuit de map **webui\scripts\localhost** in uw lokale kopie van de opslag plaats naar de installatiemap van **nginx\conf** .
* Voer **nginx** uit.

Zie [nginx voor Windows](https://nginx.org/en/docs/windows.html)voor meer informatie over het uitvoeren van **nginx**.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dash board

Om toegang te krijgen tot het dash board van de oplossing voor externe controle, gaat u naar http: \/ /localhost: 9000 in uw browser.

## <a name="clean-up"></a>Opschonen

Om onnodige kosten te voor komen, kunt u, wanneer u klaar bent met testen, de Cloud Services verwijderen uit uw Azure-abonnement. Als u de services wilt verwijderen, gaat u naar de [Azure Portal](https://ms.portal.azure.com) en verwijdert u de resource groep waarin het script **Start. cmd** is gemaakt.

U kunt ook de lokale kopie van de externe bewakings opslagplaats verwijderen die is gemaakt bij het klonen van de bron code van GitHub.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor controle op afstand hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het dash board van de oplossing](quickstart-remote-monitoring-deploy.md).
