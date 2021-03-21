---
title: Telemetrie van apparaat naar Azure IoT Hub Snelstartgids verzenden (Node.js)
description: In deze Quick Start gebruikt u de Azure IoT Hub Device SDK voor Node.js om telemetrie van een apparaat naar een IOT-hub te verzenden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102197806"
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

1. Ga naar de map *Azure-IOT-SDK-node/apparaat/samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Installeer de Azure IoT Node.js SDK en de nood zakelijke afhankelijkheden:

    ```console
    npm install
    ```
    Met deze opdracht worden de juiste afhankelijkheden geïnstalleerd zoals opgegeven in de *package.jsvoor* het bestand in de map device samples.

1. Stel de verbindings reeks van het apparaat in als een omgevings variabele met de naam `DEVICE_CONNECTION_STRING` . De teken reeks waarde die u moet gebruiken, is de teken reeks die u hebt verkregen in de vorige sectie nadat u uw gesimuleerde Node.js apparaat hebt gemaakt. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Voor Windows-CMD zijn er geen aanhalings tekens rond de connection string.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Voer in uw open CLI-shell de opdracht [AZ IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) uit om te beginnen met het controleren op gebeurtenissen op uw gesimuleerde IOT-apparaat.  Gebeurtenis berichten worden afgedrukt in de Terminal wanneer ze binnenkomen.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Voer in uw Node.js-Terminal de code uit voor het geïnstalleerde voorbeeld bestand *simple_sample_device.js* . Met deze code wordt het gesimuleerde IoT-apparaat geopend en wordt een bericht verzonden naar de IoT-hub.

    Het Node.js-voor beeld uitvoeren vanaf de terminal:
    ```console
    node ./simple_sample_device.js
    ```

    U kunt eventueel de Node.js code uitvoeren vanuit het voor beeld in uw Java script IDE:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Als de Node.js code een gesimuleerd telemetrie-bericht van uw apparaat naar de IoT-hub verzendt, wordt het bericht weer gegeven in uw CLI-shell die bewakings gebeurtenissen zijn:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
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