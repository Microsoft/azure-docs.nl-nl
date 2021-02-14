---
title: Modules implementeren vanuit de Azure CLI-opdracht regel-Azure IoT Edge
description: Gebruik de Azure-CLI met de Azure IoT-extensie om een IoT Edge module van uw IoT Hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatie manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 6e4782ad25886c4121742634e9e73f62ff4f0a2c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370237"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure IoT Edge-modules implementeren met Azure CLI

Wanneer u IoT Edge modules met uw bedrijfs logica hebt gemaakt, wilt u deze implementeren op uw apparaten zodat ze aan de rand kunnen worden uitgevoerd. Als u meerdere modules hebt die samen werken om gegevens te verzamelen en te verwerken, kunt u ze allemaal tegelijk implementeren en de routerings regels declareren waarmee ze verbinding maken.

[Azure cli](/cli/azure) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT Edge. U kunt hiermee Azure IoT Hub-resources, Device Provisioning Service-instanties en gekoppelde-hubs uit het vak beheren. De nieuwe IoT-extensie verrijkt Azure CLI met functies als Apparaatbeheer en volledige IoT Edge mogelijkheid.

In dit artikel wordt beschreven hoe u een JSON-implementatie manifest maakt en dat bestand gebruikt om de implementatie naar een IoT Edge apparaat te pushen. Voor informatie over het maken van een implementatie die is gericht op meerdere apparaten op basis van hun gedeelde labels, Zie [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* Een IoT Edge apparaat

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

* [Azure cli](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.

Als u modules wilt implementeren met behulp van de Azure CLI, moet u het implementatie manifest lokaal opslaan als een. JSON-bestand. Wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat, gebruikt u het bestandspad in de volgende sectie.

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
              "version": "1.0",
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
          "schemaVersion": "1.1",
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

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeert modules op uw apparaat door het implementatie manifest toe te passen dat u met de module gegevens hebt geconfigureerd.

Wijzig de mappen in de map waarin uw implementatie manifest is opgeslagen. Als u een van de VS code IoT Edge sjablonen hebt gebruikt, gebruikt u het `deployment.json` bestand in de map **config** van uw map met oplossingen en niet in het `deployment.template.json` bestand.

Gebruik de volgende opdracht om de configuratie toe te passen op een IoT Edge apparaat:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

De para meter voor de apparaat-ID is hoofdletter gevoelig. De inhouds parameter verwijst naar het manifest bestand van de implementatie dat u hebt opgeslagen.

   ![AZ IOT Edge set-modules output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modules op uw apparaat weer geven

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven met de volgende opdracht:

De modules op uw IoT Edge-apparaat bekijken:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

De para meter voor de apparaat-ID is hoofdletter gevoelig.

   ![AZ IOT hub module-uitvoer van de lijst met identiteiten](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal](how-to-deploy-at-scale.md)