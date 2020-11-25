---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487785"
---
IoT Plug en Play vereenvoudigt IoT omdat u kunt communiceren met het model van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Python gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart wilt uitvoeren, moet Python 3.7 op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

Het **azure-iot-device**-pakket wordt gepubliceerd als PIP.

Installeer het pakket als volgt in uw lokale Python-omgeving:

```cmd/sh
pip install azure-iot-device
```

Installeer het **azure-iot-device**-pakket door de volgende opdracht uit te voeren:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [leesmij-voorbeeldbestand](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) (Engelstalig) voor meer informatie over de voorbeeldconfiguratie.

In deze quickstart gebruikt u als voorbeeld een thermostaat die in Python is geschreven als IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open een terminalvenster in een map naar keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) te klonen op deze locatie:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Dit terminalvenster wordt gebruikt als uw **apparaat** terminal. Ga naar de map van de gekloonde opslagplaats en ga naar de map */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Voer het voorbeeldapparaat (thermostaat) uit met de volgende opdracht:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat het servicevoorbeeld werkt.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in Python om te communiceren met het voorbeeldapparaat dat u zojuist hebt ingesteld.

1. Open een ander terminalvenster om als **service** terminal te gebruiken.

1. Ga naar de map */azure-iot-sdk-python/azure-iot-hub/samples* van de gekloonde Python SDK-opslagplaats.

1. Open bestand *registry_manager_pnp_sample.py* en controleer de code. In dit voorbeeld ziet u hoe u de klasse **IoTHubRegistryManager** gebruikt om te communiceren met uw IoT Plug en Play-apparaat.

> [!NOTE]
> Deze servicevoorbeelden gebruiken de klasse **IoTHubRegistryManager** vanuit de **IoT Hub-serviceclient**. Zie de [handleiding voor serviceontwikkelaars](../articles/iot-pnp/concepts-developer-guide-service.md) voor meer informatie over de API's, waaronder de API's voor digitale dubbels.

### <a name="get-the-device-twin"></a>De apparaatdubbel ophalen

In [Quickstarts en zelfstudies voor het instellen van uw omgeving voor IoT Plug en Play](../articles/iot-pnp/set-up-environment.md) hebt u twee omgevingsvariabelen gemaakt om het voorbeeld zo te configureren dat verbinding wordt gemaakt met uw IoT-hub en apparaat:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Gebruik de volgende opdracht in de **service** terminal om dit voorbeeld uit te voeren:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Als u dit voorbeeld uitvoert op Linux, gebruikt u `export` in plaats van `set`.

In de uitvoer ziet u de apparaatdubbel en wordt de model-id ervan afgedrukt:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

In het volgende codefragment ziet u de voorbeeldcode uit *registry_manager_pnp_sample.py*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Een apparaatdubbel bijwerken

In dit voorbeeld ziet u hoe u de beschrijfbare eigenschap `targetTemperature` in het apparaat bijwerkt:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

U kunt controleren of de update is toegepast als in de **apparaat** terminal de volgende uitvoer wordt weergegeven:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

In de **service** terminal wordt bevestigd dat de patch is gelukt:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

Het voorbeeld roept vervolgens een opdracht aan:

In de **service** terminal ziet u een bevestigingsbericht van het apparaat:

```cmd/sh
The device method has been successfully invoked
```

U kunt in de **apparaat** terminal zien dat het apparaat de opdracht ontvangt:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
