---
title: Aan de slag Azure IoT Hub apparaattweeling (Node) | Microsoft Docs
description: Het gebruik van Azure IoT Hub om tags toe te voegen en vervolgens een query IoT Hub gebruiken. U gebruikt de Azure IoT SDK's voor Node.js voor het implementeren van de gesimuleerde apparaat-app en een service-app die de tags toevoegt en de IoT Hub uitvoert.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2047de2c1e61554ba96af8471905a22291861184
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484571"
---
# <a name="get-started-with-device-twins-nodejs"></a>Aan de slag met apparaattweeling (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelfstudie hebt u twee Node.js-console-apps:

* **AddTagsAndQuery.js**, een Node.js back-end-app, waarmee tags en query's worden toegevoegd voor apparaattweelingen.

* **TwinSimulatedDevice.js**, een Node.js-app, die een apparaat simuleert dat verbinding maakt met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt en de connectiviteitsvoorwaarde rapporteert.

> [!NOTE]
> Het artikel [Azure IoT SDK's](iot-hub-devguide-sdks.md) bevat informatie over de Azure IoT SDK's die u kunt gebruiken om zowel apparaat- als back-end-apps te bouwen.
>

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Node.js versie 10.0.x of hoger.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in dit artikel wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT-hub-connection string

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Node.js-console-app waarmee locatiemetagegevens worden toegevoegd aan de apparaat dubbel die is gekoppeld **aan myDeviceId.** Vervolgens worden de apparaattweelingen opgevraagd die zijn opgeslagen in de IoT-hub en worden de apparaten geselecteerd die zich in de VS bevinden en vervolgens de apparaten die een mobiele verbinding rapporteren.

1. Maak een nieuwe lege map met de **naam addtagsandqueryapp.** Maak in **de map addtagsandqueryapp** een nieuwe package.jsbestand met behulp van de volgende opdracht bij de opdrachtprompt. De `--yes` parameter accepteert alle standaardwaarden.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdrachtprompt in de map **addtagsandqueryapp** de volgende opdracht uit om het **pakket azure-iothub te** installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met een teksteditor een nieuw **AddTagsAndQuery.js** in de **map addtagsandqueryapp.**

4. Voeg de volgende code toe aan **hetAddTagsAndQuery.js** bestand. Vervang `{iot hub connection string}` door de IoT Hub connection string die u hebt gekopieerd in De [IoT-hub connection string.](#get-the-iot-hub-connection-string)

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

    Het **registerobject** geeft alle methoden weer die nodig zijn om te communiceren met apparaattweelingen van de service. Met de vorige code wordt eerst het **registerobject** initialiseert, vervolgens wordt de apparaattweeling voor **myDeviceId** opgehaald en worden de tags ten slotte bijgewerkt met de gewenste locatiegegevens.

    Nadat de tags zijn bijgewerkt, wordt de **functie queryTwins** aanroepen.

5. Voeg de volgende code toe aan het einde  **vanAddTagsAndQuery.js** om de **functie queryTwins te** implementeren:

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

    Met de vorige code worden twee query's uitgevoerd: de eerste code selecteert alleen de apparaattweeling van apparaten in de fabriek **Redmond43** en de tweede verfijnt de query om alleen de apparaten te selecteren die ook zijn verbonden via een mobiel netwerk.

    Wanneer de code het **queryobject maakt,** wordt het maximum aantal geretourneerde documenten in de tweede parameter opgegeven. Het **queryobject** bevat een booleaanse eigenschap **hasMoreResults** die u kunt gebruiken om de **nextAsTwin-methoden** meerdere keren aan te roepen om alle resultaten op te halen. De volgende methode is **beschikbaar** voor resultaten die geen apparaattweeling zijn, bijvoorbeeld de resultaten van aggregatiequery's.

6. Voer de toepassing uit met:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   U ziet één apparaat in de resultaten voor de query waarin wordt gevraagd naar alle apparaten in **Redmond43** en geen voor de query die de resultaten beperkt tot apparaten die gebruikmaken van een mobiel netwerk.

   ![Het ene apparaat bekijken in de queryresultaten](media/iot-hub-node-node-twin-getstarted/service1.png)

In de volgende sectie maakt u een apparaat-app die de verbindingsgegevens rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een Node.js-console-app die verbinding maakt met uw hub als **myDeviceId** en werkt u vervolgens de gerapporteerde eigenschappen van de apparaattweeling bij met de informatie die is verbonden via een mobiel netwerk.

1. Maak een nieuwe lege map met de **naam reportconnectivity**. Maak in **de map reportconnectivity** een nieuwe package.jsbestand met behulp van de volgende opdracht bij de opdrachtprompt. De `--yes` parameter accepteert alle standaardwaarden.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdrachtprompt in de map **reportconnectivity** de volgende opdracht uit om de **pakketten azure-iot-device** en **azure-iot-device-mqtt te** installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een teksteditor een nieuw **ReportConnectivity.js** in de **map reportconnectivity.**

4. Voeg de volgende code toe aan het **ReportConnectivity.js** bestand. Vervang door de apparaat-connection string u hebt gekopieerd toen u de `{device connection string}` **apparaat-id myDeviceId** maakte in Een nieuw apparaat registreren [in de IoT-hub](#register-a-new-device-in-the-iot-hub).

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

    Het **object Client** geeft alle methoden weer die u nodig hebt om te communiceren met apparaattweeling vanaf het apparaat. De vorige code, nadat het **clientobject** is initialiseert, haalt de apparaat dubbel op voor **myDeviceId** en werkt de gerapporteerde eigenschap bij met de verbindingsgegevens.

5. De apparaat-app uitvoeren

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Als het goed is, ziet u nu het bericht `twin state reported`.

6. Nu het apparaat de connectiviteitsgegevens heeft gerapporteerd, zou het in beide query's moeten worden weergegeven. Terug in de **map addtagsandqueryapp** en voer de query's opnieuw uit:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Deze keer **moet myDeviceId** in beide queryresultaten worden weergegeven.

    ![MyDeviceId in beide queryresultaten tonen](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt metagegevens van apparaten toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om verbindingsgegevens van het apparaat te rapporteren in de apparaat dubbel. U hebt ook geleerd hoe u query's kunt uitvoeren op deze informatie met behulp van de SQL-IoT Hub querytaal.

Gebruik de volgende bronnen voor meer informatie over:

* telemetrie verzenden vanaf apparaten met de zelfstudie [Aan de](quickstart-send-telemetry-node.md) slag IoT Hub apparaten,

* apparaten configureren met behulp van de gewenste eigenschappen van de apparaattwee met de zelfstudie [Gewenste eigenschappen gebruiken om apparaten te configureren,](tutorial-device-twins.md)

* apparaten interactief beheren (zoals het in-/uitschakelen van een ventilator vanuit een door de gebruiker beheerde app) met de [zelfstudie Directe methoden](quickstart-control-device-node.md) gebruiken.
