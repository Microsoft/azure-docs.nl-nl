---
title: 'Quickstart: Telemetrie verzenden naar Azure IoT Hub (CLI) quickstart'
description: In deze quickstart lezen ontwikkelaars die nog geen ervaring hebben met IoT Hub hoe ze de Azure CLI kunnen gaan gebruiken om een IoT-hub te maken, telemetrie te verzenden en berichten tussen een apparaat en de hub weer te geven.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: a34fd5480ae47678f250dbf888005c396ba32f38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792143"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub en deze bewaken met de Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze quickstart gebruikt u de Azure CLI om een IoT-hub en een gesimuleerd apparaat te maken, telemetrie van het apparaat naar de hub te verzenden en een bericht van cloud naar apparaat te verzenden. U kunt de Azure-portal ook gebruiken om metrische gegevens over apparaten te visualiseren. Dit is een basiswerkstroom voor ontwikkelaars die de CLI gebruiken om te communiceren met een IoT Hub-toepassing.

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze quickstart uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u ervoor kiest om de CLI lokaal te gebruiken, hebt u voor deze snelstart versie 2.0.76 of hoger van Azure CLI nodig. Voer az --version uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Meld u aan bij Azure Portal op https://portal.azure.com.

Houd de portal in de browser geopend, ongeacht of u de CLI lokaal of in de Cloud Shell uitvoert.  U gebruikt deze verderop in deze quickstart.

## <a name="launch-the-cloud-shell"></a>De Cloud Shell starten
In deze sectie start u een exemplaar van de Azure Cloud Shell. Als u de CLI lokaal gebruikt, gaat u naar de sectie [Twee CLI-sessies voorbereiden](#prepare-two-cli-sessions).

Zo start u de Cloud Shell:

1. Selecteer de knop **Cloud Shell** in de menubalk rechtsboven in de Azure-portal. 

    ![Knop Cloud Shell in de Azure-portal](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Als de Cloud Shell voor het eerst gebruikt, wordt u gevraagd opslag te maken. Dit is nodig om de Cloud Shell te gebruiken.  Selecteer een abonnement om een opslagaccount en een Microsoft Azure Files-share te maken. 

2. Selecteer uw favoriete CLI-omgeving in de vervolgkeuzelijst **Omgeving selecteren**. In deze snelstartgids wordt gebruikgemaakt van de **bash**-omgeving. Alle volgende CLI-opdrachten werken ook in de PowerShell-omgeving. 

    ![CLI-omgeving selecteren](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Twee CLI-sessies voorbereiden

In deze sectie bereidt u twee Azure CLI-sessies voor. Als u de Cloud Shell gebruikt, voert u de twee sessies uit op afzonderlijke browsertabbladen. Als u een lokale CLI-client gebruikt, voert u twee afzonderlijke CLI-exemplaren uit. U gebruikt de eerste sessie als een gesimuleerd apparaat en de tweede sessie om berichten te controleren en te verzenden. Als u een opdracht wilt uitvoeren, selecteert u **Kopiëren** om een codeblok uit deze snelstartgids te kopiëren; plak de code in de shell-sessie en voer deze uit.

Voor Azure CLI moet u zijn aangemeld bij uw Azure-account. Alle communicatie tussen uw Azure CLI-shell-sessie en uw IoT-hub wordt geverifieerd en versleuteld. Als gevolg hiervan heeft deze snelstartgids geen aanvullende verificatie, zoals een verbindingsreeks, nodig die u wel zou gebruiken met een echt apparaat.

*  Voer de opdracht [az extension add](/cli/azure/extension#az_extension_add) toe om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw CLI-shell. Met de IoT-extensie worden IoT Hub-, IoT Edge- en IoT DPS-specifieke (Device Provisioning Service) opdrachten toegevoegd aan Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Nadat u de Azure IOT-extensie hebt geïnstalleerd, hoeft u deze niet opnieuw te installeren in een Cloud Shell-sessie. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Open een tweede CLI-sessie.  Als u de Cloud Shell gebruikt, selecteert u **Nieuwe sessie openen**. Als u de CLI lokaal gebruikt, opent u een tweede exemplaar. 

    >[!div class="mx-imgBorder"]
    >![Nieuwe Cloud Shell-sessie openen](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Een IoT Hub maken
In deze sectie gebruikt u de Azure CLI voor het maken van een resourcegroep en een IoT Hub.  Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. IoT Hub fungeert als centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten. 

> [!TIP]
> U kunt desgewenst een Azure-resourcegroep, een IoT Hub en andere resources maken met behulp van de [Azure-portal](iot-hub-create-through-portal.md), [Visual Studio-code](iot-hub-create-use-iot-toolkit.md) of andere programmatische methoden.  

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken. Met de volgende opdracht wordt een resourcegroep met de naam *MyResourceGroup* gemaakt op de locatie *VS - oost*. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Voer de opdracht [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) uit om een IoT-hub te maken. Het kan enkele minuten duren voordat een IoT-hub is gemaakt. 

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. De naam van de IoT-hub moet wereldwijd uniek zijn in Azure. Deze tijdelijke aanduiding wordt gebruikt in de rest van deze quickstart om de naam van uw IoT-hub aan te geven.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Een apparaat maken en bewaken
In deze sectie maakt u een gesimuleerd apparaat in de eerste CLI-sessie. Het gesimuleerde apparaat verzendt telemetrie van het apparaat naar uw IoT-hub. In de tweede CLI-sessie bewaakt u gebeurtenissen en telemetrie en verzendt u een bericht van de cloud naar het gesimuleerde apparaat.

Zo maakt en start u een gesimuleerd apparaat:
1. Voer de opdracht [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) uit in de eerste CLI-sessie. Hiermee maakt u de id van het gesimuleerde apparaat. 

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    *simDevice*. U kunt deze naam in de rest van deze quickstart rechtstreeks voor het gesimuleerde apparaat gebruiken. U kunt ook een andere naam gebruiken. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Voer de opdracht [az iot device simulate](/cli/azure/ext/azure-iot/iot/device#ext-azure-iot-az-iot-device-simulate) uit in de eerste CLI-sessie.  Hiermee start u het gesimuleerde apparaat. Het apparaat verzendt telemetrie naar uw IoT-hub en ontvangt berichten van de hub.  

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Zo bewaakt u een apparaat:
1. Voer in de tweede CLI-sessie de opdracht [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) uit. Hiermee start u het bewaken van het gesimuleerde apparaat. De uitvoer toont telemetrie die het gesimuleerde apparaat naar de IoT-hub verzendt.

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell-gebeurtenissen bewaken](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Nadat u het gesimuleerde apparaat in de tweede CLI-sessie hebt bewaakt, drukt u op Ctrl+C om het bewaken te stoppen. 

## <a name="use-the-cli-to-send-a-message"></a>De CLI gebruiken om een bericht te verzenden
In deze sectie gebruikt u de tweede CLI-sessie om een bericht te verzenden naar het gesimuleerde apparaat.

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat actief is. Als het apparaat is gestopt, voert u de volgende opdracht uit om het te starten:

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Voer in de tweede CLI-sessie de opdracht [az iot device c2d-message send](/cli/azure/ext/azure-iot/iot/device/c2d-message#ext-azure-iot-az-iot-device-c2d-message-send) uit. Hiermee verzendt u vanuit uw IoT-hub een cloud-naar-apparaat-bericht naar het gesimuleerde apparaat. Het bericht bevat een tekenreeks en twee sleutel-waardeparen.  

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    U kunt cloud-naar-apparaat-berichten ook verzenden met behulp van de Azure-portal. Daartoe bladert u naar de overzichtspagina voor uw IoT Hub, selecteert u **IoT-apparaten**, selecteert u het gesimuleerde apparaat en selecteert u **Bericht naar apparaat**. 

1. Controleer in de eerste CLI-sessie of het gesimuleerde apparaat het bericht heeft ontvangen. 

    ![Cloud-naar-apparaat-bericht van Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Nadat u het bericht hebt bekeken, sluit u de tweede CLI-sessie. Houd de eerste CLI-sessie geopend. U kunt deze gebruiken om resources in een latere stap op te schonen.

## <a name="view-messaging-metrics-in-the-portal"></a>Metrische gegevens over berichten weergeven in de portal
Met de Azure-portal kunt u alle aspecten van uw IoT Hub en apparaten beheren. In een typische IoT Hub-toepassing die telemetrie van apparaten opneemt, wilt u mogelijk apparaten bewaken of metrische gegevens over de telemetrie van het apparaat bekijken. 

Zo visualiseert u metrische gegevens voor berichten in de Azure-portal:
1. Selecteer in het linkernavigatiemenu in de portal de optie **Alle resources**. Hiermee worden alle resources in uw abonnement weergegeven, met inbegrip van de IoT-hub die u hebt gemaakt. 

1. Selecteer de koppeling in de IoT-hub die u hebt gemaakt. In de portal wordt de overzichtspagina voor de hub weergegeven.

1. Selecteer **Metrische gegevens** in het linkerdeelvenster van uw IoT Hub. 

    ![Metrische gegevens voor IoT Hub-berichten](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Voer in **Bereik** de naam van de IoT-hub in.

2. Selecteer *Metrische standaardgegevens voor IOT hub* in **Metrische naamruimte**.

3. Selecteer in **Metrisch gegeven** de optie *Totaal aantal gebruikte berichten*. 

4. Beweeg de muisaanwijzer over het gebied van de tijdlijn waarin uw apparaat berichten heeft verzonden. Het totale aantal berichten op een bepaald moment wordt weergegeven in de linkerbenedenhoek van de tijdlijn.

    ![Metrische gegevens voor Azure IoT Hub weergeven](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. U kunt ook de vervolgkeuzelijst **Metrisch gegeven** gebruiken om andere metrische gegevens op uw gesimuleerde apparaat weer te geven. Bijvoorbeeld *Voltooide C2d-berichtleveringen* of *Totaal aantal apparaten (preview)* . 

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, kunt u de Azure CLI gebruiken om ze te verwijderen.

Als u verder wilt gaan met het volgende aanbevolen artikel, kunt u de resources die u al hebt gemaakt behouden en opnieuw gebruiken. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [az group delete](/cli/azure/group#az_group_delete) uit. Hiermee verwijdert u de resourcegroep, de IoT Hub en de apparaatregistratie die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de opdracht [az group list](/cli/azure/group#az_group_list) uit om te controleren of de resourcegroep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u de Azure CLI gebruikt om een IoT-hub en een gesimuleerd apparaat te maken, telemetrie te verzenden, telemetrie te bewaken, een bericht van cloud naar apparaat te verzenden en resources op te schonen. U hebt de Azure-portal gebruikt om metrische gegevens van berichten op uw apparaat te visualiseren.

Als u een apparaatontwikkelaar bent, is de voorgestelde volgende stap het doornemen van de quickstart over telemetrie, waarin de Azure IoT Device SDK voor C wordt gebruikt. U kunt ook een van de beschikbare quickstart-artikelen over Azure IoT Hub-telemetrie in uw taal of SDK van voorkeur bekijken.

> [!div class="nextstepaction"]
> [Snelstart: Telemetrie verzenden van een apparaat naar een IoT-hub (C)](quickstart-send-telemetry-c.md)