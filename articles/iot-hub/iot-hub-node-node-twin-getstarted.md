---
title: Aan de slag met Azure IoT Hub apparaat apparaatdubbels (node) | Microsoft Docs
description: Azure IoT Hub Device apparaatdubbels gebruiken om labels toe te voegen en vervolgens een IoT Hub query te gebruiken. U gebruikt de Azure IoT-Sdk's voor Node.js voor het implementeren van de gesimuleerde apparaat-app en een service-app waarmee de tags worden toegevoegd en de IoT Hub-query wordt uitgevoerd.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 65ced3812072bd2650fc36bbb7a7b0f3f75e0def
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336782"
---
# <a name="get-started-with-device-twins-nodejs"></a>Aan de slag met apparaatdubbels (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelf studie hebt u twee Node.js console-apps:

* **AddTagsAndQuery.js**, een Node.js back-end-app, waarmee Tags en apparaatdubbels worden toegevoegd.

* **TwinSimulatedDevice.js**, een Node.js-app, die een apparaat simuleert dat verbinding maakt met uw IOT-hub met de apparaat-id die u eerder hebt gemaakt, en rapporteert de connectiviteits voorwaarde.

> [!NOTE]
> Het artikel [Azure IOT sdk's](iot-hub-devguide-sdks.md) bevat informatie over de Azure IOT-sdk's die u kunt gebruiken om zowel apparaat-als back-end-apps te bouwen.
>

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Node.js versie 10.0. x of hoger.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Node.js-console-app die de locatie van meta gegevens toevoegt aan het apparaat dat is gekoppeld aan **myDeviceId**. Vervolgens wordt een query uitgevoerd op het apparaat apparaatdubbels dat is opgeslagen in de IoT-hub en worden de apparaten die zich in de Verenigde Staten bevinden, geselecteerd en vervolgens de apparatuur die een mobiele verbinding rapporteert.

1. Maak een nieuwe lege map met de naam **addtagsandqueryapp**. Maak in de map **addtagsandqueryapp** een nieuw package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt. De `--yes` para meter accepteert alle standaard waarden.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdracht prompt in de map **addtagsandqueryapp** de volgende opdracht uit om het **Azure-iothub-** pakket te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met een tekst editor een nieuw **AddTagsAndQuery.js** -bestand in de map **addtagsandqueryapp** .

4. Voeg de volgende code toe aan het **AddTagsAndQuery.js** -bestand. Vervang door `{iot hub connection string}` de IOT hub-Connection String die u hebt gekopieerd in [de IOT Hub-Connection String ophalen](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    Het **register** object bevat alle methoden die nodig zijn om te communiceren met apparaatdubbels van de service. De vorige code initialiseert eerst het **register** object, haalt het apparaat op met de dubbele voor **myDeviceId**, waarna de labels worden bijgewerkt met de gewenste locatie-informatie.

    Na het bijwerken van de tags wordt de functie **queryTwins** aangeroepen.

5. Voeg de volgende code toe aan het einde van  **AddTagsAndQuery.js** voor het implementeren van de functie **queryTwins** :

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    In de vorige code worden twee query's uitgevoerd: de eerste selecteert alleen het apparaat apparaatdubbels van apparaten die zich in de **Redmond43** -installatie bevinden en de tweede verfijnt de query om alleen de apparaten te selecteren die ook zijn verbonden via een mobiel netwerk.

    Wanneer de code het **query** -object maakt, geeft het het maximum aantal geretourneerde documenten op in de tweede para meter. Het **query** -object bevat een **hasMoreResults** Boolean-eigenschap die u kunt gebruiken om de **nextAsTwin** -methoden meerdere keren aan te roepen om alle resultaten op te halen. Een methode met de naam **Next** is beschikbaar voor resultaten die geen apparaatdubbels zijn, bijvoorbeeld de resultaten van aggregatie query's.

6. Voer de toepassing uit met:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   U ziet één apparaat in de resultaten voor de query die vraagt naar alle apparaten in **Redmond43** en geen voor de query waarmee de resultaten worden beperkt tot apparaten die gebruikmaken van een mobiel netwerk.

   ![Het ene apparaat weer geven in de query resultaten](media/iot-hub-node-node-twin-getstarted/service1.png)

In de volgende sectie maakt u een apparaat-app die de connectiviteits gegevens rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een Node.js-console-app die als **myDeviceId** verbinding maakt met uw hub, en vervolgens de gerapporteerde eigenschappen van het apparaat bijwerkt met de informatie die is verbonden met een mobiel netwerk.

1. Maak een nieuwe lege map met de naam **reportconnectivity**. Maak in de map **reportconnectivity** een nieuw package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt. De `--yes` para meter accepteert alle standaard waarden.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdracht prompt in de map **reportconnectivity** de volgende opdracht uit om de **Azure-IOT-Device**-en **Azure-IOT-Device-mqtt** -pakketten te installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een tekst editor een nieuw **ReportConnectivity.js** -bestand in de map **reportconnectivity** .

4. Voeg de volgende code toe aan het **ReportConnectivity.js** -bestand. Vervang door `{device connection string}` het apparaat Connection String u hebt gekopieerd tijdens het maken van de **myDeviceId** -apparaat-id bij het [registreren van een nieuw apparaat in de IOT-hub](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Het **client** object bevat alle methoden die u nodig hebt om te communiceren met apparaatdubbels van het apparaat. De vorige code, na het initialiseren van het **client** object, haalt het apparaat op voor **myDeviceId** en werkt de gerapporteerde eigenschap bij met de verbindings gegevens.

5. De apparaat-app uitvoeren

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Als het goed is, ziet u nu het bericht `twin state reported`.

6. Nu het apparaat de verbindings gegevens heeft gerapporteerd, zou het in beide query's moeten worden weer gegeven. Ga terug naar de map **addtagsandqueryapp** en voer de query's opnieuw uit:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Deze tijd **myDeviceId** moet in beide query resultaten worden weer gegeven.

    ![MyDeviceId in beide query resultaten weer geven](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt meta gegevens van apparaten toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om connectiviteits gegevens van apparaten te rapporteren in het dubbele apparaat. U hebt ook geleerd hoe u deze gegevens kunt zoeken met behulp van de SQL-achtige IoT Hub query taal.

Gebruik de volgende bronnen voor meer informatie over:

* Verzend telemetrie van apparaten met de zelf studie [aan de slag met IOT hub](quickstart-send-telemetry-node.md)

* Configureer apparaten met behulp van de gewenste eigenschappen van het apparaat met de zelf studie [gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) ,

* apparaten interactief beheren (bijvoorbeeld door een ventilator in te scha kelen vanuit een door de gebruiker beheerde app), met de zelf studie [directe methoden gebruiken](quickstart-control-device-node.md) .
