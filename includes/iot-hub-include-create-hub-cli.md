---
title: bestand opnemen
description: bestand opnemen
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 10bd2c4902157b9e01b1cb0ff10b3ebdf448568c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244526"
---
In de volgende secties stelt u een terminal in en gebruikt u Azure CLI om een IoT-hub te maken. Als u een Terminal wilt configureren waarop Azure CLI-opdrachten worden uitgevoerd, kunt u de op de browser gebaseerde Azure Cloud Shell gebruiken of een lokale Terminal gebruiken.
* Als u Cloud Shell wilt gebruiken, gaat u naar de volgende sectie: [de Cloud shell starten](#launch-the-cloud-shell). 
* Als u een lokale terminal wilt gebruiken, slaat u de volgende sectie over en gaat u naar [een lokale terminal openen](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>De Cloud Shell starten
In deze sectie maakt u een Cloud Shell-sessie en configureert u uw terminal omgeving.

Meld u aan bij Azure Portal op https://portal.azure.com.  

Zo start u de Cloud Shell:

1. Selecteer de knop **Cloud Shell** in de menubalk rechtsboven in de Azure-portal. 

    ![Knop Cloud Shell in de Azure-portal](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Als de Cloud Shell voor het eerst gebruikt, wordt u gevraagd opslag te maken. Dit is nodig om de Cloud Shell te gebruiken.  Selecteer een abonnement om een opslagaccount en een Microsoft Azure Files-share te maken. 

2. Selecteer uw favoriete CLI-omgeving in de vervolgkeuzelijst **Omgeving selecteren**. In deze snelstartgids wordt gebruikgemaakt van de **bash**-omgeving. Alle volgende CLI-opdrachten werken ook in de Power shell-omgeving. 

    ![CLI-omgeving selecteren](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Sla de volgende sectie over en ga naar [de Azure IOT-extensie installeren](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Open een lokale terminal
Als u ervoor kiest om een lokale terminal te gebruiken in plaats van Cloud Shell, voltooit u deze sectie.  

1. Open een lokale terminal.
1. Voer de opdracht [AZ login](/cli/azure/reference-index#az_login) :

   ```azurecli
   az login
   ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op https://aka.ms/devicelogin en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

    Als er geen webbrowser beschikbaar is of als de webbrowser niet kan worden geopend, gebruikt u de code stroom van het apparaat `az login --use-device-code` .

1. Meldt u zich in de browser aan met uw accountreferenties.

    Raadpleeg [Aanmelden met Azure CLI]( /cli/azure/authenticate-azure-cli ) voor meer informatie over verschillende verificatiemethoden.

1. Ga naar de volgende sectie: [Installeer de Azure IOT-extensie](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>De Azure IoT-extensie installeren
In deze sectie installeert u de Microsoft Azure IoT-extensie voor Azure CLI naar uw CLI-shell. Met de IoT-extensie worden IoT Hub-, IoT Edge- en IoT DPS-specifieke (Device Provisioning Service) opdrachten toegevoegd aan Azure CLI.

> [!IMPORTANT]
> De Terminal opdrachten in de rest van deze Snelstartgids werken hetzelfde in Cloud Shell of een lokale terminal. Als u een opdracht wilt uitvoeren, selecteert u **kopiëren** om een code blok in deze Quick Start te kopiëren. Plak deze in uw CLI-shell en voer deze uit.

Voer de opdracht [AZ extension add](/cli/azure/extension#az-extension-add) uit. 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT-hub maken
In deze sectie gebruikt u Azure CLI om een IoT-hub en een resource groep te maken.  Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Een IoT-hub fungeert als een centrale Message hub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten. 

Een IoT-hub en een resource groep maken:

1. Voer de opdracht [az group create](/cli/azure/group#az-group-create) uit om een resourcegroep te maken. Met de volgende opdracht wordt een resourcegroep met de naam *MyResourceGroup* gemaakt op de locatie *VS - oost*. 
    >[!NOTE]
    > U kunt desgewenst een alternatieve locatie instellen. Als u beschik bare locaties wilt zien, voert u uit `az account list-locations` . In deze zelf studie wordt *Oost* -as gebruikt, zoals weer gegeven in de voor beeld-opdracht. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Voer de opdracht [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) uit om een IoT-hub te maken. Het kan enkele minuten duren voordat een IoT-hub is gemaakt. 

    *YourIotHubName*. Vervang deze tijdelijke aanduiding en de omringende accolades in de volgende opdracht, met de naam die u hebt gekozen voor uw IoT-hub. De naam van de IoT-hub moet wereldwijd uniek zijn in Azure. Gebruik de naam van uw IoT-hub in de rest van deze Snelstartgids, waar u de tijdelijke aanduiding ziet.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken
In deze sectie maakt u een gesimuleerd IoT-apparaat dat is verbonden met uw IoT-hub. 

Een gesimuleerd apparaat maken:
1. Voer de opdracht [AZ IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) uit in uw cli-shell. Hiermee maakt u de id van het gesimuleerde apparaat. 

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    *mijn*. U kunt deze naam rechtstreeks gebruiken voor de gesimuleerde apparaat-ID in de rest van dit artikel. U kunt ook een andere naam gebruiken. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Voer de opdracht [AZ IOT hub apparaat-identiteits verbinding-reeks weer geven](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) uit. 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    De connection string uitvoer heeft de volgende indeling:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Sla de connection string op een veilige locatie op. 

> [!NOTE]
> Zorg ervoor dat de CLI-shell is geopend. U gebruikt deze in latere stappen.