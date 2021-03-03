---
title: Live video analyseren met OpenVINO™ Model Server – AI Extension van Intel
description: In deze zelfstudie gebruikt u een AI-modelserver van Intel om de live videofeed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 68b5b7561cc31e156a745bcfb07e3203de10d425
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702212"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Zelfstudie: Live video analyseren met OpenVINO™ Model Server – AI Extension van Intel 

In deze zelfstudie wordt uitgelegd hoe u de OpenVINO™ Model Server – AI Extension van Intel gebruikt om een live videofeed van een (gesimuleerde) IP-camera te analyseren. U ontdekt hoe deze deductieserver u toegang geeft tot modellen voor het detecteren van objecten (een persoon, een voertuig of een fiets) en u een model biedt voor het classificeren van voertuigen. Een subset van de frames in de live videofeed wordt verzonden naar een deductieserver en de resultaten worden verzonden naar de IoT Edge-hub.

In deze zelfstudie wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live videostream. De quickstart is gebaseerd op de voorbeeldcode die is geschreven in C# en bouwt voort op de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md).

> [!NOTE]
> Deze zelfstudie vereist het gebruik van een x86-64-apparaat als Edge-apparaat.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) als u nog geen account hebt.
  > [!NOTE]
  > U hebt een Azure-abonnement met machtigingen nodig voor het maken van service-principals (de **rol van eigenaar** biedt dit). Als u niet over de juiste machtigingen beschikt, neemt u contact op met uw account beheerder om u de juiste machtigingen te verlenen. 
* [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Als u de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md) nog niet hebt voltooid, moet u de stappen om [Azure-resources in te stellen](detect-motion-emit-events-quickstart.md#set-up-azure-resources) voltooien.

> [!TIP]
> Tijdens de installatie van de Azure IoT Tools wordt u mogelijk gevraagd om Docker te installeren. U kunt de vraag negeren.

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken

Bij het instellen van de Azure-resources wordt een korte video van een parkeerplaats gekopieerd naar de virtuele Linux-machine in Azure die u als IoT Edge-apparaat gebruikt. In deze quickstart wordt het videobestand gebruikt voor het simuleren van een livestream.

Open een toepassing als [VLC Media Player](https://www.videolan.org/vlc/). Selecteer CTRL + N en plak vervolgens een link naar [de video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) om het afspelen te starten. U ziet de beelden van de voertuigen op een parkeerplaats. Het merendeel staat geparkeerd en één voertuig beweegt.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

In deze quickstart gebruikt u Live Video Analytics op IoT Edge en de OpenVINO™ Model Server – AI Extension van Intel om objecten zoals voertuigen te detecteren of classificeren. U publiceert de resulterende deductiegebeurtenissen naar IoT Edge-hub.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Overzicht":::

Dit diagram laat zien hoe de signalen in deze quickstart stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server en verstuurt videoframes naar het [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor)-knooppunt. 

Het knooppunt HTTP-extensie speelt de rol van een proxy. Het knooppunt samplet de door uw '`samplingOptions`'-veld ingestelde binnenkomende videoframes en converteert de videoframes bovendien naar het gespecificeerde afbeeldingstype. Vervolgens wordt de afbeelding via REST doorgestuurd naar een andere Edge-module die AI-modellen achter een HTTP-eindpunt uitvoert. In dit voorbeeld is die Edge-module de OpenVINO™ Model Server – AI Extension van Intel. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

In deze zelfstudie leert u het volgende:

1. De mediagrafiek maken en implementeren, waardoor deze wordt gewijzigd.
1. De resultaten interpreteren.
1. Resources opschonen.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Over OpenVINO™ Model Server – AI Extension van Intel

De Intel®-distributie van de [OpenVINO™-toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (open visuele deductie en optimalisatie van neurale netwerken) is een gratis softwareset waarmee ontwikkelaars en gegevenswetenschappers Computer Vision-workloads kunnen versnellen, deductie en implementaties van deep learning kunnen stroomlijnen en eenvoudige, heterogene uitvoering mogelijk kunnen maken op Intel®-platforms van rand naar cloud. De set bevat de Intel® Deep Learning Deployment Toolkit met modeloptimalisatie en deductieprogramma, en de opslagplaats [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo), die meer dan 40 geoptimaliseerde, vooraf getrainde modellen bevat.

Als u complexe, hoogwaardige analyseoplossingen voor live videoanalyse wilt maken, moet u de module Live Video Analytics op IoT Edge koppelen aan een krachtige deductie-engine die de schaal aan de rand kan benutten. In deze zelfstudie worden deductieaanvragen verzonden naar de [OpenVINO™ Model Server – AI Extension van Intel](https://aka.ms/lva-intel-ovms), een Edge-module die is ontworpen om te werken met Live Video Analytics op IoT Edge. Deze deductieservermodule bevat de OpenVINO™ Model Server (OVMS), een deductieserver die wordt aangedreven door de OpenVINO™-toolkit, die geoptimaliseerd is voor Computer Vision-workloads en die is ontwikkeld voor Intel®-architecturen. Er is een uitbreiding toegevoegd aan OVMS voor een eenvoudige uitwisseling van videoframes en deductieresultaten tussen de deductieserver en Live Video Analytics-module op IoT Edge, zodat u elk voor OpenVINO™-toolkit ondersteund model kunt uitvoeren (u kunt de deductieservermodule aanpassen door de [code](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper) aan te passen). U kunt verder kiezen uit de vele versnellingsmechanismen van Intel®-hardware. Dit zijn onder andere CPU's (Atom, Core, Xeon), FPGA's en VPU’s.

In de eerste versie van deze deductieserver hebt u toegang tot de volgende [modellen](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Voertuigdetectie (deductie-URL: http://{module-name}: 4000/vehicleDetection)
- Detectie van personen/voertuigen/fietsen (deductie-URL: http://{module-name}:4000/personVehicleBikeDetection)
- Voertuigclassificatie (deductie-URL: http://{module-name}: 4000/vehicleClassification)
- Gezichtsdetectie (deductie-URL: http://{module-name}: 4000/faceDetection)

> [!NOTE]
> Wanneer u de Edge-module: OpenVINO™ Model Server – AI Extension van Intel en de bijbehorende software downloadt en gebruikt, gaat u akkoord met de algemene voorwaarden van de [licentieovereenkomst](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel hecht veel waarde aan mensenrechten en probeert nooit betrokken te raken bij zaken waarbij mensenrechten worden geschonden. Bekijk de [Mensenrechtenprincipes van Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). De producten en software van Intel zijn alleen bedoeld om te worden gebruikt in toepassingen die internationaal erkende mensenrechten niet schenden of bijdragen aan een schending.

## <a name="create-and-deploy-the-media-graph"></a>De mediagrafiek maken en implementeren

### <a name="examine-and-edit-the-sample-files"></a>De voorbeeld bestanden bekijken en bewerken

Als onderdeel van de vereisten hebt u de voorbeeldcode naar een map gedownload. Volg deze stappen om de voorbeeldbestanden te bekijken en te bewerken.

1. Ga in Visual Studio Code naar *src/edge*. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat. Deze bevat enkele tijdelijke waarden. Het *.env*-bestand bevat de waarden voor die variabelen.

1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:

    * ***c2d-console-app.csproj***: het projectbestand voor Visual Studio Code.
    * ***operations.json***: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    * ***Program.cs***: de voorbeeldcode van het programma. Deze code:

        * De app-instellingen laden.
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen.
        * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
        * Roept directe methoden aan voor het opschonen van resources.


1. Bewerk het bestand *operations.json*:
    * Wijzig de link naar de graaftopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * Bewerk onder `GraphInstanceSet` de naam van de graaftopologie zodat deze overeenkomt met de waarde in de voorgaande link:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Bewerk de naam onder `GraphTopologyDelete`:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

1. Klik met de rechtermuisknop op het bestand *src/edge/deployment.openvino.template.json* en klik op **IoT Edge-implementatiemanifest genereren**.

    ![IoT Edge-implementatiemanifest genereren](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    De *deployment.openvino.amd64.jsin* het manifest bestand wordt gemaakt in de map *src/Edge/config* .

1. Als u de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md) hebt voltooid, kunt u deze stap overslaan. 

    Als dat niet het geval is, selecteert u in het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek het pictogram **Meer acties** en selecteert u vervolgens **IoT Hub-verbindingsreeks instellen**. U kunt de tekenreeks uit het bestand *appsettings.json* kopiëren. Als u er zeker van wilt zijn dat u de juiste IoT-hub in Visual Studio Code hebt geconfigureerd, gebruikt u de [opdracht IoT Hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Mogelijk wordt u gevraagd om ingebouwde eindpunt gegevens voor de IoT Hub op te geven. Als u deze informatie wilt ophalen, gaat u in Azure Portal naar uw IoT Hub en zoekt u naar de optie **ingebouwde eind punten** in het navigatie deel venster links. Klik op deze en zoek naar het **eind punt Event hub** onder **Event hub-compatibel eind punt** . Kopieer en gebruik de tekst in het vak. Het eind punt ziet er ongeveer als volgt uit:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klik met de rechtermuisknop op *src/edge/config/deployment.openvino.amd64.json* en selecteer **Implementatie voor één apparaat maken**. 

    ![Implementatie voor één apparaat maken](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, selecteert u **lva-sample-device**.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:

    * De Live Video Analytics-module met de naam **lvaEdge**
    * De **rtspsim**-module, die een RTSP-server simuleert en fungeert als de bron van een livevideofeed
    * De module **openvino**, de module OpenVINO™ Model Server – AI Extension van Intel 

### <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster **UITVOER** van Visual Studio Code. 

![Bewaking starten](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Het voorbeeldprogramma uitvoeren om voertuigen te detecteren
Als u de [grafiektopologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) voor deze zelfstudie in een browser opent, ziet u dat de waarde van `inferencingUrl` is ingesteld op `http://openvino:4000/vehicleDetection`, wat betekent dat de deductieserver resultaten in de live video weergeeft na het detecteren van voertuigen, indien aanwezig.

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. Selecteer de toets F5 om een foutopsporingssessie te starten. U ziet de berichten die worden afgedrukt in het venster **TERMINAL**.
1. De code *operations.json* wordt gestart met aanroepen naar de directe methoden `GraphTopologyList` en `GraphInstanceList`. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de Enter-toets om door te gaan.

    In het **TERMINAL**-venster wordt de volgende set aanroepen van directe methoden weergegeven:

     * Een aanroep van `GraphTopologySet` waarin gebruik wordt gemaakt van de voorgaande `topologyUrl`
     * Een aanroep van `GraphInstanceSet` waarin gebruik wordt gemaakt van de volgende hoofdtekst:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * Een aanroep van `GraphInstanceActivate` waarmee het graafexemplaar en de videostroom worden gestart
     * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het graafexemplaar wordt uitgevoerd
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij een `Press Enter to continue`-prompt. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het **TERMINAL**-venster en selecteert u Enter. 

    Met de volgende reeks aanroepen worden resources opgeschoond:
      * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
      * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
      * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
      * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer u de mediagraaf uitvoert, worden de resultaten van het HTTP-extensieprocessor-knooppunt via het IoT Hub-sink-knooppunt naar de IoT-hub doorgevoerd. De berichten in het **UITVOER**-venster bevatten een sectie `body` en een sectie `applicationProperties`. Zie [IoT Hub-berichten maken en lezen](../../iot-hub/iot-hub-devguide-messages-construct.md) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live555-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt. Het gebeurtenistype is **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

In dit bericht ziet u deze gegevens:

* Het bericht is een diagnostische gebeurtenis. `MediaSessionEstablished` geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In `applicationProperties` geeft `subject` aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een diagnostische gebeurtenis is.
* De `eventTime` geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De `body` bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Deductiegebeurtenis

Het knooppunt voor de HTTP-extensieprocessor ontvangt de deductieresultaten van de module OpenVINO™ Model Server – AI Extension. Vervolgens worden de resultaten door het knooppunt voor de IoT Hub-sink verzonden als deductiegebeurtenissen. 

In deze gebeurtenissen wordt het type ingesteld op `entity` om aan te geven dat het een entiteit is, zoals een auto of vrachtwagen. De waarde `eventTime` is de UTC-tijd waarop het object is gedetecteerd. 

In het volgende voorbeeld zijn twee voertuigen gedetecteerd, met een betrouwbaarheidswaarde van meer dan 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

Let in de berichten op de volgende details:

* In `applicationProperties` verwijst `subject` naar het knooppunt in de graaftopologie vanwaaruit het bericht is gegenereerd. 
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een analytische gebeurtenis is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden.
* De sectie `body` bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en bevat de hoofdtekst daarom `inferences`-gegevens.
* De sectie `inferences` geeft aan dat het `type` `entity` is. Deze sectie bevat aanvullende gegevens over de entiteit.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Het voorbeeldprogramma uitvoeren om personen, voertuigen of fietsen te detecteren
Als u een ander model wilt gebruiken, moet u de grafiektopologie en het `operations.json`-bestand wijzigen.

Kopieer de [grafiektopologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) naar een lokaal bestand, bijvoorbeeld `C:\TEMP\topology.json`. Open die kopie en bewerk de waarde van `inferencingUrl` naar `http://openvino:4000/personVehicleBikeDetection`.

Ga vervolgens in Visual Studio Code naar de map *src/cloud-to-device-console-app* en open het bestand `operations.json`. Bewerk de regel met `topologyUrl` naar:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
U kunt nu de bovenstaande stappen herhalen om het voorbeeldprogramma opnieuw uit te voeren met de nieuwe topologie. De deductieresultaten zijn vergelijkbaar (in het schema) met die van het voertuigdetectiemodel, maar dan met de `subtype` ingesteld op `personVehicleBikeDetection`.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Het voorbeeldprogramma uitvoeren om voertuigen te classificeren
Open in Visual Studio Code de lokale kopie van `topology.json` uit de vorige stap en bewerk de waarde van `inferencingUrl` naar `http://openvino:4000/vehicleClassification`. Als u het vorige voorbeeld hebt uitgevoerd om personen, voertuigen of fietsen te detecteren, hoeft u het `operations.json`-bestand niet opnieuw te wijzigen.

U kunt nu de bovenstaande stappen herhalen om het voorbeeldprogramma opnieuw uit te voeren met de nieuwe topologie. Hier is een voorbeeld van een classificatieresultaat.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Het voorbeeldprogramma uitvoeren om gezichten te detecteren
Open in Visual Studio Code de lokale kopie van `topology.json` uit de vorige stap en bewerk de waarde van `inferencingUrl` naar `http://openvino:4000/faceDetection`. Als u het vorige voorbeeld hebt uitgevoerd om personen, voertuigen of fietsen te detecteren, hoeft u het `operations.json`-bestand niet opnieuw te wijzigen.

U kunt nu de bovenstaande stappen herhalen om het voorbeeldprogramma opnieuw uit te voeren met de nieuwe topologie. Hier is een voorbeeld van een detectieresultaat (opmerking: de gebruikte video van de parkeerplaats bevat geen herkenbare gezichten, dus moet u een andere video gebruiken om dit model te proberen).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u andere quickstarts of zelfstudies wilt proberen, moet u de resources die u hebt gemaakt, bewaren. Anders gaat u in Azure Portal naar de resourcegroepen, selecteert u de resourcegroep waar u deze zelfstudie hebt uitgevoerd en verwijdert u vervolgens alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md). Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) te volgen.
