---
title: 'Zelfstudie: Node.js-module maken voor Linux - Azure IoT Edge | Microsoft Docs'
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Node.js-code maakt en deze implementeert op een Edge-apparaat
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-python, devx-track-js
ms.openlocfilehash: a8a6d09819aaa65645da3d4d697866609c226723
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461144"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-using-linux-containers"></a>Zelf studie: een Node.js IoT Edge module ontwikkelen en implementeren met behulp van Linux-containers

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Gebruik Visual Studio code om Node.js code te ontwikkelen en te implementeren op een apparaat met Azure IoT Edge.

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het IoT Edge apparaat dat u hebt gemaakt in de Quick starts. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge Node.js-module te maken
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register
> * De module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheidgegevens die worden gecommuniceerd naar en opgeslagen in de cloud te reduceren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt gedemonstreerd hoe u een module ontwikkelt in **Node.js** met **Visual Studio code** en hoe u deze implementeert op een IOT edge apparaat.

IoT Edge biedt geen ondersteuning voor Node.js-modules met Windows-containers.

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van Node.js-modules:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![VS Code gebruiken voor Node.js-modules op Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![VS Code gebruiken voor Node.js-modules op Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules met behulp van Linux-containers](tutorial-develop-for-linux.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een apparaat met Azure IoT Edge. U kunt de Quick starts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat](quickstart.md)in te stellen.
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

Om een IoT Edge-module te ontwikkelen in Node.js, installeert u de volgende aanvullende vereisten op uw ontwikkelcomputer:

* [Node.js en NPM](https://nodejs.org). Het NPM-pakket wordt gedistribueerd met Node.js. Dit betekent dat NMP automatisch wordt geïnstalleerd op de computer wanneer u Node.js downloadt.

## <a name="create-a-module-project"></a>Een moduleproject maken

De volgende stappen laten zien hoe u een IoT-Edge Node.js-module maakt met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's.

### <a name="create-a-new-project"></a>Een nieuw project maken

Gebruik **NPM** om een Node.js-oplossingssjabloon te maken waarop u verder kunt bouwen.

1. Selecteer in Visual Studio Code **View** > **Integrated Terminal** om de met VS code geïntegreerde terminal te openen.

2. Voer in de geïntegreerde terminal de volgende opdracht in om **yeoman** en de generator voor de Node.js Azure IoT Edge-module te installeren:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecteer **View** > **Command Palette** om het VS Code-opdrachtpalet te openen.

4. Typ in het opdrachtenpalet de opdracht **Azure: Aanmelden** in, voer deze uit en volg de instructies om u aan te melden bij uw Azure-account. Als u zich al hebt aangemeld, kunt u deze stap overslaan.

5. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **Node.js Module**. |
   | Een modulenaam opgeven | Geef de module de naam **NodeModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/nodemodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor de containeropslagplaats opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open in VS Code Explorer het bestand **.env**.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

Momenteel kunt u met Visual Studio Code Node.js-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat de container voor elk type architectuur anders wordt opgebouwd en anders wordt uitgevoerd. De standaardinstelling is Linux AMD64.

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Elke sjabloon bevat voorbeeldcode. Met deze code worden gesimuleerde sensorgegevens uit de **SimulatedTemperatureSensor**-module gerouteerd naar IoT Hub. Voeg in deze sectie code toe om de berichten te analyseren met NodeModule voordat u ze verzendt.

1. Open in VS Code Explorer **modules** > **NodeModule** > **app.js**.

2. Voeg een variabele voor de temperatuurdrempel toe onder de vereiste knooppuntmodules. De temperatuurdrempel bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Vervang de hele functie `PipeMessage` door de functie `FilterMessage`.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Vervang in functie `client.on()` de functienaam `pipeMessage` door `filterMessage`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Kopieer het volgende codefragment in de functieaanroep `client.open()`, na `client.on()` binnen de `else`-instructie. Deze functie wordt aangeroepen wanneer de gewenste eigenschappen zijn bijgewerkt.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Sla het bestand app.js op.

7. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

8. Voeg de moduledubbel NodeModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-node-module/module-twin.png)

9. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de NodeModule waarmee berichten worden gefilterd waarin de gemelde temperatuur van de computer binnen de aanvaardbare grenzen is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de met VS Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

2. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

3. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

Gebruik Visual Studio Code Explorer en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.amd64.json** in de map **config** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe **NodeModule** wordt uitgevoerd, samen met de module **SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub**.

    Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in de Visual Studio Code Explorer met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

2. Bekijk de berichten die in uw IoT Hub arriveren. Het kan even duren voordat de berichten binnenkomen. Het IoT Edge-apparaat moet de nieuwe implementatie ontvangen en alle modules starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de NodeModule-code totdat de computertemperatuur 25 graden bereikt voordat berichten worden verzonden. Daarnaast wordt het berichttype **Waarschuwing** toegevoegd aan berichten die de drempelwaarde voor de temperatuur bereiken.

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

We hebben de NodeModule-moduledubbel in het implementatiemanifest gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de actieve modules weer te geven.

2. Klik met de rechtermuisknop op **NodeModule** en selecteer **Moduledubbel bewerken**.

3. Zoek **TemperatureThreshold** op in de gewenste eigenschappen. Wijzig de waarde in een nieuwe temperatuur van vijf graden tot 10 graden hoger dan de laatst gerapporteerde temperatuur.

4. Sla het moduledubbelbestand op.

5. Klik met de rechtermuisknop op een willekeurige plaats in het deelvenster Moduledubbel bewerken en selecteer **Moduledubbel bijwerken**.

6. Bewaak de binnenkomende apparaat-naar-cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat.

U kunt doorgaan met de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)