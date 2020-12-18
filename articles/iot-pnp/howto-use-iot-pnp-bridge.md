---
title: Verbinding maken met een IoT Plug en Play Bridge-voor beeld dat wordt uitgevoerd in Linux of Windows naar een IoT-hub | Microsoft Docs
description: Maak een IoT-Plug en Play-brug op Linux of Windows en voer deze uit om verbinding te maken met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bf730dbc28d15c3d036e9ebeedbe035db087c5d8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673020"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Verbinding maken met een IoT Plug en Play Bridge-voor beeld dat wordt uitgevoerd in Linux of Windows naar IoT Hub

In dit artikel wordt beschreven hoe u de voor beeld-omgeving van de IoT Plug en Play-brug bouwt, verbinding maakt met uw IoT-hub en het Azure IoT Explorer-hulp programma gebruikt om de telemetrie-verzen ding weer te geven. De IoT Plug en Play-brug wordt geschreven in C en bevat de Azure IoT Device SDK voor C. Aan het einde van deze zelf studie moet u de IoT Plug en Play-brug kunnen uitvoeren en de telemetrie van het rapport in azure IoT Explorer bekijken:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Een scherm opname waarin Azure IoT Explorer wordt weer gegeven met een tabel met gerapporteerde telemetrie (vochtigheid, Tempe ratuur) van IOT Plug en Play Bridge.":::

## <a name="prerequisites"></a>Vereisten

U kunt het voor beeld uitvoeren in het artikel op Windows of Linux. De shell-opdrachten in deze hand leiding volgen de Windows-Conventie voor pad scheidings tekens ' `\` ', als u de volgende stap uitvoert in Linux: Zorg ervoor dat u deze scheidings tekens verwisselt voor `/` .

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Als u in het tweede deel van dit artikel wilt communiceren met het voor beeld-apparaat, gebruikt u het hulp programma **Azure IOT Explorer** . [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT Hub Connection String_ voor uw hub op te halen. Noteer deze connection string u dit later in dit artikel gebruikt:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze connection string u dit later in dit artikel gebruikt:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>De bridge downloaden en uitvoeren

In dit artikel hebt u twee opties om de Bridge uit te voeren. U kunt het volgende doen:

- Down load een vooraf gebouwd uitvoerbaar bestand en voer dit uit zoals beschreven in deze sectie.
- Down load de bron code en [bouw en voer de Bridge uit](#build-and-run-the-bridge) zoals beschreven in de volgende sectie.

De bridge downloaden en uitvoeren:

1. Ga naar de pagina IoT-Plug en Play [releases](https://github.com/Azure/iot-plug-and-play-bridge/releases).
1. Down load het vooraf gemaakte uitvoer bare bestand voor uw besturings systeem: **pnpbridge_bin.exe** voor Windows of **pnpbridge_bin** voor Linux.
1. Down load de voorbeeld [config.jsin](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) het configuratie bestand voor het voor beeld van de omgevings sensor. Zorg ervoor dat het configuratie bestand zich in dezelfde map bevindt als het uitvoer bare programma.
1. Bewerk de *config.jsin* het bestand:

    - Voeg de `connection-string` waarde die het _apparaat is Connection String_ u eerder een notitie hebt gemaakt.
    - Voeg de `symmetric_key` waarde toe die de waarde van de gedeelde toegangs sleutel van het _apparaat Connection String_.
    - Vervang de `root_interface_model_id` waarde door `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    De eerste sectie van de *config.jsin* het bestand ziet er nu uit als in het volgende code fragment:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Voer het uitvoer bare bestand uit in de opdracht regel omgeving. De Bridge genereert een uitvoer die er als volgt uitziet:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>De Bridge bouwen en uitvoeren

Als u liever zelf het uitvoer bare bestand maakt, kunt u de bron code downloaden en scripts bouwen.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [IoT Plug en Play](https://github.com/Azure/iot-plug-and-play-bridge) -github-opslag plaats naar deze locatie te klonen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Werk de submodules bij nadat u de opslag plaats hebt gekloond. De submodules omvatten de Azure IoT SDK voor C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Deze bewerking kan enkele minuten in beslag nemen.

> [!TIP]
> Als u problemen ondervindt met het uitvoeren van een update van de Git-kloon submodule, is dit een bekend probleem met Windows-bestands paden. U kunt de volgende opdracht proberen om het probleem op te lossen: `git config --system core.longpaths true`

De vereisten voor het bouwen van de Bridge verschillen per besturings systeem:

### <a name="windows"></a>Windows

Als u de IoT Plug en Play-brug wilt maken in Windows, installeert u de volgende software:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/): zorg ervoor dat u de workload **Desktop Development with C++** kiest tijdens het [installeren](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) van Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

In dit artikel wordt ervan uitgegaan dat u Ubuntu Linux gebruikt. De stappen in dit artikel zijn getest met Ubuntu 18,04.

Als u de IoT Plug en Play-brug op Linux wilt bouwen, installeert u **gcc**, **Git**, **cmake** en alle vereiste afhankelijkheden met behulp van de `apt-get` opdracht:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Controleer of de versie van `cmake` hoger is dan **2.8.12** en of de versie van **GCC** hoger dan **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug bouwen

Ga naar de map *pnpbridge* in de map opslag plaats.

Voor Windows voert u het volgende uit in een [opdracht prompt voor ontwikkel aars voor Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Net als voor Linux voert u het volgende uit:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>In Windows kunt u de oplossing openen die is gegenereerd door de opdracht cmake in Visual Studio 2019. Open het project bestand *azure_iot_pnp_bridge. SLN* in de map cmake en stel het *pnpbridge_bin* project in als het opstart project in de oplossing. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

### <a name="edit-the-configuration-file"></a>Het configuratie bestand bewerken

Meer informatie over de configuratie bestanden vindt u in het [document IoT Plug en Play Bridge-concepten](concepts-iot-pnp-bridge.md).

Open de *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.jsin* het bestand in een tekst editor.

- Voeg de `connection-string` waarde die het _apparaat is Connection String_ u eerder een notitie hebt gemaakt.
- Voeg de `symmetric_key` waarde toe die de waarde van de gedeelde toegangs sleutel van het _apparaat Connection String_.
- Vervang de `root_interface_model_id` waarde door `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

De eerste sectie van de *config.jsin* het bestand ziet er nu uit als in het volgende code fragment:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug uitvoeren

Start het voor beeld van de IoT Plug en Play Bridge-omgevings sensor. De para meter is het pad naar het `config.json` bestand dat u in de vorige sectie hebt bewerkt:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

De Bridge genereert een uitvoer die er als volgt uitziet:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Gebruik de volgende opdrachten om de Bridge uit te voeren op Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>De model bestanden downloaden

U kunt Azure IoT Explorer later gebruiken om het apparaat weer te geven wanneer dit verbinding maakt met uw IoT-hub. Azure IoT Explorer heeft een lokale kopie van het model bestand nodig dat overeenkomt met de **model-id** die uw apparaat verzendt. Met het model bestand kunnen IoT Explorer de telemetrie, eigenschappen en opdrachten weer geven die uw apparaat implementeert.

De modellen voor Azure IoT Explorer downloaden:

1. Maak een map *models* op uw lokale computer.
1. Sla [EnvironmentalSensor.jsop](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) naar de map *modellen* die u in de vorige stap hebt gemaakt.
1. Als u dit model bestand in een tekst editor opent, ziet u het model definieert een onderdeel met de `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` id. Dit is dezelfde model-ID die u hebt gebruikt in de *config.jsvoor* het bestand.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat de Bridge is gestart, gebruikt u het hulp programma Azure IoT Explorer om te controleren of het werkt. U kunt de telemetrie, eigenschappen en opdrachten zien die in het `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` model zijn gedefinieerd.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

* [Wat is IoT Plug en Play Bridge](./concepts-iot-pnp-bridge.md)
* [IoT Plug en Play-brug bouwen, implementeren en uitbreiden](howto-build-deploy-extend-pnp-bridge.md)
* [IoT Plug en Play-brug op GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
