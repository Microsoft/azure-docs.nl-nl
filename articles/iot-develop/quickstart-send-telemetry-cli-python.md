---
title: Apparaat-telemetrie verzenden naar Azure IoT Hub quickstart (Python)
description: In deze quickstart gebruikt u de Azure IoT Hub Device SDK voor Python om telemetrie van een apparaat naar een IoT-hub te verzenden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: ea0b161a9038666e1e7ddd5a6c6af2078afff8aa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766516"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Quickstart: Telemetrie vanaf een apparaat verzenden naar een Azure IoT-hub (Python)

**Van toepassing op**: [Ontwikkeling van apparaattoepassing](about-iot-develop.md#device-application-development)

In deze quickstart leert u een eenvoudige werkstroom voor het ontwikkelen van IoT-apparaattoepassing. U gebruikt de Azure CLI om een Azure IoT-hub en een apparaat te maken. Vervolgens gebruikt u de Azure IoT Python SDK om een gesimuleerd clientapparaat te bouwen en telemetrie te verzenden naar de hub. 

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze quickstart uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u ervoor kiest om de CLI lokaal te gebruiken, hebt u voor deze snelstart versie 2.0.76 of hoger van Azure CLI nodig. Voer az --version uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.
- [Python 3.7+](https://www.python.org/downloads/). Zie [Functies van Azure IoT-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) voor andere versies van Python die worden ondersteund.
    
    Voer uit om ervoor te zorgen dat uw Python-versie up-to-date `python --version` is. Als u zowel Python 2 als Python 3 hebt geïnstalleerd en een Python 3-omgeving gebruikt, installeert u alle bibliotheken met behulp van `pip3` . Met deze opdracht zorgt u ervoor dat de bibliotheken zijn geïnstalleerd in uw Python 3-runtime.
    > [!IMPORTANT]
    > Selecteer in het Python-installatieprogramma de optie Python **toevoegen aan PAD**. Als Python 3.7 of hoger al is geïnstalleerd, controleert u of u de Python-installatiemap hebt toegevoegd aan de `PATH` omgevingsvariabele.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>De Python-SDK gebruiken om berichten te verzenden
In deze sectie gebruikt u de Python SDK om berichten van uw gesimuleerde apparaat naar uw IoT-hub te verzenden.

1. Open een nieuw terminalvenster. U gebruikt deze terminal om de Python-SDK te installeren en met Python-voorbeeldcode te werken. U hebt nu twee terminals geopend: de terminal die u zojuist hebt geopend om met Python te werken en de CLI-shell die u in de vorige secties hebt gebruikt om Azure CLI-opdrachten in te voeren.       

1. Kopieer de [Azure IoT Python SDK-apparaatvoorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) naar uw lokale computer:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Navigeer *naar de map azure-iot-sdk-python/azure-iot-device/samples/pnp:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installeer de Azure IoT Python SDK:

    ```console
    pip install azure-iot-device
    ```
1. Stel beide van de volgende omgevingsvariabelen in, zodat het gesimuleerde apparaat verbinding kan maken met Azure IoT.
    * Stel een omgevingsvariabele in met de naam `IOTHUB_DEVICE_CONNECTION_STRING` . Gebruik voor de variabele waarde de apparaatwaarde connection string u in de vorige sectie hebt opgeslagen.
    * Stel een omgevingsvariabele in met de naam `IOTHUB_DEVICE_SECURITY_TYPE` . Gebruik voor de variabele de letterlijke tekenreekswaarde `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Voor Windows CMD zijn er geen aanhalingstekens rond de tekenreekswaarden voor elke variabele.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux of Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Voer in uw open CLI-shell de [opdracht az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) uit om te beginnen met het controleren op gebeurtenissen op uw gesimuleerde IoT-apparaat.  Gebeurtenisberichten worden in de terminal afgedrukt wanneer ze binnenkomen.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Voer in uw Python-terminal de code uit voor het geïnstalleerde *voorbeeldbestand simple_thermostat.py.* Deze code heeft toegang tot het gesimuleerde IoT-apparaat en verzendt een bericht naar de IoT-hub.

    Het Python-voorbeeld uitvoeren vanuit de terminal:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > In dit codevoorbeeld wordt gebruikgemaakt van Azure IoT Plug en Play, waarmee u slimme apparaten in uw oplossingen kunt integreren zonder handmatige configuratie.  De meeste voorbeelden in deze documentatie maken standaard gebruik van IoT Plug en Play. Zie Wat [is IoT](../iot-pnp/overview-iot-plug-and-play.md) PnP? voor meer informatie over de voordelen van IoT PnP en cases voor het al dan niet gebruiken Plug en Play ervan.

 Wanneer de Python-code een bericht van uw apparaat naar de IoT-hub verzendt, wordt het bericht weergegeven in de CLI-shell die gebeurtenissen bewakingt:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Uw apparaat is nu veilig verbonden en stuurt telemetrie naar Azure IoT Hub.

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, kunt u de Azure CLI gebruiken om ze te verwijderen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [az group delete](/cli/azure/group#az_group_delete) uit. Met deze opdracht verwijdert u de resourcegroep, de IoT Hub en de apparaatregistratie die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de opdracht [az group list](/cli/azure/group#az_group_list) uit om te controleren of de resourcegroep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een eenvoudige Azure IoT-toepassingswerkstroom geleerd voor het veilig verbinden van een apparaat met de cloud en het verzenden van telemetrie van apparaat naar cloud. U hebt de Azure CLI gebruikt om een IoT-hub en een apparaat te maken. Vervolgens hebt u de Azure IoT Python SDK gebruikt om een gesimuleerd apparaat te bouwen en telemetrie te verzenden naar de hub. 

Als volgende stap verkent u de Azure IoT Python SDK via toepassingsvoorbeelden.

- [Asynchrone voorbeelden:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)deze map bevat asynchrone Python-voorbeelden voor meer IoT Hub scenario's.
- [Synchrone voorbeelden:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)deze map bevat Python-voorbeelden voor gebruik met Python 2.7 of synchrone compatibiliteitsscenario's voor Python 3.6+
- [IoT Edge voorbeelden:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)deze map bevat Python-voorbeelden voor het werken met Edge-modules en downstreamapparaten.
