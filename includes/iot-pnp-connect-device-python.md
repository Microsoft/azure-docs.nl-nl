---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95511631"
---
In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven voor Python en is inbegrepen in de Azure IoT Hub Device SDK for Python. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart wilt uitvoeren, moet Python 3.7 op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

Installeer het pakket in uw lokale Python-omgeving als volgt:

```cmd/sh
pip install azure-iot-device
```

Kloon de IoT-opslagplaats van de Python-SDK en bekijk **master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

De map *azure-iot-sdk-python\azure-iot-device\samples\pnp* bevat de voorbeeldcode voor het IoT Plug en Play-apparaat. In deze quickstart wordt het bestand *simple_thermostat.py* gebruikt. Met deze voorbeeldcode wordt een apparaat geïmplementeerd dat compatibel is met IoT Plug. In de voorbeeldcode wordt gebruikgemaakt van de clientbibliotheek van het Azure IoT Python-apparaat.

Open het bestand **simple_thermostat.py** in een teksteditor. U ziet het volgende:

1. Het bestand definieert één dubbele model-id (DTMI) van een apparaat die een unieke aanduiding vormt voor de [thermostaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Er moet een DTMI bekend zijn bij de gebruiker, afhankelijk van het scenario voor de implementatie van het apparaat. In het huidige voorbeeld representeert het model een thermostaat met telemetrie, eigenschappen en opdrachten die zijn gekoppeld aan het bewaken van de temperatuur.

1. Bevat functies voor het definiëren van implementaties van opdrachthandlers. U schrijft deze handlers om te definiëren hoe het apparaat reageert op opdrachtaanvragen.

1. Heeft een functie om een opdrachtrespons te definiëren. U kunt een opdrachtrespons maken om een antwoord terug te sturen naar uw IoT-hub.

1. Hiermee wordt een functie voor het invoeren van toetsenbordaanslagen gedefinieerd, waarmee u de toepassing kunt afsluiten.

1. Heeft een **hoofdfunctie**. De **hoofdfunctie**:

    1. De apparaat-SDK gebruikt om een apparaatclient te maken en verbinding te maken met uw IoT-hub.

    1. Werkt de eigenschappen bij. Het model dat we gebruiken, **Thermostaat**, definieert `targetTemperature` en `maxTempSinceLastReboot` als de twee eigenschappen voor onze Thermostaat en zullen dus worden gebruikt. Eigenschappen worden bijgewerkt met behulp van de methode `patch_twin_reported_properties` die wordt gedefinieerd op `device_client`.

    1. Begint te luisteren naar opdrachtaanvrage. Hiervoor wordt gebruikgemaakt van de functie **execute_command_listener**. De functie stelt een listener in om naar opdrachtverzoeken te luisteren die afkomstig zijn van de service. Wanneer u de listener instelt, geeft u een `method_name`, `user_command_handler` en `create_user_response_handler` op.
        - De functie `user_command_handler` definieert wat het apparaat moet doen wanneer het een opdracht ontvangt. Als uw wekker bijvoorbeeld afgaat, is het effect van deze opdracht dat u wakker wordt. Dit kunt u zien als het effect van de opdracht die wordt aangeroepen.
        - De functie `create_user_response_handler` maakt een antwoord aan dat naar uw IoT-hub verzonden kan worden wanneer een opdracht met succes wordt uitgevoerd. Als uw wekker bijvoorbeeld afgaat, reageert u door op Snooze te drukken, wat feedback is voor de service. U kunt dit zien als het antwoord dat u de service geeft. U kunt dit antwoord bekijken in de portal.

    1. Begint telemetrie te verzenden. De **pnp_send_telemetry** is gedefinieerd in het bestand pnp_methods.py. De voorbeeldcode gebruikt een lus om deze functie elke acht seconden aan te roepen.

    1. Schakelt alle listeners en taken uit, en sluit de lus af wanneer u op **Q** of **q** drukt.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) voor meer informatie over de voorbeeldconfiguratie.

Nu u de code hebt gezien, gebruikt u de volgende opdracht om het voorbeeld uit te voeren:

```cmd/sh
python simple_thermostat.py
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
