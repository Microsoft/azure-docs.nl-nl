---
title: 'Zelfstudie: Python-voorbeeldcode voor een IoT Plug and Play-apparaat met meerdere onderdelen koppelen aan Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: Python-voorbeeldcode maken en uitvoeren voor een IoT Plug and Play-apparaat dat meerdere onderdelen gebruikt en verbinding maakt met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.'
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0a4130688d479902f7836ae09c4006bb36fc16d2
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421393"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-python"></a>Zelfstudie: Een voorbeeld van een IoT Plug and Play-apparaattoepassing met verschillende onderdelen verbinden met IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in Python en is inbegrepen in de Azure IoT device-SDK voor Python. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Voor deze zelfstudie hebt u Python 3.7 nodig in uw ontwikkelomgeving. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/).

## <a name="download-the-code"></a>De code downloaden

Het pakket **azure-iot-device** wordt gepubliceerd als PIP.

Installeer het pakket in uw lokale Python-omgeving als volgt:

```cmd/sh
pip install azure-iot-device
```

Als u klaar bent met [Quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Windows wordt uitgevoerd met IoT Hub (Python)](quickstart-connect-device-python.md). U hebt de opslagplaats al gekloond.

Kloon de IoT-opslagplaats van de Python-SDK:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het temperatuurregelingsapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De map *azure-iot-sdk-python\azure-iot-device\samples\pnp* bevat de voorbeeldcode voor het IoT Plug en Play-apparaat. De bestanden voor het voorbeeld van een temperatuurregeling zijn:

- temp_controller_with_thermostats.py
- pnp_helper.py

Temperatuurregeling heeft meerdere onderdelen en een standaardcomponent, gebaseerd op het DTDL-model van de temperatuurregeling.

Open het bestand *temp_controller_with_thermostats.py* in een editor naar keuze. De code in dit bestand:

1. Importeert `pnp_helper.py` om toegang te krijgen tot de hulpmethoden.

1. Definieert twee DTMI's (digital twin model identifiers) die twee verschillende interfaces vertegenwoordigen, zoals gedefinieerd in het DTDL-model. De onderdelen van een echte temperatuurregeling moeten deze twee interfaces implementeren. Deze twee interfaces zijn al gepubliceerd in een centrale opslagplaats. Deze DTMI's moeten bekend zijn bij de gebruiker, en variëren in functie van het scenario van de apparaatimplementatie. Voor het huidige voorbeeld vertegenwoordigen deze twee interfaces:

    - Een thermostaat
    - Apparaatgegevens ontwikkeld door Azure.

1. Definieert de DTMI `model_id` voor het apparaat dat wordt geïmplementeerd. De DTMI is gebruikersgedefinieerd en moet overeenkomen met de DTMI in het DTDL-modelbestand.

1. Definieert de namen voor de onderdelen in het DTDL-bestand. De DTDL bevat twee thermostaten en één onderdeel met apparaatgegevens. Er wordt ook een constante met de naam `serial_number` gedefinieerd in de standaardcomponent. Een `serial_number` kan niet veranderen voor apparaat.

1. Definieert de implementaties van opdrachthandlers. Deze bepalen wat het apparaat doet wanneer het opdrachtaanvragen ontvangt.

1. Definieert functies om een opdrachtreactie te maken. Deze bepalen hoe het apparaat reageert op opdrachtaanvragen. U maakt functies voor opdrachtreacties als een opdracht een aangepaste reactie moet terugsturen naar de IoT-hub. Als er geen antwoordfunctie is opgegeven voor een opdracht, wordt een algemeen antwoord verzonden. In dit voorbeeld heeft alleen de **getMaxMinReport**-opdracht een aangepast antwoord.

1. Definieert een functie om telemetrie te verzenden vanaf dit apparaat. Zowel de thermostaten als de standaardcomponent verzenden telemetrie. Deze functie bevat een optionele parameter voor de onderdeelnaam om te kunnen bepalen welk onderdeel de telemetrie heeft verzonden.

1. Definieert een listener voor opdrachtverzoeken.

1. Definieert een listener voor gewenste updates van eigenschappen.

1. Heeft een `main`-functie die:

    - De apparaat-SDK gebruikt om een apparaatclient te maken en verbinding te maken met uw IoT-hub. Het apparaat verzendt de `model_id` zodat de IoT-hub het apparaat kan identificeren als een IoT Plug en Play-apparaat.

    - Maakt gebruik van de functie `create_reported_properties` in het hulpbestand om de eigenschappen te maken. Geeft de onderdeelnaam en de eigenschappen door aan deze functie als sleutel-/waardeparen.

    - Werkt de leesbare eigenschappen voor zijn onderdelen bij door `patch_twin_reported_properties` aan te roepen.

    - Begint te luisteren naar opdrachtaanvragen met de functie `execute_command_listener`. De functie stelt een listener in voor opdrachtverzoeken vanaf de service. Wanneer u de listener instelt, geeft u een `method_name`, `user_command_handler` en een optionele `create_user_response_handler` in als parameters.
        - De `method_name` definieert de opdrachtaanvraag. In dit voorbeeld definieert het model de opdrachten **reboot** en **getMaxMinReport**.
        - De functie `user_command_handler` definieert wat het apparaat moet doen wanneer het een opdracht ontvangt.
        - De functie `create_user_response_handler` maakt een antwoord aan dat naar uw IoT-hub verzonden kan worden wanneer een opdracht met succes wordt uitgevoerd. U kunt dit antwoord bekijken in de portal. Als deze functie niet wordt opgegeven, dan wordt een algemeen antwoord verzonden naar de service.

    - Gebruikt `execute_property_listener` om naar updates van eigenschappen te luisteren.

    - Begint telemetrie te verzenden met `send_telemetry`. De voorbeeldcode gebruikt een lus om drie functies voor het verzenden van telemetrie aan te roepen. Ze worden elk om de acht seconden aangeroepen

    - Schakelt alle listeners en taken uit, en sluit de lus af wanneer u op **Q** of **q** drukt.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) voor meer informatie over de voorbeeldconfiguratie.

Gebruik de volgende opdracht om het voorbeeld uit te voeren:

```cmd/sh
python temp_controller_with_thermostats.py
```

Het voorbeeldapparaat verzendt om de paar seconden telemetrieberichten naar uw IoT-hub.

U ziet de volgende uitvoer, wat aangeeft dat het apparaat telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

![Bevestigingsberichten apparaat](media/tutorial-multiple-components-python/multiple-component.png)

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat met onderdelen kunt verbinden met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug and Play-modellen](concepts-developer-guide-device-csharp.md)
