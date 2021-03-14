---
title: 'Zelfstudie: Aangepaste modules maken en implementeren - Machine Learning op Azure IoT Edge'
description: Deze zelfstudie laat zien hoe u IoT Edge-modules maakt en implementeert waarmee gegevens van bladknooppuntapparaten worden verwerkt via een Machine Learning-model en vervolgens de inzichten naar IoT Hub verzenden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c096b26f31bdf92bb5ab91c8dad7876f228ff14
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462793"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Zelfstudie: Aangepaste IoT Edge-modules maken en implementeren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In dit artikel maken we drie IoT Edge-modules die berichten ontvangen van bladknooppuntapparaten van IoT, de gegevens via uw Machine Learning-model uitvoeren en inzichten naar de IoT Hub doorsturen.

IoT Edge-hub vereenvoudigt de communicatie tussen modules. Als u de IoT Edge-hub als berichtenbroker gebruikt, blijven de modules onafhankelijk van elkaar. Modules hoeven alleen de invoer op te geven waarmee de berichten worden geaccepteerd en de uitvoer waarmee ze berichten schrijven.

We willen dat het IoT Edge-apparaat vier dingen voor ons doet:

* Gegevens ontvangen van de bladknooppuntapparaten.
* De resterende levensduur voorspellen voor het apparaat dat de gegevens heeft verzonden.
* Een bericht met de resterende levensduur voor het apparaat verzenden naar IoT Hub. Deze functie kan worden gewijzigd om alleen gegevens te verzenden als de resterende levensduur onder een opgegeven niveau daalt.
* Sla de gegevens van het bladknooppuntapparaat op in een lokaal bestand op het IoT Edge-apparaat. Dit gegevensbestand wordt regelmatig geüpload naar IoT Hub om de training van het Machine Learning-model te verfijnen. Het is rendabeler om bestanden te uploaden dan constante berichten te streamen.

We gebruiken drie aangepaste modules om deze taken uit te voeren:

* **RUL-classificatie:** De turboFanRulClassifier-module die u hebt gemaakt in [Een Azure Machine Learning-model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md) is een standaard Machine Learning-module, waarmee een invoer met de naam 'amlInput' en een uitvoer met de naam 'amlOutput' wordt weer gegeven. Er wordt verwacht dat de invoer van 'amlInput' precies hetzelfde is als de invoer die we hebben verzonden naar de ACI-gebaseerde webservice. Op dezelfde manier retourneert 'amlOutput' dezelfde gegevens als de webservice.

* **Avro-schrijver:** Deze module ontvangt berichten over de invoer van 'avroModuleInput' en bewaart het bericht in de Avro-indeling op de schijf om deze later te uploaden naar IoT Hub.

* **Routermodule:** De routermodule ontvangt berichten van downstreambladknooppuntapparaten, vervolgens worden de berichten opgemaakt en verzonden naar de classificatie. De module ontvangt vervolgens de berichten van de classificatie en stuurt het bericht door naar de Avro-schrijfmodule. Ten slotte verzendt de module alleen de resterende voorspelling van de levensduur naar de IoT Hub.

  * Invoer:
    * **deviceInput**: berichten ontvangen van bladknooppuntapparaten
    * **rulInput:** ontvangt berichten van de 'amlOutput'

  * Uitvoer:
    * **classificeren:** stuurt berichten naar 'amlInput'
    * **writeAvro:** stuurt berichten naar 'avroModuleInput'
    * **toIotHub:** stuurt berichten naar $upstream, waarmee de berichten worden doorgestuurd naar de verbonden IoT Hub

Het volgende diagram toont de module, invoer, uitvoer en de IoT Edge-hubroutes voor de volledige solutie:

![Architectuurdiagram van drie modules IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

De stappen in dit artikel worden doorgaans uitgevoerd door een cloud-ontwikkelaar.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een IoT Edge-module maken met behulp van aangepaste code.
> * Een Docker-installatiekopie maken vanuit de aangepaste module.
> * IoT Hub-routering opnieuw configureren om ondersteuning te bieden voor uw aangepaste modules.
> * Aangepaste modules bouwen, publiceren en implementeren.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="create-a-new-iot-edge-solution"></a>Een nieuwe IoT Edge-oplossing maken

Tijdens de uitvoering van de tweede van onze twee Azure Notebooks hebben we een containerinstallatiekopie gemaakt en gepubliceerd met het RUL-model. Azure Machine Learning, als onderdeel van het proces voor het maken van afbeeldingen, heeft dat model zo verpakt dat de afbeelding als een Azure IoT Edge-module kan worden ingezet.

In deze stap gaan we een Azure IoT Edge-oplossing maken met behulp van de 'Azure Machine Learning' en de module wijzen op de afbeelding die we hebben gepubliceerd met behulp van Azure Notebooks.

1. Open een extern-bureaubladsessie naar je ontwikkelings-VM.

1. Open map **C:\\bron\\IoTEdgeAndMlSample** in Visual Studio Code.

1. Klik met de rechtermuisknop op het venster Explorer (in de lege ruimte) en selecteer **Nieuwe IoT Edge-oplossing**.

    ![Een nieuwe IoT Edge-oplossing maken](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. **EdgeSolution** accepteren als nieuwe standaard naam voor de oplossing.

1. Kies **Azure Machine Learning** als het modulesjabloon.

1. **turbofanRulClassifier** als naam van module.

1. Kies uw werkruimte voor machine learning. Deze werkruimte is de werkruimte **turboFanDemo** die u heeft gemaakt in [zelfstudie: Een Azure Machine Learning-model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md)

1. Selecteer de afbeelding die u hebt gemaakt tijdens het uitvoeren van de Azure Notebook.

1. Bekijk de oplossing en let op de bestanden die zijn gemaakt:

   * **deployment.template.json:** Dit bestand bevat de definitie van elk van de modules in de oplossing. Er zijn drie secties waar u op moet letten in dit bestand:

     * **Registerreferenties toevoegen:** Stelt de set aangepaste containerregisters vast die u gebruikt in uw oplossing. Op dit moment moet het het register bevatten uit uw werkruimte voor machine learning, waar de installatiekopie voor uw Azure Machine Learning is opgeslagen. U kunt een onbeperkt aantal containerregisters hebben, maar voor de eenvoud gebruiken we dit ene register voor alle modules.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules:** Deze sectie bevat de set met door de gebruiker gedefinieerde modules die bij deze oplossing passen. De definitie van de turbofanRulClassifier-module verwijst naar de installatiekopie in het containerregister. Wanneer we meer modules toevoegen aan de oplossing, worden ze weergegeven in deze sectie.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Routes:** in deze zelfstudie werken we vrij veel met routes. Routes bepalen hoe modules met elkaar communiceren. De bestaande route die is gedefinieerd door het sjabloon komt niet overeen met de routering die we nodig hebben. De route `turbofanRulClassifierToIoTHub` verwijderen.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** dit bestand is de debugversie van deployment.template.json. Normaal gesproken wordt dit bestand gesynchroniseerd met de inhoud van het bestand deployment.template.json, maar dit is niet vereist voor deze zelfstudie.

   * **.env:** dit bestand is waar u de gebruikersnaam en het wachtwoord voor toegang tot het register moet opgeven.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Klik in Visual Studio Code Explorer met de rechtermuisknop op het bestand deployment.template.json en selecteer **IoT Edge-oplossing bouwen**.

1. U ziet dat met deze opdracht een configuratiemap met een deployment.amd64.json-bestand wordt gemaakt. Dit bestand is het concrete implementatiesjabloon voor de oplossing.

## <a name="add-router-module"></a>Routermodule toevoegen

Vervolgens voegen we de routermodule toe aan onze oplossing. De routermodule verwerkt verschillende verantwoordelijkheden voor onze oplossing:

* **Berichten ontvangen van bladknooppuntapparaten:** wanneer berichten binnenkomen op het IoT Edge-apparaat van downstreamapparaten, ontvangt de routermodule het bericht en begint de routering van het bericht.
* **Berichten verzenden naar de module RUL-classificatie:** wanneer een nieuw bericht wordt ontvangen van een downstreamapparaat, wijzigt de routermodule het bericht naar de indeling die de module RUL-classificatie verwacht. De router verzendt het bericht naar de RUL-classificatie voor een RUL-voorspelling. Zodra de classificatie een voorspelling heeft gedaan, stuurt deze het bericht terug naar de routermodule.
* **RUL-berichten naar IoT Hub sturen:** wanneer de router berichten van de classificatie ontvangt, wordt het bericht getransformeerd zodat alleen de essentiële informatie, apparaat-id en resterende levensduur worden opgeslagen en wordt het verkorte bericht naar de IoT-hub verzonden. Een verdere verfijning, die hier nog niet is uitgevoerd, stuurt alleen berichten naar de IoT Hub wanneer de voorspelling van de resterende levensduur lager is dan een drempelwaarde (bijvoorbeeld wanneer de resterende levensduur minder is dan 100 cycli). Op deze manier filteren, vermindert het aantal berichten en de kosten voor de IoT-hub.
* **Bericht verzenden naar de Avro-schrijfmodule:** om alle gegevens te bewaren die worden verzonden door het downstreamapparaat, verzendt de routermodule het volledige bericht dat van de classificatie wordt ontvangen, naar de Avro-schrijfmodule, die de gegevens permanent maakt en uploadt met behulp van de uploadfunctie van het IoT Hub-bestand.

De routermodule is een belangrijk onderdeel van de oplossing die ervoor zorgt dat berichten in de juiste volgorde worden verwerkt.

### <a name="create-the-module-and-copy-files"></a>De module maken en bestanden kopiëren

1. Klik met de rechtermuisknop op de map modules in Visual Studio Code en kies **IoT Edge-module toevoegen**.

1. Kies **C#-module** als het modulesjabloon.

1. **turbofanRouter** als naam van module.

1. Wanneer u wordt gevraagd om de opslagplaats voor de Docker-installatiekopieën, gebruikt u het register vanuit de werkruimte voor machine learning (u kunt het register vinden in het knooppunt registryCredentials van uw *deployment.template.json*-bestand). Deze waarde is het volledig gekwalificeerde adres van het register, zoals **\<your registry\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > In dit artikel gebruiken we het Azure Container Registry dat is gemaakt door de werkruimte Azure Machine Learning. Dit is uitsluitend voor het gemak. We hebben een nieuw containerregister gemaakt en onze modules daar gepubliceerd.

1. In de terminal met een opdrachtpromptshell kopieert u de bestanden van de voorbeeldmodule naar de oplossing.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Antwoord bevestigend op de vraag om het program.cs-bestand te overschrijven.

### <a name="build-router-module"></a>Routermodule bouwen

1. In Visual Studio Code selecteert u **Terminal** > **Standaard build-taak maken**.

1. Selecteer **Maak tasks. json-bestand van sjabloon**.

1. Selecteer **.NET core-** .

1. Vervang de inhoud van tasks.json door de volgende code.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. Tasks.json opslaan en sluiten.

1. Voer build uit met `Ctrl + Shift + B` of **Terminal** > **Build-taak uitvoeren**.

### <a name="set-up-module-routes"></a>Moduleroutes instellen

Zoals hierboven vermeld, gebruikt de IoT Edge-runtime routes die zijn geconfigureerd in het bestand *deployment.template.json* om de communicatie tussen de losjes met elkaar verbonden modules te beheren. In deze sectie gaan we dieper in op hoe we de routes instellen voor de turbofanRouter-module. De invoerroutes worden eerst beschreven en vervolgens verplaatst naar de uitvoer.

#### <a name="inputs"></a>Invoer

1. In de methode init() van program.cs registreren we twee callbacks voor de module:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. De eerste callback luistert naar berichten die worden verzonden naar de sink **deviceInput**. In het bovenstaande diagram zien we dat we berichten van elk bladknooppuntapparaat naar deze invoer willen routeren. Voeg in het bestand *deployment.sjabloon.json* een route toe die aangeeft dat de Edge-hub elk bericht stuurt dat door het IoT Edge-apparaat is ontvangen en dat niet door een IoT Edge-module is verzonden naar de invoer 'deviceInput' in de turbofanRouter-module:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Voeg vervolgens een route voor berichten uit de module rulClassifier toe aan de turbofanRouter-module:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Uitvoer

Voeg vier extra routes toe aan de parameter $edgeHub-route om uitvoer van de routermodule af te handelen.

1. Program.cs definieert de methode SendMessageToClassifier() die gebruikmaakt van de moduleclient om een bericht te verzenden naar de RUL-classificatie met behulp van de route:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() gebruikt de moduleclient voor het verzenden van alleen de RUL-gegevens voor het apparaat naar de IoT Hub via de route:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() gebruikt de moduleclient om het bericht te verzenden met de RUL-gegevens die zijn toegevoegd aan de avroFileWriter-module.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. Met HandleBadMessage() worden mislukte berichten verzonden naar de IoT Hub waar ze later kunnen worden gerouteerd.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Met alle routes die samen worden genomen, zou je knooppunt '$edgeHub' er moeten uitzien als in de volgende JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > Bij het toevoegen van de turbofanRouter-module is de volgende extra route gemaakt: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Verwijder deze route, waarbij alleen de routes worden weergegeven die hierboven in uw deployment.template.json-bestand worden vermeld.

## <a name="add-avro-writer-module"></a>Avro-schrijfmodule toevoegen

De Avro-schrijfmodule heeft twee verantwoordelijkheden in onze oplossing: berichten opslaan en bestanden uploaden.

* **Berichten opslaan**: wanneer de Avro-schrijfmodule een bericht ontvangt, wordt het bericht naar het lokale bestandssysteem geschreven in Avro-indeling. We gebruiken een verbindingskoppeling, die een directory (in dit geval /data/avrofiles) koppelt aan een pad in de container van de module. Met deze koppeling kan de module schrijven naar een lokaal pad (/avrofiles) en deze bestanden rechtstreeks vanuit het IoT Edge-apparaat toegankelijk maken.

* **Bestanden uploaden**: de Avro-schrijfmodule maakt gebruik van de functie voor het uploaden van Azure IoT Hub-bestand om bestanden te uploaden naar een Azure-opslagaccount. Zodra een bestand is geüpload, verwijdert de module het bestand van de schijf

### <a name="create-module-and-copy-files"></a>De module maken en bestanden kopiëren

1. Selecteer in Visual Studio Code **Weergeven** > **Opdrachtpalet** en zoek daarna naar **Python en selecteer deze: Interpreter selecteren** in.

1. Selecteer de geïnstalleerde Python-versie 3.7 of hoger.

1. Klik met de rechtermuisknop op de map modules in Visual Studio Code en kies **IoT Edge-module toevoegen**.

1. Kies **Python-module**.

1. Noem de module `avroFileWriter`.

1. Wanneer u wordt gevraagd naar de opslagplaats voor de Docker-installatiekopieën, gebruikt u hetzelfde register als voor het toevoegen van de modulerouter.

1. Kopieer bestanden van de voorbeeldmodule naar de oplossing.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Accepteer de overschrijving van main.py.

1. U ziet dat filemanager.py en schema.py zijn toegevoegd aan de oplossing en dat main.py is bijgewerkt.

> [!NOTE]
> Wanneer u een Python-bestand opent, wordt u mogelijk gevraagd om pylint te installeren. U hoeft de linter niet te installeren om deze zelfstudie te voltooien.

### <a name="bind-mount-for-data-files"></a>Bindingskoppeling voor gegevensbestanden

Zoals eerder vermeld, is de schrijfmodule afhankelijk van de aanwezigheid van een bindingskoppeling om Avro-bestanden naar het bestandssysteem van het apparaat te schrijven.

#### <a name="add-directory-to-device"></a>Map toevoegen aan apparaat

1. Start in de Azure Portal de VM van uw IoT Edge-apparaat als deze niet wordt uitgevoerd. Maak er verbinding mee met behulp van SSH. Voor de verbinding moet u de DNS-naam gebruiken die u kunt kopiëren van de overzichtspagina voor de VM in de Azure Portal.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. Nadat u zich hebt aangemeld, maakt u de map waarin de opgeslagen bladknooppuntapparaten worden bewaard.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Update directorymachtigingen om de container schrijfbaar te maken.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Controleer of de directory nu de schrijfmachtiging (w) heeft voor gebruiker, groep en eigenaar.

   ```bash
   ls -la /data
   ```

   ![Directorymachtigingen voor avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Map toevoegen aan de module

Als u de map wilt toevoegen aan de container van de module, wijzigt u de Dockerfiles die zijn gekoppeld aan de avroFileWriter-module. Er zijn drie Dockerfiles gekoppeld aan de module: Dockerfile.amd64, Dockerfile.amd64.debug en Dockerfile.arm32v7. Deze bestanden moeten worden gesynchroniseerd als u fouten wilt opsporen of op een arm32-apparaat wilt implementeren. In dit artikel focussen we ons alleen op Dockerfile.amd64.

1. Open het bestand **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** op uw virtuele ontwikkelingsmachine.

1. Wijzig het bestand zodat het er zo uitziet als in het volgende voorbeeld:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Met de opdrachten `mkdir` en `chown` wordt het Docker-buildproces opdracht gegeven om een map op het hoogste niveau met de naam /avrofiles in de installatiekopie te maken en vervolgens de modulegebruiker de eigenaar van die map te maken. Het is belangrijk dat deze opdrachten worden ingevoegd nadat de modulegebruiker is toegevoegd aan de installatiekopie met de opdracht `useradd` en voordat de context wordt overgeschakeld naar de modulegebruiker (modulegebruiker USER).

1. Indien nodig moet u de bijbehorende wijzigingen aanbrengen in Dockerfile.amd64.debug en Dockerfile.arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Bindingsconfiguratie toevoegen aan de avroFileWriter

De laatste stap voor het maken van de binding is het bijwerken van de bestanden deployment.template.json-bestand (en deployment.debug.template.json) met de bindingsinformatie.

1. Open deployment.template.json.

2. Wijzig de moduledefinitie voor avroFileWriter door de parameter `Binds` toe te voegen die de containermap /avrofiles naar de lokale map op het edge-apparaat wijst. De moduledefinitie moet overeenkomen met dit voorbeeld:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>Bindingskoppeling voor toegang tot config.yaml

Er moet nog één binding worden toegevoegd voor de schrijfmodule. Deze binding geeft de module toegang om de verbindingsreeks te lezen uit het /etc/iotedge/config.yaml-bestand op het IoT Edge-apparaat. We hebben de verbindingsreeks nodig om een IoTHubClient te maken, zodat we de methode voor het uploaden van \_blob\_async kunnen aanroepen om bestanden te uploaden naar de IoT-hub. De stappen voor het toevoegen van deze binding zijn vergelijkbaar met die in de vorige sectie.

#### <a name="update-directory-permission"></a>Directorymachtigingen bijwerken

1. Verbinding maken met uw IoT Edge-apparaat via SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Leesmachtiging toevoegen aan het bestand config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Controleer of de machtigingen juist zijn ingesteld.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Zorg ervoor dat de machtigingen voor config.yaml **-r--r--r--** zijn.

#### <a name="add-directory-to-module"></a>Map toevoegen aan module

1. Open het bestand **Dockerfile.amd64** op uw ontwikkelingsmachine.

1. Voeg een extra set van opdrachten `mkdir` en `chown` toe aan het bestand, zodat het er ongeveer als volgt uitziet:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. Breng de bijbehorende wijzigingen aan in Dockerfile.amd64.debug en Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Moduleconfiguratie bijwerken

1. Open het bestand **deployment.template.json**.

1. Wijzig de moduledefinitie voor avroFileWriter door een tweede regel toe te voegen aan de parameter `Binds` die de containermap (/app/iotconfig) naar de lokale map op het apparaat (/etc/iotedge) wijst.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. Breng de bijbehorende wijzigingen aan in de deployment.debug.template.json.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

De schrijfmodule heeft een afhankelijkheid van twee Python-bibliotheken: fastavro en PyYAML. De afhankelijkheden op onze ontwikkelingsmachine moeten worden geïnstalleerd en het Docker-buildproces moet worden geïnstalleerd in de installatiekopie van de module.

### <a name="pyyaml"></a>PyYAML

1. Open het bestand `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` op uw ontwikkelingsmachine en voeg 'pyyaml' toe aan een nieuwe regel in het bestand.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Open het bestand **Dockerfile.amd64** en voeg een `pip install`-opdracht toe om setuptools bij te werken.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. Installeer pyyaml bij een opdrachtprompt op de ontwikkelingsmachine.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Voeg fastavro tpe in requirements.txt na pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Installeer fastavro op uw ontwikkelingsmachine.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub opnieuw configureren

Door het IoT Edge-apparaat en de modules aan het systeem te introduceren, hebben we onze verwachtingen gewijzigd over welke gegevens naar de hub worden verzonden en wat het doel is. De routering in de hub moet opnieuw worden geconfigureerd om te kunnen omgaan met onze nieuwe realiteit.

> [!NOTE]
> De hub wordt opnieuw geconfigureerd voordat modules worden geïmplementeerd, omdat sommige hub-instellingen, met name het uploaden van bestanden, correct moeten worden ingesteld om ervoor te zorgen dat de avroFileWriter-module correct wordt uitgevoerd

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Route instellen voor Rul-berichten in IoT Hub

Als de router en classificatie zijn geïmplementeerd, wordt verwacht dat er regelmatig berichten worden ontvangen met alleen de apparaat-id en de RUL-voorspelling voor het apparaat. We willen de RUL-gegevens naar een eigen opslaglocatie routeren, waar we de status van de apparaten kunnen bewaken, rapporten kunnen maken en waarschuwingen kunnen versturen, indien nodig. Op hetzelfde moment willen we dat alle apparaatgegevens rechtstreeks worden verzonden door een bladknooppuntapparaat dat nog niet is gekoppeld aan het apparaat van IoT Edge om naar de huidige opslaglocatie te gaan.

#### <a name="create-a-rul-message-route"></a>Een route voor een RUL-bericht maken

1. Ga in Azure Portal naar uw IoT Hub.

1. In het menu in het linkerdeelvenster, selecteert u onder **Berichten** de optie **Routering bericht**.

1. Selecteer op het tabblad **Routes** de optie **Toevoegen**.

1. Noem de route **RulMessageRoute**.

1. Selecteer **Eindpunt toevoegen** aan de rechterkant van de **Eindpunt**-selector en kies **Opslag**.

1. Noem op de pagina **Een opslageindpunt toevoegen** het eindpunt **ruldata**.

1. Selecteer **Een container kiezen**.

1. Zoek op de pagina **Opslagaccounts** het opslagaccount dat u in deze zelfstudie gebruikt, zoals **iotedgeandml\<unique suffix\>** .

1. Selecteer de container **ruldata** en klik op **Selecteren**.

1. Selecteer op de pagina **Een opslageindpunt toevoegen** de optie **Maken** om het opslageindpunt te maken.

1. Op de pagina **Een route toevoegen** vervangt u voor de optie **Routeringsquery** `true` door de volgende query:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Breid de sectie **Test** uit en klik vervolgens op de sectie **Berichttekst**. Vervang de berichttekst door dit voorbeeld van onze verwachte berichten:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Selecteer **Testroute**. Als de test is geslaagd, ziet u de melding 'Het bericht komt overeen met de query'.

1. Klik op **Opslaan**.

#### <a name="update-turbofandevicedatatostorage-route"></a>TurbofanDeviceDataToStorage-route bijwerken

We willen de nieuwe voorspellingsgegevens niet naar onze oude opslaglocatie routeren. Werk daarom de route bij om dit te voorkomen.

1. Selecteer op de pagina **Berichtroutering** van IoT Hub het tabblad **Routes**.

1. Selecteer **turbofanDeviceDataToStorage** of de naam die u hebt gegeven aan de oorspronkelijke gegevensroute van uw apparaat.

1. Werk de routeringsquery bij naar

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Breid de sectie **Test** uit en klik vervolgens op de sectie **Berichttekst**. Vervang het bericht door dit voorbeeld van onze verwachte berichten:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. Selecteer **Testroute**. Als de test is geslaagd, ziet u de melding 'Het bericht komt overeen met de query'.

1. Selecteer **Opslaan**.

### <a name="configure-file-upload"></a>Uploaden van bestanden configureren

Configureer de functie voor het uploaden van IoT Hub-bestanden om de schrijfmodule voor bestanden in te schakelen voor het uploaden van bestanden naar opslag.

1. Kies in het menu van het linkerdeelvenster in uw IoT Hub onder **Berichten** voor **Bestanden uploaden**.

1. **Azure Storage-container** selecteren.

1. Selecteer uw opslagaccount in de lijst.

1. Selecteer de container die begint met **azureml-blobarchief** en waaraan een guid is toegevoegd, en klik op **Selecteren**.

1. Selecteer **Opslaan**. De portal waarschuwt u wanneer het opslaan is voltooid.

> [!Note]
> Er worden geen uploadmeldingen voor deze zelfstudie ingeschakeld, maar zie [Een melding over het uploaden van bestanden ontvangen](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) voor meer informatie over het verwerken van berichten over het uploaden van bestanden.

## <a name="build-publish-and-deploy-modules"></a>Modules bouwen, publiceren en implementeren

Nu u de wijzigingen hebt aangebracht in de configuratie, kunt u de installatiekopieën bouwen en publiceren naar ons Azure-containerregister. Het bouwproces maakt gebruik van het bestand deployment.template.json om te bepalen welke modules moeten worden gebouwd. De instellingen voor elke module, inclusief versie, vindt u in het bestand module.json in de modulemap. Het bouwproces voert eerst een Docker-build uit op de Dockerfiles die overeenkomt met de huidige configuratie. Deze vindt u in het bestand module.json en daarmee kunt u een installatiekopie maken. Vervolgens wordt de installatiekopie naar het register gepubliceerd vanuit het bestand module.json met een versielabel dat overeenkomt met de naam in het bestand module.json. Ten slotte wordt er een distributiemanifest geproduceerd (bijvoorbeeld deployment.amd64.json) die specifiek bij de configuratie hoort. Dit implementeren we op het IoT Edge-apparaat. Het IoT Edge-apparaat leest de gegevens van het distributiemanifest en op basis van de instructies worden de modules gedownload, de routes geconfigureerd en de gewenste eigenschappen ingesteld. Deze implementatiemethode heeft twee neveneffecten waarmee u rekening moet houden:

* **Vertraging bij implementatie:** omdat de IoT Edge-runtime de wijziging in de gewenste eigenschappen moet herkennen voordat deze opnieuw wordt geconfigureerd, kan het een tijdje duren, nadat u de modules hebt geïmplementeerd, totdat de runtime deze ophaalt en het IoT Edge-apparaat begint bij te werken.

* **Moduleversies:** als u een nieuwe versie van de container van een module naar het containerregister publiceert met dezelfde versietags als de vorige module, wordt de nieuwe versie van de module niet door de runtime gedownload. Het maakt een vergelijking van de versiecode van de lokale installatiekopie en de gewenste installatiekopie uit het distributiemanifest. Als deze versies overeenkomen, hoeft de runtime geen actie te ondernemen. Daarom is het belangrijk om de versie van uw module bij te werken op elk moment dat u nieuwe wijzigingen wilt implementeren. Werk de versie bij door de eigenschap van de **versie** te wijzigen onder de eigenschap **Tag** in het bestand module.json voor de module die u wilt wijzigen. Vervolgens bouwt en publiceert u de module.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Bouwen en publiceren

1. Start Docker op uw virtuele ontwikkelingsmachine als deze niet actief is.

1. In Visual Studio Code start u een nieuwe terminal met een opdrachtprompt en meldt u zich aan bij uw Azure Container Registry (ACR).

  De vereiste waarden voor gebruikersnaam, wachtwoord en aanmeldingsserver vindt u in de Azur P-portal. De naam van het containerregister heeft de indeling 'turbofandemo\<unique id\>'. Selecteer in het menu van het linkerdeelvenster onder **Instellingen** de optie **Toegangssleutels** om ze weer te geven.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Klik in Visual Studio Code met de rechtermuisknop op deployment.template.json en kies **IoT Edge-oplossingen bouwen en pushen**.

### <a name="view-modules-in-the-registry"></a>Modules in het register weergeven

Zodra de build is voltooid, kunnen we de Azure Portal gebruiken om onze gepubliceerde modules te bekijken.

1. Open de Azure Container Registry voor deze zelfstudie. De naam van het containerregister heeft de indeling 'turbofandemo\<unique id\>'. 

1. In het menu van het linkerdeelvenster onder **Services** selecteert u **Opslagplaatsen**.

1. Houd er rekening mee dat beide modules die u hebt gemaakt (**avrofilewriter** en **turbofanrouter**) als opslagplaatsen worden weer gegeven.

1. Selecteer **turbofanrouter** en houd er rekening mee dat u één afbeelding hebt gepubliceerd als 0.0.1-amd64.

   ![Eerste gemarkeerde versie van turbofanrouter weergeven](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modules naar uw IoT Edge-apparaat implementeren

We hebben de modules in onze oplossing gemaakt en geconfigureerd. Nu gaan we de modules implementeren op het IoT Edge-apparaat.

1. Klik in Visual Studio Code met de rechtermuisknop op het bestand **deployment.amd64.json** in de configuratiemap.

1. Kies **Implementatie voor één apparaat maken**.

1. Kies uw IoT Edge-apparaat **aaTurboFanEdgeDevice**.

1. Vernieuw het deelvenster Azure IoT Hub-apparaten in Visual Studio Code Explorer. U ziet dat de drie nieuwe modules zijn geïmplementeerd, maar nog niet worden uitgevoerd.

1. Vernieuw de gegevens na een paar minuten opnieuw. Hierna zult u zien dat de modules worden uitgevoerd.

   ![Actieve modules weergeven in Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Het kan enkele minuten duren voordat de modules worden gestart en woren uitgevoerd. Gedurende die tijd worden modules gestart en gestopt wanneer ze proberen verbinding te maken met de IoT Edge-hubmodule.

## <a name="diagnosing-failures"></a>Fouten vaststellen

In deze sectie delen we enkele technieken om te weten wat er mis is met een module of modules. Vaak kan een fout worden gezien in de status van de Visual Studio Code.

### <a name="identify-failed-modules"></a>Mislukte modules identificeren

* **Visual Studio Code:** Bekijk het venster Azure IoT Hub-apparaten. Als de meeste modules een actieve status hebben, maar één is gestopt, moet u de gestopte module verder onderzoeken. Als alle modules gedurende een lange periode een gestopte status hebben, kan dit ook duiden op storingen.

* **Azure Portal:** Door te navigeren naar uw IoT-hub in de portal en vervolgens de pagina met details van het apparaat te zoeken (onder IoT Edge, zoomt u in op uw apparaat), ziet u mogelijk dat een module een fout heeft gerapporteerd of niets heeft genoteerd voor de IoT-hub.

### <a name="diagnosing-from-the-device"></a>Diagnoses van het apparaat

Als u zich aanmeldt bij het IoT Edge-apparaat (de Linux-VM in ons geval), kunt u toegang krijgen tot een grote hoeveelheid informatie over de status van uw modules. Het belangrijkste mechanisme dat wordt gebruikt, zijn de Docker-opdrachten waarmee we de containers en installatiekopieën op het apparaat kunnen onderzoeken.

1. Meld u aan bij uw IoT Edge-apparaat:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Vermeld alle actieve containers. Verwacht wordt dat er voor elke module een container wordt weergegeven met een naam die overeenkomt met de module. Met deze opdracht wordt ook de exacte installatiekopie voor de container met de versie weergegeven, zodat dit met uw verwachting kan overeenkomen. U kunt ook afbeeldingen weergeven door 'afbeelding' te vervangen door 'container' in de opdracht.

   ```bash
   sudo docker container ls
   ```

1. De logboeken voor een container ophalen. Met deze opdracht worden alle bewerkingen uitgevoerd, ongeacht of deze zijn geschreven naar StdErr en StdOut in de container. Deze opdracht werkt voor containers die zijn gestart en vervolgens om een of andere reden vastlopen. Het is ook handig om te weten wat er is gebeurd met de containers edgeAgent of edgeHub.

   ```bash
   sudo docker container logs <container id>
   ```

1. Een container onderzoeken. Met deze opdracht geeft u een grote hoeveelheid informatie over de installatiekopie. De gegevens kunnen worden gefilterd, afhankelijk van wat u zoekt. Als u bijvoorbeeld wilt zien of de bindingen op de avroFileWriter juist zijn, kunt u de volgende opdracht gebruiken:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Maak verbinding met een actieve container. Deze opdracht kan handig zijn als u de container wilt controleren terwijl deze wordt uitgevoerd:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht met het opschonen van resources totdat u de laatste zelfstudie hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT Edge-oplossing gemaakt in Visual Studio Code met drie modules: een classificatie, een router en een schrijver/uploader voor bestanden. We hebben de routes zo ingesteld dat de modules met elkaar kunnen communiceren op het edge-apparaat. De configuratie van het edge-apparaat is gewijzigd en de Dockerfiles zijn bijgewerkt om afhankelijkheden te installeren en bindingskoppelingen toe te voegen aan de containers van de modules. 

Daarna hebben we de configuratie van de IoT Hub bijgewerkt om onze berichten te routeren op basis van het type en om de uploads van bestanden te verwerken. Nu alles aanwezig is, hebben we de modules geïmplementeerd op het IoT Edge-apparaat en hebben ze gezorgd voor de juiste uitvoering van de modules.

Ga door naar het volgende artikel om te beginnen met het verzenden van gegevens en om uw oplossing in actie te zien.

> [!div class="nextstepaction"]
> [Gegevens verzenden via een transparante gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)