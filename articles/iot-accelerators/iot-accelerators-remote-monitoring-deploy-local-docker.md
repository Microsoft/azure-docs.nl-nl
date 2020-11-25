---
title: Externe bewakings oplossing implementeren lokaal-docker-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u de oplossing voor externe controle kunt implementeren op uw lokale computer met behulp van docker voor testen en ontwikkeling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020234"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>De externe controle oplossings versneller implementeren lokaal-docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel wordt beschreven hoe u de oplossing voor externe controle op uw lokale computer implementeert voor testen en ontwikkeling. U leert hoe u de micro Services implementeert in lokale docker-containers. Een lokale implementatie van micro Services maakt gebruik van de volgende Cloud Services: IoT Hub, Cosmos DB, Azure streaming Analytics en Azure Time Series Insights Services in de Cloud.

Als u de oplossings versneller voor externe controle in een IDE op uw lokale computer wilt uitvoeren, raadpleegt u [de externe bewaking Solution Accelerator lokaal-Visual Studio implementeren](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-Services wilt implementeren die worden gebruikt door de oplossings versneller voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine instellen

Als u de lokale implementatie wilt volt ooien, moet u de volgende hulpprogram ma's installeren op uw lokale ontwikkel computer:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) : als u van plan bent om wijzigingen aan te brengen in de micro Services.
* [Node.js V8](https://nodejs.org/) : deze software is een vereiste voor de pc's cli die de scripts gebruiken om Azure-resources te maken. Gebruik Node.js V10 toevoegen niet.

> [!NOTE]
> Deze hulpprogram ma's zijn beschikbaar op verschillende platforms, waaronder Windows, Linux en iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>De micro Services uitvoeren in docker

Open een nieuwe opdracht prompt om ervoor te zorgen dat u toegang hebt tot de omgevings variabelen die zijn ingesteld door het script **Start. cmd** . In Windows kunt u controleren of de omgevings variabelen zijn ingesteld door de volgende opdracht uit te voeren:

```cmd
set PCS
```

De opdracht geeft alle omgevings variabelen weer die zijn ingesteld door het script **Start. cmd** .

Zorg ervoor dat Docker wordt uitgevoerd op de lokale computer.
> [!NOTE]
> Docker moet Linux- [containers](https://docs.docker.com/docker-for-windows/) uitvoeren als deze wordt uitgevoerd in Windows.

De micro services die worden uitgevoerd in de lokale docker-containers, moeten toegang hebben tot de Azure-Cloud Services. U kunt de Internet verbinding van uw docker-omgeving testen met behulp van de volgende opdracht om een Internet adres te pingen vanuit een container:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Als u de oplossings versneller wilt uitvoeren, gaat u naar de **\\ \\ lokale map Services-scripts** in uw opdracht regel omgeving en voert u de volgende opdracht uit:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Zorg ervoor dat u [een lokaal station deelt](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) met docker voordat u uitvoert `docker-compose up` .

De eerste keer dat u deze opdracht uitvoert, downloadt docker de installatie kopieën van micro service uit docker hub om de containers lokaal te bouwen. Op de volgende plaatsen voert docker de containers onmiddellijk uit.

> [!TIP]
> Micro soft publiceert regel matig nieuwe docker-installatie kopieën met nieuwe functionaliteit. U kunt de volgende reeks opdrachten gebruiken om uw lokale docker-containers en de bijbehorende installatie kopieën op te schonen voordat u de meest recente bestanden haalt:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

U kunt een afzonderlijke shell gebruiken om de logboeken van de container weer te geven. Zoek eerst de container-ID met behulp van de `docker ps` opdracht. Gebruik vervolgens `docker logs {container-id} --tail 1000` om de laatste 1000 vermeldingen voor de opgegeven container weer te geven.

### <a name="start-the-stream-analytics-job"></a>De Stream Analytics-taak starten

Volg deze stappen om de Stream Analytics taak te starten:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Navigeer naar de **resource groep** die is gemaakt voor uw oplossing. De naam van de resource groep is de naam die u voor uw oplossing hebt gekozen toen u het script **Start. cmd** hebt uitgevoerd.
1. Klik op de **taak stream Analytics** in de lijst met resources.
1. Klik op de pagina **overzicht** van stream Analytics op de knop **starten** . Klik vervolgens op **Start** om de taak nu te starten.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dash board

Ga naar in uw browser om toegang te krijgen tot het dash board van de oplossing voor externe controle `http://localhost:8080` . U kunt nu de Web-UI en de lokale micro Services gebruiken.

## <a name="clean-up"></a>Opschonen

Om onnodige kosten te voor komen, kunt u, wanneer u klaar bent met testen, de Cloud Services verwijderen uit uw Azure-abonnement. Als u de services wilt verwijderen, gaat u naar de [Azure Portal](https://ms.portal.azure.com) en verwijdert u de resource groep waarin het script **Start. cmd** is gemaakt.

Gebruik de `docker-compose down --rmi all` opdracht om de docker-installatie kopieën te verwijderen en ruimte vrij te maken op de lokale computer. U kunt ook de lokale kopie van de externe bewakings opslagplaats verwijderen die is gemaakt bij het klonen van de bron code van GitHub.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor controle op afstand hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het dash board van de oplossing](quickstart-remote-monitoring-deploy.md).
