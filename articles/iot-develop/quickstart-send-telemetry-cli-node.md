---
title: 'Snelstart: Apparaat-telemetrie verzenden Azure IoT Hub (Node.js)'
description: In deze quickstart gebruikt u de Azure IoT Hub Device SDK voor Node.js om telemetrie van een apparaat naar een IoT-hub te verzenden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 3d42ac814678136c2f6342cd1064e3c3ff394507
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777235"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub (Node.js)

**Van toepassing op**: [Ontwikkeling van apparaattoepassing](about-iot-develop.md#device-application-development)

In deze quickstart leert u een eenvoudige werkstroom voor het ontwikkelen van IoT-apparaten. U gebruikt de Azure CLI om een Azure IoT-hub en een gesimuleerd apparaat te maken. Vervolgens gebruikt u de Azure IoT Node.js SDK voor toegang tot het apparaat en het verzenden van telemetrie naar de hub.

## <a name="prerequisites"></a>Vereisten
- Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Azure CLI. U kunt alle opdrachten in deze quickstart uitvoeren met behulp van de Azure Cloud Shell, een interactieve CLI-shell die in uw browser wordt uitgevoerd. Als u de Cloud Shell gebruikt, hoeft u niets te installeren. Als u ervoor kiest om de CLI lokaal te gebruiken, hebt u voor deze snelstart versie 2.0.76 of hoger van Azure CLI nodig. Voer az --version uit om de versie te zoeken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.
- [Node.js 10+](https://nodejs.org). Als u Azure Cloud Shell gebruikt, moet u de geïnstalleerde versie van Node.js niet bijwerken. Azure Cloud Shell heeft al de meest recente versie van Node.js.

    Controleer de huidige versie van Node.js uw ontwikkelmachine met behulp van de volgende opdracht:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>De SDK Node.js gebruiken om berichten te verzenden
In deze sectie gebruikt u de Node.js SDK om berichten van uw gesimuleerde apparaat naar uw IoT-hub te verzenden. 

1. Open een nieuw terminalvenster. U gebruikt deze terminal om de Node.js SDK te installeren en te werken met Node.js voorbeeldcode. U hebt nu twee terminals geopend: de terminal die u zojuist hebt geopend om met Node.js te werken en de CLI-shell die u in de vorige secties hebt gebruikt om Azure CLI-opdrachten in te voeren.

1. Kopieer de [azure IoT Node.js SDK-apparaatvoorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) naar uw lokale computer:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navigeer *naar de map azure-iot-sdk-node/device/samples/pnp:*

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Installeer de Azure IoT Node.js SDK en de benodigde afhankelijkheden:

    ```console
    npm install
    ```

    Met deze opdracht installeert u de juiste afhankelijkheden zoals opgegeven in de *package.jsin* het bestand in de map met voorbeelden van apparaten.

1. Stel beide van de volgende omgevingsvariabelen in, zodat uw gesimuleerde apparaat verbinding kan maken met Azure IoT.
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

1. Voer in Node.js terminal de code uit voor het geïnstalleerde voorbeeldbestand *simple_thermostat.js* . Deze code heeft toegang tot het gesimuleerde IoT-apparaat en verzendt een bericht naar de IoT-hub.

    U kunt het Node.js uitvoeren vanuit de terminal:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > In dit codevoorbeeld wordt gebruikgemaakt van Azure IoT Plug en Play, waarmee u slimme apparaten in uw oplossingen kunt integreren zonder handmatige configuratie.  De meeste voorbeelden in deze documentatie maken standaard gebruik van IoT Plug en Play. Zie Wat [is IoT](../iot-pnp/overview-iot-plug-and-play.md) PnP? voor meer informatie over de voordelen van IoT PnP en cases voor het al dan niet gebruiken Plug en Play ervan.

Wanneer de Node.js-code een gesimuleerd telemetriebericht van uw apparaat naar de IoT-hub verzendt, wordt het bericht weergegeven in de CLI-shell waarin gebeurtenissen worden gecontroleerd:

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

In deze quickstart hebt u een eenvoudige Azure IoT-toepassingswerkstroom geleerd voor het veilig verbinden van een apparaat met de cloud en het verzenden van telemetrie van apparaat naar cloud. U hebt de Azure CLI gebruikt om een IoT-hub en een gesimuleerd apparaat te maken. Vervolgens hebt u de Azure IoT Node.js SDK gebruikt om toegang te krijgen tot het apparaat en telemetrie te verzenden naar de hub. 

Als volgende stap verkent u de Azure IoT Node.js SDK via toepassingsvoorbeelden.

- [Meer Node.js Voorbeelden:](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)deze map bevat meer voorbeelden uit de Node.js SDK-opslagplaats om de IoT Hub laten zien.