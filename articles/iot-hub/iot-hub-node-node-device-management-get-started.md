---
title: Aan de slag Azure IoT Hub apparaatbeheer (Node) | Microsoft Docs
description: Het gebruik van IoT Hub om het opnieuw opstarten van een extern apparaat te starten. U gebruikt de Azure IoT SDK voor Node.js om een gesimuleerde apparaat-app te implementeren die een directe methode en een service-app bevat die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 774a8fa05dd5044a07450deb675a6761de9869a3
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890632"
---
# <a name="get-started-with-device-management-nodejs"></a>Aan de slag met apparaatbeheer (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de [Azure Portal](https://portal.azure.com) om een IoT Hub te maken en een apparaat-id te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat waarmee dat apparaat opnieuw wordt opgestart. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een Node.js console-app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelfstudie hebt u twee Node.js console-apps:

* **dmpatterns_getstarted_device.js,** dat verbinding maakt met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt, een directe methode voor opnieuw opstarten ontvangt, een fysieke herstart simuleert en de tijd rapporteert voor de laatste keer opnieuw opstarten.

* **dmpatterns_getstarted_service.js**, waarmee een directe methode in de gesimuleerde apparaat-app wordt aanroept, wordt het antwoord weergegeven en de bijgewerkte gerapporteerde eigenschappen weergegeven.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [In Prepare your development environment (Uw](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) ontwikkelomgeving voorbereiden) wordt beschreven hoe u Node.js installeert voor deze zelfstudie in Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in dit artikel wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* U maakt een Node.js-console-app die reageert op een directe methode die door de cloud wordt aangeroepen.

* Het opnieuw opstarten van een gesimuleerd apparaat activeren

* Gebruik de gerapporteerde eigenschappen om query's voor apparaat dubbels in te stellen om apparaten te identificeren en wanneer ze voor het laatst opnieuw zijn opgestart

1. U maakt een lege map met de naam **simulateddevice**.  Maak in de map **simulateddevice** een bestand met de naam package.json door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de opdrachtprompt in de map **manageddevice** de volgende opdracht uit om het **azure-iot-device** Device SDK-pakket en **het azure-iot-device-mqtt-pakket** te installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een teksteditor een **dmpatterns_getstarted_device.js** in de **map manageddevice.**

4. Voeg de volgende 'require'-instructies toe aan het begin van **dmpatterns_getstarted_device.js** bestand:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client** exemplaar te maken.  Vervang de waarde van de tijdelijke aanduiding door de connection string die u eerder hebt gekopieerd in Een nieuw apparaat `{yourdeviceconnectionstring}` [registreren in de IoT-hub](#register-a-new-device-in-the-iot-hub).  

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

7. Open de verbinding met uw IoT-hub en start de directe methodelistener:

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

8. Sla hetdmpatterns_getstarted_device.js **op** en sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleidsregels voor opnieuw proberen implementeren (zoals exponentieel uitvalt), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)(Afhandeling van tijdelijke fouten).

## <a name="get-the-iot-hub-connection-string"></a>De IoT Hub-connection string

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een Node.js-console-app die een externe herstart op een apparaat start met behulp van een directe methode. De app maakt gebruik van query's voor apparaattwee om de laatste keer dat het apparaat opnieuw wordt opgestart te ontdekken.

1. Maak een lege map met **de naam triggerrebootondevice**. Maak in **de map triggerrebootondevice** een package.jsbestand met behulp van de volgende opdracht bij de opdrachtprompt. Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de opdrachtprompt in de map **triggerrebootondevice** de volgende opdracht uit om het **azure-iothub** Device SDK-pakket en **het azure-iot-device-mqtt-pakket** te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met behulp van een **teksteditor eendmpatterns_getstarted_service.js** in de **map triggerrebootondevice.**

4. Voeg de volgende 'require'-instructies toe aan het begin van **dmpatterns_getstarted_service.js** bestand:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Voeg de volgende variabeledeclaraties toe en vervang de waarde van de tijdelijke aanduiding door de IoT-hub connection string die u eerder hebt gekopieerd in De `{iothubconnectionstring}` [IoT-hub-connection string:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Voeg de volgende functie toe om de apparaatmethode aan te roepen om het doelapparaat opnieuw op te starten:

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

7. Voeg de volgende functie toe om een query voor het apparaat uit te voeren en de laatste keer opnieuw op te starten:

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

8. Voeg de volgende code toe om de functies aan te roepen die de directe methode voor opnieuw opstarten activeren en een query uitvoeren voor de laatste keer dat de computer opnieuw wordt opgestart:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Sla hetdmpatterns_getstarted_service.js **op** en sluit het.

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij de opdrachtprompt in **de map manageddevice** de volgende opdracht uit om te luisteren naar de directe methode voor opnieuw opstarten.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Voer bij de opdrachtprompt in de map **triggerrebootondevice** de volgende opdracht uit om het extern opnieuw opstarten te activeren en voer een query uit voor de apparaat dubbel om de laatste keer opnieuw op te starten.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. U ziet de reactie van het apparaat op de directe methode voor opnieuw opstarten en de status van het opnieuw opstarten in de console.

   Hieronder ziet u de reactie van het apparaat op de directe methode voor opnieuw opstarten die door de service wordt verzonden:

   ![uitvoer van manageddevice-app](./media/iot-hub-node-node-device-management-get-started/device.png)

   Hieronder ziet u de service die het opnieuw opstarten activeert en de apparaattweede polling voor de laatste keer dat het apparaat opnieuw wordt opgestart:

   ![triggerrebootondevice app output](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
