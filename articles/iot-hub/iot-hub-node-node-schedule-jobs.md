---
title: Taken plannen met Azure IoT Hub (Node) | Microsoft Docs
description: Het plannen van een Azure IoT Hub om een directe methode op meerdere apparaten aan te roepen. U gebruikt de Azure IoT SDK's voor Node.js om de gesimuleerde apparaat-apps en een service-app te implementeren om de taak uit te voeren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc0ea9817b9cbc27816354c48abbe26d79a83f04
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477890"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Taken plannen en uitzenden (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub is een volledig beheerde service waarmee een back-end-app taken kan maken en bijhouden waarmee miljoenen apparaten worden gepland en bijgewerkt.  Taken kunnen worden gebruikt voor de volgende acties:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Conceptueel gezien verpakt een taak een van deze acties en volgt deze de voortgang van de uitvoering op een set apparaten, die wordt gedefinieerd door een apparaattweelingsquery.  Een back-end-app kan bijvoorbeeld een taak gebruiken om een methode voor opnieuw opstarten aan te roepen op 10.000 apparaten, opgegeven door een query van een apparaattweeling en gepland op een toekomstig tijdstip. Deze toepassing kan vervolgens de voortgang bijhouden wanneer elk van deze apparaten de methode voor opnieuw opstarten ontvangt en uitvoert.

Meer informatie over elk van deze mogelijkheden in deze artikelen:

* Apparaat dubbel en eigenschappen: [Aan de slag met apparaattweeling](iot-hub-node-node-twin-getstarted.md) en [Zelfstudie: Dubbeleigenschappen apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IoT Hub ontwikkelaarshandleiding - directe methoden en](iot-hub-devguide-direct-methods.md) [Zelfstudie: directe methoden](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze zelfstudie ontdekt u hoe u:

* Maak een Node.js gesimuleerde apparaat-app met een directe methode, waarmee **lockDoor** wordt mogelijk, dat kan worden aangeroepen door de back-end van de oplossing.

* Maak een Node.js-console-app die de directe **lockDoor-methode** in de gesimuleerde apparaat-app aanroept met behulp van een taak en de gewenste eigenschappen bij werkt met behulp van een apparaat-taak.

Aan het einde van deze zelfstudie hebt u twee Node.js apps:

* **simDevice.js,** die verbinding maakt met uw IoT-hub met de apparaat-id en een directe **lockDoor-methode** ontvangt.

* **scheduleJobService.js,** waarmee een directe methode wordt aanroepen in de gesimuleerde apparaat-app en de gewenste eigenschappen van de apparaattweeling worden bijgewerkt met behulp van een taak.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [In Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) wordt beschreven hoe u Node.js installeert voor deze zelfstudie in Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in dit artikel wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Node.js-console-app die reageert op een directe methode met de naam door de cloud, die een gesimuleerde **lockDoor-methode activeert.**

1. Maak een nieuwe lege map met de **naam simDevice**.  Maak in **de map simDevice** een package.jsbestand met behulp van de volgende opdracht bij de opdrachtprompt.  Accepteer alle standaardwaarden:

   ```console
   npm init
   ```

2. Voer bij de opdrachtprompt in de **map simDevice** de volgende opdracht uit om het **azure-iot-device** Device SDK-pakket en **het azure-iot-device-mqtt-pakket te** installeren:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Maak met behulp van een **teksteditor** eensimDevice.jsbestand in de **map simDevice.**

4. Voeg de volgende 'require'-instructies toe aan het begin van **simDevice.js** bestand:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client** exemplaar te maken. Vervang de `{yourDeviceConnectionString}` waarde van de tijdelijke aanduiding door de connection string u eerder hebt gekopieerd.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Voeg de volgende functie toe om de **lockDoor-methode te** verwerken.

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

7. Voeg de volgende code toe om de handler voor de **methode lockDoor te** registreren.

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

8. Sla hetsimDevice.js **op** en sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleidsregels voor opnieuw proberen implementeren (zoals exponentieel uitvalt), zoals wordt voorgesteld in het artikel Transient Fault Handling ( [Afhandeling van tijdelijke fouten).](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>De IoT Hub-connection string

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Taken plannen voor het aanroepen van een directe methode en het bijwerken van de eigenschappen van een apparaat dubbel

In deze sectie maakt u een Node.js-console-app die via een directe methode een extern **lockDoor** op een apparaat initieert en de eigenschappen van de apparaattweeling bij werkt.

1. Maak een nieuwe lege map met de **naam scheduleJobService.**  Maak in **de map scheduleJobService** een package.jsbestand met behulp van de volgende opdracht bij de opdrachtprompt.  Accepteer alle standaardwaarden:

    ```console
    npm init
    ```

2. Voer bij de opdrachtprompt in de **map scheduleJobService** de volgende opdracht uit om het **azure-iothub** Device SDK-pakket en **het pakket azure-iot-device-mqtt te** installeren:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Maak met behulp van een **teksteditor** eenscheduleJobService.jsbestand in de **map scheduleJobService.**

4. Voeg de volgende 'require'-instructies toe aan het begin van **scheduleJobService.js** bestand:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Voeg de volgende variabeledeclaraties toe. Vervang de `{iothubconnectionstring}` tijdelijke aanduidingswaarde door de waarde die u hebt gekopieerd in [De IoT-hub connection string.](#get-the-iot-hub-connection-string) Als u een ander apparaat dan **myDeviceId hebt** geregistreerd, moet u dit wijzigen in de queryvoorwaarde.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe die wordt gebruikt om de uitvoering van de taak te controleren:

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

7. Voeg de volgende code toe om de taak te plannen die de apparaatmethode aanroept:
  
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

8. Voeg de volgende code toe om de taak voor het bijwerken van de apparaattweeling te plannen:

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

9. Sla hetscheduleJobService.js **op** en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdrachtprompt in **de map simDevice** de volgende opdracht uit om te luisteren naar de directe methode voor opnieuw opstarten.

    ```console
    node simDevice.js
    ```

2. Voer bij de opdrachtprompt in de **map scheduleJobService** de volgende opdracht uit om de taken te activeren om de deur te vergrendelen en de tweeling bij te werken

    ```console
    node scheduleJobService.js
    ```

3. U ziet de reactie van het apparaat op de directe methode en de taakstatus in de console.

   Hieronder ziet u de reactie van het apparaat op de directe methode:

   ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Hieronder ziet u de serviceplanningstaken voor de update van de directe methode en de apparaatt dubbel, en de taken die worden uitgevoerd tot voltooiing:

   ![De app voor een gesimuleerd apparaat uitvoeren](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een taak gebruikt om een directe methode voor een apparaat en de update van de eigenschappen van de apparaat dubbel te plannen.

Zie Zelfstudie: Een [firmware-update](tutorial-firmware-update.md)IoT Hub om aan de slag te gaan met patronen voor IoT Hub en apparaatbeheer, zoals extern via de firmware-update.

Zie Aan de slag met IoT Hub om door te gaan met [Azure IoT Edge.](../iot-edge/quickstart-linux.md)
