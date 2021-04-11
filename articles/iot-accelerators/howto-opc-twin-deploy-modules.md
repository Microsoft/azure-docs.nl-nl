---
title: Een volledig nieuwe OPC-dubbele module voor Azure implementeren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de OPC twee ledig kunt implementeren met behulp van de IoT Edge Blade van de Azure Portal en ook met AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 38235f9b01b321e27664ee837763732971f0b85c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646168"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC dubbele module en afhankelijkheden helemaal zelf implementeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

De OPC-dubbele module wordt uitgevoerd op IoT Edge en biedt verschillende Edge-services aan de OPC Device-dubbele en register Services. 

Er zijn verschillende opties voor het implementeren van modules op uw [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) gateway.

- [Implementeren vanaf de IoT Edge Blade van Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Implementeren met AZ CLI](../iot-edge/how-to-deploy-cli-at-scale.md)

> [!NOTE]
> Zie de GitHub- [opslag plaats](https://github.com/Azure/azure-iiot-components)voor meer informatie over de implementatie details en instructies.

## <a name="deployment-manifest"></a>Distributiemanifest

Alle modules worden geïmplementeerd met een implementatie manifest.  Hieronder ziet u een voor beeld van een manifest voor het implementeren van de [OPC-Uitgever](https://github.com/Azure/iot-edge-opc-publisher) en de [OPC dubbele](https://github.com/Azure/azure-iiot-opc-twin-module) .

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--tm\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implementeren vanaf Azure Portal

De eenvoudigste manier om de modules op een Azure IoT Edge gateway-apparaat te implementeren, is via de Azure Portal.  

### <a name="prerequisites"></a>Vereisten

1. Implementeer de OPC-dubbele [afhankelijkheden](howto-opc-twin-deploy-dependencies.md) en haal het resulterende `.env` bestand op. Houd er rekening mee `hub name` dat de `PCS_IOTHUBREACT_HUB_NAME` variabele in het resulterende bestand is geïmplementeerd `.env` .

2. Registreer en start een [Linux](../iot-edge/how-to-install-iot-edge.md) -of [Windows](../iot-edge/how-to-install-iot-edge.md) IOT Edge gateway en noteer deze `device id` .

### <a name="deploy-to-an-edge-device"></a>Implementeren op een edge-apparaat

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en ga naar uw IoT Hub.

2. Selecteer **IOT Edge** in het menu aan de linkerkant.

3. Klik op de ID van het doel apparaat in de lijst met apparaten.

4. Selecteer **modules instellen**.

5. Selecteer in de sectie **implementatie modules** van de pagina de module **toevoegen** en **IOT Edge.**

6. In het dialoog venster **aangepaste Module IOT Edge** gebruikt u de `opctwin` naam voor de module en geeft u vervolgens de URI van de container *installatie kopie* op als

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Gebruik de volgende JSON als *container opties* voor het maken van containers:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Vul zo nodig de optionele velden in. Zie [EdgeAgent gewenste eigenschappen](../iot-edge/module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over de opties voor het maken van containers, het opnieuw opstarten van beleid en de gewenste status. Zie voor meer informatie over de module twee [gewenste eigenschappen definiëren of bijwerken](../iot-edge/module-composition.md#define-or-update-desired-properties).

7. Selecteer **Opslaan** en herhaal stap **5**.  

8. Gebruik in het dialoog venster aangepaste module IoT Edge `opcpublisher` als naam voor de module en de URI van de container *installatie kopie* als 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Gebruik de volgende JSON als *container opties* voor het maken van containers:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--tm","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecteer **Opslaan** en klik vervolgens op **volgende** om door te gaan naar de sectie routes.

10. Plak het volgende op het tabblad routes: 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    en selecteer **volgende**

11. Controleer uw implementatie-informatie en-manifest.  Deze moet eruitzien als in het bovenstaande implementatie manifest.  Selecteer **Indienen**.

12. Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven op de pagina **Details van apparaat** van de portal. Op deze pagina ziet u de naam van elke geïmplementeerde module, evenals nuttige informatie, zoals de implementatie status en afsluit code.

## <a name="deploying-using-azure-cli"></a>Implementeren met behulp van Azure CLI

### <a name="prerequisites"></a>Vereisten

1. Installeer [hier](/cli/azure/install-azure-cli)de nieuwste versie van de [Azure-opdracht regel interface (AZ)](/cli/azure/) .

### <a name="quickstart"></a>Snelstart

1. Sla het bovenstaande implementatie manifest op in een `deployment.json` bestand.  

2. Gebruik de volgende opdracht om de configuratie toe te passen op een IoT Edge apparaat:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   De `device id` para meter is hoofdletter gevoelig. De inhouds parameter verwijst naar het manifest bestand van de implementatie dat u hebt opgeslagen. 
    ![AZ IoT Edge set-modules output](/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven met de volgende opdracht:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   De para meter voor de apparaat-ID is hoofdletter gevoelig. ![AZ IOT hub module-uitvoer van de lijst met identiteiten](/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC twee ledig kunt implementeren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC van twee tot een bestaand project implementeren](howto-opc-twin-deploy-existing.md)