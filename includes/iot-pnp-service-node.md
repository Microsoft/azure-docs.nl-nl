---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95487786"
---
IoT Plug en Play vereenvoudigt IoT en stelt u in staat om gebruik te maken van de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Node.js gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Kloon de SDK-opslagplaats met de voorbeeldcode

Kloon de voorbeelden vanuit een [Node SDK-opslagplaats](https://github.com/Azure/azure-iot-sdk-node). Open een terminalvenster in een map naar keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats van [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md) voor meer informatie over de voorbeeldconfiguratie.

In deze quickstart gebruikt u een voorbeeldthermostaat die in Node.js is geschreven als IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open een terminalvenster en navigeer naar de lokale map met de Microsoft Azure IoT SDK voor Node.js-opslagplaats die u hebt gekloond vanuit GitHub.

1. Dit terminalvenster wordt gebruikt als uw **apparaatterminal**. Ga naar de map van de gekloonde opslagplaats en navigeer naar de map */azure-iot-sdk-node/device/samples/pnp*. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Voer het voorbeeldapparaat (thermostaat) uit met de volgende opdracht:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat het servicevoorbeeld werkt.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In [Quickstarts en zelfstudies voor het instellen van uw omgeving voor IoT Plug en Play](../articles/iot-pnp/set-up-environment.md) hebt u twee omgevingsvariabelen gemaakt om het voorbeeld zo te configureren dat verbinding wordt gemaakt met uw IoT-hub en apparaat:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in C# om te communiceren met het voorbeeldapparaat dat u zojuist hebt ingesteld.

1. Open een ander terminalvenster om als **serviceterminal** te gebruiken.

1. Navigeer in de gekloonde Node SDK-opslagplaats naar de map */azure-iot-sdk-node/service/samples/javascript*. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het voorbeeldapparaat (thermostaat) in de **apparaatterminal** hebt uitgevoerd, zag u het volgende bericht om de online status aan te geven:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Ga naar de terminal **service** en gebruik de volgende opdracht om het voorbeeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    node twin.js
    ```

1. Bekijk het antwoord van de apparaatdubbel in de uitvoer van de **serviceterminal**. U ziet hier de model-id van het apparaat en de bijbehorende eigenschappen die zijn gerapporteerd:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Het volgende fragment toont de code in *twin.js*, waarmee de model-id van de apparaatdubbel wordt opgehaald:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

In dit scenario wordt `Model Id: dtmi:com:example:Thermostat;1` uitgevoerd.

> [!NOTE]
> Deze servicevoorbeelden gebruiken de klasse **Register** vanuit de **IoT Hub-serviceclient**. Zie de [handleiding voor serviceontwikkelaars](../articles/iot-pnp/concepts-developer-guide-service.md) voor meer informatie over de API's, waaronder de API's voor digitale dubbels.

### <a name="update-a-writable-property"></a>Een schrijfbare eigenschap bijwerken

1. Open het bestand *twin.js* in de code-editor.

1. Bekijk de voorbeeldcode. Er wordt twee manieren weergegeven waarop u de apparaatdubbel kunt bijwerken. Als u de eerste manier wilt gebruiken, wijzigt u de variabele `twinPatch` als volgt:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    De eigenschap `targetTemperature` wordt gedefinieerd als een beschrijfbare eigenschap in het apparaatmodel van de thermostaat.

1. Gebruik de volgende opdracht in de terminal **service** om het voorbeeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    node twin.js
    ```

1. U kunt in de terminal **apparaat** zien dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. Voer in de **serviceterminal** de volgende opdracht uit om te bevestigen dat de eigenschap is bijgewerkt:

    ```cmd/sh
    node twin.js
    ```

1. In de uitvoer van de **serviceterminal**, in de eigenschappensectie `reported`, ziet u dat de bijgewerkte doeltemperatuur wordt vermeld. Het kan enige tijd duren voordat het apparaat de update heeft voltooid. Herhaal deze stap totdat het apparaat de update van de eigenschap heeft verwerkt:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Open het bestand *device_method.js* en controleer de code.

1. Ga naar de terminal **service**. Gebruik de volgende opdracht om het voorbeeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. De uitvoer van de terminal **service** geeft de volgende bevestiging weer:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. U kunt in de terminal **apparaat** zien dat de opdracht is bevestigd:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
