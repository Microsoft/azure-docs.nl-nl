---
title: Taken plannen met Azure IoT Hub (knoop punt) | Microsoft Docs
description: Een Azure IoT Hub-taak plannen voor het aanroepen van een directe methode op meerdere apparaten. U gebruikt de Azure IoT-Sdk's voor Node.js om de gesimuleerde apparaat-apps en een service-app te implementeren om de taak uit te voeren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: e1992c806619154fa7b3c33500b2e54fbc919f20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151435"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Taken plannen en uitzenden (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub is een volledig beheerde service waarmee een back-end-app taken kan maken en bijhouden waarmee miljoenen apparaten kunnen worden gepland en bijgewerkt.  Taken kunnen worden gebruikt voor de volgende acties:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Met een taak wordt een van deze acties gewikkeld en wordt de voortgang van de uitvoering van een set apparaten getraceerd, die wordt gedefinieerd door een dubbele query voor een apparaat.  Een back-end-app kan bijvoorbeeld een taak gebruiken om een methode voor opnieuw opstarten op te roepen op 10.000-apparaten, opgegeven door een dubbele query van een apparaat en in een later tijdstip te worden gepland. Deze toepassing kan vervolgens de voortgang volgen wanneer deze apparaten de methode voor opnieuw opstarten ontvangen en uitvoeren.

Meer informatie over elk van deze mogelijkheden vindt u in de volgende artikelen:

* Apparaat-dubbele en eigenschappen: [aan de slag met apparaatdubbels](iot-hub-node-node-twin-getstarted.md) en [zelf studie: een dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IOT hub ontwikkelaars handleiding-directe methoden](iot-hub-devguide-direct-methods.md) en [zelf studie: directe methoden](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze zelfstudie ontdekt u hoe u:

* Maak een Node.js gesimuleerde apparaat-app die een directe methode heeft, waardoor **lockDoor** kan worden aangeroepen door de back-end van de oplossing.

* Maak een Node.js-console-app die de directe methode **lockDoor** aanroept in de gesimuleerde apparaat-app met behulp van een taak en de gewenste eigenschappen bijwerkt met behulp van een apparaat taak.

Aan het einde van deze zelf studie hebt u twee Node.js-apps:

* **simDevice.js**, die verbinding maakt met uw IOT-hub met de apparaat-id en een **lockDoor** directe methode ontvangt.

* **scheduleJobService.js**, waarmee een directe methode wordt aangeroepen in de gesimuleerde apparaat-app en de gewenste eigenschappen van het apparaat worden bijgewerkt met behulp van een taak.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0. x of hoger. [Uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) bevat informatie over het installeren van Node.js voor deze zelf studie over Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Node.js-console-app die reageert op een directe methode die wordt aangeroepen door de Cloud, waardoor een gesimuleerde **lockDoor** -methode wordt geactiveerd.

1. Maak een nieuwe lege map met de naam **simDevice**.  Maak in de map **simDevice** een package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt.  Accepteer alle standaardwaarden:

   ```console
   npm init
   ```

2. Voer bij de opdracht prompt in de map **simDevice** de volgende opdracht uit om het **Azure-IOT-Device-SDK-** pakket te installeren en **Azure-IOT-Device-mqtt** -pakket:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Maak met een tekst editor een nieuw **simDevice.js** -bestand in de map **simDevice** .

4. Voeg de volgende ' vereist '-instructies toe aan het begin van het **simDevice.js** -bestand:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client** exemplaar te maken. Vervang de `{yourDeviceConnectionString}` waarde van de tijdelijke aanduiding door het apparaat Connection String u eerder hebt gekopieerd.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Voeg de volgende functie toe om de methode **lockDoor** te verwerken.

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Voeg de volgende code toe om de handler voor de methode **lockDoor** te registreren.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Sla het **simDevice.js** bestand op en sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productie code moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële uitstel), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Taken plannen voor het aanroepen van een directe methode en het bijwerken van de eigenschappen van een apparaat met dubbele gegevens

In deze sectie maakt u een Node.js-console-app die een externe **lockDoor** op een apparaat initieert met behulp van een directe methode en de eigenschappen van het apparaat twee bijwerkt.

1. Maak een nieuwe lege map met de naam **scheduleJobService**.  Maak in de map **scheduleJobService** een package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt.  Accepteer alle standaardwaarden:

    ```console
    npm init
    ```

2. Voer bij de opdracht prompt in de map **scheduleJobService** de volgende opdracht uit om het **Azure-iothub** apparaat SDK-pakket en het **Azure-IOT-Device-mqtt** -pakket te installeren:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Maak met een tekst editor een nieuw **scheduleJobService.js** -bestand in de map **scheduleJobService** .

4. Voeg de volgende ' vereist '-instructies toe aan het begin van het **scheduleJobService.js** -bestand:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Voeg de volgende variabelen declaraties toe. Vervang de `{iothubconnectionstring}` waarde van de tijdelijke aanduiding door de waarde die u hebt gekopieerd in [de IOT hub ophalen Connection String](#get-the-iot-hub-connection-string). Als u een ander apparaat dan **myDeviceId** hebt geregistreerd, moet u dit wijzigen in de query voorwaarde.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe die wordt gebruikt om de uitvoering van de taak te bewaken:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Voeg de volgende code toe om de taak te plannen die de methode van het apparaat aanroept:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Voeg de volgende code toe om de taak te plannen om het apparaat bij te werken:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Sla het **scheduleJobService.js** bestand op en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdracht prompt in de map **simDevice** de volgende opdracht uit om te beginnen met Luis teren naar de methode voor direct opnieuw opstarten.

    ```console
    node simDevice.js
    ```

2. Voer bij de opdracht prompt in de map **scheduleJobService** de volgende opdracht uit om de taken te activeren om de deur te vergren delen en de dubbele

    ```console
    node scheduleJobService.js
    ```

3. U ziet de reactie van het apparaat op de directe methode en de taak status in de-console.

   Hieronder ziet u de reactie van het apparaat op de directe methode:

   ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Hieronder ziet u de service plannings taken voor de directe methode en dubbele update van het apparaat, en de taken die worden uitgevoerd voor voltooiing:

   ![De app voor een gesimuleerd apparaat uitvoeren](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een taak gebruikt voor het plannen van een directe methode op een apparaat en het bijwerken van de eigenschappen van het apparaat.

Zie [zelf studie: een firmware-update uitvoeren](tutorial-firmware-update.md)om aan de slag te gaan met IOT hub en patronen voor Apparaatbeheer, zoals extern via de Air firmware-update.

Zie aan de slag [met Azure IOT Edge](../iot-edge/quickstart-linux.md)om aan de slag te gaan met IOT hub.