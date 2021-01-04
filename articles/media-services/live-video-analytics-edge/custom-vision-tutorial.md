---
title: Live video analyseren met Live Video Analytics op IoT Edge en Azure Custom Vision
description: Meer informatie over het gebruik van Azure Custom Vision om een containermodel te bouwen dat een speelgoedwagen kan detecteren en de AI-uitbreidbaarheid van Azure Live Video Analytics op Azure IoT Edge kan gebruiken om het model op de rand te implementeren om speelgoedwagens te detecteren in een live-videostream.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 614c4e401579eda68d8030dc2d2a42b2c4736031
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401692"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Zelfstudie: Live video analyseren met Live Video Analytics op IoT Edge en Azure Custom Vision

In deze zelfstudie leert u Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) te gebruiken om een containermodel te bouwen dat een speelgoedwagen kan detecteren en de [AI-uitbreidbaarheid](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) van Live Video Analytics op IoT Edge kan gebruiken om het model op de rand te implementeren om speelgoedwagens te detecteren in een live-videostream.

We laten u zien hoe u Custom Vision kunt inzetten om een computervisiemodel te maken en te trainen door enkele afbeeldingen te uploaden en er labels aan toe te voegen. U hebt geen kennis nodig van datawetenschappen, machine learning of AI. U leert ook de mogelijkheden van Live Video Analytics kennen om gemakkelijk een aangepast model te implementeren als een container aan de rand en een gesimuleerde live video-feed te analyseren.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De relevante resources instellen.
> * Een Custom Vision-model in de cloud maken om speelgoedwagens te detecteren en te implementeren aan de rand.
> * Een mediagrafiek met een http-extensie maken en implementeren in een Custom Vision-model.
> * De voorbeeldcode uitvoeren.
> * De resultaten onderzoeken en interpreteren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

Lees de volgende artikelen voordat u begint:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Azure Custom Vision-overzicht](../../cognitive-services/custom-vision-service/overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)
* [Live Video Analytics zonder video-opname](analyze-live-video-concept.md)
* [Live Video Analytics uitvoeren met uw eigen model](use-your-model-quickstart.md)
* [Zelfstudie: Een IoT Edge-module ontwikkelen](../../iot-edge/tutorial-develop-for-linux.md)
* [Implementatie .*.template.json bewerken](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Vereisten


::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end
## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken


In deze zelfstudie wordt gebruikgemaakt van een bestand met een [video van een speelgoedwagen](https://lvamedia.blob.core.windows.net/public/t2.mkv) om een livestream te simuleren. U kunt de video bekijken via een toepassing zoals [VLC Media Player](https://www.videolan.org/vlc/). Selecteer **CTRL + N** en plak vervolgens een link naar de [video van een speelgoedwagen](https://lvamedia.blob.core.windows.net/public/t2.mkv) om het afspelen te starten. U zult zien dat na 36 seconden in de video een speelgoedwagen verschijnt. Het aangepaste model is getraind om deze specifieke speelgoedwagen te detecteren. In deze zelfstudie gebruikt u Live Video Analytics op IoT Edge om dergelijke speelgoedwagens te detecteren en bijbehorende deductiegebeurtenissen te publiceren naar de IoT Edge-hub.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagram met een Custom Vision-overzicht.":::

Dit diagram laat zien hoe de signalen in deze zelfstudie stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server en verstuurt videoframes naar het [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor)-knooppunt.

Het knooppunt HTTP-extensie speelt de rol van een proxy.  Het knooppunt samplet de binnenkomende videoframes die u hebt ingesteld met het veld `samplingOptions`, en converteert de videoframes bovendien naar het gespecificeerde afbeeldingstype. Vervolgens wordt de afbeelding via REST doorgestuurd naar een andere Edge-module die een AI-model achter een HTTP-eindpunt uitvoert. In dit voorbeeld is die Edge-module het detectormodel van de speelgoedwagen, gebouwd met behulp van Custom Vision. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [Azure IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar de [IoT Edge-hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Een Custom Vision-detectiemodel voor speelgoed bouwen en implementeren 

Zoals de naam Custom Vision doet vermoeden, kunt u deze toepassing gebruiken om uw eigen aangepaste objectdetector of classificatie te bouwen in de cloud. De toepassing heeft een eenvoudige, gebruiksvriendelijke en intuïtieve interface voor het bouwen van Custom Vision-modellen die via containers kunnen worden geïmplementeerd in de cloud of aan de rand.

Voor het maken van een detector van speelgoedwagens, volgt u de stappen in [Quickstart: Een objectdetector bouwen met de Custom Vision-website](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

Aanvullende opmerkingen:
 
* Gebruik voor deze zelfstudie niet de voorbeeldafbeeldingen uit de [sectie Vereisten](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) van het quickstart-artikel. In plaats daarvan hebben we een bepaalde afbeelding gebruikt om een Custom Vision-model voor een speelgoeddetector te maken. Gebruik [deze afbeeldingen](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) wanneer u wordt gevraagd [trainingsafbeeldingen](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) in de quickstart te kiezen.
* Zorg er in de sectie Afbeelding labelen van de quickstart voor dat u de speelgoedwagen in de foto labelt als 'bestelwagen'.

Wanneer u klaar bent, kunt u het model exporteren naar een Docker-container met behulp van de knop **Exporteren** op het tabblad **Prestaties**. Zorg ervoor dat u Linux kiest als containerplatformtype. Dit is het platform waarop de container wordt uitgevoerd. De computer waarop u de container downloadt, kan Windows of Linux zijn. De volgende instructies zijn gebaseerd op een containerbestand dat op een Windows-computer gedownload is.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Schermopname met Dockerfile geselecteerd.":::
 
1. U moet een zip-bestand downloaden naar de lokale computer met de naam `<projectname>.DockerFile.Linux.zip`. 
1. Controleer of Docker is geïnstalleerd. Als dat niet het geval is, installeert u [Docker](https://docs.docker.com/get-docker/) voor uw Windows-bureaublad.
1. Pak het gedownloade bestand uit op een locatie naar keuze. Gebruik de opdrachtregel om naar de uitgepakte map te gaan.
    
    Voer de volgende opdrachten uit:
    
    1. `docker build -t cvtruck` 
    
        Met deze opdracht worden veel pakketten gedownload, wordt de Docker-installatiekopie gebouwd en gelabeld als `cvtruck:latest`.
    
        > [!NOTE]
        > Als dat is gelukt, ziet u de volgende berichten: `Successfully built <docker image id>` en `Successfully tagged cvtruck:latest`. Als de opdracht mislukt, probeert u het opnieuw. Soms worden afhankelijkheidspakketten niet meteen de eerste keer gedownload.
    1. `docker  image ls`

        Met deze opdracht wordt gecontroleerd of de nieuwe installatiekopie zich in het lokale register bevindt.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Deze opdracht publiceert de opengestelde poort (80) van Docker naar de poort van uw lokale machine (80).
    1. `docker container ls`
    
        Deze opdracht controleert de toewijzing van de poorten, en of de Docker-container correct wordt uitgevoerd op uw machine. De uitvoer moet er ongeveer als volgt uitzien:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Met deze opdracht wordt de container op de lokale computer getest. Als de installatiekopie dezelfde bestelwagen heeft als waarop we het model hebben getraind, moet de uitvoer er ongeveer als volgt uitzien. Er wordt aangegeven dat de bestelwagen werd gedetecteerd met een waarschijnlijkheid van 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>De voorbeeldbestanden bekijken


::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Het implementatiemanifest genereren en implementeren

1. Ga in Visual Studio Code naar src/cloud-to-device-console-app/operations.json.

1. Controleer onder `GraphTopologySet` of het volgende waar is:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Controleer onder `GraphInstanceSet` het volgende:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. En voeg het volgende toe bovenaan de matrix met parameters: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Wijzig de waarde van de `rtspUrl`-parameter in `"rtsp://rtspsim:554/media/t2.mkv"`.
1. Controleer of `GraphTopologyDelete` is ingesteld op `"name": "InferencingWithHttpExtension"`.
1. Klik met de rechtermuisknop op het bestand src/edge/deployment.customvision.template.json en selecteer **IoT Edge-implementatiemanifest genereren**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Schermopname van IoT Edge-implementatiemanifest genereren.":::
  
    Hiermee maakt u een manifestbestand in de map src/edge/config met de naam deployment.customvision.amd64.json.
1. Open het bestand src/edge/ deployment.customvision.template.json en zoek het JSON-blok `registryCredentials`. In dit blok vindt u het adres van uw Azure-containerregister samen met de gebruikersnaam en het wachtwoord.
1. Push de lokale Custom Vision-container naar uw Azure Container Registry door de volgende stappen bij de opdrachtregel te volgen:

    1. Meld u aan bij het register met de volgende opdracht:
    
        `docker login <address>`
    
        Voer de gebruikersnaam en het wachtwoord in als u om verificatie gevraagd wordt.
        
        > [!NOTE]
        > Het wachtwoord is niet zichtbaar in de opdrachtregel.
    1. Label uw installatiekopie met behulp van deze opdracht: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Push uw installatiekopie met behulp van deze opdracht: <br/>`docker push <address>/cvtruck`.

        Als het goed is, wordt `Pushed` in de opdrachtregel weergegeven samen met de SHA voor de installatiekopie.
    1. U kunt ook bevestigen door uw Azure Container Registry te controleren in de Azure-portal. Hier ziet u de naam van de opslagplaats samen met het label.
1. Stel de IoT Hub-verbindingsreeks in door naast het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek te klikken op het pictogram **Meer acties**. U kunt de tekenreeks uit het bestand appsettings.json kopiëren. (Hier volgt een andere aanbevolen methode om ervoor te zorgen dat u de juiste IoT Hub hebt geconfigureerd in Visual Studio code via de opdracht [IOT Hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Schermopname van IoT Hub-verbindingsreeks instellen.":::
1. Klik vervolgens met de rechtermuisknop op src/edge/config/ deployment.customvision.amd64.json en selecteer **Implementatie voor één apparaat maken**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Schermopname van Implementatie voor één apparaat maken.":::
1. Vervolgens wordt u gevraagd om een IoT Hub-apparaat te selecteren. Selecteer **lva-sample-device** in de vervolgkeuzelijst.
1. Vernieuw de Azure IoT-hub na ongeveer 30 seconden in de sectie linksonder. U ziet dat op het edge-apparaat de volgende modules zijn geïmplementeerd:

    * Live Video Analytics in de IoT Edge-module `lvaEdge`.
    * Een module met de naam `rtspsim`, die een RTSP-server simuleert die fungeert als de bron van een live-videofeed.
    * Een module met de naam `cv` die, zoals de naam doet vermoeden, het Custom Vision-detectiemodel voor de speelgoedwagen is dat Custom Vision toepast op de afbeeldingen en meerdere labeltypes retourneert. (Ons model is op slechts één label getraind: "bestelwagen").

## <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster **UITVOER** van Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Schermopname van Bewaking van ingebouwd gebeurteniseindpunt starten.":::

## <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

Als u de grafiektopologie voor deze zelfstudie in een browser opent, ziet u dat de waarde van `inferencingUrl` is ingesteld op `http://cv:80/image`. Deze instelling betekent dat de deductieserver resultaten retourneert na het detecteren van speelgoedwagens, als die er zijn, in de live video.

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op **Ctrl+Shift+X**) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Schermopname met Extensie-instellingen geselecteerd.":::
1. Zoek **Uitgebreid bericht tonen** en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Schermopname van Uitgebreid bericht tonen.":::
1. Selecteer de toets **F5** om een foutopsporingssessie te starten. U ziet de berichten die worden afgedrukt in het venster **TERMINAL**.
1. De code operations.json wordt gestart met aanroepen naar de directe methoden `GraphTopologyList` en `GraphInstanceList`. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de **Enter**-toets om door te gaan.
    
   In het **TERMINAL**-venster wordt de volgende set aanroepen van directe methoden weergegeven:
    
   * Een aanroep van `GraphTopologySet` waarin gebruik wordt gemaakt van de voorgaande `topologyUrl`.
   * Een aanroep van `GraphInstanceSet` waarin gebruik wordt gemaakt van de volgende hoofdtekst:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
   ```
    
   * Een aanroep van `GraphInstanceActivate` waarmee de instantie van de grafiek en de videostroom worden gestart.
   * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het grafiekexemplaar wordt uitgevoerd.
    
1. De uitvoer in het **TERMINAL**-venster wordt onderbroken met de prompt **Druk op Enter om door te gaan**. Druk nog niet op **Enter**. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze zelfstudie worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagrafiek wilt stoppen, keert u terug naar het **TERMINAL**-venster en drukt u op **Enter**.
Met de volgende reeks aanroepen worden resources opgeschoond:
    
   * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
   * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
   * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
   * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.
    
## <a name="interpret-the-results"></a>De resultaten interpreteren

Wanneer u de mediagraaf uitvoert, worden de resultaten van het HTTP-extensieprocessor-knooppunt via het IoT Hub-sink-knooppunt naar de IoT-hub doorgevoerd. De berichten in het **UITVOER**-venster bevatten een hoofdgedeelte en een `applicationProperties`-gedeelte. Zie [IoT Hub-berichten maken en lezen](../../iot-hub/iot-hub-devguide-messages-construct.md) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagrafiek wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live555-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt. Het gebeurtenistype is `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

In dit bericht ziet u deze gegevens:

* Het bericht is een diagnostische gebeurtenis. `MediaSessionEstablished` geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een gesimuleerde livefeed.
* In `applicationProperties` geeft `subject` aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In `applicationProperties` geeft het gebeurtenistype aan dat dit een diagnostische gebeurtenis is.
* De gebeurtenistijd geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De hoofdtekst bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Deductiegebeurtenis

Het knooppunt van de HTTP-extensieprocessor ontvangt deductieresultaten van de Custom Vision-container en verzendt de resultaten via het IoT Hub Sink-knooppunt als deductiegebeurtenissen.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

* Het onderwerp in `applicationProperties` verwijst naar het knooppunt in de MediaGraph vanwaaruit het bericht werd gegenereerd. In dit geval is het bericht afkomstig van de HTTP-extensieprocessor.
* Het gebeurtenistype in `applicationProperties` geeft aan dat dit een analytische deductiegebeurtenis is.
* De gebeurtenistijd geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* Het hoofdgedeelte bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en daarom bevat het hoofdgedeelte een matrix met deducties, 'voorspellingen' genaamd.
* Het gedeelte 'voorspellingen' bevat een lijst met voorspellingen over waar de speelgoedbestelwagen (label=bestelwagen) zich in het frame bevindt. Zoals u weet, is 'bestelwagen' de aangepaste tag die u hebt geleverd aan uw getrainde model voor de speelgoedwagen. Het model deduceert en identificeert de speelgoedwagen in de invoervideo met verschillende betrouwbaarheidsscores.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere zelfstudies of quickstarts wilt proberen, kunt u daarvoor de resources gebruiken die u hebt gemaakt. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze zelfstudie hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md).

Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) te volgen.