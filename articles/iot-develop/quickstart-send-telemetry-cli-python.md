---
title: Telemetrie van apparaat verzenden naar Azure IoT Hub Snelstartgids (python)
description: In deze Quick Start gebruikt u de Azure IoT Hub Device SDK voor python voor het verzenden van telemetrie van een apparaat naar een IOT-hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d73f8eeb7b69440f8db67d0b95b40ed6258ee8e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201784"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Quick Start: telemetrie verzenden van een apparaat naar een Azure IoT hub (python)

**Van toepassing op**: [toepassings ontwikkeling van apparaten](about-iot-develop.md#device-application-development)

In deze Quick Start leert u een eenvoudige IoT Device Application Development-werk stroom. U gebruikt de Azure CLI om een Azure IoT hub en een apparaat te maken. vervolgens gebruikt u de Azure IoT python SDK om een gesimuleerd client apparaat te bouwen en telemetrie naar de hub te verzenden. 

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze quickstart uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u ervoor kiest om de CLI lokaal te gebruiken, hebt u voor deze snelstart versie 2.0.76 of hoger van Azure CLI nodig. Voer az --version uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.
- [Python 3.7+](https://www.python.org/downloads/). Zie [Functies van Azure IoT-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) voor andere versies van Python die worden ondersteund.
    
    Voer uit om ervoor te zorgen dat uw python-versie up-to-date is `python --version` . Als u zowel python 2 als python 3 hebt geïnstalleerd en een python 3-omgeving gebruikt, installeert u alle bibliotheken met `pip3` . Dit zorgt ervoor dat de bibliotheken worden geïnstalleerd in uw python 3-runtime.
    > [!IMPORTANT]
    > Selecteer in het installatie programma python de optie om **python toe te voegen aan het pad**. Als u python 3,7 of hoger al hebt geïnstalleerd, controleert u of u de installatie-map python hebt toegevoegd aan de `PATH` omgevings variabele.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>De python-SDK gebruiken om berichten te verzenden
In deze sectie gebruikt u de python-SDK voor het verzenden van berichten van uw gesimuleerde apparaat naar uw IoT-hub.

1. Open een nieuw terminalvenster. U gebruikt deze terminal om de python-SDK te installeren en te werken met python-voorbeeld code. Er zijn nu twee terminals geopend: de ene die u zojuist hebt geopend om te werken met python, en de CLI-shell die u in de vorige secties hebt gebruikt om Azure CLI-opdrachten in te voeren.       

1. Kopieer de voor [beelden van Azure IOT PYTHON SDK-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) naar uw lokale computer:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    en navigeert u naar de map *Azure-IOT-SDK-python/Azure-IOT-device/samples* :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installeer de Azure IoT python SDK:

    ```console
    pip install azure-iot-device
    ```
1. Stel de verbindings reeks van het apparaat in als een omgevings variabele met de naam `IOTHUB_DEVICE_CONNECTION_STRING` . Dit is de teken reeks die u hebt verkregen in de vorige sectie nadat u uw gesimuleerde python-apparaat hebt gemaakt.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Voor Windows-CMD zijn er geen aanhalings tekens rond de connection string.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Voer in uw open CLI-shell de opdracht [AZ IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) uit om te beginnen met het controleren op gebeurtenissen op uw gesimuleerde IOT-apparaat.  Gebeurtenis berichten worden afgedrukt in de Terminal wanneer ze binnenkomen.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Voer in de python-Terminal de code uit voor het geïnstalleerde voorbeeld bestand *simple_send_message. py* . Met deze code wordt het gesimuleerde IoT-apparaat geopend en wordt een bericht verzonden naar de IoT-hub.

    Het python-voor beeld uitvoeren vanuit de terminal:
    ```console
    python ./simple_send_message.py
    ```

    U kunt eventueel de python-code uitvoeren vanuit het voor beeld in uw python IDE:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Als de python-code een bericht van uw apparaat naar de IoT-hub verzendt, wordt het bericht weer gegeven in uw CLI-shell die bewakings gebeurtenissen zijn:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Uw apparaat is nu veilig verbonden met en verzenden van telemetrie naar Azure IoT Hub.

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, kunt u de Azure CLI gebruiken om ze te verwijderen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [az group delete](/cli/azure/group#az-group-delete) uit. Hiermee verwijdert u de resourcegroep, de IoT Hub en de apparaatregistratie die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de opdracht [az group list](/cli/azure/group#az-group-list) uit om te controleren of de resourcegroep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een eenvoudige Azure IoT-toepassings werk stroom geleerd voor het veilig verbinden van een apparaat met de Cloud en het verzenden van apparaat-naar-Cloud-telemetrie. U hebt de Azure CLI gebruikt om een IoT-hub en een apparaat te maken. vervolgens hebt u de Azure IoT python SDK gebruikt om een gesimuleerd apparaat te bouwen en telemetrie naar de hub te verzenden. 

Als volgende stap moet u de Azure IoT python SDK verkennen via toepassings voorbeelden.

- [Asynchrone voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): deze map bevat asynchrone python-voor beelden voor aanvullende IOT hub scenario's.
- [Synchrone voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): deze map bevat python-voor beelden voor gebruik met python 2,7-of synchrone compatibiliteits Scenario's voor python 3.5 +
- [IOT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)-voor beelden: deze map bevat python-voor beelden voor het werken met Edge-modules en downstream-apparaten.