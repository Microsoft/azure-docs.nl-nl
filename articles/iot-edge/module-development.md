---
title: Modules ontwikkelen voor Azure IoT Edge | Microsoft Docs
description: Ontwikkel aangepaste modules voor Azure IoT Edge die kunnen communiceren met de runtime en IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a30b4b056d56e096f80b9494ab80a585fff76e66
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489918"
---
# <a name="develop-your-own-iot-edge-modules"></a>Uw eigen IoT Edge-modules ontwikkelen

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge modules kunnen verbinding maken met andere Azure-Services en bijdragen aan uw grotere gegevens pijplijn voor de Cloud. In dit artikel wordt beschreven hoe u modules kunt ontwikkelen om te communiceren met de IoT Edge runtime en IoT Hub, en dus ook de rest van de Azure-Cloud.

## <a name="iot-edge-runtime-environment"></a>Runtime-omgeving IoT Edge

De IoT Edge runtime biedt de infra structuur voor het integreren van de functionaliteit van meerdere IoT Edge modules en voor het implementeren ervan op IoT Edge apparaten. Elk programma kan worden verpakt als een IoT Edge module. Een programma dat in een module wordt uitgevoerd, kan de Azure IoT Device SDK gebruiken om verbinding te maken met de lokale IoT Edge hub om optimaal te profiteren van IoT Edge communicatie-en beheer functionaliteit.
::: moniker range=">=iotedge-2020-11"
Modules kunnen ook elke MQTT-client gebruiken om verbinding te maken met de lokale IoT Edge hub MQTT Broker.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Uw programma inpakken als een IoT Edge module

Als u uw programma wilt implementeren op een IoT Edge apparaat, moet het eerst worden containerd en worden uitgevoerd met een docker-compatibele engine. IoT Edge gebruikt [Moby](https://github.com/moby/moby), het open-source project achter docker, als de docker-compatibele engine. De para meters die u gebruikt voor docker, kunnen worden door gegeven aan uw IoT Edge-modules. Zie voor meer informatie [container maken opties voor IOT Edge modules configureren](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>De IoT Edge hub gebruiken

De IoT Edge hub biedt twee belang rijke functies: proxy naar IoT Hub en lokale communicatie.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Verbinding maken met IoT Edge hub vanuit een module

Als u verbinding wilt maken met de lokale IoT Edge hub vanuit een module, moet u dezelfde verbindings stappen uitvoeren als voor clients. Zie [verbinding maken met de IOT Edge hub](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)voor meer informatie.

Als u IoT Edge route ring wilt gebruiken via AMQP of MQTT, kunt u de ModuleClient van de Azure IoT SDK gebruiken. Maak een ModuleClient-exemplaar om uw module te koppelen aan de IoT Edge hub die op het apparaat wordt uitgevoerd, vergelijkbaar met de manier waarop DeviceClient-instanties IoT-apparaten verbinden met IoT Hub. Voor meer informatie over de ModuleClient-klasse en de communicatie methoden raadpleegt u de API-verwijzing voor uw voorkeurs taal van de SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)of [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Als u IoT Edge MQTT Broker wilt gebruiken, moet u uw eigen MQTT-client meenemen en de verbinding zelf initiëren met gegevens die u ophaalt uit de API van de IoT Edge daemon-workload. <!--Need to add details here-->

Zie [lokale communicatie](iot-edge-runtime.md#local-communication)voor meer informatie over het kiezen tussen route ring of publiceren/abonneren op de MQTT Broker.

### <a name="mqtt-broker-primitives"></a>MQTT Broker-primitieven

#### <a name="send-a-message-on-a-user-defined-topic"></a>Een bericht verzenden naar een door de gebruiker gedefinieerd onderwerp

Met de IoT Edge MQTT Broker kunt u berichten publiceren op alle door de gebruiker gedefinieerde onderwerpen. Als u dit wilt doen, moet u uw module machtigen om op specifieke onderwerpen te publiceren en vervolgens een Token ophalen uit de workload API om als wacht woord te gebruiken wanneer u verbinding maakt met de MQTT Broker en vervolgens berichten publiceert op de geautoriseerde onderwerpen met de MQTT-client van uw keuze.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Berichten ontvangen in een door de gebruiker gedefinieerd onderwerp

Met de IoT Edge MQTT Broker is het ontvangen van berichten vergelijkbaar. Zorg er eerst voor dat uw module is gemachtigd om u te abonneren op specifieke onderwerpen en vervolgens een token op te halen uit de workload API om te gebruiken als wacht woord bij het maken van verbinding met de MQTT-Broker, en vervolgens te abonneren op berichten over de geautoriseerde onderwerpen met de MQTT-client van uw keuze.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub primitieven

IoT Hub ziet een module-exemplaar vergelijkbaar met een apparaat, in de zin dat:

* Er kunnen [apparaat-naar-Cloud-berichten](../iot-hub/iot-hub-devguide-messaging.md)worden verzonden.
* het kan [directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) ontvangen die specifiek zijn gericht op de identiteit.
* het heeft een module die twee ledig is en die is geïsoleerd van het [apparaat](../iot-hub/iot-hub-devguide-device-twins.md) en de andere module apparaatdubbels van dat apparaat.

Momenteel kunnen modules geen Cloud-naar-apparaat-berichten ontvangen of de functie voor het uploaden van bestanden gebruiken.

Wanneer u een module schrijft, kunt u verbinding maken met de IoT Edge hub en IoT Hub primitieven gebruiken zoals u dat zou doen wanneer u IoT Hub gebruikt met een apparaat-app. Het enige verschil tussen IoT Edge modules en IoT Device-toepassingen is dat u moet verwijzen naar de module-identiteit in plaats van de apparaat-id.

#### <a name="device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten

Een IoT Edge module kan berichten verzenden naar de Cloud via de IoT Edge hub die fungeert als een lokale Broker en berichten doorstuurt naar de Cloud. Als u complexe verwerking van apparaat-naar-Cloud-berichten wilt inschakelen, kan een IoT Edge-module ook berichten die door andere modules of apparaten zijn verzonden, onderscheppen en verwerken naar de lokale IoT Edge hub en nieuwe berichten verzenden met verwerkte gegevens. Er kunnen dus ketens van IoT Edge modules worden gemaakt om lokale verwerkings pijplijnen te bouwen.

Gebruik de ModuleClient van de Azure IoT SDK om apparaat-naar-Cloud-telemetrie-berichten te verzenden met behulp van route ring. Met de Azure IoT SDK beschikt elke module over het concept van module- *invoer* -en *uitvoer* eindpunten, die zijn toegewezen aan speciale MQTT-onderwerpen. Gebruik de- `ModuleClient.sendMessageAsync` methode en er worden berichten verzonden naar het uitvoer eindpunt van uw module. Configureer vervolgens een route in edgeHub om dit uitvoer eindpunt naar IoT Hub te verzenden.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Het verzenden van apparaat-naar-Cloud-telemetrie-berichten met de MQTT Broker is vergelijkbaar met het publiceren van berichten op door de gebruiker gedefinieerde onderwerpen, maar met behulp van het volgende IoT Hub speciale onderwerp voor uw module: `devices/<device_name>/<module_name>/messages/events` . Autorisaties moeten op de juiste wijze worden ingesteld. De MQTT-brug moet ook worden geconfigureerd om de berichten in dit onderwerp door te sturen naar de Cloud.

::: moniker-end

Als u berichten wilt verwerken met behulp van route ring, moet u eerst een route instellen om berichten te verzenden die afkomstig zijn van een ander eind punt (module of apparaat) naar het invoer eindpunt van uw module. vervolgens luistert u naar berichten op het invoer eindpunt van uw module. Telkens wanneer een nieuw bericht terugkomt, wordt een call back-functie geactiveerd door de Azure IoT SDK. Verwerk uw bericht met deze Terugbel functie en verzend eventueel nieuwe berichten in uw module-eindpunt wachtrij.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Het verwerken van berichten met behulp van de MQTT-Broker is vergelijkbaar met het abonneren op berichten op door de gebruiker gedefinieerde onderwerpen, maar met behulp van de IoT Edge speciale onderwerpen van de uitvoer wachtrij van uw module: `devices/<device_name>/<module_name>/messages/events` . Autorisaties moeten op de juiste wijze worden ingesteld. U kunt desgewenst nieuwe berichten verzenden naar de onderwerpen van uw keuze.

::: moniker-end

#### <a name="twins"></a>Apparaatdubbels

Apparaatdubbels zijn een van de primitieven die door IoT Hub worden verschaft. Er zijn JSON-documenten waarin status informatie wordt opgeslagen, inclusief meta gegevens, configuraties en voor waarden. Elke module of elk apparaat heeft zijn eigen twee.

Als u een module wilt ophalen met de Azure IoT SDK, roept u de- `ModuleClient.getTwin` methode aan.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Om een module te verkrijgen met een wille keurige MQTT-client, is er een beetje meer werk nodig, omdat het ophalen van een twee geen typisch MQTT-patroon is. De module moet eerst worden geabonneerd op IoT Hub speciaal onderwerp `$iothub/twin/res/#` . De naam van dit onderwerp wordt overgenomen van IoT Hub en alle apparaten/modules moeten worden geabonneerd op hetzelfde onderwerp. Dit betekent niet dat apparaten het dubbele van elkaar ontvangen. IoT Hub en edgeHub weet welke dubbele moet worden geleverd, zelfs als alle apparaten naar dezelfde onderwerps naam Luis teren. Zodra het abonnement is gemaakt, moet de module naar de dubbele vraag een bericht publiceren naar het volgende IoT Hub speciale onderwerp met een aanvraag-ID `$iothub/twin/GET/?$rid=1234` . Deze aanvraag-ID is een wille keurige ID (dat wil zeggen, een GUID) die wordt teruggestuurd door IoT Hub samen met de aangevraagde gegevens. Zo kan een client zijn of haar aanvragen koppelen aan de antwoorden. De resultaat code is een HTTP-achtige status code, waarbij geslaagd is gecodeerd als 200.

::: moniker-end

Als u een module dubbele patch wilt ontvangen voor de Azure IoT SDK, implementeert u een call back-functie en registreert u deze met de `ModuleClient.moduleTwinCallback` methode van de Azure IOT SDK, zodat uw call back-functie telkens wordt geactiveerd wanneer er een dubbele patch beschikbaar is.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Om een module dubbele patch te ontvangen met een MQTT-client, is het proces vergelijkbaar met het ontvangen van volledige apparaatdubbels: een client moet zich abonneren op een speciaal IoT Hub onderwerp `$iothub/twin/PATCH/properties/desired/#` . Nadat het abonnement is gemaakt, ontvangt de client wanneer IoT Hub een wijziging van de gewenste sectie van de dubbele waarde verzendt.

::: moniker-end

#### <a name="receive-direct-methods"></a>Directe methoden ontvangen

Als u een directe methode wilt ontvangen met de Azure IoT SDK, implementeert u een call back-functie en registreert u deze bij de- `ModuleClient.methodCallback` methode van de Azure IOT SDK, zodat uw call back-functie elke keer wordt geactiveerd wanneer er een directe methode is.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Het proces is vergelijkbaar met het ontvangen van dubbele patches om een directe methode met een MQTT-client te ontvangen. De client moet bevestigen dat deze de aanroep heeft ontvangen en kan de gegevens op hetzelfde moment opnieuw verzenden. Speciaal IoT Hub onderwerp waarop u zich kunt abonneren `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Ondersteuning voor taal en architectuur

IoT Edge ondersteunt meerdere besturings systemen, architecturen en ontwikkelings talen, zodat u het scenario kunt bouwen dat aan uw behoeften voldoet. Gebruik deze sectie om inzicht te krijgen in uw opties voor het ontwikkelen van aangepaste IoT Edge-modules. U vindt meer informatie over hulp middelen en vereisten voor elke taal in [het voorbereiden van uw ontwikkel-en test omgeving voor IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

IoT Edge ondersteunt de ontwikkeling voor AMD64-en ARM32 Linux-apparaten voor alle talen in de volgende tabel.

| Ontwikkelingstaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Ondersteuning voor het ontwikkelen en opsporen van fouten voor ARM64 Linux-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen in Visual Studio Code en er fouten in opsporen (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) voor meer informatie.

### <a name="windows"></a>Windows

IoT Edge ondersteunt de ontwikkeling van AMD64 Windows-apparaten voor alle talen in de volgende tabel.

| Ontwikkelingstaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio code (geen mogelijkheden voor fout opsporing)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Volgende stappen

[Bereid uw ontwikkel-en test omgeving voor op IoT Edge](development-environment.md)

[Visual Studio gebruiken voor het ontwikkelen van C#-modules voor IoT Edge](how-to-visual-studio-develop-module.md)

[Visual Studio code gebruiken voor het ontwikkelen van modules voor IoT Edge](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK's leren kennen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md)
