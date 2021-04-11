---
title: Telemetrie van apparaat naar Azure IoT Hub Snelstartgids verzenden (Node.js)
description: In deze Quick Start gebruikt u de Azure IoT Hub Device SDK voor Node.js om telemetrie van een apparaat naar een IOT-hub te verzenden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 047700be674dfab997b5c87f7446c19fdea9e0eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605957"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Quick Start: verzend telemetrie van een apparaat naar een IoT-hub (Node.js)

**Van toepassing op**: [toepassings ontwikkeling van apparaten](about-iot-develop.md#device-application-development)

In deze Quick Start leert u een eenvoudige IoT Device Application Development-werk stroom. U gebruikt de Azure CLI om een Azure IoT hub en een gesimuleerd apparaat te maken. vervolgens gebruikt u de Azure IoT Node.js SDK om toegang te krijgen tot het apparaat en telemetrie naar de hub te verzenden.

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze quickstart uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u ervoor kiest om de CLI lokaal te gebruiken, hebt u voor deze snelstart versie 2.0.76 of hoger van Azure CLI nodig. Voer az --version uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.
- [Node.js 10+](https://nodejs.org). Als u Azure Cloud Shell gebruikt, moet u de geïnstalleerde versie van Node.js niet bijwerken. Azure Cloud Shell heeft al de meest recente versie van Node.js.

    Controleer de huidige versie van Node.js op uw ontwikkel computer met behulp van de volgende opdracht:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>De Node.js SDK gebruiken om berichten te verzenden
In deze sectie gebruikt u de SDK van Node.js om berichten te verzenden van uw gesimuleerde apparaat naar uw IoT-hub. 

1. Open een nieuw terminalvenster. U gebruikt deze terminal om de Node.js SDK te installeren en te werken met Node.js voorbeeld code. Er zijn nu twee terminals geopend: de computer die u zojuist hebt geopend om te werken met Node.js en de CLI-shell die u in de vorige secties hebt gebruikt om Azure CLI-opdrachten in te voeren.

1. Kopieer de voor [beelden van Azure IoT Node.js SDK-apparaten](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) naar uw lokale computer:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Ga naar de map *Azure-IOT-SDK-node/apparaat/samples/PnP* :

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Installeer de Azure IoT Node.js SDK en de nood zakelijke afhankelijkheden:

    ```console
    npm install
    ```

    Met deze opdracht worden de juiste afhankelijkheden geïnstalleerd zoals opgegeven in de *package.jsvoor* het bestand in de map device samples.

1. Stel beide van de volgende omgevings variabelen in om het gesimuleerde apparaat in te scha kelen om verbinding te maken met Azure IoT.
    * Stel een omgevings variabele in met de naam `IOTHUB_DEVICE_CONNECTION_STRING` . Gebruik voor de waarde van de variabele het apparaat connection string dat u in de vorige sectie hebt opgeslagen.
    * Stel een omgevings variabele in met de naam `IOTHUB_DEVICE_SECURITY_TYPE` . Voor de variabele gebruikt u de letterlijke teken reeks waarde `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Voor Windows-CMD zijn er geen aanhalings tekens rond de teken reeks waarden voor elke variabele.

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
1. Voer in uw open CLI-shell de opdracht [AZ IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) uit om te beginnen met het controleren op gebeurtenissen op uw gesimuleerde IOT-apparaat.  Gebeurtenis berichten worden afgedrukt in de Terminal wanneer ze binnenkomen.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Voer in uw Node.js-Terminal de code uit voor het geïnstalleerde voorbeeld bestand *simple_thermostat.js* . Met deze code wordt het gesimuleerde IoT-apparaat geopend en wordt een bericht verzonden naar de IoT-hub.

    Het Node.js-voor beeld uitvoeren vanaf de terminal:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Dit code voorbeeld maakt gebruik van Azure IoT Plug en Play, waarmee u smart-apparaten kunt integreren in uw oplossingen zonder hand matige configuratie.  Standaard gebruiken de meeste voor beelden in deze documentatie IoT Plug en Play. Zie [Wat is iot Plug en Play?](../iot-pnp/overview-iot-plug-and-play.md) voor meer informatie over de voor delen van IOT PnP en gevallen waarin u deze kunt gebruiken of niet gebruikt.

Als de Node.js code een gesimuleerd telemetrie-bericht van uw apparaat naar de IoT-hub verzendt, wordt het bericht weer gegeven in uw CLI-shell die bewakings gebeurtenissen zijn:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

Uw apparaat is nu veilig verbonden met en verzenden van telemetrie naar Azure IoT Hub.

## <a name="clean-up-resources"></a>Resources opschonen
Als u de Azure-resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, kunt u de Azure CLI gebruiken om ze te verwijderen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Een resourcegroep verwijderen op naam:
1. Voer de opdracht [az group delete](/cli/azure/group#az-group-delete) uit. Met deze opdracht verwijdert u de resource groep, het IoT Hub en de apparaatregistratie die u hebt gemaakt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Voer de opdracht [az group list](/cli/azure/group#az-group-list) uit om te controleren of de resourcegroep is verwijderd.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige Azure IoT-toepassings werk stroom geleerd voor het veilig verbinden van een apparaat met de Cloud en het verzenden van apparaat-naar-Cloud-telemetrie. U hebt de Azure CLI gebruikt om een IoT-hub en een gesimuleerd apparaat te maken. vervolgens hebt u de Azure IoT Node.js SDK gebruikt om toegang te krijgen tot het apparaat en telemetrie naar de hub te verzenden. 

Als volgende stap moet u de Azure IoT Node.js SDK verkennen via toepassings voorbeelden.

- [Meer Node.js voor beelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): deze map bevat meer voor beelden van de Node.js SDK-opslag plaats om IOT hub scenario's te demonstreren.