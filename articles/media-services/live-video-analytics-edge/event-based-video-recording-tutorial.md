---
title: Zelfstudie voor video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de cloud - Azure
description: In deze zelfstudie leert u hoe u Azure Live Video Analytics in Azure IoT Edge kunt gebruiken om een video-opname op basis van gebeurtenissen te maken in de cloud, en deze af te spelen vanuit de cloud.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: fb8d8c8499029777ae5765f53ff51db92b3bd587
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954460"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Zelfstudie: Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de cloud

In deze zelfstudie leert u hoe u Azure Live Video Analytics in Azure IoT Edge kunt gebruiken om selectief delen van een live-videobron op te nemen naar Azure Media Services in de cloud. Deze use case wordt in deze zelfstudie aangeduid als [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md) (EVR). Om gedeelten van een livevideo op te nemen gebruikt u een AI-model voor objectdetectie om naar objecten in de video te zoeken en videoclips alleen op te nemen wanneer een bepaald type object wordt gedetecteerd. U leert ook hoe u de opgenomen videoclips kunt afspelen met behulp van Media Services. Deze mogelijkheid is handig voor een aantal scenario's waarbij een archief met belangrijke videoclips moet worden bewaard. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De relevante resources instellen.
> * De code controleren die EVR uitvoert.
> * De voorbeeldcode uitvoeren.
> * De resultaten controleren en de video bekijken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

Lees deze artikelen voordat u begint:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md) 
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Zelfstudie: Een IoT Edge-module ontwikkelen](../../iot-edge/tutorial-develop-for-linux.md)
* [Implementatie .*.template.json bewerken](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sectie over [routes declareren in het IoT Edge-distributiemanifest](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Vereisten

Vereisten voor deze zelfstudie:

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer met de extensies [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) en [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > U wordt mogelijk gevraagd om Docker te installeren. U kunt deze prompt negeren.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) op uw ontwikkelcomputer.
* Voltooi het [Installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) en [stel de omgeving in](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Aan het einde van deze stappen hebt u relevante Azure-resources geïmplementeerd in uw Azure-abonnement:

* Azure IoT Hub
* Azure Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](../../iot-edge/how-to-install-iot-edge.md) geïnstalleerd

> [!TIP]
> Als u problemen ondervindt met Azure-resources die worden gemaakt, raadpleegt u onze **[probleemoplossingsgids](troubleshoot-how-to.md#common-error-resolutions)** , waarmee u enkele veelvoorkomende problemen kunt oplossen.

## <a name="concepts"></a>Concepten

Video-opname op basis van gebeurtenissen verwijst naar het proces van het opnemen van video geactiveerd op basis van een gebeurtenis. Deze gebeurtenis kan worden gegenereerd vanuit:
- Verwerking van het videosignaal zelf, bijvoorbeeld bij het detecteren van een bewegend object in de video.
- Een onafhankelijke bron, bijvoorbeeld het openen van een deur. 

U kunt de opname alleen activeren wanneer een deductieservice detecteert dat er een bepaalde gebeurtenis is opgetreden. In deze zelfstudie gebruikt u een video van voertuigen die op een snelweg rijden, en neemt u videoclips op wanneer een truck wordt gedetecteerd.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Mediagrafiek":::

In het diagram ziet u een afbeelding van een [mediagrafiek](media-graph-concept.md) en aanvullende modules die helpen het gewenste scenario bereiken. Er worden vier IoT Edge-modules gebruikt:

* Live Video Analytics in IoT Edge-module.
* Een Edge-module met een AI-model achter een HTTP-eindpunt. Deze AI-module maakt gebruik van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, waarmee vele typen objecten kunnen worden gedetecteerd.
* Een aangepaste module voor het tellen en filteren van objecten, die in het diagram een Object Counter wordt genoemd. U maakt een Object Counter en implementeert deze in deze zelfstudie.
* Een [RTSP Simulator-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) om een RTSP-camera te simuleren.
    
Zoals in het diagram wordt getoond, gebruikt u een [RTSP-bronknooppunt](media-graph-concept.md#rtsp-source) in de mediagrafiek om de gesimuleerde livevideo van verkeer op een snelweg vast te leggen en deze video naar twee paden te verzenden:

* Het eerste pad loopt naar een HTTP-extensieknooppunt. Het knooppunt test de videoframes op een waarde die is ingesteld door u, met behulp van het veld `samplingOptions`, en geeft vervolgens de frames als afbeeldingen door naar de AI-module YOLOv3, wat een objectdetector is. Het knooppunt ontvangt de resultaten. Dit zijn de objecten (voertuigen in het verkeer) die via het model zijn gedetecteerd. Het HTTP-extensieknooppunt publiceert vervolgens de resultaten via het IoT Hub Message Sink-knooppunt naar de IoT Edge-hub.
* De objectCounter-module is ingesteld om berichten te ontvangen van de IoT Edge-hub, waaronder de objectdetectieresultaten (voertuigen in het verkeer). De module controleert deze berichten op objecten van een bepaald type, die zijn geconfigureerd via een instelling. Wanneer een dergelijk object is gevonden, wordt via deze module een bericht verzonden naar de IoT Edge-hub. Deze berichten met 'object gevonden' worden vervolgens doorgestuurd naar het IoT Hub-bronknooppunt van de mediagrafiek. Na ontvangst van een dergelijk bericht activeert het IoT Hub-bronknooppunt in de mediagrafiek het [signaalpoortprocessor](media-graph-concept.md#signal-gate-processor)-knooppunt. Het signaalpoortprocessor-knooppunt is vervolgens gedurende een geconfigureerde tijdsperiode geopend. De video stroomt gedurende die tijd door de poort naar het Asset Sink-knooppunt. Dit gedeelte van de live stream wordt vervolgens opgenomen via het [Asset Sink](media-graph-concept.md#asset-sink)-knooppunt naar een [Asset](terminology.md#asset) in uw Azure Media Services-account.

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Controleer voordat u begint of u de derde stap in [Vereisten](#prerequisites) hebt voltooid. Zodra het script voor de installatie van de resource is voltooid, klikt u op de accolades om de mapstructuur zichtbaar te maken. Er zijn enkele bestanden gemaakt in de map ~/clouddrive/lva-sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="App-instellingen":::

In deze zelfstudie zijn de volgende bestanden van belang:

* **~/clouddrive/lva-sample/edge-deployment/.env**: bevat eigenschappen waarvan Visual Studio Code gebruikmaakt om modules te implementeren op een Edge-apparaat.
* **~/clouddrive/lva-sample/appsetting.json**: wordt gebruikt in Visual Studio Code voor het uitvoeren van de voorbeeldcode.

U hebt de bestanden nodig voor de volgende stappen.

1. Kloon de opslagplaats van de GitHub-koppeling https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Start Visual Studio Code en open de map waar u de opslagplaats hebt gedownload.
1. In Visual Studio Code bladert u naar de map src/cloud-to-device-console-app en maakt u een bestand met de naam **appsettings.json**. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. Kopieer de inhoud uit het bestand ~/clouddrive/lva-sample/appSettings.json. De tekst moet er als volgt uitzien:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Met de IoT Hub-verbindingsreeks kunt u Visual Studio Code gebruiken om opdrachten naar de Edge-modules te verzenden via Azure IoT Hub.
    
1. Blader vervolgens naar de map src/edge en maak een bestand met de extensie **.env**.
1. Kopieer de inhoud uit het bestand ~/clouddrive/lva-sample/edge-deployment/.env. De tekst moet er als volgt uitzien:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Controleer het sjabloonbestand 

In de vorige stap hebt u Visual Studio Code gestart en de map met de voorbeeldcode geopend.

Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, en een paar sjabloonbestanden voor de implementatie. Met deze sjabloon wordt gedefinieerd welke Edge-modules u op het Edge-apparaat implementeert (de Azure Linux-VM). Het .env-bestand bevat waarden voor de variabelen die in deze sjablonen worden gebruikt, zoals de aanmeldingsgegevens voor Media Services.

Open src/edge/deployment.objectCounter.template.json. Er zijn vier vermeldingen in de sectie **Modules** die overeenkomen met de items die worden vermeld in de vorige de sectie Concepten:

* **lvaEdge**: dit is de Live Video Analytics in IoT Edge-module.
* **yolov3**: dit is de AI-module die is gemaakt met behulp van het YOLO v3-model.
* **rtspsim**: dit is de RTSP-simulator.
* **objectCounter**: dit is de module die zoekt naar specifieke objecten in de resultaten van yolov3.

Voor de objectCounter-module raadpleegt u de tekenreeks (${MODULES.objectCounter}) die wordt gebruikt voor de waarde voor ‘afbeelding’. Dit is gebaseerd op de [zelfstudie](../../iot-edge/tutorial-develop-for-linux.md) over het ontwikkelen van een IoT Edge-module. In Visual Studio code wordt automatisch herkend dat de code voor de deobjectCounter-module zich bevindt onder src/edge/modules/objectCounter. 

Lees [deze sectie](../../iot-edge/module-composition.md#declare-routes) over routes declareren in het IoT Edge-implementatiemanifest Bekijk vervolgens de routes in de JSON-bestandssjabloon. Let op het volgende:

* LVAToObjectCounter wordt gebruikt voor het verzenden van specifieke gebeurtenissen naar een bepaald eindpunt in de objectCounter-module.
* ObjectCounterToLVA wordt gebruikt voor het verzenden van een triggergebeurtenis naar een bepaald eindpunt (dit moet het IoT Hub-bronknooppunt zijn) in de lvaEdge-module.
* objectCounterToIoTHub wordt gebruikt als foutopsporingsprogramma, zodat u de uitvoer van objectCounter kunt zien wanneer u deze zelfstudie uitvoert.

> [!NOTE]
> Controleer de gewenste eigenschappen voor de objectCounter-module, die zijn ingesteld om te zoeken naar objecten die zijn gelabeld als 'vrachtwagen' en een betrouwbaarheidsniveau van ten minste 50% hebben.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren 

Het distributiemanifest definieert welke modules op een Edge-apparaat worden geïmplementeerd en de configuratie-instellingen voor deze modules. Volg deze stappen om een dergelijk manifest van het sjabloonbestand te genereren en vervolgens te implementeren op het Edge-apparaat.

Volg met behulp van Visual Studio Code [deze instructies](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) om u aan te melden bij Docker. Selecteer vervolgens **IoT Edge-oplossingen bouwen en pushen**. Gebruik voor deze stap src/edge/deployment.objectCounter.template.json.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="IoT Edge-oplossingen bouwen en pushen":::

Met deze actie maakt u de objectCounter-module voor het tellen van objecten en pusht u de kopie naar uw Azure Container Registry.

* Controleer of u de omgevingsvariabelen CONTAINER_REGISTRY_USERNAME_myacr en CONTAINER_REGISTRY_PASSWORD_myacr zijn gedefinieerd in het .env-bestand.

Met deze stap maakt u het IoT Edge-distributiemanifest op src/edge/config/deployment.objectCounter.amd64.json. Klik met de rechtermuisknop op het bestand en selecteer **Implementatie maken voor één apparaat**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Implementatie voor één apparaat maken":::

Als dit uw eerste zelfstudie met Live Video Analytics in IoT Edge is, wordt u in Visual Studio Code gevraagd om de IoTHub-verbindingsreeks in te voeren. U kunt deze kopiëren uit het bestand appsettings.json.

> [!NOTE]
> Mogelijk wordt u gevraagd om ingebouwde eindpunt gegevens voor de IoT Hub op te geven. Als u deze informatie wilt ophalen, gaat u in Azure Portal naar uw IoT Hub en zoekt u naar de optie **ingebouwde eind punten** in het navigatie deel venster links. Klik op deze en zoek naar het **eind punt Event hub** onder **Event hub-compatibel eind punt** . Kopieer en gebruik de tekst in het vak. Het eind punt ziet er ongeveer als volgt uit:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Vervolgens wordt u in Visual Studio Code gevraagd om een IoT Hub-apparaat te selecteren. Selecteer uw IoT Edge-apparaat. Dit moet lva-sample-device zijn.

Nu is de implementatie van Edge-modules op uw IoT Edge-apparaat gestart.
Vernieuw Azure IoT Hub, over ongeveer 30 seconden, in de sectie linksonder in Visual Studio Code. U ziet dat er vier modules zijn geïmplementeerd, genaamd lvaEdge, rtspsim, yolov3 en objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Vier modules geïmplementeerd":::

## <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

Volg deze stappen om de gebeurtenissen van de objectCounter-module en van de Live Video Analytics in IoT Edge-module te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek **Azure IoT Hub** in de linkerbenedenhoek.
1. Vouw het knooppunt **Apparaten** uit.
1. Klik met de rechtermuisknop op Iva-sample-device en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Bewaking van ingebouwd gebeurteniseindpunt starten":::
    
## <a name="run-the-program"></a>Het programma uitvoeren

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. <!--In Visual Studio Code, go-->Navigeer naar src/cloud-to-device-console-app/operations.json.
1. Bewerk onder het knooppunt **GraphTopologySet** het volgende:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Bewerk vervolgens onder de knooppunten **GraphInstanceSet** en **GraphTopologyDelete**:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Start een foutopsporingssessie door F5 te selecteren. In het **TERMINAL**-venster ziet u enkele berichten verschijnen.
1. Het bestand operations.json begint met het uitvoeren van aanroepen naar GraphTopologyList en GraphInstanceList. Als u resources hebt opgeschoond na vorige quickstarts of zelfstudies, worden met deze actie lege lijsten geretourneerd en wordt er gepauzeerd zodat u op **ENTER** kunt drukken, zoals hieronder:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
1. Wanneer u **Enter** selecteert in het **TERMINAL**-venster, wordt de volgende set met aanroepen met de directe methode gemaakt:
   * Een aanroep van GraphTopologySet met behulp van de vorige topologyUrl
   * Een aanroep van GraphInstanceSet met behulp van de volgende hoofdtekst
     
        ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
            "description": "Sample graph description",
            "parameters": [
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
    
   * Een aanroep van GraphInstanceActivate om het grafiekexemplaar te starten en de videostream te starten
   * Een tweede aanroep van GraphInstanceList om aan te geven dat het grafiekexemplaar actief is
     
1. De uitvoer in het **TERMINAL**-venster wordt nu onderbroken met de prompt **Druk op ENTER** om door te gaan. Klik nog niet op **Enter**. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Als u nu overschakelt naar het **UITVOER**-venster in Visual Studio Code, worden berichten die worden verzonden naar de IoT Hub, weergegeven door de Live Video Analytics in IoT Edge-module.

   Deze berichten worden beschreven in de volgende sectie.
1. Het grafiekexemplaar blijft de video uitvoeren en opnemen. Via de RTSP-simulator wordt de bronvideo continu herhaald. Bekijk de berichten zoals wordt beschreven in de volgende sectie. Ga vervolgens, om de opname te stoppen, terug naar het **TERMINAL**-venster en klik op **Enter**. De volgende reeks aanroepen wordt gedaan om resources op te schonen:

   * Een aanroep van GraphInstanceDeactivate om het grafiekexemplaar te deactiveren.
   * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen.
   * Een aanroep van GraphTopologyDelete om de topologie te verwijderen.
   * Een laatste aanroep van GraphTopologyList om te laten zien dat de lijst nu leeg is.

## <a name="interpret-the-results"></a>De resultaten interpreteren 

Bij het uitvoeren van de mediagrafiek stuurt de Live Video Analytics in IoT Edge-module bepaalde diagnostische en operationele gebeurtenissen naar de IoT Edge-hub. Deze gebeurtenissen zijn de berichten die u ziet in het **UITVOER**-venster van Visual Studio Code. Deze bevatten een hoofdtekstsectie en een sectie applicationProperties. Zie [IoT Hub-berichten maken en lezen](../../iot-hub/iot-hub-devguide-messages-construct.md)als u wilt weten wat deze secties inhouden.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst door de module Live Video Analytics bepaald.

## <a name="diagnostics-events"></a>Diagnostische gebeurtenissen

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis 

Wanneer een mediagrafiek wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd op de RTSP Simulator-container. Als dit lukt, wordt deze gebeurtenis geregistreerd. Het gebeurtenistype is **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Het bericht is een diagnostische gebeurtenis (MediaSessionEstablished). Het geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* De onderwerpsectie in applicationProperties verwijst naar het knooppunt in de grafiektopologie van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* De sectie eventType in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* De sectie eventTime geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden. Dit is het tijdstip waarop de verkeersvideo (MKV-bestand) als livestream in de module arriveerde.
* De hoofdtekstsectie bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-details.


## <a name="operational-events"></a>Operationele gebeurtenissen

Nadat de mediagrafiek een tijdje actief is, ontvangt u uiteindelijk een gebeurtenis uit de objectCounter-module. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

De sectie applicationProperties bevat de tijd van de gebeurtenis. Dit is de tijd waarop met de objectCounter-module is geconstateerd dat de resultaten van de yolov3-module objecten (vrachtwagens) bevatten.

Mogelijk ziet u meer van deze gebeurtenissen verschijnen naarmate andere vrachtwagens worden gedetecteerd in de video.

### <a name="recordingstarted-event"></a>RecordingStarted-gebeurtenis

Bijna onmiddellijk nadat de Object Counter de gebeurtenis heeft verzonden, ziet u een gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingStarted** verzonden:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

De onderwerpsectie in applicationProperties verwijst naar het AssetSink-knooppunt in de grafiek, waarop dit bericht is gegenereerd. De hoofdtekstsectie bevat informatie over de uitvoerlocatie. In dit geval is het de naam van de Azure Media Services-asset waarin de video wordt vastgelegd. Noteer deze waarde.

### <a name="recordingavailable-event"></a>RecordingAvailable-gebeurtenis

Wanneer via het knooppunt AssetSink een video is geüpload naar de asset, wordt deze gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingAvailable** verzonden:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat er voldoende gegevens zijn weggeschreven naar de asset, en dat spelers of clients de video dus kunnen afspelen. De sectie subject in applicationProperties verwijst naar het knooppunt van de Assetsink in de graaf, waardoor dit bericht is gegenereerd. De hoofdtekstsectie bevat informatie over de uitvoerlocatie. In dit geval is het de naam van de Azure Media Services-asset waarin de video wordt vastgelegd.

### <a name="recordingstopped-event"></a>RecordingStopped-gebeurtenis

Als u de activeringsinstellingen (maximumActivationTime) voor het signaalpoortproces-knooppunt in de [topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)bekijkt, ziet u dat de poort is ingesteld om te worden gesloten na het verzenden van 30 seconden video. Ongeveer 30 seconden na de RecordingStarted-gebeurtenis ziet u een gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingStopped**. Deze gebeurtenis geeft aan dat het Asset Sink-knooppunt is gestopt met het opnemen van video op de asset.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat de opname is gestopt. De sectie subject in applicationProperties verwijst naar het knooppunt van de Assetsink in de graaf, waardoor dit bericht is gegenereerd. De hoofdtekstsectie bevat informatie over de uitvoerlocatie. In dit geval is het de naam van de Azure Media Services-asset waarin de video wordt vastgelegd.

## <a name="media-services-asset"></a>Media Services-asset  

U kunt de Media Services-asset die met de grafiek is gemaakt, controleren door u aan te melden bij de Azure-portal en de video te bekijken.

1. Open uw webbrowser en ga naar de [Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.
1. Zoek uw Media Services-account op tussen de resources in uw abonnement. Open het deelvenster Account.
1. Selecteer **Assets** in de **Media Services**-lijst.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Continue video-opname":::
1. U vindt een asset in de lijst met de naam sampleAssetFromEVR-LVAEdge-{DateTime}. Dit is de naam die is opgegeven in de eigenschap outputLocation van de gebeurtenis RecordingStarted. De assetNamePattern in de topologie bepaalt hoe deze naam wordt gegenereerd.
1. Selecteer de asset.
1. Klik op de pagina met assetdetails op **Nieuwe maken** onder het tekstvak **Streaming-URL**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Nieuwe asset":::
1. Accepteer in de wizard die wordt geopend, de standaardopties en klik op **Toevoegen**. Zie [video afspelen](video-playback-concept.md) voor meer informatie.

    > [!TIP]
    > Zorg ervoor dat uw [streaming-eindpunt](../latest/streaming-endpoint-concept.md) wordt uitgevoerd.
1. De video wordt nu geladen in de speler. Selecteer **Afspelen** om deze weer te geven.

> [!NOTE]
> Omdat de bron van de video een container is die een camerafeed simuleert, zijn de tijdstempels in de video gerelateerd aan het moment waarop u het grafiekexemplaar hebt geactiveerd en uitgeschakeld. Als u de afspeelknoppen gebruikt die u hebt ingebouwd in de zelfstudie [ Meerdaagse opnamen afspelen](playback-multi-day-recordings-tutorial.md), kunt u de tijdstempels zien in de video die op het scherm wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere zelfstudies wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze zelfstudie hebt uitgevoerd en verwijdert u de resourcegroep.

## <a name="next-steps"></a>Volgende stappen

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's met RTSP-ondersteuning op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten door te zoeken naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md). Volg de instructies in de quickstart [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) om het apparaat te registreren bij Azure IoT Hub.