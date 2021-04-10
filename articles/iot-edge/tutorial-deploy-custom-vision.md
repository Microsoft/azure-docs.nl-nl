---
title: 'Zelfstudie: Custom Vision-classificatie op een apparaat implementeren met Azure IoT Edge'
description: In deze zelfstudie leert u hoe u een Computer Vision-model uitvoert als een container met behulp van Custom Vision en IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8da5c24265a43eaaf1ecfe80649b6f4fea588b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464056"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Zelfstudie: Custom Vision Service gebruiken om afbeeldingsclassificatie uit te voeren aan de rand

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Met Azure IoT Edge kunt uw IoT-oplossing efficiënter maken door werkbelastingen van de cloud naar de rand te verplaatsen. Deze mogelijkheid is met name geschikt voor services die grote hoeveelheden gegevens verwerken, zoals Computer Vision-modellen. Met de [Custom Vision Service](../cognitive-services/custom-vision-service/overview.md) kunt u aangepaste afbeeldingsclassificaties maken en deze als containers op apparaten implementeren. Met deze twee services samen kunt u inzichten vinden op basis van afbeeldingen of videostreams zonder dat u eerst alle gegevens van de site moet overbrengen. Custom Vision biedt een classificatie die een installatiekopie vergelijkt met een getraind model om inzichten te genereren.

Zo kunt u met Custom Vision op een IoT Edge-apparaat bepalen of er op een snelweg meer of minder verkeer is dan normaal, en of er in een parkeergarage nog vrije plaatsen zijn in een rij. Deze inzichten kunnen worden gedeeld met een andere service om actie te ondernemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een afbeeldingsclassificatie bouwen met Custom Vision.
> * Een IoT Edge-module ontwikkelen die gegevens opvraagt van de Custom Vision-webserver op uw apparaat.
> * De resultaten van de afbeeldingsclassificatie verzenden naar IoT Hub.

<center>

![Diagram - architectuur van zelfstudie, classificatie faseren en implementeren](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

>[!TIP]
>Deze zelfstudie is een vereenvoudigde versie van het voorbeeldproject [Custom Vision en Azure IoT Edge op een Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi). Deze zelfstudie is ontworpen om te worden uitgevoerd op een virtuele machine in de cloud en maakt gebruik van statische afbeeldingen om de functie voor afbeeldingsclassificatie te trainen en te testen, wat handig is voor iemand die net begint met het evalueren van Custom Vision in IoT Edge. Het voorbeeldproject maakt gebruik van fysieke hardware en stelt een live camerafeed in voor het trainen en testen van de functie voor afbeeldingsclassificatie, wat handig is voor iemand die een meer gedetailleerd praktijkscenario wil uitproberen.

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules met behulp van Linux-containers](tutorial-develop-for-linux.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een apparaat met Azure IoT Edge. U kunt de Quick starts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat](quickstart.md)in te stellen.
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

Om een IoT Edge-module te ontwikkelen met de Custom Vision-service, moeten de volgende aanvullende vereisten op uw ontwikkelcomputer zijn geïnstalleerd:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Een afbeeldingsclassificatie bouwen met Custom Vision

Voor het bouwen van een afbeeldingsclassificatie moet u een Custom Vision-project maken en trainingsafbeeldingen verstrekken. Zie [Een classificatie bouwen met Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) voor meer informatie over de stappen die u in deze sectie uitvoert.

Wanneer de afbeeldingsclassificatie is gemaakt en getraind, kunt u deze als een Docker-container exporteren en implementeren op een IoT Edge-apparaat.

### <a name="create-a-new-project"></a>Een nieuw project maken

1. Navigeer in uw webbrowser naar de [Custom Vision-webpagina](https://customvision.ai/).

2. Selecteer **Aanmelden** en meld u aan met het account dat u gebruikt om toegang te krijgen tot Azure-resources.

3. Selecteer **Nieuw project**.

4. Maak uw project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Naam | Geef een naam op voor uw project, bijvoorbeeld **EdgeTreeClassifier**. |
   | Beschrijving | Optionele projectbeschrijving. |
   | Resource | Selecteer een van uw Azure-resource groepen die een Custom Vision Service Resource bevat of **maak nieuwe groepen** als u er nog geen hebt toegevoegd. |
   | Projecttypen | **Classificatie** |
   | Classificatietypen | **Multiclass (één label per afbeelding)** |
   | Domeinen | **Algemeen (compact)** |
   | Exportmogelijkheden | **Basisplatforms (TensorFlow, CoreML, ONNX, ...)** |

5. Selecteer **Project maken**.

### <a name="upload-images-and-train-your-classifier"></a>Afbeeldingen uploaden en de classificatie trainen

Om een afbeeldingsclassificatie te maken, hebt u een reeks trainingsafbeeldingen en testafbeeldingen nodig.

1. U kunt voorbeeldafbeeldingen klonen of van de [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows)- opslagplaats downloaden naar uw lokale ontwikkelcomputer.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Ga terug naar uw Custom Vision-project en selecteer **Afbeeldingen toevoegen**.

3. Blader naar de git-opslagplaats die u lokaal hebt gekloond en navigeer naar de eerste afbeeldingenmap, **Cognitive-CustomVision-Windows / Samples / afbeeldingen / Hemlock**. Selecteer alle 10 de afbeeldingen in de map en kies **Openen**.

4. Voeg het label **hemlock** toe aan deze groep afbeeldingen en druk op **Enter** om het label toe te passen.

5. Selecteer **10 bestanden uploaden**.

   ![Met hemlock gelabelde bestanden uploaden naar Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Wanneer de afbeeldingen zijn geüpload, selecteert u **Gereed**.

7. Selecteer opnieuw **Afbeeldingen toevoegen**.

8. Blader naar de tweede afbeeldingenmap, **Cognitive-CustomVision-Windows / Samples / afbeeldingen / Japanese Cherry**. Selecteer alle 10 de afbeeldingen in de map en kies **Openen**.

9. Voeg het label **japanese cherry** toe aan deze groep afbeeldingen en druk op **Enter** om het label toe te passen.

10. Selecteer **10 bestanden uploaden**. Wanneer de afbeeldingen zijn geüpload, selecteert u **Gereed**.

11. Wanneer beide sets met afbeeldingen zijn gelabeld en geüpload, selecteert u **Trainen** om de classificatie te trainen.

### <a name="export-your-classifier"></a>De classificatie exporteren

1. Nadat u de classificatie hebt getraind, selecteert u **Exporteren** op de pagina Prestaties van de classificatie.

   ![De getrainde classificatie van de installatiekopie exporteren](./media/tutorial-deploy-custom-vision/export.png)

2. Selecteer **DockerFile** als platform. 

3. Selecteer **Linux** als de versie.  

4. Selecteer **Exporteren**. 

   ![Exporteren als DockerFile met Linux-containers](./media/tutorial-deploy-custom-vision/export-2.png)

5. Wanneer de export is voltooid, selecteert u **Downloaden** en slaat u het ZIP-pakket lokaal op uw computer op. Pak alle bestanden uit het pakket uit. U gebruikt deze bestanden om een IoT Edge-module te maken die de afbeeldingsclassificatieserver bevat. 

Op dit punt aangekomen, bent u klaar met het maken en trainen van uw Custom Vision-project. U gebruikt de geëxporteerde bestanden in de volgende sectie, maar u bent klaar met de Custom Vision-webpagina. 

## <a name="create-an-iot-edge-solution"></a>Een IoT Edge-oplossing maken

U hebt nu de bestanden voor een containerversie van de afbeeldingsclassificatie op uw ontwikkelcomputer. In deze sectie configureert u de container van de afbeeldingsclassificatie om te worden uitgevoerd als een IoT Edge-module. U maakt ook een tweede module die naast de afbeeldingsclassificatie wordt geïmplementeerd. De tweede module stuurt aanvragen naar de classificatie en verzendt de resultaten als berichten naar IoT Hub. 

### <a name="create-a-new-solution"></a>Een nieuwe oplossing maken

Een oplossing is een logische manier om meerdere modules voor één IoT Edge-implementatie te ontwikkelen en te organiseren. Een oplossing bevat code voor een of meer modules, evenals het implementatiemanifest waarin wordt gedeclareerd hoe u deze modules op een IoT Edge-apparaat worden geconfigureerd. 

1. Selecteer in Visual Studio Code **View** > **Command Palette** om het VS Code-opdrachtpalet te openen. 

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken: 

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor uw oplossing in, bijvoorbeeld **CustomVisionSolution**, of accepteer de standaardinstelling. |
   | Modulesjabloon selecteren | Kies **Python-module**. |
   | Een modulenaam opgeven | Noem de module **classificatie**.<br><br>Het is belangrijk dat deze modulenaam in kleine letters wordt opgegeven. IoT Edge is hoofdlettergevoelig wat de verwijzing naar modules betreft, en deze oplossing gebruikt een bibliotheek die alle aanvragen opmaakt in kleine letters. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de laatste stap. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal.<br><br>De uiteindelijke tekenreeks ziet er als volgt uit **\<registry name\>.azurecr.io/classifier**. |
 
   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-deploy-custom-vision/repository.png)

De werkruimte van de IoT Edge-oplossing wordt geladen in het Visual Studio Code-venster.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

Momenteel kunt u met Visual Studio Code modules ontwikkelen voor apparaten met Linux AMD64 en Linux ARM32v7. U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat de container voor elk type architectuur anders wordt opgebouwd en anders wordt uitgevoerd. De standaardwaarde is Linux AMD64, wat we ook gaan gebruiken voor deze zelfstudie. 

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster. 

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan. 

### <a name="add-your-image-classifier"></a>Een afbeeldingsclassificatie toevoegen

De Python-modulesjabloon in Visual Studio Code bevat wat voorbeeldcode die u kunt uitvoeren om IoT Edge te testen. U gaat deze code niet gebruiken in dit scenario. Gebruik in plaats daarvan de stappen in deze sectie om de voorbeeldcode te vervangen door de container van de afbeeldingsclassificatie die u eerder hebt geëxporteerd. 

1. Blader in de Verkenner naar het Custom Vision-pakket dat u hebt gedownload en uitgepakt. Kopieer de volledige inhoud van het uitgepakte pakket. Deze bestaat uit de mappen **app** en **azureml**, en de bestanden **Dockerfile** en **README**. 

2. Blader in de Verkenner naar de map die u hebt opgegeven als de map waarin Visual Studio Code uw IoT Edge-oplossing moet maken. 

3. Open de map met de classificatiemodule. Als u de voorgestelde namen in de vorige sectie hebt gebruikt, ziet de mapstructuur er ongeveer zo uit: **CustomVisionSolution / modules / classificatie**. 

4. Plak de bestanden in de map **classificatie**. 

5. Ga terug naar het Visual Studio Code-venster. De oplossingswerkruimte moet nu de afbeeldingsclassificatiebestanden weergeven in de modulemap. 

   ![Oplossingswerkruimte met afbeeldingsclassificatiebestanden](./media/tutorial-deploy-custom-vision/workspace.png)

6. Open het bestand **module.json** in de map classificatie. 

7. Werk de parameter **platforms** bij zodat deze verwijst naar het nieuwe Dockerfile dat u hebt toegevoegd en verwijder alle opties behalve AMD64. Dit is de enige architectuur die we voor deze zelfstudie gebruiken. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Sla uw wijzigingen op. 

### <a name="create-a-simulated-camera-module"></a>Een module voor een gesimuleerde camera maken

In een echte Custom Vision-implementatie zou u een camera hebben die live afbeeldingen of videostreams levert. Voor dit scenario kunt u de camera simuleren door een module te bouwen die een testafbeelding naar de afbeeldingsclassificatie verzendt. 

#### <a name="add-and-configure-a-new-module"></a>Een nieuwe module toevoegen en configureren

In deze sectie voegt u een nieuwe module aan dezelfde CustomVisionSolution toe en geeft u code op om de gesimuleerde camera te maken. 

1. In hetzelfde Visual Studio Code-venster gebruikt u het opdrachtenpalet om **Azure IoT Edge: Add IoT Edge Module** uit te voeren. Geef in het opdrachtenpalet de volgende informatie op voor uw nieuwe module: 

   | Vraag | Waarde | 
   | ------ | ----- |
   | Implementatiesjabloonbestand selecteren | Selecteer het bestand **deployment.template.json** in de map CustomVisionSolution. |
   | Modulesjabloon selecteren | Selecteer **Python-module** |
   | Een modulenaam opgeven | Noem de module **cameraCapture** |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Vervang **localhost:5000** door de waarde van **Aanmeldingsserver** voor uw Azure-containerregister.<br><br>De uiteindelijke tekenreeks ziet er als volgt uit **\<registryname\>.azurecr.io/cameracapture**. |

   De nieuwe module wordt in de oplossingswerkruimte van het VS Code-venster geladen en het bestand deployment.template.json wordt bijgewerkt. Nu moet u twee modulemappen zien: classificatie en cameraCapture. 

2. Open het bestand **main.py** in de map  / **cameraCapture** van de **module**. 

3. Vervang het hele bestand door de volgende code. Deze voorbeeldcode verzendt POST-aanvragen naar de beeldverwerkingsservice die wordt uitgevoerd in de classificatiemodule. We geven deze modulecontainer met een voorbeeldafbeelding die u in de aanvragen kunt gebruiken. Vervolgens wordt het antwoord verpakt als een IoT Hub-bericht en naar een uitvoerwachtrij verzonden.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Sla het bestand **main.py** op. 

5. Open het bestand **requirements.txt**. 

6. Voeg een nieuwe regel toe om een bibliotheek in de container op te nemen.

   ```Text
   requests
   ```

7. Sla het bestand **requirements.txt** op.


#### <a name="add-a-test-image-to-the-container"></a>Een testafbeelding toevoegen aan de container

In plaats van een echte camera te gebruiken om dit scenario een afbeeldingsfeed te bieden, gebruiken we één testafbeelding. U vindt een testafbeelding in de GitHub-opslagplaats die u eerder in deze zelfstudie hebt gedownload voor de trainingsafbeeldingen. 

1. Navigeer naar de testafbeelding in **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Kopieer **test_image.jpg** 

3. Blader naar de map van uw IoT Edge-oplossing en plak de testafbeelding in de map **modules** / **cameraCapture**. De afbeelding moet zich in dezelfde map bevinden als het bestand main.py dat u in de vorige sectie hebt bewerkt. 

4. Open in Visual Studio Code het bestand **Dockerfile.amd64** voor de cameraCapture-module.

5. Voeg na de regel die de werkmap instelt, `WORKDIR /app`, de volgende regel code toe:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Sla de Dockerfile op.

### <a name="prepare-a-deployment-manifest"></a>Een implementatiemanifest voorbereiden

U hebt tot nu toe in deze zelfstudie een Custom Vision-model getraind om afbeeldingen van bomen te classificeren, en u hebt dit model verpakt als een IoT Edge-module. Daarna hebt u een tweede module gemaakt die query's kan uitvoeren naar de afbeeldingsclassificatieserver en de resultaten kan rapporteren aan IoT Hub. Nu kunt u het implementatiemanifest gaan maken dat aan een IoT Edge-apparaat doorgeeft hoe deze twee modules samen kunnen worden gestart en uitgevoerd. 

De IoT Edge-extensie voor Visual Studio Code bevat een sjabloon in elke IoT Edge-oplossing voor het maken van een implementatiemanifest. 

1. Open het bestand **deployment.template.json** in de oplossingsmap. 

2. Zoek de sectie **modules**, drie modules moet bevatten: de twee die u hebt gemaakt, classificatie en cameraCapture, en een derde, die standaard is opgenomen, SimulatedTemperatureSensor. 

3. Verwijder de module **SimulatedTemperatureSensor** met alle bijbehorende parameters. Deze module is opgenomen om voorbeeldgegevens voor testscenario's te verstrekken, maar deze is niet nodig in deze implementatie. 

4. Als u de afbeeldingsclassificatiemodule een andere naam hebt gegeven dan **classificatie**, gebruik die naam dan en controleer of deze alleen kleine letters bevat. De module cameraCapture roept de classificatiemodule aan met behulp van een bibliotheek met aanvragen die alle aanvragen omzet in kleine letters, en IoT Edge is hoofdlettergevoelig. 

5. Werk de parameter **createOptions** voor de module cameraCapture bij met de volgende JSON. Met deze informatie worden omgevingsvariabelen in de modulecontainer gemaakt die zijn opgehaald in het main.py-proces. Door deze gegevens op te nemen in het implementatiemanifest, kunt u de afbeelding of het eindpunt wijzigen zonder de moduleafbeelding opnieuw te moeten bouwen. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Als u de Custom Vision-module een andere naam hebt gegeven dan *classificatie*, werk dan de waarde van het afbeeldingsverwerkingseindpunt dienovereenkomstig bij. 

6. Werk onderaan het bestand de parameter **routes** voor de module $edgeHub bij. U wilt de voorspellingsresultaten van cameraCapture omleiden naar IoT Hub.

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Als u de tweede module een andere naam hebt gegeven dan *cameraCapture*, werk dan de routewaarde dienovereenkomstig bij. 

7. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-iot-edge-solution"></a>Uw IoT Edge-oplossing bouwen en pushen

Nu beide modules zijn gemaakt en de implementatiemanifestsjabloon is geconfigureerd, kunt u de containerafbeeldingen gaan bouwen en naar uw containerregister pushen.

Wanneer de afbeeldingen in het register staan, kunt u de oplossing implementeren op een IoT Edge-apparaat. U kunt modules op een apparaat instellen via de IoT Hub, maar u hebt ook toegang tot uw IoT Hub en apparaten via Visual Studio Code. In dit gedeelte stelt u de toegang tot uw IoT Hub in en gebruikt u VS Code om uw oplossing op uw IoT Edge-apparaat te implementeren.

Eerst gaat u de oplossing bouwen en naar het containerregister pushen.

1. Open de met VS Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

2. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

3. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, die is gebouwd op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

Gebruik Visual Studio Code Explorer en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.amd64.json** in de map **config** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu de nieuwe modules **classifier** en **cameraCapture**, die worden uitgevoerd samen met de **$edgeAgent** en **$edgeHub**.  

U kunt ook controleren of alle modules op uw eigen apparaat actief zijn. Voer op uw IoT Edge-apparaat de volgende opdracht uit om de status van de modules te bekijken.

   ```bash
   iotedge list
   ```

Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

## <a name="view-classification-results"></a>Classificatieresultaten weergeven

Er zijn twee manieren waarop u de resultaten van uw modules kunt bekijken: op het apparaat zelf, via de berichten die worden gegenereerd en verzonden, of vanuit Visual Studio Code via de berichten die binnenkomen bij IoT Hub. 

Bekijk op uw apparaat de logboeken van de module cameraCapture om de berichten te zien die zijn verzonden en de bevestiging dat ze zijn ontvangen door de IoT Hub. 

   ```bash
   iotedge logs cameraCapture
   ```

Klik in de Visual Studio Code Explorer met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. 

> [!NOTE]
> Mogelijk ziet u in eerste instantie verbindingsfouten in de uitvoer van de cameraCapture-module. Dit komt door de vertraging tussen het implementeren en starten van de modules.
>
> De cameraCapture-module probeert de verbinding automatisch opnieuw tot stand te brengen totdat dit is gelukt. Nadat de verbinding is geslaagd, ziet u de verwachte afbeeldingsclassificatieberichten die hieronder worden beschreven.

De resultaten van de Custom Vision-module, die als berichten vanuit de module cameraCapture zijn verzonden, geven de waarschijnlijkheid aan dat de afbeelding een Canadese den of een Japanse kers is. Omdat het een afbeelding van een Canadese den is, moet u de waarschijnlijkheid 1.0 zien.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Custom Vision-model getraind en het als een module op een IoT Edge-apparaat geïmplementeerd. Daarna hebt u een module gemaakt die query's kan uitvoeren naar de afbeeldingsclassificatieservice en de resultaten kan rapporteren aan IoT Hub.

Ga verder met de volgende zelfstudies om te leren hoe Azure IoT Edge u verder kan helpen bij het omzetten van uw gegevens in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Gegevens opslaan met SQL Server-databases](tutorial-store-data-sql-server.md)