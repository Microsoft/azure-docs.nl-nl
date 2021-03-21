---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613042"
---
In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing is geschreven in C en is opgenomen in de Azure IoT device SDK voor C. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Download de voorbeeldcode.
> * De voorbeeld code maken.
> * Voer de toepassing voor voorbeeld apparaten uit en controleer of deze verbinding maakt met uw IoT-hub.
> * Controleer de bron code.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

U kunt deze zelfstudie voltooien in Linux of Windows. De shell-opdrachten in deze zelfstudie volgen de Linux-conventie voor padscheidingstekens `/`. Als u de stappen uitvoert in Windows, moet u deze scheidingstekens vervangen door `\`.

De vereisten verschillen per besturingssysteem:

### <a name="linux"></a>Linux

In deze zelfstudie wordt ervan uitgegaan dat u Ubuntu Linux gebruikt. De stappen in deze zelfstudie zijn getest met Ubuntu 18.04.

Om deze zelfstudie in Linux te voltooien installeert u de volgende software in uw lokale Linux-omgeving:

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

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/): zorg ervoor dat u de workload **Desktop Development with C++** kiest tijdens het [installeren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) van Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>De code downloaden

Als u klaar bent met de [quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Linux of Windows wordt uitgevoerd met IoT Hub (C)](../articles/iot-pnp/quickstart-connect-device.md). U hebt de code al gedownload.

In deze zelfstudie bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C-SDK.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT C-SDK’s en -bibliotheken](https://github.com/Azure/azure-iot-sdk-c) te klonen op deze locatie:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U kunt de code bouwen en uitvoeren met behulp van Visual Studio of `cmake` op de opdrachtregel.

### <a name="use-visual-studio"></a>Visual Studio gebruiken

1. Open de hoofdmap van de gekloonde opslagplaats. Na enkele seconden maakt de **CMake**-ondersteuning in Visual Studio alles wat u nodig hebt om het project uit te voeren en fouten op te sporen.
1. Wanneer Visual Studio gereed is, gaat u in **Solution Explorer** naar het voorbeeld *iothub_client/samples/pnp/pnp_temperature_controller/* .
1. Klik met de rechtermuisknop op het bestand *pnp_temperature_controller.c* en selecteer **Foutopsporing van configuratie**. Selecteer **Standaard**.
1. Het bestand *launch.vs.json* wordt geopend in Visual Studio. Bewerk dit bestand, zoals wordt weergegeven in het volgende fragment, om de vereiste omgevingsvariabelen in te stellen. U hebt een notitie gemaakt van de bereik-ID en de primaire sleutel voor inschrijving na het[instellen van uw omgeving voor de quickstarts en zelfstudies voor IoT Plug en Play](../articles/iot-pnp/set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Klik met de rechtermuisknop op het bestand *pnp_temperature_controller.c* en selecteer **Instellen als item voor opstarten**.
1. Om de code-uitvoering in Visual Studio bij te houden voegt u een onderbrekingspunt toe aan de functie `main` in het bestand *pnp_temperature_controller.c*.
1. U kunt het voorbeeld nu uitvoeren en fouten opsporen vanuit het menu **Foutopsporing**.

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

### <a name="use-cmake-at-the-command-line"></a>Gebruik `cmake` in de opdrachtregel

Het voorbeeld bouwen:

1. Maak een submap _cmake_ in de hoofdmap van de gekloonde Device SDK en ga naar deze map:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de projectbestanden voor SDK en voorbeelden te genereren en te bouwen:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md) voor meer informatie over de voorbeeldconfiguratie.

Het voorbeeld uitvoeren:

1. Navigeer vanuit de map _cmake_ naar de map met het uitvoerbare bestand en voer het uit:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Code valideren met de Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Met dit voorbeeld wordt een model met [meerdere onderdelen](../articles/iot-pnp/concepts-modeling-guide.md) geïmplementeerd. Het [Digital Twins Definition Language-modelbestand (DTDL) voor het temperatuurregelingsapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

### <a name="iot-plug-and-play-helper-functions"></a>Helper-functies van IoT Plug en Play

Voor dit voorbeeld gebruikt de code enkele helper-functies uit de map */common*:

*pnp_device_client_ll*: bevat de verbindingsmethode voor IoT Plug en Play met `model-id` opgenomen als een parameter: `PnP_CreateDeviceClientLLHandle`.

*pnp_protocol*: bevat de helper-functies van IoT Plug en Play:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Deze helper-functies zijn algemeen genoeg om in uw eigen project te gebruiken. In dit voorbeeld worden ze in drie bestanden gebruikt die overeenkomen met elk onderdeel in het model:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

In het bestand *pnp_deviceinfo_component* gebruikt de functie `SendReportedPropertyForDeviceInformation` bijvoorbeeld twee helper-functies:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Elk onderdeel in het voorbeeld volgt dit patroon.

### <a name="code-flow"></a>Codestroom

Met de functie `main` wordt de verbinding geïnitialiseerd en de model-id verzonden:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

De code gebruikt `PnP_CreateDeviceClientLLHandle` om verbinding te maken met de IoT-hub. Stel `modelId` in als een optie, en stel de apparaatmethode en callback-handlers voor de apparaatdubbel in, voor directe methoden en updates voor de apparaatdubbel:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` bevat ook de verbindingsgegevens. De omgevingsvariabele **IOTHUB_DEVICE_SECURITY_TYPE** bepaalt of het voorbeeld via een verbindingsreeks of via de service voor apparaatinrichting verbinding maakt met de IoT-hub.

Wanneer het apparaat een model-id verzendt, wordt het een IoT Plug en Play-apparaat.

Nu de callback-handlers zijn ingesteld, reageert het apparaat op updates voor de apparaatdubbel en op aanroepen via de directe methode:

* Voor de apparaatdubbel-callback roept de `PnP_TempControlComponent_DeviceTwinCallback` de functie `PnP_ProcessTwinData` aan om de gegevens te verwerken. `PnP_ProcessTwinData` maakt gebruik van het *bezoeker-patroon* om de JSON te parseren en vervolgens elke eigenschap te bezoeken, waarbij `PnP_TempControlComponent_ApplicationPropertyCallback` voor elk element wordt aangeroepen.

* Voor de opdrachten-callback gebruikt de functie `PnP_TempControlComponent_DeviceMethodCallback` de helper-functie om de namen van opdrachten en onderdelen te parseren:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    De functie `PnP_TempControlComponent_DeviceMethodCallback` roept vervolgens de opdracht voor het onderdeel aan:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

De functie `main` initialiseert de eigenschappen met het kenmerk Alleen-lezen die naar de IoT-hub zijn verzonden:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

De functie `main` start een lus om gebeurtenis- en telemetriegegevens voor elk onderdeel bij te werken:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

De functie `PnP_ThermostatComponent_SendTelemetry` laat zien hoe u de struct `PNP_THERMOSTAT_COMPONENT` gebruikt. In het voorbeeld wordt deze struct gebruikt om informatie op te slaan over de twee thermostaten in de temperatuurregelaar. De code gebruikt de functie `PnP_CreateTelemetryMessageHandle` om het bericht voor te bereiden en te verzenden:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

De functie `main` vernietigt ten slotte de verschillende onderdelen en sluit de verbinding met de hub.
