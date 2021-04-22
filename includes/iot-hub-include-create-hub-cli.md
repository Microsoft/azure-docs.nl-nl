---
title: bestand opnemen
description: bestand opnemen
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 4999bd93f338ca7b34b141b88e06e4a769a4aaa1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876421"
---
In de volgende secties stelt u een terminal in en gebruikt u Azure CLI om een IoT-hub te maken. Als u een terminal met Azure CLI-opdrachten wilt configureren, kunt u de browsergebaseerde Azure Cloud Shell of een lokale terminal gebruiken.
* Als u Cloud Shell wilt gebruiken, gaat u naar de volgende [sectie: Start de Cloud Shell](#launch-the-cloud-shell). 
* Als u een lokale terminal wilt gebruiken, slaat u de volgende sectie over en gaat u naar [Een lokale terminal openen.](#open-a-local-terminal)

## <a name="launch-the-cloud-shell"></a>De Cloud Shell starten
In deze sectie maakt u een Cloud Shell en configureert u uw terminalomgeving.

Meld u aan bij Azure Portal op https://portal.azure.com.  

Zo start u de Cloud Shell:

1. Selecteer de knop **Cloud Shell** in de menubalk rechtsboven in de Azure-portal. 

    ![Knop Cloud Shell in de Azure-portal](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Als de Cloud Shell voor het eerst gebruikt, wordt u gevraagd opslag te maken. Dit is nodig om de Cloud Shell te gebruiken.  Selecteer een abonnement om een opslagaccount en een Microsoft Azure Files-share te maken. 

2. Selecteer uw favoriete CLI-omgeving in de vervolgkeuzelijst **Omgeving selecteren**. In deze snelstartgids wordt gebruikgemaakt van de **bash**-omgeving. Alle volgende CLI-opdrachten werken ook in de PowerShell-omgeving. 

    ![CLI-omgeving selecteren](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Sla de volgende sectie over en ga naar [De Azure IoT-extensie installeren.](#install-the-azure-iot-extension) 

## <a name="open-a-local-terminal"></a>Een lokale terminal openen
Als u ervoor hebt gekozen om een lokale terminal te gebruiken in plaats Cloud Shell, voltooit u deze sectie.  

1. Open een lokale terminal.
1. Voer de [opdracht az login](/cli/azure/reference-index#az_login) uit:

   ```azurecli
   az login
   ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op https://aka.ms/devicelogin en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

    Als er geen webbrowser beschikbaar is of de webbrowser niet kan worden geopend, gebruikt u apparaatcodestroom met `az login --use-device-code` .

1. Meldt u zich in de browser aan met uw accountreferenties.

    Raadpleeg [Aanmelden met Azure CLI]( /cli/azure/authenticate-azure-cli ) voor meer informatie over verschillende verificatiemethoden.

1. Ga naar de volgende sectie: [Installeer de Azure IoT-extensie](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>De Azure IoT-extensie installeren
In deze sectie installeert u de IoT Microsoft Azure extensie voor Azure CLI in uw CLI-shell. Met de IoT-extensie worden IoT Hub-, IoT Edge- en IoT DPS-specifieke (Device Provisioning Service) opdrachten toegevoegd aan Azure CLI.

> [!IMPORTANT]
> De terminalopdrachten in de rest van deze quickstart werken hetzelfde in Cloud Shell of een lokale terminal. Als u een opdracht wilt uitvoeren, **selecteert u** Kopiëren om een codeblok in deze quickstart te kopiëren. Plak deze vervolgens in de CLI-shell en voer deze uit.

Voer de [opdracht az extension add](/cli/azure/extension#az_extension_add) uit. 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT-hub maken
In deze sectie gebruikt u Azure CLI om een IoT-hub en een resourcegroep te maken.  Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Een IoT-hub fungeert als een centrale berichtenhub voor bi-directionele communicatie tussen uw IoT-toepassing en de apparaten. 

Een IoT-hub en een resourcegroep maken:

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken. Met de volgende opdracht wordt een resourcegroep met de naam *MyResourceGroup* gemaakt op de locatie *VS - oost*. 
    >[!NOTE]
    > U kunt eventueel een alternatieve locatie instellen. Voer uit om de beschikbare locaties te `az account list-locations` bekijken. In deze zelfstudie *wordt eastus gebruikt,* zoals wordt weergegeven in de voorbeeldopdracht. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Voer de opdracht [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) uit om een IoT-hub te maken. Het kan enkele minuten duren voordat een IoT-hub is gemaakt. 

    *YourIotHubName*. Vervang deze tijdelijke aanduiding en de omliggende accolades in de volgende opdracht met behulp van de naam die u hebt gekozen voor uw IoT-hub. De naam van de IoT-hub moet wereldwijd uniek zijn in Azure. Gebruik de naam van uw IoT-hub in de rest van deze quickstart, waar u de tijdelijke aanduiding ook ziet.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken
In deze sectie maakt u een gesimuleerd IoT-apparaat dat is verbonden met uw IoT-hub. 

Een gesimuleerd apparaat maken:
1. Voer de [opdracht az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) uit in uw CLI-shell. Hiermee maakt u de id van het gesimuleerde apparaat. 

    *YourIotHubName*. vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub. 

    *myDevice*. U kunt deze naam rechtstreeks gebruiken voor de id van het gesimuleerde apparaat in de rest van dit artikel. U kunt ook een andere naam gebruiken. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Voer de [opdracht az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) uit. 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    De connection string heeft de volgende indeling:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Sla de connection string op een veilige locatie op. 

> [!NOTE]
> Houd de CLI-shell geopend. U gebruikt deze in latere stappen.