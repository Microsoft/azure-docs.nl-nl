---
title: Modules op schaal implementeren met behulp van Azure CLI-Azure IoT Edge
description: Gebruik de IoT-extensie voor Azure CLI om automatische implementaties te maken voor groepen IoT Edge apparaten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370373"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>IoT Edge modules op schaal implementeren en bewaken met behulp van de Azure CLI

Maak een **IOT Edge automatische implementatie** met behulp van de Azure-opdracht regel interface voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](../iot-hub/iot-hub-automatic-device-management.md) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt volgen en waar nodig wijzigingen kunt aanbrengen.

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

In dit artikel stelt u Azure CLI en de IoT-uitbrei ding in. U leert hoe u modules implementeert op een set IoT Edge-apparaten en de voortgang kunt controleren met behulp van de beschik bare CLI-opdrachten.

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* Een of meer IoT Edge apparaten.

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

* [Azure cli](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

Als u modules wilt implementeren met behulp van Azure CLI, slaat u het manifest van de implementatie lokaal op als een. txt-bestand. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat.

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

## <a name="layered-deployment"></a>Gelaagde implementatie

Gelaagde implementaties zijn een type automatische implementatie dat boven op elkaar kan worden geplaatst. Zie voor meer informatie over gelaagde implementaties [begrijpen IOT Edge automatische implementaties voor afzonderlijke apparaten of op schaal](module-deployment-monitoring.md).

U kunt gelaagde implementaties maken en beheren met de Azure CLI, zoals elke automatische implementatie, met slechts enkele verschillen. Zodra een gelaagde implementatie is gemaakt, werkt dezelfde Azure CLI voor gelaagde implementaties hetzelfde als elke implementatie. Als u een gelaagde implementatie wilt maken, voegt `--layered` u de markering toe aan de opdracht Create.

Het tweede verschil bevindt zich in de constructie van het implementatie manifest. Hoewel standaard automatische implementatie de System runtime-modules moet bevatten naast eventuele gebruikers modules, kunnen gelaagde implementaties alleen gebruikers modules bevatten. In plaats daarvan moet er ook een standaard automatische implementatie op een apparaat worden uitgevoerd om de vereiste onderdelen van elk IoT Edge apparaat op te geven, zoals de System runtime-modules.

Hier volgt een eenvoudig gelaagd implementatie manifest met een module als voor beeld:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

In het vorige voor beeld is een gelaagde implementatie ingesteld `properties.desired` voor een module. Als deze gelaagde implementatie gericht is op een apparaat waarop dezelfde module al was toegepast, worden de bestaande gewenste eigenschappen overschreven. Als u een update wilt gebruiken in plaats van overschrijven, gewenste eigenschappen, kunt u een nieuwe Subsectie definiëren. Bijvoorbeeld:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Zie [gelaagde implementatie](module-deployment-monitoring.md#layered-deployment) voor meer informatie over het configureren van module apparaatdubbels in gelaagde implementaties.

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van Tags

Voordat u een implementatie kunt maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met behulp van **Tags** op het apparaat. Elk apparaat kan meerdere labels hebben die u op een wille keurige manier definieert voor uw oplossing. Als u bijvoorbeeld een universiteit van Smart gebouwen beheert, kunt u de volgende Tags toevoegen aan een apparaat:

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

Zie voor meer informatie over apparaatdubbels en tags voor apparaten [inzicht in de apparaatdubbels in IOT hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Een implementatie maken

U implementeert modules op uw doel apparaten door een implementatie te maken die bestaat uit het implementatie manifest en andere para meters.

Gebruik de opdracht [AZ IOT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) om een implementatie te maken:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Gebruik dezelfde opdracht met de `--layered` vlag voor het maken van een gelaagde implementatie.

De opdracht voor het maken van de implementatie heeft de volgende para meters:

* **--laagst** : een optionele vlag voor het identificeren van de implementatie als een gelaagde implementatie.
* **--implementatie-id** : de naam van de implementatie die wordt gemaakt in de IOT-hub. Geef uw implementatie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /` . Vereiste parameter.
* **--Content** -filepath naar de JSON van het implementatie manifest. Vereiste parameter.
* **--hub-naam** -naam van de IOT-hub waarin de implementatie wordt gemaakt. De hub moet zich in het huidige abonnement benemen. Wijzig uw huidige abonnement met de `az account set -s [subscription name]` opdracht.
* **--labels** : Voeg labels toe om uw implementaties bij te houden. Labels zijn naam-, waardeparen die uw implementatie beschrijven. Labels maken de JSON-indeling voor de namen en waarden. Bijvoorbeeld: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--doel-condition** : Voer een doel voorwaarde in om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--Priority** : een positief geheel getal. In het geval van twee of meer implementaties op hetzelfde apparaat is de implementatie met de hoogste numerieke waarde voor prioriteit van toepassing.
* **--metrische gegevens** : Maak metrische gegevens die de edgeHub-gerapporteerde eigenschappen doorzoeken om de status van een implementatie bij te houden. Metrische gegevens hebben een JSON-invoer of een filepath. Bijvoorbeeld `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Zie [IOT Edge-implementaties controleren](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)voor het bewaken van een implementatie met behulp van Azure cli.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doel apparaten.

Als u de doel voorwaarde bijwerkt, worden de volgende updates uitgevoerd:

* Als een apparaat niet voldoet aan de oude doel voorwaarde, maar voldoet aan de nieuwe doel voorwaarde en deze implementatie de hoogste prioriteit voor dat apparaat is, wordt deze implementatie toegepast op het apparaat.
* Als een apparaat waarop deze implementatie momenteel wordt uitgevoerd niet meer voldoet aan de doel voorwaarde, wordt deze implementatie verwijderd en neemt de implementatie van de volgende met de hoogste prioriteit in beslag.
* Als een apparaat dat deze implementatie momenteel uitvoert niet meer voldoet aan de doel voorwaarde en niet voldoet aan de doel voorwaarde van andere implementaties, vindt er geen wijzigingen plaats op het apparaat. Het apparaat blijft de huidige modules in hun huidige staat uitvoeren, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra de app aan de doel voorwaarde van een andere implementatie voldoet, wordt deze implementatie verwijderd en wordt de nieuwe geïmplementeerd.

U kunt de inhoud van een implementatie niet bijwerken, die de modules en routes bevat die in het implementatie manifest zijn gedefinieerd. Als u de inhoud van een implementatie wilt bijwerken, kunt u dit doen door een nieuwe implementatie te maken die gericht is op dezelfde apparaten met een hogere prioriteit. U kunt bepaalde eigenschappen van een bestaande module wijzigen, met inbegrip van de doel voorwaarde, labels, metrische gegevens en prioriteit.

Gebruik de opdracht [AZ IOT Edge Deployment update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) om een implementatie bij te werken:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

De implementatie-update opdracht heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Overschakelen naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`
* **--set** -een eigenschap in de implementatie bijwerken. U kunt de volgende eigenschappen bijwerken:
  * targetCondition-bijvoorbeeld `targetCondition=tags.location.state='Oregon'`
  * Labels
  * priority
* **--Voeg** een nieuwe eigenschap toe aan de implementatie, met inbegrip van de doel voorwaarden of labels.
* **--Remove** -een bestaande eigenschap verwijderen, met inbegrip van de doel voorwaarden of labels.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, nemen alle apparaten de volgende implementatie van de hoogste prioriteit. Als uw apparaten niet voldoen aan de doel voorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd.

Gebruik de opdracht [AZ IOT Edge Deployment delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) om een implementatie te verwijderen:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht voor het verwijderen van de implementatie heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Overschakelen naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).