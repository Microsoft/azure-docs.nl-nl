---
title: IoT Plug en Play-brug bouwen en implementeren | Microsoft Docs
description: Identificeer de IoT Plug en Play Bridge-onderdelen. Meer informatie over hoe u deze kunt uitvoeren op IoT-apparaten, gateways en als IoT Edge-module.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c43a49c6b4675e98b557ff73d6f8b6468d74da8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057436"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug bouwen en implementeren

Met de [iot Plug en Play-brug](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) kunt u de bestaande apparaten die zijn gekoppeld aan een gateway, verbinden met uw IOT-hub. U gebruikt de Bridge om IoT Plug en Play-interfaces toe te wijzen aan de gekoppelde apparaten. Een IoT Plug en Play-interface definieert de telemetrie die een apparaat verzendt, de eigenschappen gesynchroniseerd tussen het apparaat en de Cloud en de opdrachten waarop het apparaat reageert. U kunt de open-source Bridge-toepassing installeren en configureren op Windows-of Linux-gateways. Daarnaast kan de Bridge worden uitgevoerd als een Azure IoT Edge runtime-module.

In dit artikel wordt uitgelegd hoe u het volgende kunt doen:

- Een brug configureren.
- Hoe u de Bridge in verschillende omgevingen bouwt en uitvoert.

Zie voor een eenvoudig voor beeld waarin wordt getoond hoe u de Bridge kunt gebruiken [hoe u verbinding maakt met het IoT Plug en Play Bridge-voor beeld dat op Linux of Windows wordt uitgevoerd om te IOT hub](howto-use-iot-pnp-bridge.md).

In de richt lijnen en voor beelden in dit artikel wordt ervan uitgegaan dat u bekend bent met de basis kennis van [Azure Digital apparaatdubbels](../digital-twins/overview.md) en [IOT Plug en Play](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Algemene vereisten

### <a name="supported-os-platforms"></a>Ondersteunde besturings systemen

De volgende platformen en versies van het besturings systeem worden ondersteund:

|Platform  |Ondersteunde versies  |
|---------|---------|
|Windows 10 |     Alle Windows Sku's worden ondersteund. Bijvoorbeeld: IoT Enter prise, Server, Desktop, IoT core. *Voor de functionaliteit voor camera status controle wordt 20H1 of hoger aanbevolen. Alle andere functies zijn beschikbaar op alle Windows 10-builds.*  |
|Linux     |Getest en ondersteund op Ubuntu 18,04, de functionaliteit van andere distributies is niet getest.         |
||

### <a name="hardware"></a>Hardware

- Elk hardware-platform dat ondersteuning biedt voor de bovenstaande Sku's en versies van het besturings systeem.
- De rand apparaten en Sens oren van serie, USB, Bluetooth en camera worden standaard ondersteund. De IoT Plug en Play-brug kan worden uitgebreid ter ondersteuning van een aangepaste rand apparaat of sensor.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-producten en-hulpprogram ma's

- **Azure IOT hub** : u hebt een [Azure IOT-hub](../iot-hub/index.yml) in uw Azure-abonnement nodig om uw apparaat te verbinden met. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Als u geen IoT-hub hebt, [volgt u deze instructies om er een te maken](../iot-hub/iot-hub-create-using-cli.md). IoT-Plug en Play-ondersteuning is niet opgenomen in IoT-hubs van de Basic-laag.
- **Azure IOT Explorer** : u kunt het hulp programma Azure IOT Explorer gebruiken om te communiceren met uw IOT Plug en Play-apparaat. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem. Configureer het Azure IoT Explorer-hulp programma om verbinding te maken met uw IoT-hub en om model definities te zoeken in de map *pnpbridge\docs\schemas* in de **IOT-Plug en Play-Bridge** die u hebt gekloond.

## <a name="configure-a-bridge"></a>Een brug configureren

Er zijn twee manieren om de Bridge te configureren:

- Als de Bridge systeem eigen op een IoT-apparaat of gateway wordt uitgevoerd, gebruikt u het configuratie bestand. Dit scenario kan gebruikmaken van een Linux-of Windows-machine.
- Als de Bridge als IoT Edge module in de IoT Edge-runtime wordt uitgevoerd, gebruikt u de module dubbele gewenste eigenschap. In dit scenario wordt ervan uitgegaan dat de IoT Edge runtime wordt gehost in een Linux-omgeving.

### <a name="configuration-file"></a>Configuratiebestand

Als Plug en Play Bridge systeem eigen op een IoT-apparaat of gateway wordt uitgevoerd, wordt een configuratie bestand gebruikt voor het volgende:

- De IoT Central-of IoT Hub verbindings gegevens ophalen.
- Apparaten configureren waarvoor IoT Plug en Play-interfaces worden gepubliceerd.

Gebruik een van de volgende opties om het configuratie bestand toe te voegen aan de Bridge:

- Geef het pad naar het configuratie bestand door als opdracht regel parameter naar het uitvoer bare bestand Bridge.
- Gebruik de bestaande *config.js* in het bestand in de map *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* .

Het [schema van het configuratie bestand](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) is beschikbaar in de GitHub-opslag plaats.

> [!TIP]
> Als u een bestand met een brug configuratie bewerkt in VS code, kunt u dit schema bestand gebruiken om het bestand te valideren.

### <a name="iot-edge-module-configuration"></a>Configuratie van IoT Edge module

Wanneer de Bridge wordt uitgevoerd als een IoT Edge module op een IoT Edge runtime, wordt het configuratie bestand verzonden vanuit de Cloud als een update naar de `PnpBridgeConfig` gewenste eigenschap. De brug wacht op het bijwerken van deze eigenschap voordat de adapters en onderdelen worden geconfigureerd.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>De brug bouwen en uitvoeren op een IoT-apparaat of-gateway

| Platform | Ondersteund |
| :-----------: | :-----------: |
| Windows |  Yes |
| Linux | Yes |

### <a name="prerequisites"></a>Vereisten

Als u deze sectie wilt volt ooien, moet u de volgende software installeren op uw lokale computer:

- Een ontwikkel omgeving die ondersteuning biedt voor het compileren van C++ [, zoals Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) . Zorg ervoor dat u het onderdeel NuGet package manager en de werk belasting **van het bureau blad** opneemt tijdens de installatie van Visual Studio.
- [Cmake](https://cmake.org/download/) : als u cmake installeert, selecteert u de optie **cmake toevoegen aan het** systeempad.
- Als u op Windows bouwt, moet u ook de [Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)downloaden.

### <a name="source-code"></a>Broncode

Kloon de [IoT Plug en Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) -opslag plaats naar uw lokale computer:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Het duurt enkele minuten voordat de vorige opdracht is uitgevoerd.

> [!NOTE]
> Als u problemen ondervindt met het `git submodule update` mislukken van Windows, kunt u de volgende opdracht uitvoeren om het probleem op te lossen: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>De brug bouwen in Windows

Open de **opdracht prompt voor ontwikkel aars voor VS 2019** en navigeer naar de map met de opslag plaats die u hebt gekloond en voer de volgende opdrachten uit:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Het duurt enkele minuten voordat de vorige opdracht is uitgevoerd.

U kunt eventueel het gegenereerde *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. SLN* -oplossings bestand openen in Visual Studio om de code te bewerken, opnieuw te bouwen en fouten op te sporen.

> [!TIP]
> In dit project wordt CMAKE gebruikt om de project bestanden te genereren. Wijzigingen die u aanbrengt in het project in Visual Studio, kunnen verloren gaan als de juiste CMAKE-bestanden niet worden bijgewerkt.

### <a name="build-the-bridge-on-linux"></a>De brug bouwen op Linux

Ga met behulp van de bash-shell naar de map met de opslag plaats die u hebt gekloond en voer de volgende opdrachten uit:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>De algemene Sens oren configureren

Wijzig de volgende para meters onder het `pnp_bridge_connection_parameters` knoop punt in het *config.jsop* bestand in de map *IOT-plug-and-Play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* in Windows of de map *IOT-plug-and-Play-bridge/pnpbridge/cmake/pnpbridge_linux \ src/pnpbridge/samples/console* in Windows:

Als u een connection string gebruikt om verbinding te maken met uw IoT-hub:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> De `symmetric_key` waarde moet overeenkomen met de SAS-sleutel in de Connection String.

Als u DPS gebruikt om verbinding te maken met uw IoT hub of IoT Central toepassing:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Bekijk de rest van het configuratie bestand om te zien welke interface onderdelen en globale para meters in dit voor beeld zijn geconfigureerd.

### <a name="start-the-bridge-in-windows"></a>De brug starten in Windows

Start de Bridge door het uit te voeren vanaf de opdracht prompt:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Als u een configuratie bestand van een andere locatie wilt gebruiken, geeft u het pad door als opdracht regel parameter wanneer u de Bridge uitvoert.
  
> [!TIP]
> Als u een ingebouwde camera of een USB-camera op uw PC hebt aangesloten, kunt u een toepassing starten die gebruikmaakt van een camera, zoals de ingebouwde **camera** -app. Wanneer de **camera** -app wordt uitgevoerd, worden in de Bridge console-uitvoer de bewakings statistieken weer gegeven en wordt de frame frequentie van de gerapporteerde via de digitale twee interface naar uw IOT-hub.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>De Bridge bouwen en uitvoeren als een IoT Edge module

| Platform | Ondersteund |
| :-----------: | :-----------: |
| Windows |  Nee |
| Linux | Yes |

### <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze sectie hebt u een Azure IoT hub met een gratis of Standard-laag nodig. Zie [een IOT-hub maken](../iot-hub/iot-hub-create-through-portal.md)om Lean een IOT-hub te maken.

Bij de stappen in deze sectie wordt ervan uitgegaan dat u de volgende ontwikkel omgeving op een Windows 10-computer hebt. Met deze hulpprogram ma's kunt u een IoT Edge module bouwen en implementeren op uw IoT Edge-apparaat:

- Windows-subsysteem voor Linux (WSL) 2 met Ubuntu 18,04 LTS. Zie de [installatie handleiding voor het Windows-subsysteem voor Linux voor Windows 10](/windows/wsl/install-win10)voor meer informatie.
- Docker Desktop voor Windows dat is geconfigureerd voor het gebruik van WSL 2. Zie [docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/)voor meer informatie.
- [Visual Studio-code die in uw Windows-omgeving is geïnstalleerd](https://code.visualstudio.com/docs/setup/windows) en waarop de volgende drie uitbrei dingen zijn geïnstalleerd:

  - [Uitbreidings pakket voor externe ontwikkeling](https://aka.ms/vscode-remote/download/extension) : met deze extensie kunt u code maken en uitvoeren in WSL 2.
  - [Docker voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) : deze extensie moet zijn ingeschakeld in de **WSL: Ubuntu-18,04-** omgeving van VS code.
  - [Azure IOT-Hulpprogram ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) : deze extensie moet zijn ingeschakeld in de **WSL: Ubuntu-18,04-** omgeving van VS code.

- Voer de volgende opdrachten uit in de WSL 2-omgeving om de vereiste hulpprogram ma's voor bouwen te installeren:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- De [Azure cli](/cli/azure/install-azure-cli-apt) die is geïnstalleerd in uw WSL 2-omgeving om uw Azure-resources te beheren.

  > [!TIP]
  > Als u wilt, kunt u de `az` opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/) waarop de CLI vooraf is geïnstalleerd.

### <a name="create-an-iot-edge-device"></a>Een IoT Edge-apparaat maken

Met de opdrachten in dit onderwerp maakt u een IoT Edge apparaat dat wordt uitgevoerd op een virtuele Azure-machine. Het uitvoeren van een IoT Edge apparaat in een virtuele machine is handig voor het testen van uw implementatie als u geen toegang hebt tot een echt apparaat.

Als u een IoT Edge apparaatregistratie in uw IoT-hub wilt maken, voert u de volgende opdrachten uit in uw WSL 2-omgeving. Gebruik de `az login` opdracht om u aan te melden bij uw Azure-abonnement:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Voer de volgende opdrachten uit om een virtuele Azure-machine te maken waarop de IoT Edge-runtime is geïnstalleerd. Werk de tijdelijke aanduidingen bij met de juiste waarden:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

U hebt nu de IoT Edge runtime die wordt uitgevoerd op een virtuele machine. U kunt de volgende opdracht gebruiken om te controleren of de **$edgeAgent** en **$edgeHub** worden uitgevoerd op het apparaat:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> U wordt gefactureerd terwijl deze virtuele machine wordt uitgevoerd. Zorg ervoor dat u het programma afsluit wanneer u het niet gebruikt en het verwijdert wanneer u klaar bent met het proces.

### <a name="download-the-source-code"></a>De bron code downloaden

In de volgende stappen maakt u de docker-installatie kopie in uw WSL 2-omgeving. Voor het klonen van de **IOT-Plug en Play-Bridge-** opslag plaats voert u de volgende opdrachten uit in een WSL 2 bash shell in een geschikte map:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Het duurt enkele minuten voordat de vorige opdracht is uitgevoerd.

### <a name="update-the-dockerfile"></a>De *Dockerfile* bijwerken

Start VS code, open het opdracht palet, Voer *externe WSL in: map openen in WSL* en open vervolgens de map *IOT-Plug en Play-Bridge* die de gekloonde opslag plaats bevat.

Open het *pnpbridge\Dockerfile.amd64* -bestand. Bewerk de definities van omgevings variabelen als volgt:

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> U kunt de volgende opdracht gebruiken om het apparaat op te halen connection string: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Er zijn voorbeeld *Dockerfiles* voor andere architecturen.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>De installatie kopie van de IoT Plug en Play-brug module bouwen

Open in VS code het opdracht palet, Voer *docker-registers in: Meld u aan bij docker cli* en selecteer vervolgens uw Azure-abonnement en container register. Met deze opdracht kan docker verbinding maken met het container register en de module installatie kopie uploaden.

Open de *pnpbridge/module.jsin* het bestand. Toevoegen `{your container registry name}.azurecr.io/iotpnpbridge` als de opslag plaats voor container installatie kopieën en `v1` als de versie.

In VS code klikt u met de rechter muisknop op de *pnpbridge/module.js* in het bestand in de weer gave **Explorer** , selecteert u **installatie kopie van de module build en push IOT Edge** en selecteert u **amd64** als het platform.

In VS code kunt u in de **docker** -weer gave bladeren door de inhoud van uw container register die nu de **iotpnpbridge: v1-amd64-** module installatie kopie bevat.

### <a name="create-a-container-registry"></a>Een containerregister maken

Een IoT Edge apparaat downloadt de module-installatie kopieën uit een container register. In dit voor beeld wordt een Azure container Registry gebruikt.

Maak een Azure-container register in de resource groep **Bridge-Edge-resources** . Schakel vervolgens beheerders toegang in voor het container register en ontvang de referenties die uw IoT Edge apparaat nodig heeft om de module installatie kopieën te downloaden:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Het implementatie manifest maken

Een IoT Edge implementatie manifest geeft de modules en configuratie waarden op die moeten worden geïmplementeerd op een IoT Edge apparaat.

Open in VS code de *pnpbridge/deployment.template.jsin* het bestand:

- Werk de `registryCredentials` met de waarden bij van de vorige opdracht. De `address` waarde ziet er als volgt uit `{your container registry name}.azurecr.io` .
- Werk de `image` waarde voor `ModulePnpBridge` . De module afbeelding ziet er als volgt uit: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Vervang de `PnPBridgeConfig` door de volgende JSON om de CO2-detectie adapter te configureren:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Sla de wijzigingen op.

In VS code klikt u met de rechter muisknop op de *pnpbridge/deployment.template.js* in het bestand in de **Verkenner** -weer gave en selecteert u **IOT Edge implementatie manifest genereren**. Met deze opdracht wordt het *pnpbridge/config/deployment.amd64.jsin* het implementatie manifest gegenereerd.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>De brug implementeren op uw IoT Edge apparaat

Open in VS code het opdracht palet, Voer *Azure IOT hub in: selecteer IOT hub* en selecteer vervolgens uw abonnement en IOT hub.

In VS code klikt u met de rechter muisknop op de *pnpbridge/config/deployment.amd64.js* in het bestand in de **Verkenner** -weer gave, selecteert u **implementatie voor één apparaat maken** en selecteert u de **brug-edge-apparaat**.

Als u de status van de modules op uw apparaat wilt weer geven, voert u de volgende opdracht uit:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

De lijst met actieve modules bevat nu de **ModulePnpBridge** -module die is geconfigureerd om de CO2-detectie adapter te gebruiken.

### <a name="tidy-up"></a>Opruimen

Als u de virtuele machine en het container register van uw Azure-abonnement wilt verwijderen, voert u de volgende opdracht uit:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Mapstructuur

*pnpbridge\deps\azure-IOT-SDK-c-PnP*: git-submodules die de Azure IOT Device SDK voor c SDK bevatten.

*pnpbridge\scripts*: scripts maken.

*pnpbridge\src*: bron code voor IOT Plug en Play Bridge core.

*pnpbridge\src\adapters*: bron code voor verschillende IOT Plug en Play Bridge-adapters.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over de IoT Plug en Play-brug naar de [iot Plug en Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) github-opslag plaats.