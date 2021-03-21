---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611926"
---
In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing is geschreven in C en is opgenomen in de Azure IoT device SDK voor C. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

U kunt deze quickstart uitvoeren in Linux of Windows. De shell-opdrachten in deze quickstart volgen de Linux-conventie voor padscheidingstekens `/`. Als u de stappen uitvoert in Windows, moet u deze scheidingstekens vervangen door `\`.

De vereisten verschillen per besturingssysteem:

### <a name="linux"></a>Linux

In deze quickstart wordt ervan uitgegaan dat u Ubuntu Linux gebruikt. De stappen in deze quickstart zijn getest met Ubuntu 18.04.

Om deze quickstart in Linux te voltooien, installeert u de volgende software in uw lokale Linux-omgeving:

Installeer **GCC**, **Git**, **cmake** en alle vereiste afhankelijkheden met behulp van de opdracht `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Controleer of de versie van `cmake` hoger is dan **2.8.12** en of de versie van **GCC** hoger dan **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Om deze quickstart in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/): zorg ervoor dat u de workload **Desktop Development with C++** kiest tijdens het [installeren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) van Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>De code downloaden

In deze quickstart bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C-SDK.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT C-SDK’s en -bibliotheken](https://github.com/Azure/azure-iot-sdk-c) te klonen op deze locatie:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de opgenomen voorbeeldcode te maken:

1. Maak een submap _cmake_ in de hoofdmap van de device-SDK en navigeer naar die map:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK en voorbeelden te genereren:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> In Windows gebruikt u Visual Studio 2019 om de oplossing te openen die is gegenereerd met de opdracht `cmake`. Open het projectbestand *azure_iot_sdks.sln* in de map _cmake_ en stel het project **pnp_simple_thermostat** in als het opstartproject in de oplossing. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

## <a name="run-the-device-sample"></a>Het apparaatvoorbeeld uitvoeren

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md) voor meer informatie over de voorbeeldconfiguratie.

U kunt als volgt de voorbeeldtoepassing in de SDK uitvoeren waarmee een IoT Plug en Play-apparaat wordt gesimuleerd en telemetrie van het apparaat wordt verzonden naar uw IoT-hub:

Navigeer vanuit de map _cmake_ naar de map met het uitvoerbare bestand en voer het uit:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Om de code-uitvoering in Visual Studio in Windows bij te houden, voegt u een onderbrekingspunt toe aan de functie `main` in het bestand _pnp_simple_thermostat.c_.

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een eenvoudig IoT Plug and Play-thermostaatapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt geen gebruik van IoT Plug and Play-[onderdelen](../articles/iot-pnp/concepts-modeling-guide.md). Het [DTDL-modelbestand voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt gebruik van de standaardfunctie om verbinding te maken met uw IoT-hub:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

De code die eigenschappen bijwerkt, opdrachten verwerkt en telemetrie verzendt, is identiek aan de code voor een apparaat die de IoT Plug and Play-conventies niet gebruikt.

De code maakt gebruik van de Parson-bibliotheek om JSON-objecten in de payloads te parseren die worden verzonden vanuit uw IoT-hub:

```c
// JSON parser
#include "parson.h"
```
