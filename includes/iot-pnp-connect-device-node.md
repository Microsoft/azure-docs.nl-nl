---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f6a5c2732663a8b3a9149554c173ea3a019400e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612566"
---
In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven in Node.js en is opgenomen in de Azure IoT Device-SDK voor Node.js. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>De code downloaden

In deze quickstart bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C#-SDK voor Node.js.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen op deze locatie:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de opgenomen voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van de gekloonde opslagplaats en ga naar de map */azure-iot-sdk-node/device/samples/pnp*. Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de omgevingswaarde met de verbindingsreeks voor het apparaat die u eerder hebt genoteerd:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

Met dit voorbeeld wordt een eenvoudig IoT Plug and Play-thermostaatapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt geen gebruik van IoT Plug and Play-[onderdelen](../articles/iot-pnp/concepts-modeling-guide.md). Het [DTDL-modelbestand voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

Open het bestand _simple_thermostat.js_. In dit bestand ziet u hoe u het volgende kunt doen:

1. De vereiste interfaces importeren.
1. Een handler voor updates en een handler voor opdrachten schrijven.
1. De gewenste patches voor eigenschappen verwerken en telemetrie verzenden.
1. Uw apparaat desgewenst inrichten met behulp van Azure Device Provisioning Service (DPS).

In de main-functie kunt u zien hoe alles bij elkaar komt:

1. Maak het apparaat met behulp van de tekenreeks voor verbinding of richt het apparaat in met behulp van DPS.
1. Gebruik de optie **modelID** om het IoT Plug en Play-apparaatmodel op te geven.
1. Schakel de opdracht-handler in.
1. Verzend telemetrie van het apparaat naar uw hub.
1. Haal de apparaatdubbel op en werk de gerapporteerde eigenschappen bij.
1. Schakel de gewenste handler voor het bijwerken van eigenschappen in.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md) voor meer informatie over de voorbeeldconfiguratie.

Voer de voorbeeldtoepassing uit om een IoT Plug en Play-apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik de volgende opdracht om de voorbeeldtoepassing uit te voeren:

```cmd\sh
node simple_thermostat.js
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat nu telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

![Bevestigingsberichten apparaat](media/iot-pnp-connect-device-node/device-confirmation-node.png)

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
