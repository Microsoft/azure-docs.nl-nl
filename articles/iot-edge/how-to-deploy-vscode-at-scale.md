---
title: Modules op schaal implementeren met behulp van Visual Studio code-Azure IoT Edge
description: Gebruik de IoT-extensie voor Visual Studio code voor het maken van automatische implementaties voor groepen IoT Edge apparaten.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a20992ef26b74bcc37a7403e4ee77cacc0f8f66e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200299"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>IoT Edge-modules op schaal implementeren met behulp van Visual Studio code

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

U kunt een **IOT Edge automatische implementatie** maken met Visual Studio code voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](../iot-hub/iot-hub-automatic-device-management.md) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren. U kunt ook de status en status van de modules bijhouden en zo nodig wijzigingen aanbrengen.

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

In dit artikel stelt u Visual Studio code en de IoT-uitbrei ding in. Vervolgens leert u hoe u modules implementeert op een set IoT Edge apparaten.

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een of meer IoT Edge apparaten.

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) voor Visual Studio code.

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen uit te voeren met uw hub. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en de IoT-hub selecteren waaraan u werkt.

1. Open in Visual Studio code de weer gave **Explorer** .

1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het weglatings teken niet ziet, beweegt u de muis aanwijzer over de koptekst.

1. Kies **IOT hub selecteren**.

1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.

1. Selecteer uw Azure-abonnement.

1. Selecteer uw IoT-hub.

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd. Ook wordt beschreven hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

Als u modules wilt implementeren met Visual Studio code, slaat u het implementatie manifest lokaal op als een. JSON-bestand. U moet de locatie opgeven wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat.

Hier volgt een basis implementatie manifest met een module als voor beeld:

>[!NOTE]
>In dit voor beeld-implementatie manifest wordt schema versie 1,1 gebruikt voor de IoT Edge agent en hub. Schema versie 1,1 is uitgebracht samen met IoT Edge versie 1.0.10 en biedt functies als opstart volgorde voor modules en prioriteiten voor route ring.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Als u wilt bepalen welke IoT Edge apparaten u momenteel kunt configureren, voert u de opdracht **IOT Edge: apparaatgegevens ophalen** uit.

## <a name="identify-devices-with-target-conditions"></a>Apparaten identificeren met doel voorwaarden

Als u de IoT Edge apparaten wilt identificeren die de implementatie moeten ontvangen, moet u een doel voorwaarde opgeven. Er wordt voldaan aan een doel voorwaarde wanneer opgegeven criteria worden vergeleken met een deviceId, een label waarde of een gerapporteerde eigenschaps waarde.

U configureert labels op het apparaat dubbele. Hier volgt een voor beeld van een apparaat dat bestaat uit Tags:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Dit apparaat ontvangt een implementatie als de doel voorwaarde voor de implementatie een expressie bevat die overeenkomt met een van de waarden van de tag, zoals `tag.location.building = '20'` .

Als u een specifiek apparaat wilt richten, ongeacht de labels of andere waarden, geeft u alleen de `deviceId` voor waarde voor het doel op.

Hier volgen enkele voor beelden:

* deviceId = ' linuxprod1 '
* deviceId = ' linuxprod1 ' of deviceId = ' linuxprod2 ' of deviceId = ' linuxprod3 '
* Tags. Environment = ' Prod '
* Tags. Environment = ' Prod ' en tags. Location = ' westus2 '
* Tags. Environment = ' Prod ' of tags. Location = ' westus2 '
* Tags. operator = ' John ' en tags. Environment = ' Prod ' en niet deviceId = ' linuxprod1 '

Zie de [doel voorwaarde](module-deployment-monitoring.md#target-condition) voor meer informatie. Zie voor meer informatie over apparaatdubbels en tags voor apparaten [inzicht in de apparaatdubbels in IOT hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Het apparaat dubbele bewerkingen

U kunt het apparaat dubbele in Visual Studio code bewerken om de labels te configureren. Selecteer in het menu **weer gave** de optie **opdracht palet** en voer de opdracht **IOT Edge: apparaat verdubbelen bewerken** . Selecteer uw IoT Edge-apparaat en het dubbele apparaat wordt weer gegeven.

In dit voor beeld zijn er geen labels gedefinieerd. Vervang de huidige lege sectie `"tags": {}` door de definitie van uw eigen Tags.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Nadat u het lokale bestand hebt opgeslagen, voert u de opdracht **IOT Edge: apparaat verdubbelen bijwerken** uit.

## <a name="create-deployment-at-scale"></a>Implementatie op schaal maken

Nadat u het implementatie manifest en de geconfigureerde labels op het apparaat dubbele hebt geconfigureerd, bent u klaar om te implementeren.

1. Selecteer in het menu **weer gave** de optie **opdracht palet** en selecteer de opdracht **Azure IOT Edge: implementatie maken op basis** van de schaal.

1. Navigeer naar het JSON-bestand van het implementatie manifest dat u wilt gebruiken en klik op **Edge-implementatie manifest selecteren**.

1. Geef de gevraagde waarden op, beginnend met de **implementatie-id**.

   ![Een implementatie-ID opgeven](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Geef waarden op voor deze para meters:

  | Parameter | Beschrijving |
  | --- | --- |
  | Implementatie-ID | De naam van de implementatie die wordt gemaakt in de IoT-hub. Geef uw implementatie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /` . |
  | Doel voorwaarde | Geef een doel voorwaarde op om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioriteit |  Een positief geheel getal. Als twee of meer implementaties op hetzelfde apparaat zijn gericht, is de implementatie met de hoogste numerieke waarde voor prioriteit van toepassing. |

  Nadat u de prioriteit hebt opgegeven, moet de Terminal de uitvoer weer geven die lijkt op de volgende voors telling:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Implementaties bewaken en aanpassen

Gebruik de [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) of de [Azure cli](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) voor het bewaken, wijzigen en verwijderen van implementaties. Beide geven metrische gegevens over uw implementaties.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).