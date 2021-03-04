---
title: Live video analyseren door gebruik te maken van Intel™ DL streamer-Edge ai-extensie via gRPC
description: In deze zelf studie leert u hoe u de Intel™ DL streamer-Edge AI-extensie van Intel kunt gebruiken om een live video-feed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 62787bfb586f2847d984499cf966708749184ee1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096340"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Zelf studie: Live video analyseren door gebruik te maken van Intel™ DL streamer-Edge ai-extensie 

In deze zelf studie leert u hoe u de Intel™ DL streamer-Edge AI-extensie van Intel kunt gebruiken om een live video-feed van een (gesimuleerde) IP-camera te analyseren. U ziet hoe deze Afleidings server u toegang geeft tot verschillende modellen voor het detecteren van objecten (een persoon, een voer tuig of een fiets), object classificatie (toewijzing van het Voer tuig) en een model voor object tracking (persoon, voer tuig en fiets). Dankzij de integratie met de gRPC-module kunt u video frames verzenden naar de AI-server voor inschakeling. De resultaten worden vervolgens naar de IoT Edge hub verzonden. Wanneer u deze service voor afwijzen uitvoert op hetzelfde reken knooppunt als live video analyse, kunt u gebruikmaken van het verzenden van video gegevens via gedeeld geheugen. Op die manier kunt u zich afdoen van de frame snelheid van de live video-feed (bijvoorbeeld 30 frames per seconde). 

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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

In deze Quick Start gebruikt u live video Analytics op IoT Edge samen met de Intel open-™ DL streamer-Edge AI-extensie van Intel om objecten zoals Voer tuigen te detecteren, voor het classificeren van Voer tuigen of het volgen van Voer tuigen, personen of fietsen. U publiceert de resulterende deductiegebeurtenissen naar IoT Edge-hub.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Overzicht van LVA MediaGraph":::

Dit diagram laat zien hoe de signalen in deze quickstart stromen. Een [rand module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera waarop een RTSP-server (Real-Time Streaming Protocol) wordt gehost. Een [RTSP-bron](media-graph-concept.md#rtsp-source) knooppunt haalt de video-feed van deze server op en verzendt video frames naar het [gRPC extension-processor](media-graph-concept.md#grpc-extension-processor) knooppunt. 

Het gRPC extension-processor knooppunt gebruikt gedecodeerde video frames als invoer en stuurt deze frames door naar een [gRPC](terminology.md#grpc) -eind punt dat door een GRPC-server wordt weer gegeven. Het knoop punt ondersteunt het overdragen van gegevens met behulp van [gedeeld geheugen](https://en.wikipedia.org/wiki/Shared_memory) of het rechtstreeks insluiten van inhoud in de hoofd tekst van gRPC-berichten. Daarnaast heeft het knoop punt een ingebouwde afbeeldings indeling voor het schalen en coderen van video frames voordat ze worden doorgestuurd naar het gRPC-eind punt. De schaalset bevat opties voor de hoogte-breedte verhouding van de afbeelding die moet worden behouden, aangevuld of uitgerekt. De afbeeldings encoder ondersteunt de indelingen JPEG, PNG of bmp. Meer informatie over de processor [vindt u hier](media-graph-extension-concept.md#grpc-extension-processor).

In deze zelfstudie leert u het volgende:

1. Implementeer de media grafiek.
1. De resultaten interpreteren.
1. Resources opschonen.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Over de Intel open-wijnbouw™ DL streamer-Edge AI-extensie module


De open-wijn™ DL streamer-Edge AI-extensie module is een micro service op basis van de video analyse van Intel (in de staat van de vaste lijn) die video Analytics-pijp lijnen bevat die zijn gebouwd met open-wijnbouw™ DL-streamer. Ontwikkel aars kunnen gedecodeerde video frames verzenden naar de AI-extensie module waarmee detectie, classificatie of tracking wordt uitgevoerd en de resultaten worden geretourneerd. De AI-extensie module geeft gRPC-Api's weer die compatibel zijn met video analyse platforms zoals live video Analytics op IoT Edge van micro soft. 

Als u complexe, hoogwaardige analyseoplossingen voor live videoanalyse wilt maken, moet u de module Live Video Analytics op IoT Edge koppelen aan een krachtige deductie-engine die de schaal aan de rand kan benutten. In deze zelf studie worden aanvragen voor het afschermen van e-mail verzonden naar de [Intel openwijnbouw™ DL streamer-Edge ai-extensie](https://aka.ms/lva-intel-openvino-dl-streamer), een Edge-module die is ontworpen voor gebruik met live video Analytics op IOT Edge. 

In de eerste versie van deze deductieserver hebt u toegang tot de volgende [modellen](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options):

- object_detection voor person_vehicle_bike_detection ![ object detectie voor het Voer tuig](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification voor vehicle_attributes_recognition ![ object classificatie voor het Voer tuig](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking voor het ![ volgen van person_vehicle_bike_tracking object voor een persoons voertuig](./media/use-intel-openvino-tutorial/object-tracking.png)

Er wordt gebruikgemaakt van vooraf geladen object detectie, object classificaties en object tracerings pijplijnen om snel aan de slag te gaan. Daarnaast wordt het geleverd met vooraf geladen [persoon-motor voertuigen-fiets detectie-Crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) en [voertuig Attributes-Recognition-barrière-0039-modellen](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md).

> [!NOTE]
> Door de Edge-module te downloaden en gebruiken: open-wijnbouw™ DL streamer-Edge AI-extensie van Intel en de meegeleverde software, gaat u akkoord met de voor waarden van de [licentie overeenkomst](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel hecht veel waarde aan mensenrechten en probeert nooit betrokken te raken bij zaken waarbij mensenrechten worden geschonden. Bekijk de [Mensenrechtenprincipes van Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). De producten en software van Intel zijn alleen bedoeld om te worden gebruikt in toepassingen die internationaal erkende mensenrechten niet schenden of bijdragen aan een schending.

U kunt de flexibiliteit van de verschillende pijp lijnen voor uw specifieke use-case gebruiken door eenvoudigweg de pijplijn omgevings variabelen in uw implementatie sjabloon te wijzigen. Zo kunt u het pijplijn model snel wijzigen en in combi natie met live video analyse is het een paar seconden voor het wijzigen van de media pijplijn en het depresentatie model.  

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Bewerk onder `GraphInstanceSet` de naam van de graaftopologie zodat deze overeenkomt met de waarde in de voorgaande link:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Bewerk de naam onder `GraphTopologyDelete`:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

1. Klik met de rechter muisknop op het bestand *src/Edge/deployment.openvino.grpc.cpu.template.js* en selecteer vervolgens **IOT Edge implementatie manifest genereren**.

    ![IoT Edge-implementatiemanifest genereren](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    De *deployment.openvino.grpc.cpu.amd64.jsin* het manifest bestand wordt gemaakt in de map *src/Edge/config* .

> [!NOTE]
We hebben ook een *deployment.openvino.grpc.gpu.template.jsopgenomen in* een sjabloon waarmee GPU-ondersteuning wordt ingeschakeld voor de Intel open-wijn DL-uitbreidings module voor de AI-extensie. Deze sjablonen verwijzen naar de installatie kopie van de docker hub van Intel.

De hierboven genoemde sjablonen verwijzen naar de Intel docker hub-installatie kopie. Als u liever een kopie op uw eigen Azure Container Registry wilt hosten, kunt u stap 1 en 2 hieronder volgen:
1. SSH naar een apparaat waarop docker CLI-hulpprogram ma's zijn geïnstalleerd (dat wil zeggen uw edge-apparaat) en pull/tag/push de container met de volgende stappen:
    * Intel-installatie kopie van docker hub halen:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Label de installatie kopie van Intel met uw eigen Azure Container Registry naam. Vervang {YOUR ACR NAME} door de naam van uw ACR die u in het. env-bestand kunt vinden:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Uw gecodeerde installatie kopie naar uw Azure Container Registry pushen:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Nu moet u de sjablonen bewerken om te verwijzen naar uw nieuwe installatie kopie die wordt gehost op Azure Container Registry.
    * Klik met de rechter muisknop op de *deployment.openvino.grpc.cpu.template.jsop* en navigeer naar het module *lavExtension* en vervang:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        door:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Herhaal stap 2 voor de *deployment.openvino.grpc.gpu.template.jsop*


3. Als u de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md) hebt voltooid, kunt u deze stap overslaan. 

    Als dat niet het geval is, selecteert u in het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek het pictogram **Meer acties** en selecteert u vervolgens **IoT Hub-verbindingsreeks instellen**. U kunt de tekenreeks uit het bestand *appsettings.json* kopiëren. Als u er zeker van wilt zijn dat u de juiste IoT-hub in Visual Studio Code hebt geconfigureerd, gebruikt u de [opdracht IoT Hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Mogelijk wordt u gevraagd om ingebouwde eindpunt gegevens voor de IoT Hub op te geven. Als u deze informatie wilt ophalen, gaat u in Azure Portal naar uw IoT Hub en zoekt u naar de optie **ingebouwde eind punten** in het navigatie deel venster links. Klik op deze en zoek naar het **eind punt Event hub** onder **Event hub-compatibel eind punt** . Kopieer en gebruik de tekst in het vak. Het eind punt ziet er ongeveer als volgt uit:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klik met de rechter muisknop op *src/Edge/config/deployment.openvino.grpc.cpu.template.js* en selecteer **implementatie maken voor één apparaat**. 

    ![Implementatie voor één apparaat maken](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, selecteert u **lva-sample-device**.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:

    * De Live Video Analytics-module met de naam **lvaEdge**
    * De **rtspsim**-module, die een RTSP-server simuleert en fungeert als de bron van een livevideofeed
    * De **lvaExtension** -module, de Intel open-wijn™ DL streamer-Edge ai-extensie 

### <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster **UITVOER** van Visual Studio Code. 

![Bewaking starten](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Voer het voorbeeld programma uit om de Voer tuigen, personen of fiets te detecteren
Als u de [grafiek topologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) voor deze zelf studie in een browser opent, ziet u dat de waarde van `grpcExtensionAddress` is ingesteld op `tcp://lvaExtension:5001` , vergeleken met het *httpExtensionOpenVINO* -voor beeld, dan hoeft u de URL naar de gRPC-server niet te wijzigen. In plaats daarvan geeft u de module instructies om een specifieke pijp lijn uit te voeren op basis van de omgevings variabelen die u eerder hebt opgegeven. In de standaard sjabloon is dit ingesteld op: ' object_detection ' voor ' person_vehicle_bike_detection '. U kunt experimenteren met andere ondersteunde pijp lijnen. 

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
             "topologyName": "InferencingWithOpenVINOgRPC",
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
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

In dit bericht ziet u deze gegevens:

* Het bericht is een diagnostische gebeurtenis. `MediaSessionEstablished` geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In `applicationProperties` geeft `subject` aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een diagnostische gebeurtenis is.
* De `eventTime` geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De `body` bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Deductiegebeurtenis

Het processor knooppunt van de gRPC-extensie ontvangt een afkomst van de resultaten van de™ Intel open DL streamer-Edge AI-extensie. Vervolgens worden de resultaten door het knooppunt voor de IoT Hub-sink verzonden als deductiegebeurtenissen. 

In deze gebeurtenissen wordt het type ingesteld op `entity` om aan te geven dat het een entiteit is, zoals een auto of vrachtwagen. De waarde `eventTime` is de UTC-tijd waarop het object is gedetecteerd. 

In het volgende voor beeld ziet u dat er een voer tuig wordt geïdentificeerd, het type van het Voer tuig (van) en de kleur (wit), allemaal met een betrouwbaarheids niveau van meer dan 0,9, wordt er ook een ID aan de entiteit toegewezen wanneer we het object tracking model gebruiken.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
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
Als u een ander model wilt gebruiken, moet u de implementatie sjabloon wijzigen. Als u wilt scha kelen tussen de ondersteunde modellen, kunt u de omgevings variabelen in de lvaExtenstion-module wijzigen. Zie dit [document op github](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) voor de ondersteunde waarden en combi Naties voor modellen.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP] Kopieer de sjabloon en sla deze onder een nieuwe naam op voor elke mogelijke pijp lijn. Op deze manier kunt u scha kelen tussen modellen door een nieuwe implementatie te maken op basis van een van deze sjablonen.

Zodra u de variabelen hebt gewijzigd, kunt u de sjabloon opnieuw implementeren op het apparaat. U kunt nu de bovenstaande stappen herhalen om het voorbeeld programma opnieuw uit te voeren met de nieuwe pijp lijn. De resultaten van de deinterferentie zijn vergelijkbaar (in schema), maar er wordt meer of minder informatie weer gegeven, afhankelijk van het pijplijn model dat u hebt gekozen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u andere quickstarts of zelfstudies wilt proberen, moet u de resources die u hebt gemaakt, bewaren. Anders gaat u in Azure Portal naar de resourcegroepen, selecteert u de resourcegroep waar u deze zelfstudie hebt uitgevoerd en verwijdert u vervolgens alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een Intel x64 Linux-apparaat in plaats van een Azure Linux-VM. Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md). Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) te volgen.
