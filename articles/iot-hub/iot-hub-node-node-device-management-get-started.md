---
title: Aan de slag met Azure IoT Hub Apparaatbeheer (knoop punt) | Microsoft Docs
description: IoT Hub Apparaatbeheer gebruiken om het opnieuw opstarten van een extern apparaat te initiëren. U gebruikt de Azure IoT SDK voor Node.js voor het implementeren van een gesimuleerde apparaat-app die een directe methode en een service-app bevat die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91252014"
---
# <a name="get-started-with-device-management-nodejs"></a>Aan de slag met Apparaatbeheer (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de [Azure Portal](https://portal.azure.com) voor het maken van een IOT hub en het maken van een apparaat-id in uw IOT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat waarmee dat apparaat opnieuw wordt opgestart. Directe methoden worden vanuit de Cloud aangeroepen.

* Maak een Node.js-console-app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelf studie hebt u twee Node.js-console-apps:

* **dmpatterns_getstarted_device.js**, die verbinding maakt met uw IOT-hub met de apparaat-id die u eerder hebt gemaakt, ontvangt een directe methode voor opnieuw opstarten, simuleert fysiek opnieuw opstarten en rapporteert de tijd voor de laatste keer opnieuw opstarten.

* **dmpatterns_getstarted_service.js**, waarmee een directe methode wordt aangeroepen in de gesimuleerde apparaat-app, het antwoord wordt weer gegeven en de bijgewerkte gerapporteerde eigenschappen worden weer gegeven.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0. x of hoger. [Uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) bevat informatie over het installeren van Node.js voor deze zelf studie over Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* U maakt een Node.js-console-app die reageert op een directe methode die door de cloud wordt aangeroepen.

* Het opnieuw opstarten van een gesimuleerd apparaat activeren

* Gebruik de gerapporteerde eigenschappen om Device-dubbele query's in te scha kelen om apparaten te identificeren en wanneer deze voor het laatst opnieuw zijn opgestart

1. U maakt een lege map met de naam **simulateddevice**.  Maak in de map **simulateddevice** een bestand met de naam package.json door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de opdracht prompt in de map **simulateddevice** de volgende opdracht uit om het **Azure-IOT-Device-SDK-** pakket te installeren en **Azure-IOT-Device-mqtt** -pakket:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een tekst editor een **dmpatterns_getstarted_device.js** -bestand in de map **simulateddevice** .

4. Voeg de volgende ' vereist '-instructies toe aan het begin van het **dmpatterns_getstarted_device.js** -bestand:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client** exemplaar te maken.  Vervang de `{yourdeviceconnectionstring}` waarde van de tijdelijke aanduiding door het apparaat Connection String u eerder hebt gekopieerd in [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Voeg de volgende functie toe om de directe methode op het apparaat te implementeren

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Open de verbinding met uw IoT-hub en start de listener voor directe methoden:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Sla het **dmpatterns_getstarted_device.js** bestand op en sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productie code moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële uitstel), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe keer opnieuw opstarten op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een Node.js-console-app die een apparaat op afstand opnieuw opstarten start via een directe methode. De app maakt gebruik van Device-dubbele query's om de tijd van de laatste keer opnieuw opstarten voor dat apparaat te detecteren.

1. Maak een lege map met de naam **triggerrebootondevice**. Maak in de map **triggerrebootondevice** een package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt. Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de opdracht prompt in de map **triggerrebootondevice** de volgende opdracht uit om het **Azure-iothub** apparaat SDK-pakket en het **Azure-IOT-Device-mqtt** -pakket te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met een tekst editor een **dmpatterns_getstarted_service.js** -bestand in de map **triggerrebootondevice** .

4. Voeg de volgende ' vereist '-instructies toe aan het begin van het **dmpatterns_getstarted_service.js** -bestand:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Voeg de volgende variabelen declaraties toe en vervang de `{iothubconnectionstring}` waarde van de tijdelijke aanduiding door de IOT hub Connection String u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Voeg de volgende functie toe om de methode van het apparaat aan te roepen om het doel apparaat opnieuw op te starten:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Voeg de volgende functie toe aan een query voor het apparaat en ontvang het tijdstip van de laatste keer opnieuw opstarten:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Voeg de volgende code toe om de functies aan te roepen die de directe methode voor opnieuw opstarten activeren en de query voor de laatste keer opnieuw opstarten:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Sla het **dmpatterns_getstarted_service.js** bestand op en sluit het.

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij de opdracht prompt in de map **simulateddevice** de volgende opdracht uit om te beginnen met Luis teren naar de methode voor direct opnieuw opstarten.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Voer bij de opdracht prompt in de map **triggerrebootondevice** de volgende opdracht uit om het apparaat op afstand opnieuw op te starten en de query uit te voeren op het dubbele tijdstip van de laatste keer dat de computer opnieuw wordt opgestart.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. U ziet de reactie van het apparaat op de directe methode voor opnieuw opstarten en de status van opnieuw opstarten in de-console.

   Hieronder ziet u de reactie van het apparaat op de directe methode voor opnieuw opstarten, verzonden door de service:

   ![simulateddevice-app-uitvoer](./media/iot-hub-node-node-device-management-get-started/device.png)

   Hieronder ziet u de service waarmee de herstart wordt geactiveerd en het apparaat wordt gecontroleerd tussen de laatste keer opnieuw opstarten:

   ![triggerrebootondevice-app-uitvoer](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
