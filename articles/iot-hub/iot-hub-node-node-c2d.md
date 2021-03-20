---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (node) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor Node.js. U wijzigt een gesimuleerde apparaat-app om Cloud-naar-apparaat-berichten te ontvangen en een back-end-app te wijzigen om de Cloud-naar-apparaat-berichten te verzenden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91446881"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Cloud-naar-apparaat-berichten verzenden met IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-node.md) Quick Start laat zien hoe u een IOT-hub kunt maken, een apparaat-id kunt inrichten en een gesimuleerde apparaat-app kunt coderen die apparaat-naar-Cloud-berichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelf studie is gebaseerd op het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-node.md). U ziet hoe u het volgende kunt doen:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.
* Cloud-naar-apparaat-berichten op een apparaat ontvangen.
* Van de back-end van uw oplossing, aanvraag bezorgings bevestiging (*feedback*) aanvragen voor berichten die worden verzonden naar een apparaat vanuit IOT hub.

Meer informatie over Cloud-naar-apparaat-berichten vindt u in de [hand leiding voor IOT hub ontwikkel aars](iot-hub-devguide-messaging.md).

Aan het einde van deze zelf studie voert u twee Node.js-console-apps uit:

* **SimulatedDevice**, een gewijzigde versie van de app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-node.md), die verbinding maakt met uw IOT-hub en Cloud-naar-apparaat-berichten ontvangt.

* **SendCloudToDeviceMessage**, waarmee een Cloud-naar-apparaat-bericht naar de gesimuleerde apparaat-app wordt verzonden via IOT hub, waarna de ontvangst bevestiging wordt ontvangen.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel platformen en talen (waaronder C, Java, python en Java script) via Azure IoT-apparaat-Sdk's. Zie het [Azure IOT-ontwikkelaars centrum](https://azure.microsoft.com/develop/iot)voor stapsgewijze instructies voor het verbinden van uw apparaat met de code van deze zelf studie en over het algemeen tot Azure IOT hub.
>

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0. x of hoger. [Uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) bevat informatie over het installeren van Node.js voor deze zelf studie over Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie wijzigt u de gesimuleerde apparaat-app die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-node.md) om Cloud-naar-apparaat-berichten van de IOT-hub te ontvangen.

1. Open het **SimulatedDevice.js** -bestand met behulp van een tekst editor. Dit bestand bevindt zich in de map **IOT-hub\Quickstarts\simulated-Device** van de hoofdmap van de Node.js voorbeeld code die u hebt gedownload in de Quick Start [van een apparaat naar een IOT hub](quickstart-send-telemetry-node.md) -Snelstartgids.

2. Registreer een handler met de apparaatclient om berichten te ontvangen die vanaf IoT Hub zijn verzonden. Voeg de aanroep toe aan `client.on` net na de regel die de apparaatclient maakt, zoals in het volgende code fragment:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

In dit voor beeld roept het apparaat de **volledige** functie aan om IOT hub te melden dat het bericht is verwerkt en dat het veilig kan worden verwijderd uit de wachtrij van het apparaat. De aanroep voor **volt ooien** is niet vereist als u MQTT Trans Port gebruikt en kan worden wegge laten. Dit is vereist voor AMQP en HTTPS.

Met AMQP en HTTPS, maar niet MQTT, kan het apparaat ook:

* Een bericht afbreken, wat leidt tot IoT Hub het bericht in de wachtrij van het apparaat behouden voor toekomstig gebruik.
* Een bericht afwijzen waarmee het bericht permanent wordt verwijderd uit de wachtrij van de apparaten.

Als er iets gebeurt dat ervoor zorgt dat het apparaat het bericht niet kan volt ooien, afbreken of afwijzen, wordt IoT Hub na een vaste time-outperiode het bericht in de wachtrij geplaatst voor bezorging. Daarom moet de logica voor bericht verwerking in de apparaat-app worden *idempotent*, zodat hetzelfde bericht meerdere keren wordt ontvangen.

Zie [Cloud-naar-apparaat-berichten verzenden vanuit een IOT-hub](iot-hub-devguide-messages-c2d.md)voor meer informatie over hoe IOT hub Cloud-naar-apparaat-berichten verwerkt, met inbegrip van de details van de Cloud-naar-apparaat-bericht levenscyclus.
  
> [!NOTE]
> Als u HTTPS gebruikt in plaats van MQTT of AMQP als Trans Port, controleert het **DeviceClient** -exemplaar voor berichten van IOT hub zelden (mini maal elke 25 minuten). Zie [communicatie richtlijnen voor Cloud-naar-apparaat](iot-hub-devguide-c2d-guidance.md) en [Kies een communicatie protocol](iot-hub-devguide-protocols.md)voor meer informatie over de verschillen tussen de MQTT-, AMQP-en HTTPS-ondersteuning.
>

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-node.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

In deze sectie maakt u een Node.js-console-app die Cloud-naar-apparaat-berichten naar de gesimuleerde apparaat-app verzendt. U hebt de apparaat-ID van het apparaat dat u hebt toegevoegd in de [telemetrie van een apparaat verzenden naar een IOT hub](quickstart-send-telemetry-node.md) Quick Start. U hebt ook de IoT hub-connection string nodig die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

1. Maak een lege map met de naam **sendcloudtodevicemessage**. Maak in de map **sendcloudtodevicemessage** een package.jsin het bestand met behulp van de volgende opdracht bij de opdracht prompt. Accepteer alle standaardwaarden:

    ```shell
    npm init
    ```

2. Voer bij de opdracht prompt in de map **sendcloudtodevicemessage** de volgende opdracht uit om het **Azure-iothub-** pakket te installeren:

    ```shell
    npm install azure-iothub --save
    ```

3. Maak met een tekst editor een **SendCloudToDeviceMessage.js** -bestand in de map **sendcloudtodevicemessage** .

4. Voeg de volgende- `require` instructies toe aan het begin van het **SendCloudToDeviceMessage.js** -bestand:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Voeg de volgende code toe aan **SendCloudToDeviceMessage.js** bestand. Vervang de tijdelijke aanduidingen ' {IOT hub connection string} ' en ' {apparaat id} ' door de connection string van de IoT-hub en de apparaat-ID die u eerder hebt genoteerd:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe om de resultaten van de bewerking af te drukken naar de-console:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Voeg de volgende functie toe om verzend feedback berichten af te drukken naar de-console:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Voeg de volgende code toe om een bericht te verzenden naar uw apparaat en het feedback bericht af te handelen wanneer het apparaat het Cloud-naar-apparaat-bericht bevestigt:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Sla het **SendCloudToDeviceMessage.js** bestand op en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdracht prompt in de map **gesimuleerde apparaten** de volgende opdracht uit om telemetrie te verzenden naar IOT hub en om te Luis teren naar Cloud-naar-apparaat-berichten:

    ```shell
    node SimulatedDevice.js
    ```

    ![De app voor een gesimuleerd apparaat uitvoeren](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Voer bij een opdracht prompt in de map **sendcloudtodevicemessage** de volgende opdracht uit om een Cloud-naar-apparaat-bericht te verzenden en te wachten op de bevestigings feedback:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Voer de app uit om de Cloud-naar-apparaat-opdracht te verzenden](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Voor het gemak implementeert deze zelf studie geen beleid voor opnieuw proberen. In productie code moet u beleid voor opnieuw proberen implementeren (zoals exponentiële uitstel), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Voor voor beelden van complete end-to-end-oplossingen die gebruikmaken van IoT Hub, raadpleegt u de [Azure IOT-oplossing voor externe controle](https://azure.microsoft.com/documentation/suites/iot-suite/).

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.
