---
title: Module & routes implementeren met implementatie manifesten-Azure IoT Edge
description: Meer informatie over hoe een implementatie manifest de modules declareert die moeten worden geïmplementeerd, hoe ze moeten worden geïmplementeerd en hoe u er bericht routes tussen maakt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406420fcd517ceda8ea6eedfc955f54b15541f74
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366599"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Meer informatie over het implementeren van modules en het vaststellen van routes naar IoT Edge

Elk IoT Edge-apparaat voert ten minste twee modules uit: $edgeAgent en $edgeHub, die deel uitmaken van de IoT Edge-runtime. IoT Edge apparaat kan meerdere extra modules uitvoeren voor elk aantal processen. Gebruik een implementatie manifest om uw apparaat te laten weten welke modules moeten worden geïnstalleerd en hoe ze moeten worden geconfigureerd om samen te werken.

Het *implementatie manifest* is een JSON-document met een beschrijving van:

* De **IOT Edge agent** module, die drie onderdelen omvat:
  * De container installatie kopie voor elke module die op het apparaat wordt uitgevoerd.
  * De referenties voor toegang tot persoonlijke container registers die module-installatie kopieën bevatten.
  * Instructies voor het maken en beheren van elke module.
* De **IOT Edge hub** -module, met inbegrip van hoe berichten stromen tussen modules en uiteindelijk naar IOT hub.
* De gewenste eigenschappen van een extra module apparaatdubbels (optioneel).

Alle IoT Edge apparaten moeten worden geconfigureerd met een implementatie manifest. Een nieuw geïnstalleerde IoT Edge-runtime rapporteert een fout code totdat deze is geconfigureerd met een geldig manifest.

In de Azure IoT Edge zelf studies maakt u een implementatie manifest door een wizard in de Azure IoT Edge portal te door lopen. U kunt ook een implementatie manifest programmatisch Toep assen met behulp van REST of de IoT Hub Service-SDK. Zie [IOT Edge-implementaties begrijpen](module-deployment-monitoring.md)voor meer informatie.

## <a name="create-a-deployment-manifest"></a>Een implementatiemanifest maken

Op hoog niveau is een implementatie manifest een lijst met module apparaatdubbels die zijn geconfigureerd met de gewenste eigenschappen. Een implementatie manifest vertelt een IoT Edge apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe ze moeten worden geconfigureerd. Implementatie manifesten bevatten de *gewenste eigenschappen* voor elke module dubbele. IoT Edge-apparaten melden de *gerapporteerde eigenschappen* voor elke module terug.

In elk implementatie manifest zijn twee modules vereist: `$edgeAgent` , en `$edgeHub` . Deze modules maken deel uit van de IoT Edge runtime waarmee het IoT Edge apparaat en de modules worden beheerd die erop worden uitgevoerd. Zie voor meer informatie over deze modules [inzicht in de runtime van IOT Edge en de bijbehorende architectuur](iot-edge-runtime.md).

Naast de twee runtime modules kunt u Maxi maal 50 modules van uw eigen toepassing toevoegen voor uitvoering op een IoT Edge apparaat.

Een implementatie manifest dat alleen de IoT Edge runtime (edgeAgent en edgeHub) bevat, is geldig.

Implementatie manifesten volgen deze structuur:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Modules configureren

Definieer hoe de IoT Edge runtime de modules in uw implementatie installeert. De IoT Edge-agent is het runtime-onderdeel waarmee de installatie, updates en status rapportage voor een IoT Edge apparaat worden beheerd. Daarom bevat de $edgeAgent-module twee informatie over de configuratie en het beheer van alle modules. Deze informatie bevat de configuratie parameters voor de IoT Edge agent zelf.

De $edgeAgent eigenschappen volgen deze structuur:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

De IoT Edge agent-schema versie 1,1 is uitgebracht samen met IoT Edge versie 1.0.10 en maakt de module opstart volgorde mogelijk. Schema versie 1,1 wordt aanbevolen voor alle IoT Edge implementaties waarop versie 1.0.10 of hoger wordt uitgevoerd.

### <a name="module-configuration-and-management"></a>Module configuratie en-beheer

In de lijst met gewenste eigenschappen van IoT Edge agent definieert u welke modules op een IoT Edge apparaat worden geïmplementeerd en hoe deze moeten worden geconfigureerd en beheerd.

Zie [Eigenschappen van de IOT Edge agent en IOT Edge hub](module-edgeagent-edgehub.md)voor een volledige lijst met gewenste eigenschappen die kunnen of moeten worden opgenomen.

Bijvoorbeeld:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Elke module heeft een **instellingen** -eigenschap met de module **installatie kopie**, een adres voor de container installatie kopie in een container register en een wille keurige **createOptions** om de installatie kopie te configureren bij het opstarten. Zie voor meer informatie [container maken opties voor IOT Edge modules configureren](how-to-use-create-options.md).

De edgeHub-module en aangepaste modules hebben ook drie eigenschappen die de IoT Edge agent laten zien hoe ze ze kunnen beheren:

* **Status**: of de module moet worden uitgevoerd of wordt gestopt bij de eerste implementatie. Vereist.
* **RestartPolicy**: wanneer en de IOT Edge-agent de module opnieuw moet opstarten als deze wordt gestopt. Vereist.
* **StartupOrder**: *geïntroduceerd in IOT Edge versie 1.0.10.* In welke volg orde de IoT Edge agent de modules moet starten bij de eerste implementatie. De volg orde wordt gedeclareerd met gehele getallen, waarbij een module met een opstart waarde van 0 eerst wordt gestart en vervolgens een hoger nummer wordt weer gegeven. De edgeAgent-module heeft geen opstart waarde omdat deze altijd eerst wordt gestart. Optioneel.

  De IoT Edge-agent initieert de modules in volg orde van de opstart waarde, maar wacht niet totdat elke module is gestart voordat u naar de volgende gaat.

  Opstart volgorde is handig als sommige modules afhankelijk zijn van anderen. U kunt bijvoorbeeld de edgeHub-module eerst starten, zodat deze klaar is om berichten te routeren wanneer de andere modules worden gestart. Het is ook mogelijk dat u een opslag module wilt starten vóór de modules die er gegevens naar verzenden. U moet echter altijd uw modules ontwerpen om fouten van andere modules af te handelen. Het is de aard van de containers die ze op elk gewenst moment kunnen stoppen en opnieuw opstarten en een wille keurig aantal keren.

## <a name="declare-routes"></a>Routes declareren

De IoT Edge hub beheert de communicatie tussen modules, IoT Hub en alle blad apparaten. Daarom bevat de $edgeHub-module twee een gewenste eigenschap met de naam *routes* die declareert hoe berichten worden door gegeven in een implementatie. U kunt meerdere routes binnen dezelfde implementatie hebben.

Routes worden in de gewenste **$edgeHub** eigenschappen gedeclareerd met de volgende syntaxis:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

De IoT Edge-hub-schema versie 1,1 is uitgebracht samen met IoT Edge versie 1.0.10, en biedt prioriteit van route ring en time to Live. Schema versie 1,1 wordt aanbevolen voor alle IoT Edge implementaties waarop versie 1.0.10 of hoger wordt uitgevoerd.

Elke route moet een *bron* zijn van waaruit de berichten afkomstig zijn en een *sink* waar de berichten naartoe gaan. De *voor waarde* is een optioneel stuk dat u kunt gebruiken om berichten te filteren.

U kunt *prioriteit* toewijzen aan routes die u ervoor wilt zorgen dat ze eerst hun berichten verwerken. Deze functie is handig in scenario's waarin de upstream-verbinding zwak of beperkt is en u kritieke gegevens hebt die moeten worden geprioriteerd via standaard-telemetrie-berichten.

### <a name="source"></a>Bron

De bron geeft aan waaruit de berichten afkomstig zijn. IoT Edge kunt berichten van modules of blad apparaten routeren.

Met behulp van de IoT-Sdk's kunnen modules specifieke uitvoer wachtrijen voor hun berichten declareren met behulp van de ModuleClient-klasse. Uitvoer wachtrijen zijn niet nodig, maar zijn handig voor het beheren van meerdere routes. Blade apparaten kunnen de DeviceClient-klasse van de IoT-Sdk's gebruiken om berichten te verzenden naar IoT Edge gateway-apparaten op dezelfde manier als waarop ze berichten verzenden naar IoT Hub. Zie voor meer informatie [Azure IOT hub-Sdk's begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md).

De eigenschap source kan een van de volgende waarden hebben:

| Bron | Beschrijving |
| ------ | ----------- |
| `/*` | Alle apparaat-naar-Cloud-berichten of dubbele wijzigings meldingen van een module of Leaf-apparaat |
| `/twinChangeNotifications` | Elke dubbele wijziging (gerapporteerde eigenschappen) die afkomstig is uit een module of Leaf-apparaat |
| `/messages/*` | Een apparaat-naar-Cloud-bericht dat door een module wordt verzonden via een of geen uitvoer of door een Leaf-apparaat |
| `/messages/modules/*` | Een apparaat-naar-Cloud-bericht dat door een module wordt verzonden via een of geen uitvoer |
| `/messages/modules/<moduleId>/*` | Een apparaat-naar-Cloud-bericht dat door een bepaalde module wordt verzonden via een of geen uitvoer |
| `/messages/modules/<moduleId>/outputs/*` | Een apparaat-naar-Cloud-bericht dat door een bepaalde module wordt verzonden via een bepaalde uitvoer |
| `/messages/modules/<moduleId>/outputs/<output>` | Een apparaat-naar-Cloud-bericht dat door een specifieke module wordt verzonden via een specifieke uitvoer |

### <a name="condition"></a>Voorwaarde

De voor waarde is optioneel in een route declaratie. Als u alle berichten van de bron wilt door geven aan de sink, laat u de **where** -component gewoon weg. U kunt ook de [IOT hub query taal](../iot-hub/iot-hub-devguide-routing-query-syntax.md) gebruiken om te filteren op bepaalde berichten of bericht typen die voldoen aan de voor waarde. IoT Edge routes bieden geen ondersteuning voor het filteren van berichten op basis van dubbele Tags of eigenschappen.

De berichten die tussen modules in IoT Edge worden door gegeven, hebben dezelfde indeling als de berichten die worden door gegeven tussen uw apparaten en Azure IoT Hub. Alle berichten worden ingedeeld als JSON en hebben **systemProperties**-, **appProperties**-en **Body** -para meters.

U kunt query's maken rond een van de drie para meters met de volgende syntaxis:

* Systeem eigenschappen: `$<propertyName>` of `{$<propertyName>}`
* Toepassings eigenschappen: `<propertyName>`
* Eigenschappen van hoofd tekst: `$body.<propertyName>`

Zie voor beelden van het maken van query's voor bericht eigenschappen [apparaat-naar-Cloud-bericht routes query-expressies](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Een voor beeld dat specifiek is voor IoT Edge is wanneer u wilt filteren op berichten die op een gateway apparaat van een blad apparaat zijn ontvangen. Berichten die afkomstig zijn uit modules, bevatten een systeem eigenschap met de naam **connectionModuleId**. Als u berichten van de Blade apparaten rechtstreeks naar IoT Hub wilt routeren, gebruikt u de volgende route om module berichten uit te sluiten:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

De Sink definieert waar de berichten worden verzonden. Alleen modules en IoT Hub kunnen berichten ontvangen. Berichten kunnen niet naar andere apparaten worden doorgestuurd. De eigenschap Sink bevat geen joker teken opties.

De eigenschap Sink kan een van de volgende waarden hebben:

| Sink | Description |
| ---- | ----------- |
| `$upstream` | Het bericht naar de IoT Hub verzenden |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Het bericht naar een specifieke invoer van een specifieke module verzenden |

IoT Edge biedt ten minste één keer garanties. De IoT Edge hub slaat berichten lokaal op als een route het bericht niet aan de Sink kan verzenden. Bijvoorbeeld, als de IoT Edge hub geen verbinding kan maken met IoT Hub of de doel module niet is verbonden.

IoT Edge hub worden de berichten opgeslagen tot de tijd die is opgegeven in de `storeAndForwardConfiguration.timeToLiveSecs` eigenschap van de [gewenste eigenschappen van de IOT Edge hub](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Prioriteit en time-to-Live

Routes kunnen worden gedeclareerd met alleen een teken reeks die de route definieert, of als een object dat een route teken reeks, een geheel getal voor de prioriteit en een time-to-Live integer heeft.

Optie 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Optie 2, geïntroduceerd in IoT Edge versie 1.0.10 met IoT Edge hub-schema versie 1,1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**Prioriteits** waarden kunnen 0-9, inclusief, zijn, waarbij 0 de hoogste prioriteit is. Berichten worden in een wachtrij geplaatst op basis van hun eind punten. Alle prioriteits 0-berichten die zijn gericht op een specifiek eind punt worden verwerkt voordat de prioriteit 1 berichten die zijn gericht op hetzelfde eind punt worden verwerkt, en de lijn omlaag. Als meerdere routes voor hetzelfde eind punt dezelfde prioriteit hebben, worden hun berichten verwerkt in een eerste keer dat ze worden geleverd. Als er geen prioriteit is opgegeven, wordt de route toegewezen aan de laagste prioriteit.

De eigenschap **timeToLiveSecs** neemt de waarde over van de **storeAndForwardConfiguration** van IOT Edge hub, tenzij dit expliciet is ingesteld. De waarde kan elk positief geheel getal zijn.

Zie de pagina met verwijzingen voor [route prioriteit en time-to-Live](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)voor meer informatie over hoe prioriteits wachtrijen worden beheerd.

## <a name="define-or-update-desired-properties"></a>Gewenste eigenschappen definiëren of bijwerken

In het implementatie manifest worden de gewenste eigenschappen opgegeven voor elke module die is geïmplementeerd op het IoT Edge apparaat. Gewenste eigenschappen in het implementatie manifest overschrijven alle gewenste eigenschappen in de module dubbele.

Als u de gewenste eigenschappen van de module niet in het implementatie manifest opgeeft, wordt IoT Hub de module niet op enigerlei manier gewijzigd. In plaats daarvan kunt u de gewenste eigenschappen programmatisch instellen.

Dezelfde mechanismen waarmee u apparaatdubbels kunt wijzigen, worden gebruikt voor het wijzigen van module apparaatdubbels. Zie de [module dubbele ontwikkelaars handleiding](../iot-hub/iot-hub-devguide-module-twins.md)voor meer informatie.

## <a name="deployment-manifest-example"></a>Voor beeld van implementatie manifest

In het volgende voor beeld ziet u hoe een geldig manifest document van de implementatie eruit kan zien.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Eigenschappen van de IOT Edge agent en IOT Edge hub](module-edgeagent-edgehub.md)voor een volledige lijst met eigenschappen die kunnen of moeten worden opgenomen in $edgeAgent en $edgeHub.

* Nu u weet hoe IoT Edge-modules worden gebruikt, [begrijpt u de vereisten en hulpprogram ma's voor het ontwikkelen van IOT Edge modules](module-development.md).
