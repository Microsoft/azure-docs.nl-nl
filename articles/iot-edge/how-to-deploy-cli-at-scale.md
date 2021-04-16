---
title: Modules op schaal implementeren met behulp van Azure CLI - Azure IoT Edge
description: De IoT-extensie voor Azure CLI gebruiken om automatische implementaties te maken voor groepen IoT Edge apparaten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c502a9c02160c5a92d78ccdbb0532e6f173122da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479505"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Uw modules op IoT Edge schalen implementeren en bewaken met behulp van de Azure CLI

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Maak een **IoT Edge automatische implementatie met behulp** van de Azure-opdrachtregelinterface om lopende implementaties voor veel apparaten tegelijk te beheren. Automatische implementaties voor IoT Edge maken deel uit van de [functie voor automatisch](../iot-hub/iot-hub-automatic-device-management.md) apparaatbeheer van IoT Hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt bijhouden en indien nodig wijzigingen kunt aanbrengen.

Zie Understand [IoT Edge automatic deployments for single devices or at scale (Meer informatie over automatische implementaties voor individuele apparaten of op schaal) voor meer informatie.](module-deployment-monitoring.md)

In dit artikel gaat u Azure CLI en de IoT-extensie instellen. Vervolgens leert u hoe u modules implementeert op een set IoT Edge apparaten en hoe u de voortgang bewaakt met behulp van de beschikbare CLI-opdrachten.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* Een of meer IoT Edge apparaten.

  Als u nog geen apparaat IoT Edge ingesteld, kunt u er een maken op een virtuele Azure-machine. Volg de stappen in een van de quickstart-artikelen voor [Een virtueel Linux-apparaat](quickstart-linux.md) maken of [Een virtueel Windows-apparaat maken.](quickstart.md)

* [Azure CLI](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IoT-extensie voor Azure CLI.](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de module-tweelingen. Zie Meer informatie over het implementeren van modules en het maken van [routes in IoT Edge.](module-composition.md)

Als u modules wilt implementeren met behulp van Azure CLI, moet u het implementatiemanifest lokaal opslaan als een TXT-bestand. U gebruikt het bestandspad in de volgende sectie bij het uitvoeren van de opdracht om de configuratie op uw apparaat toe te passen.

Hier is een basisimplementatiemanifest met één module als voorbeeld:

>[!NOTE]
>Dit voorbeeld van een implementatiemanifest maakt gebruik van schemaversie 1.1 voor de IoT Edge agent en hub. Schemaversie 1.1 is uitgebracht samen met IoT Edge versie 1.0.10 en maakt functies mogelijk zoals opstartorder van modules en routeprioriteit.

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

Gelaagde implementaties zijn een type automatische implementatie dat op elkaar kan worden gestapeld. Zie Understand IoT Edge automatic deployments for single devices or at scale (Meer informatie over automatische implementaties voor één apparaat of op schaal) voor meer informatie [over gelaagde implementaties.](module-deployment-monitoring.md)

Gelaagde implementaties kunnen worden gemaakt en beheerd met de Azure CLI, net als bij elke automatische implementatie, met slechts enkele verschillen. Zodra een gelaagde implementatie is gemaakt, werkt dezelfde Azure CLI voor gelaagde implementaties hetzelfde als voor elke implementatie. Als u een gelaagde implementatie wilt maken, voegt u de `--layered` vlag toe aan de opdracht create.

Het tweede verschil zit in de constructie van het implementatiemanifest. Hoewel standaard automatische implementatie naast alle gebruikersmodules ook de systeemruntimemodules moet bevatten, kunnen gelaagde implementaties alleen gebruikersmodules bevatten. In plaats daarvan moeten gelaagde implementaties ook een standaard automatische implementatie op een apparaat hebben, om de vereiste onderdelen van elk IoT Edge-apparaat te leveren, zoals de systeemruntimemodules.

Hier is een eenvoudig gelaagd implementatiemanifest met één module als voorbeeld:

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

In het vorige voorbeeld werd een gelaagde implementatie-instelling voor `properties.desired` de voor een module getoond. Als deze gelaagde implementatie is gericht op een apparaat waarop dezelfde module al is toegepast, worden alle bestaande gewenste eigenschappen overschreven. Als u de gewenste eigenschappen wilt bijwerken in plaats van te overschrijven, kunt u een nieuwe subsectie definiëren. Bijvoorbeeld:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Zie Gelaagde implementatie voor meer informatie over het configureren van module-tweelingen in [gelaagde implementaties](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van tags

Voordat u een implementatie kunt maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met **behulp van tags** in de apparaat dubbel. Elk apparaat kan meerdere tags hebben die u definieert op een manier die zinvol is voor uw oplossing. Als u bijvoorbeeld een campus met slimme gebouwen beheert, kunt u de volgende tags toevoegen aan een apparaat:

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

Zie Apparaattweeling begrijpen en gebruiken in IoT Hub voor meer informatie [over apparaattweeling IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)

## <a name="create-a-deployment"></a>Een implementatie maken

U implementeert modules op uw doelapparaten door een implementatie te maken die bestaat uit het implementatiemanifest en andere parameters.

Gebruik de [opdracht az iot edge deployment create](/cli/azure/iot/edge/deployment) om een implementatie te maken:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Gebruik dezelfde opdracht met de vlag `--layered` om een gelaagde implementatie te maken.

De opdracht voor het maken van de implementatie heeft de volgende parameters:

* **--layered:** een optionele vlag om de implementatie te identificeren als een gelaagde implementatie.
* **--deployment-id:** de naam van de implementatie die wordt gemaakt in de IoT-hub. Geef uw implementatie een unieke naam van maximaal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /` . Vereiste parameter.
* **--content:** bestandspad naar de JSON van het implementatiemanifest. Vereiste parameter.
* **--hub-name:** de naam van de IoT-hub waarin de implementatie wordt gemaakt. De hub moet zich in het huidige abonnement. Wijzig uw huidige abonnement met de `az account set -s [subscription name]` opdracht .
* **--labels:** voeg labels toe om uw implementaties bij te houden. Labels zijn naam- en waardeparen die uw implementatie beschrijven. Labels gebruiken JSON-opmaak voor de namen en waarden. Bijvoorbeeld: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition:** voer een doelvoorwaarde in om te bepalen op welke apparaten deze implementatie wordt gericht. De voorwaarde is gebaseerd op tags van apparaattwee of gerapporteerde eigenschappen van apparaattwee en moet overeenkomen met de expressie-indeling. Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority:** een positief geheel getal. In het geval dat twee of meer implementaties op hetzelfde apparaat zijn gericht, is de implementatie met de hoogste numerieke waarde voor Prioriteit van toepassing.
* **--metrics:** maak metrische gegevens die een query uitvoeren op de gerapporteerde edgeHub-eigenschappen om de status van een implementatie bij te houden. Metrische gegevens gebruiken JSON-invoer of een bestandspad. Bijvoorbeeld `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Zie Implementaties bewaken om een implementatie met behulp van Azure CLI [IoT Edge bewaken.](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doelapparaten.

Als u de doelvoorwaarde bij werkt, worden de volgende updates uitgevoerd:

* Als een apparaat niet aan de oude doelvoorwaarde voldoet, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit voor dat apparaat heeft, wordt deze implementatie toegepast op het apparaat.
* Als een apparaat met deze implementatie niet langer voldoet aan de doelvoorwaarde, wordt deze implementatie verwijderd en wordt de implementatie met de volgende hoogste prioriteit uitgevoerd.
* Als een apparaat met deze implementatie momenteel niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van andere implementaties, wordt er geen wijziging op het apparaat uitgevoerd. Het apparaat blijft de huidige modules uitvoeren in hun huidige status, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra het voldoet aan de doelvoorwaarde van een andere implementatie, wordt deze implementatie verwijderd en wordt de nieuwe geïmplementeerd.

U kunt de inhoud van een implementatie, waaronder de modules en routes die zijn gedefinieerd in het implementatiemanifest, niet bijwerken. Als u de inhoud van een implementatie wilt bijwerken, maakt u een nieuwe implementatie die gericht is op dezelfde apparaten met een hogere prioriteit. U kunt bepaalde eigenschappen van een bestaande module wijzigen, waaronder de doelvoorwaarde, labels, metrische gegevens en prioriteit.

Gebruik de [opdracht az iot edge deployment update om](/cli/azure/iot/edge/deployment) een implementatie bij te werken:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

De opdracht voor het bijwerken van de implementatie heeft de volgende parameters:

* **--deployment-id:** de naam van de implementatie die in de IoT-hub bestaat.
* **--hub-name:** de naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`
* **--set:** werk een eigenschap bij in de implementatie. U kunt de volgende eigenschappen bijwerken:
  * targetCondition- bijvoorbeeld `targetCondition=tags.location.state='Oregon'`
  * labels
  * priority
* **--add:** voeg een nieuwe eigenschap toe aan de implementatie, inclusief doelvoorwaarden of labels.
* **--remove:** verwijder een bestaande eigenschap, inclusief doelvoorwaarden of labels.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, krijgen alle apparaten de eerstvolgende implementatie met de hoogste prioriteit. Als uw apparaten niet voldoen aan de doelvoorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd.

Gebruik de [opdracht az iot edge deployment delete](/cli/azure/iot/edge/deployment) om een implementatie te verwijderen:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht voor het verwijderen van de implementatie heeft de volgende parameters:

* **--deployment-id:** de naam van de implementatie die in de IoT-hub bestaat.
* **--hub-name:** de naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Modules implementeren op IoT Edge apparaten.](module-deployment-monitoring.md)