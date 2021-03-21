---
title: Azure IoT Edge en Azure IoT Central | Microsoft Docs
description: Meer informatie over het gebruik van Azure IoT Edge met een IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608628"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge-apparaten verbinden met een Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Azure IoT Edge kunt Cloud Analytics en aangepaste bedrijfs logica verplaatsen naar apparaten zodat uw organisatie zich kan concentreren op zakelijke inzichten in plaats van gegevens beheer. Breid uw IoT-oplossing uit door uw bedrijfs logica te verpakken in standaard containers, deze containers te implementeren op uw apparaten en ze te controleren vanuit de Cloud.

In dit artikel wordt het volgende beschreven:

* Hoe IoT Edge-apparaten verbinding maken met een IoT Central-toepassing.
* IoT Central gebruiken om uw IoT Edge-apparaten te beheren.

Zie [Wat is Azure IOT Edge?](../../iot-edge/about-iot-edge.md) voor meer informatie over IOT Edge.

## <a name="iot-edge"></a>IoT Edge

IoT Edge bestaat uit drie onderdelen:

* *IOT Edge modules* zijn containers waarop Azure-Services, partner services of uw eigen code worden uitgevoerd. Modules worden op IoT Edge apparaten geïmplementeerd en lokaal op deze apparaten uitgevoerd. Zie [Azure IOT Edge-modules begrijpen](../../iot-edge/iot-edge-modules.md)voor meer informatie.
* De *IOT Edge runtime* wordt op elk IOT edge apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd. De runtime bestaat uit twee IoT Edge modules: *IOT Edge agent* en *IOT Edge hub*. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](../../iot-edge/iot-edge-runtime.md)voor meer informatie.
* Met een *cloudinterface* kunt u op afstand de IoT Edge-apparaten controleren en beheren. IoT Central is een voor beeld van een Cloud interface.

Een IoT Edge apparaat kan zijn:

* Een zelfstandig apparaat dat bestaat uit modules.
* Een *gateway apparaat* met downstream-apparaten waarmee verbinding wordt gemaakt.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge als gateway

Een IoT Edge apparaat kan worden gebruikt als gateway die een verbinding biedt tussen andere downstream-apparaten in het netwerk en uw IoT Central toepassing.

Er zijn twee gateway patronen:

* In het *transparante gateway* patroon gedraagt de IOT Edge hub-module zich als IOT Central en verwerkt verbindingen van apparaten die zijn geregistreerd in IOT Central. Berichten worden door gegeven vanaf downstream-apparaten naar IoT Central alsof er geen gateway is.

* In het patroon van de *Vertaal gateway* worden apparaten die geen verbinding kunnen maken met IOT Central zelf, in plaats daarvan verbinding maken met een aangepaste IOT Edge module. Met de module in het IoT Edge apparaat worden binnenkomende downstream-apparaten verwerkt en vervolgens doorgestuurd naar IoT Central.

De transparante en vertaal gateway patronen sluiten elkaar niet uit. Eén IoT Edge apparaat kan worden gebruikt als een transparante gateway en een Vertaal gateway.

Zie [hoe een IOT edge apparaat kan worden gebruikt als een gateway](../../iot-edge/iot-edge-as-gateway.md)voor meer informatie over de IOT Edge gateway-patronen.

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>De relaties van downstream-apparaten met een gateway en modules

Downstream-apparaten kunnen verbinding maken met een IoT Edge gateway-apparaat via de *IOT Edge hub*  -module. In dit scenario is het IoT Edge apparaat een transparante gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagram van transparante gateway" border="false":::

Downstream-apparaten kunnen ook via een aangepaste module verbinding maken met een IoT Edge gateway-apparaat. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste *Modbus* -module. In dit scenario is het IoT Edge apparaat een Vertaal gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagram van de verbinding van de aangepaste module" border="false":::

In het volgende diagram ziet u verbindingen met een IoT Edge gateway apparaat via beide typen modules. In dit scenario is het IoT Edge apparaat zowel een transparante als een Vertaal gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagram van verbinding maken met behulp van beide verbindings modules" border="false":::

Downstream-apparaten kunnen via meerdere aangepaste modules verbinding maken met een IoT Edge gateway-apparaat. In het volgende diagram worden downstream-apparaten weer gegeven die verbinding maken via een aangepaste Modbus-module, een afgeleide aangepaste module en de module *IOT Edge hub*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagram van verbinding maken met behulp van meerdere aangepaste modules" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge apparaten en IoT Central

IoT Edge apparaten kunnen *Shared Access Signature* -tokens of X. 509-certificaten gebruiken voor verificatie met IOT Central. U kunt uw IoT Edge-apparaten hand matig registreren in IoT Central voordat ze voor de eerste keer verbinding maken, of de Device Provisioning Service gebruiken om de registratie te verwerken. Zie [Get connected to Azure IoT Central](concepts-get-connected.md) (Verbinding maken met Azure IoT Central) voor meer informatie.

IoT Central maakt gebruik van [device-sjablonen](concepts-device-templates.md) om te definiëren hoe IOT Central interactie met een apparaat. Een sjabloon voor een apparaat geeft bijvoorbeeld het volgende op:

* De typen telemetrie en eigenschappen die een apparaat verzendt zodat IoT Central ze kunnen interpreteren en visualisaties kunt maken.
* De opdrachten waarmee een apparaat reageert, zodat IoT Central een gebruikers interface kan weer geven voor een operator die wordt gebruikt om de opdrachten aan te roepen.

Een IoT Edge apparaat kan telemetrie verzenden, eigenschapwaarden synchroniseren en op dezelfde manier reageren op opdrachten als een standaard apparaat. Een IoT Edge apparaat heeft dus een sjabloon nodig in IoT Central.

### <a name="iot-edge-device-templates"></a>IoT Edge

IoT Central-apparaatprofielen modellen gebruiken om de mogelijkheden van apparaten te beschrijven. In het volgende diagram ziet u de structuur van het model voor een IoT Edge apparaat:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Structuur van het model voor IoT Edge apparaat dat is verbonden met IoT Central" border="false":::

IoT Central modellen een IoT Edge apparaat als volgt:

* Elke IoT Edge-apparaatprofiel heeft een functionaliteits model.
* Voor elke aangepaste module die wordt vermeld in het implementatie manifest, wordt een module mogelijkheidsprofiel gegenereerd.
* Er wordt een relatie tot stand gebracht tussen elk module functionaliteits model en een model apparaat.
* Een module-functionaliteits model implementeert een of meer module interfaces.
* Elke module interface bevat telemetrie, eigenschappen en opdrachten.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge implementatie manifesten en IoT Central-sjablonen

In IoT Edge kunt u bedrijfs logica in de vorm van modules implementeren en beheren. IoT Edge modules zijn de kleinste reken eenheid die wordt beheerd door IoT Edge en die Azure-Services, zoals Azure Stream Analytics, of uw eigen oplossings code kunnen bevatten.

Een IoT Edge *implementatie manifest* bevat een lijst met IOT Edge modules die op het apparaat moeten worden geïmplementeerd en hoe ze kunnen worden geconfigureerd. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](../../iot-edge/module-composition.md)voor meer informatie.

In azure IoT Central importeert u een implementatie manifest om een sjabloon voor het IoT Edge apparaat te maken.

Het volgende code fragment toont een voor beeld IoT Edge implementatie manifest:

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

In het vorige code fragment ziet u het volgende:

* Er zijn drie modules. De *IOT Edge agent* -en *IOT Edge hub* -systeem modules die aanwezig zijn in elk implementatie manifest. De aangepaste **SimulatedTemperatureSensor** -module.
* De installatie kopieën van de open bare module worden opgehaald uit een Azure Container Registry-opslag plaats waarvoor geen referenties zijn vereist om verbinding te maken. Stel voor installatie kopieën van persoonlijke modules de container register referenties in voor gebruik in de `registryCredentials` instelling voor de module *IOT Edge agent* .
* De aangepaste **SimulatedTemperatureSensor** -module heeft twee eigenschappen `"SendData": true` en `"SendInterval": 10` .

Wanneer u dit implementatie manifest in een IoT Central-toepassing importeert, wordt de volgende Device-sjabloon gegenereerd:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Scherm afbeelding met de sjabloon die is gemaakt in het implementatie manifest.":::

In de vorige scherm afbeelding ziet u het volgende:

* Een module met de naam **SimulatedTemperatureSensor**. De systeem modules *IOT Edge agent* en *IOT Edge hub* worden niet weer gegeven in de sjabloon.
* Een interface met de naam **beheer** met twee Beschrijf bare eigenschappen met de naam **SendData** en **SendInterval**.

Het implementatie manifest bevat geen informatie over de telemetrie die de **SimulatedTemperatureSensor** -module verzendt of de opdrachten waarop deze reageert. Voeg deze definities hand matig toe aan de sjabloon voor het apparaat voordat u het publiceert.

Zie [zelf studie: een Azure IOT edge apparaat toevoegen aan uw Azure IOT Central-toepassing](tutorial-add-edge-as-leaf-device.md)voor meer informatie.

### <a name="update-a-deployment-manifest"></a>Een implementatie manifest bijwerken

Als u een nieuwe [versie](howto-version-device-template.md) van de sjabloon voor het apparaat maakt, kunt u het implementatie manifest vervangen door een nieuwe versie:

Wanneer u het implementatie manifest vervangt, downloaden alle verbonden IoT Edge-apparaten het nieuwe manifest en updaten hun modules. Met IoT Central worden de interfaces in de sjabloon van het apparaat echter niet bijgewerkt met wijzigingen in de configuratie van de module. Als u bijvoorbeeld het manifest in het vorige code fragment vervangt door het volgende manifest, ziet u niet automatisch de eigenschap **SendUnits** in de **beheer** interface in de sjabloon van het apparaat. Voeg de nieuwe eigenschap hand matig toe aan de **beheer** interface voor IOT Central om deze te herkennen:

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>De IoT Edge-runtime implementeren

Zie [Azure IOT Edge ondersteunde systemen](../../iot-edge/support.md)voor meer informatie over het uitvoeren van de IOT Edge runtime.

U kunt de IoT Edge runtime ook installeren in de volgende omgevingen:

* [Azure IoT Edge voor Linux installeren of verwijderen](../../iot-edge/how-to-install-iot-edge.md)
* [Azure IoT Edge voor Linux installeren en inrichten op een Windows-apparaat (preview)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure IoT Edge uitvoeren op Ubuntu Virtual Machines in azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge gateway-apparaten

Als u een IoT Edge apparaat hebt geselecteerd om een gateway apparaat te zijn, kunt u downstream-relaties toevoegen aan apparaten modellen voor apparaten die u wilt verbinden met het gateway apparaat.

Zie [apparaten verbinden via een IOT Edge transparante gateway](how-to-connect-iot-edge-transparent-gateway.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, is een voorgestelde volgende stap het [ontwikkelen van uw eigen IOT Edge-modules](../../iot-edge/module-development.md).
