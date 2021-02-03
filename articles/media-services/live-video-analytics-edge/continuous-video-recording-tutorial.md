---
title: Zelfstudie voor continue video-opname in de cloud en afspelen vanuit de cloud - Azure
description: In deze zelfstudie leert u hoe u Azure Live Video Analytics kunt gebruiken op Azure IoT Edge om continu video in de cloud op te nemen en een deel van die video te streamen met behulp van Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 6956539595c5997569dc92b5c62e2396cb5fdd9c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524394"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Zelfstudie: Continue video-opname in de cloud en afspelen vanuit de cloud

In deze zelfstudie leert u hoe u Azure Live Video Analytics kunt gebruiken op Azure IoT Edge om [continu video in de cloud op te nemen](continuous-video-recording-concept.md) en een deel van die video te streamen met behulp van Azure Media Services. Dit is handig voor scenario's zoals beveiliging, naleving, enzovoort, waarbij een archief van de camerabeelden voor dagen of weken moet worden bijgehouden. 

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * De relevante resources instellen.
> * De code controleren die CVR uitvoert.
> * De voorbeeldcode uitvoeren.
> * De resultaten controleren en de video bekijken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

Lees deze artikelen voordat u begint:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md) 
* [Scenario’s voor continue video-opname](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Vereisten

Vereisten voor deze zelfstudie:

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer met de extensies [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) en [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > U wordt mogelijk gevraagd om Docker te installeren. U kunt deze vraag negeren.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) op uw ontwikkelcomputer.
* Installatiescript van [Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) voltooien.

Aan het einde van deze stappen hebt u relevante Azure-resources geïmplementeerd in uw Azure-abonnement:

* Azure IoT Hub
* Azure Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](../../iot-edge/how-to-install-iot-edge.md) geïnstalleerd

> [!TIP]
> Als u problemen ondervindt met Azure-resources die worden gemaakt, raadpleegt u onze **[probleemoplossingsgids](troubleshoot-how-to.md#common-error-resolutions)** , waarmee u enkele veelvoorkomende problemen kunt oplossen.

## <a name="concepts"></a>Concepten

Zoals uitgelegd in het artikel [mediagrafiek-concept](media-graph-concept.md), kunt u met een mediagrafiek het volgende definiëren:

- Waar media vandaan moeten worden gehaald.
- Hoe deze moet worden verwerkt.
- Waar de resultaten moeten worden geleverd. 
 
 Als u CVR wilt uitvoeren, moet u de video van een RTSP-compatibele camera vastleggen en deze continu opnemen in een [Azure Media Services-asset](terminology.md#asset). In het diagram ziet u een grafische weergave van die mediagrafiek.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Mediagrafiek":::

In deze zelfstudie gebruikt u één Edge-module die is gebouwd met de [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) om een RTSP-camera te simuleren. In de mediagrafiek gebruikt u een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt om de live-feed op te halen en verzendt u deze video naar het knooppunt [Asset Sink](media-graph-concept.md#asset-sink), waarmee de video wordt opgenomen in een asset. De video die wordt gebruikt in deze zelfstudie, is [een voorbeeldvideo van een knooppunt van snelwegen](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Controleer voordat u begint of u de derde stap hebt voltooid in [Vereisten](#prerequisites). Zodra het script voor de installatie van de resource is voltooid, klikt u op de accolades om de mapstructuur zichtbaar te maken. Er zijn enkele bestanden gemaakt in de map ~/clouddrive/lva-sample.

![App-instellingen](./media/quickstarts/clouddrive.png)

In deze zelfstudie zijn de volgende bestanden van belang:

* **~/clouddrive/lva-sample/edge-deployment/.env**: bevat eigenschappen die Visual Studio Code gebruikt om modules te implementeren op een Edge-apparaat.
* **~/clouddrive/lva-sample/appsettings.json**: wordt gebruikt door Visual Studio Code voor het uitvoeren van de voorbeeldcode.

U hebt de bestanden nodig voor de volgende stappen:

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
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>De voorbeeldbestanden bekijken

Open src/edge/deployment.template.json in Visual Studio Code. Met deze sjabloon wordt gedefinieerd welke Edge-modules u op het Edge-apparaat implementeert (de Azure Linux-VM). In de sectie **Modules** ziet u twee vermeldingen, met de volgende namen:

* **lvaEdge**: dit is de module Live Video Analytics in IoT Edge.
* **rtspsim**: dit is de RTSP-simulator.

Blader vervolgens naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

* **c2d-console-app.csproj**: het projectbestand voor Visual Studio Code.
* **operations.json**: dit bestand bevat de verschillende bewerkingen die u zou uitvoeren.
* **Program.cs**: de voorbeeldcode van het programma voor:
    * De app-instellingen laden.
    * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om live-videostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen.
    * Hiermee pauzeert u zodat u de uitvoer van het programma in het **TERMINAL**-venster en de gebeurtenissen die worden gegenereerd door de module in het **UITVOER** venster kunt controleren.
    * Hiermee worden directe methoden voor het opschonen van resources aangeroepen.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren 

Het distributiemanifest definieert welke modules op een Edge-apparaat worden geïmplementeerd en de configuratie-instellingen voor deze modules. Volg deze stappen om een dergelijk manifest van het sjabloonbestand te genereren en vervolgens te implementeren op het Edge-apparaat.

1. Start Visual Studio Code.
1. Stel de IoT Hub-verbindingsreeks in door naast het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek te klikken op het pictogram **Meer acties**. Kopieer de tekenreeks vanuit het bestand src/cloud-to-device-console-app/appsettings.json. 

    ![IoT Hub-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)
1. Klik met de rechtermuisknop op src/edge/deployment.template.json en selecteer **IoT Edge-implementatiemanifest genereren**. Visual Studio Code gebruikt de waarden uit het .env-bestand om de variabelen in het bestand met de implementatiesjabloon te vervangen. Hiermee maakt u een manifestbestand in de map src/edge/config met de naam **deployment.amd64.json**.

   ![IoT Edge-implementatiemanifest genereren](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Klik met de rechtermuisknop op src/edge/config/deployment.amd64.json en selecteer **Implementatie voor één apparaat maken**.

   ![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)
1. Vervolgens wordt u gevraagd om een **IoT Hub-apparaat te selecteren**. Selecteer lva-sample-device in de vervolgkeuzelijst.
1. Vernieuw Azure IoT Hub in ongeveer 30 seconden in de sectie linksonder. U ziet dat op het edge-apparaat de volgende modules zijn geïmplementeerd:
    * Live Video Analytics in IoT Edge (modulenaam **lvaEdge**)
    * RTSP-simulator (modulenaam **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Het bewaken van de modules voorbereiden 

Wanneer u de Live Video Analytics in IoT Edge-module gebruikt om de live videostream vast te leggen, worden gebeurtenissen naar de IoT-hub verzonden. Volg deze stappen om deze gebeurtenissen te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek **Azure IoT Hub** in de linkerbenedenhoek.
1. Vouw het knooppunt **Apparaten** uit.
1. Klik met de rechtermuisknop op Iva-sample-device en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

    ![Bewaking van ingebouwd gebeurteniseindpunt starten](./media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Mogelijk wordt u gevraagd om ingebouwde eindpunt gegevens voor de IoT Hub op te geven. Als u deze informatie wilt ophalen, gaat u in Azure Portal naar uw IoT Hub en zoekt u naar de optie **ingebouwde eind punten** in het navigatie deel venster links. Klik op deze en zoek naar het **eind punt Event hub** onder **Event hub-compatibel eind punt** . Kopieer en gebruik de tekst in het vak. Het eind punt ziet er ongeveer als volgt uit:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

## <a name="run-the-program"></a>Het programma uitvoeren 

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. Navigeer naar src/cloud-to-device-console-app/operations.json.
1. Bewerk het volgende onder het knooppunt **GraphTopologySet**:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Controleer vervolgens of op de knooppunten **GraphInstanceSet** en **GraphTopologyDelete** de waarde van **topologyName** overeenkomt met de waarde van de eigenschap **name** in de bovenstaande graaftopologie:

    `"topologyName" : "CVRToAMSAsset"`  
1. Open de [topologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) in een browser en kijk naar assetNamePattern. Om ervoor te zorgen dat uw asset een unieke naam heeft, kunt u de naam van het graafexemplaar wijzigen in het bestand operations.json (van de standaardwaarde van 'Sample-Graph-1').

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Start een foutopsporingssessie door F5 te selecteren. In het **TERMINAL**-venster ziet u enkele berichten verschijnen.
1. Het bestand operations.json begint met het uitvoeren van aanroepen naar GraphTopologyList en GraphInstanceList. Als u resources hebt opgeschoond na vorige quickstarts of zelfstudies, worden er lege lijsten geretourneerd, waarna wordt gepauzeerd zodat u op **ENTER** kunt drukken, zoals hieronder:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. Wanneer u op de toets **Enter** drukt in het venster **TERMINAL**, wordt de volgende set met aanroepen met de directe methode gemaakt:
   * Een aanroep van GraphTopologySet met behulp van de vorige topologyUrl
   * Een aanroep van GraphInstanceSet met behulp van de volgende hoofdtekst
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
   * Een aanroep van GraphInstanceActivate om het graafexemplaar te starten en de videostream te starten
   * Een tweede aanroep van GraphInstanceList om aan te geven dat het graafexemplaar actief is 
1. De uitvoer in het **TERMINAL**-venster wordt nu onderbroken met de prompt **Druk op ENTER** om door te gaan. Klik nog niet op **Enter**. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Als u nu overschakelt naar het **UITVOER**-venster in Visual Studio Code, worden berichten die worden verzonden naar de IoT Hub, weergegeven door de Live Video Analytics in IoT Edge-module.

   Deze berichten worden beschreven in de volgende sectie.
1. Het grafiekexemplaar blijft de video uitvoeren en opnemen. Via de RTSP-simulator wordt de bronvideo aldoor herhaald. Als u de opname wilt stoppen, gaat u terug naar het **venster TERMINAL** en klikt u op **Enter**. De volgende serie aanroepen wordt gedaan om resources op te schonen:

   * Een aanroep van GraphInstanceDeactivate om het grafiekexemplaar te deactiveren.
   * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen.
   * Een aanroep van GraphTopologyDelete om de topologie te verwijderen.
   * Een laatste aanroep van GraphTopologyList om te laten zien dat de lijst nu leeg is.

## <a name="interpret-the-results"></a>De resultaten interpreteren 

Bij het uitvoeren van de mediagrafiek stuurt de Live Video Analytics in IoT Edge-module bepaalde diagnostische en operationele gebeurtenissen naar de IoT Edge-hub. Deze gebeurtenissen zijn de berichten die u ziet in het **UITVOER**-venster van Visual Studio Code. Deze bevatten een hoofdtekstsectie en een sectie applicationProperties. Zie [IoT Hub-berichten maken en lezen](../../iot-hub/iot-hub-devguide-messages-construct.md)als u wilt weten wat deze secties inhouden.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst door de module Live Video Analytics bepaald.

## <a name="diagnostics-events"></a>Diagnostische gebeurtenissen 

### <a name="mediasession-established-event"></a>MediaSession Established-gebeurtenis

Wanneer het graafexemplaar wordt geactiveerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-module. Als dit lukt, wordt deze gebeurtenis geregistreerd:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Het bericht is een diagnostische gebeurtenis (MediaSessionEstablished). Het geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* De onderwerpsectie in applicationProperties verwijst naar het knooppunt in de grafiektopologie van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* De sectie eventType in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* De sectie eventTime geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De sectie body bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-details.

## <a name="operational-events"></a>Operationele gebeurtenissen 

### <a name="recordingstarted-event"></a>RecordingStarted-gebeurtenis

Wanneer het knooppunt AssetSink begint met het opnemen van video, wordt deze gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingStarted** verzonden:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

De onderwerpsectie in applicationProperties verwijst naar het AssetSink-knooppunt in de grafiek, waarop dit bericht is gegenereerd.

De hoofdtekstsectie bevat informatie over de uitvoerlocatie. In dit geval is het de naam van de Azure Media Services-asset waarin de video wordt vastgelegd. Noteer deze waarde.

### <a name="recordingavailable-event"></a>RecordingAvailable-gebeurtenis

Zoals de naam al doet vermoeden, wordt de gebeurtenis RecordingStarted verzonden wanneer de opname is gestart, maar de videogegevens mogelijk nog niet zijn geüpload naar de asset. Wanneer via het knooppunt AssetSink videogegevens zijn geüpload naar de asset, wordt deze gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingAvailable** verzonden:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat er voldoende gegevens zijn weggeschreven naar de asset, en dat spelers of clients de video dus kunnen afspelen.

De sectie subject in applicationProperties verwijst naar het knooppunt van de Assetsink in de graaf, waardoor dit bericht is gegenereerd.

De hoofdtekstsectie bevat informatie over de uitvoerlocatie. In dit geval is het de naam van de Azure Media Services-asset waarin de video wordt vastgelegd.

### <a name="recordingstopped-event"></a>RecordingStopped-gebeurtenis

Wanneer u het exemplaar van de instantie uitschakelt, stopt het knooppunt Assetsink de opname van de video naar de asset. Deze gebeurtenis van het type **Microsoft.Media.Graph.Operational.RecordingStopped** wordt verzonden:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat de opname is gestopt.

De sectie subject in applicationProperties verwijst naar het knooppunt van de Assetsink in de graaf, waardoor dit bericht is gegenereerd.

De hoofdtekst bevat informatie over de uitvoerlocatie, in dit geval de naam van de Azure Media Services-asset waarin de video is opgenomen.

## <a name="media-services-asset"></a>Media Services-asset  

U kunt de Media Services-assset die door de mediagrafiek is gemaakt, controleren door u aan te melden bij Azure Portal en de video te bekijken.

1. Open uw webbrowser en ga naar de [Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.
1. Zoek uw Media Services-account op tussen de resources die u in uw abonnement hebt en open het deelvenster Account.
1. Selecteer **Assets** in de lijst **Media Services**.

    ![Media Services-assets](./media/continuous-video-recording-tutorial/assets.png)
1. U vindt een asset in de lijst met de naam sampleAsset-CVRToAMSAsset-sample-Graph-1. Dit is het naamgevingspatroon dat u hebt gekozen in het graaf-topologiebestand.
1. Selecteer de asset.
1. Klik op de pagina met assetdetails op **Nieuwe maken** onder het tekstvak **Streaming-URL**.

    ![Nieuwe asset](./media/continuous-video-recording-tutorial/new-asset.png)

1. Accepteer in de wizard die wordt geopend de standaardopties en klik op **Toevoegen**. Zie [video afspelen](video-playback-concept.md) voor meer informatie.

    > [!TIP]
    > Zorg ervoor dat uw [streaming-eindpunt](../latest/streaming-endpoint-concept.md) wordt uitgevoerd.
1. De video wordt nu geladen in de speler. Selecteer **Afspelen** om deze weer te geven.

> [!NOTE]
> Omdat de bron van de video een container is die een camerafeed simuleert, zijn de tijdstempels in de video gerelateerd aan het moment waarop u het graafexemplaar hebt geactiveerd en uitgeschakeld. U kunt de zelfstudie [Opnamen van meerdere dagen afspelen](playback-multi-day-recordings-tutorial.md) bekijken om te zien hoe u door een opname van meerdere dagen kunt bladeren en delen van dat archief kunt bekijken. In dezelfde zelfstudie kunt u ook de tijdstempels zien in de video die op het scherm wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere zelfstudies wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze zelfstudie hebt uitgevoerd en verwijdert u de resourcegroep.

## <a name="next-steps"></a>Volgende stappen

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's met RTSP-ondersteuning op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten door te zoeken naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md). Volg de instructies in de quickstart [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) om het apparaat te registreren bij Azure IoT Hub.