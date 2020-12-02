---
title: Aan de slag met Live Video Analytics in IoT Edge - Azure
description: Deze quickstart laat zien hoe u aan de slag kunt met Live Video Analytics in IoT Edge. Leer hoe u beweging kunt detecteren in een live-videostream.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: e67c717a4476ab9191471483d9aa8e8f222cd750
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498265"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Quickstart: Over Live Video Analytics in IoT Edge

Deze quickstart begeleidt u door de stappen om aan de slag te gaan met Live Video Analytics in IoT Edge. Azure VM wordt gebruikt als IoT Edge-apparaat. Er wordt ook een gesimuleerde live-videostream gebruikt. 

Nadat u de installatiestappen hebt voltooid, kunt u een gesimuleerde live-videostream uitvoeren via een mediagrafiek die beweging in die stream detecteert en rapporteert. Het volgende diagram geeft een grafische weergave van die mediagrafiek.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Live Video Analytics op basis van bewegingsdetectie":::

U kunt de volgende video met gedetailleerde stappen bekijken om aan de slag te gaan met Live Video Analytics in IoT Edge:

<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) als u nog geen account hebt.
* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer. Zorg ervoor dat u beschikt over de [Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Zorg ervoor dat het netwerk waarmee uw ontwikkelcomputer is verbonden AMQP (Advanced Message Queueing Protocol) toestaat via poort 5671. Dankzij deze instelling kan Azure IoT Tools communiceren met Azure IoT Hub.

> [!TIP]
> U wordt mogelijk gevraagd om Docker te installeren wanneer u de Azure IoT Tools-extensie installeert. U mag dit negeren.

## <a name="set-up-azure-resources"></a>Azure-resources instellen

Voor deze zelfstudie hebt u de volgende Azure-resources nodig:

* IoT Hub
* Storage-account
* Azure Media Services-account
* Een Linux-VM in Azure, waarop [IoT Edge-runtime](../../iot-edge/how-to-install-iot-edge.md) is geïnstalleerd

Voor deze quickstart wordt u aangeraden gebruik te maken van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) om de vereiste Azure-resources in uw Azure-abonnement te implementeren. Voer hiervoor de volgende stappen uit:

1. Ga naar [Azure Cloud Shell](https://shell.azure.com).
1. Als u Cloud Shell voor het eerst gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslagaccount en een Microsoft Azure Files-share. Selecteer **Opslag maken** om een opslagaccount te maken voor de gegevens van uw Cloud Shell-sessie. Dit opslagaccount is gescheiden van het account dat door het script wordt gemaakt voor gebruik bij uw Azure Media Services-account.
1. Selecteer in de vervolgkeuzelijst aan de linkerkant van het Cloud Shell-venster **Bash** als uw omgeving.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Omgevingsselector":::
1. Voer de volgende opdracht uit.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Als het script is voltooid, ziet u alle vereiste resources in uw abonnement. In de scriptuitvoer bevat een tabel met resources de naam van de IoT-hub. Zoek naar het resourcetype `Microsoft.Devices/IotHubs` en noteer de naam. U hebt deze naam nodig in de volgende stap. 

Het script genereert ook enkele configuratiebestanden in de map *~/clouddrive/LVA-sample/* . U hebt deze bestanden verderop in de quickstart nodig.

## <a name="deploy-modules-on-your-edge-device"></a>Modules op uw edge-apparaat implementeren

Voer vanuit Cloud Shell de volgende opdracht uit.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Met deze opdracht worden de volgende modules geïmplementeerd op het edge-apparaat, in dit geval de Linux-VM.

* Live Video Analytics in IoT Edge (modulenaam `lvaEdge`)
* RTSP-simulator (Real-Time Streaming Protocol) (modulenaam `rtspsim`)

In de RTSP-simulatormodule wordt een live-videostream gesimuleerd met behulp van een videobestand dat werd gekopieerd naar uw edge-apparaat toen u het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) uitvoerde. 

Nu zijn de modules geïmplementeerd, maar zijn er geen mediagrafieken actief.

## <a name="configure-the-azure-iot-tools-extension"></a>Configureer de Azure IoT Tools-extensie

Volg deze instructies om verbinding te maken met uw IoT-hub met behulp van de Azure IoT Tools-extensie.

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. Selecteer **Weergave** > **Explorer**. Of selecteer Ctrl+Shift+E.
1. Selecteer **Azure IoT Hub** in de linkerbenedenhoek van het tabblad **Verkenner**.
1. Selecteer het pictogram **Meer opties** om het contextmenu weer te geven. Selecteer vervolgens **IoT Hub-verbindingsreeks instellen**.
1. Wanneer er een invoervak verschijn,. voert u uw IoT Hub-verbindingsreeks in. U kunt de verbindingstekenreeks ophalen van *~/clouddrive/lva-sample/appSettings.json* in Cloud Shell.

Als er verbinding is gemaakt, wordt de lijst met edge-apparaten weergegeven. U zou ten minste één apparaat met de naam **lva-sample-device** moeten zien. U kunt nu uw IoT Edge-apparaten beheren en interactief werken met Azure IoT Hub via het contextmenu. Om de modules die op het edge-apparaat zijn geïmplementeerd te bekijken, vouwt u het knooppunt **Modules** uit onder **Iva-sample-device**.

![Knooppunt lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Als u zelf [handmatig Live Video Analytics op IoT Edge hebt geïmplementeerd](deploy-iot-edge-device.md) op een Edge-apparaat (zoals een ARM64-apparaat), ziet u de module onder dat apparaat weergegeven, onder de Azure IoT Hub. U kunt deze module selecteren en de rest van de onderstaande stappen volgen.

## <a name="use-direct-method-calls"></a>Aanroepen van directe methoden gebruiken

U kunt de module gebruiken om live-videostreams te analyseren door directe methoden aan te roepen. Bekijk [Directe methodes voor Live Video Analytics op IoT Edge](direct-methods.md) voor meer informatie. 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList aanroepen

Om alle [grafiektopologieën](media-graph-concept.md#media-graph-topologies-and-instances) in de module op te sommen:

1. Klik in de Visual Studio Code met de rechtermuisknop op de module **lvaEdge** en selecteer **Module directe methode aanroepen**.
1. Voer in het vak dat verschijnt *GraphTopologyList* in.
1. Kopieer de volgende JSON-nettolading en plak deze in het vak. Selecteer vervolgens de Enter-toets.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Binnen een paar seconden verschijnt in het venster **UITVOER** het volgende antwoord.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Dit antwoord wordt verwacht omdat er geen grafiektopologieën zijn gemaakt.
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet aanroepen

Met behulp van de stappen voor het aanroepen van `GraphTopologyList` kunt u `GraphTopologySet` aanroepen om een [grafiektopologie](media-graph-concept.md#media-graph-topologies-and-instances) in te stellen. Gebruik de volgende JSON als de nettolading.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Deze JSON-nettolading maakt een grafiektopologie die drie parameters instelt. Twee van deze parameters hebben standaardwaarden. De topologie heeft één bronknooppunt (RTSP-bron), één processorknooppunt (bewegingsdetectieprocessor) en één sinkknooppunt (IoT Hub-sink).

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

De geretourneerde status is 201. De status geeft aan dat een nieuwe topologie is gemaakt. 

Voer de volgende stappen uit:

1. Roep `GraphTopologySet` opnieuw aan. De geretourneerde statuscode is 200. Deze statuscode geeft aan dat een bestaande topologie is bijgewerkt.
1. Roep `GraphTopologySet` opnieuw aan, maar wijzig de tekenreeks voor de beschrijving. De geretourneerde statuscode is 200 en de beschrijving is bijgewerkt naar de nieuwe waarde.
1. Roep `GraphTopologyList` aan zoals beschreven in de vorige sectie. Nu kunt u de `MotionDetection`-topologie zien in de geretourneerde nettolading.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet aanroepen

Roep `GraphTopologyGet` aan met behulp van de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

In de nettolading van het antwoord ziet u deze details:

* De statuscode is 200, die aangeeft dat de bewerking geslaagd is.
* De nettolading bevat de tijdstempel `created` en de tijdstempel `lastModified`.

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet aanroepen

Maak een grafiekexemplaar aan die verwijst naar de voorgaande grafiektopologie. Met grafiekexemplaren kunt u live-videostreams van meerdere camera's analyseren met behulp van dezelfde grafiektopologie. Zie de [Topologieën en exemplaren van mediagrafieken](media-graph-concept.md#media-graph-topologies-and-instances) voor meer informatie.

Roep de directe methode `GraphInstanceSet` aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Merk op dat deze nettolading:

* De naam van de topologie (`MotionDetection`) opgeeft waarvoor het exemplaar moet worden gemaakt.
* Een parameterwaarde voor `rtspUrl` bevat, die geen standaardwaarde had in de nettolading van de topologie.

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

In de nettolading van het antwoord ziet u dat:

* De statuscode 201 is, wat aangeeft dat er een nieuw exemplaar is gemaakt.
* De status `Inactive` is, wat aangeeft dat het exemplaar van de grafiek is aangemaakt maar niet geactiveerd. Zie de [Statussen voor mediagrafiek](media-graph-concept.md) voor meer informatie.

Voer de volgende stappen uit:

1. Roep `GraphInstanceSet` opnieuw aan met dezelfde nettolading. U ziet dat de geretourneerde statuscode 200 is.
1. Roep `GraphInstanceSet` opnieuw aan, maar gebruik een andere beschrijving. Merk op dat de beschrijving in de nettolading is bijgewerkt, wat aangeeft dat het exemplaar van de grafiek is bijgewerkt.
1. Roep `GraphInstanceSet` aan, maar wijzig de naam naar `Sample-Graph-2`. Merk op dat er een nieuw grafiekexemplaar is aangemaakt in de nettolading van het antwoord (statuscode 201).

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate aanroepen

Activeer nu het grafiekexemplaar om de stroom van live-video via de module te starten. Roep de directe methode `GraphInstanceActivate` aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

De statuscode 200 geeft aan dat het grafiekexemplaar is geactiveerd.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet aanroepen

Roep nu de directe methode `GraphInstanceGet` aan met de volgende nettolading.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

In de nettolading van het antwoord ziet u de volgende details:

* De statuscode is 200, die aangeeft dat de bewerking geslaagd is.
* De status is `Active`, wat aangeeft dat de grafiek nu actief is.

## <a name="observe-results"></a>Resultaten bekijken

De grafiek die we hebben gemaakt en geactiveerd, maakt gebruik van het knooppunt van de bewegingsdetectieprocessor om beweging in de inkomende live-videostream te detecteren. Het verzendt gebeurtenissen naar het sink-knooppunt van de IoT Hub. Deze gebeurtenissen worden doorgegeven aan de IoT Edge-hub. 

Volg deze stappen om de resultaten te bekijken.

1. Open het venster **Verkenner** in Visual Studio Code. Zoek naar **Azure IoT Hub** in de linkerbenedenhoek.
2. Vouw het knooppunt **Apparaten** uit.
3. Klik met de rechtermuisknop op **Iva-sample-device** en selecteer vervolgens **Bewaking van ingebouwde gebeurtenisbewaking starten**.

    ![Bewaking van IOT Hub-gebeurtenissen starten](./media/quickstarts/start-monitoring-iothub-events.png)
    
In het venster **UITVOER** wordt het volgende bericht weergegeven:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Let op deze details:

* Het bericht bevat een sectie `body` en een sectie `applicationProperties`. Zie [IoT Hub-berichten maken en lezen](../../iot-hub/iot-hub-devguide-messages-construct.md) voor meer informatie.
* In `applicationProperties` verwijst `subject` naar het knooppunt in de `MediaGraph` vanwaaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van de bewegingsdetectieprocessor.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een analytische gebeurtenis is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden.
* De sectie `body` bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en bevat de hoofdtekst daarom `timestamp`- en `inferences`-gegevens.
* De sectie `inferences` aan dat het `type` `motion` is. Het biedt aanvullende gegevens over de `motion`-gebeurtenis.

Als u de mediagrafiek even uitvoert, verschijnt het volgende bericht in het venster **UITVOER**.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

U ziet de volgende details dit bericht:

* In `applicationProperties` geeft `subject` aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis diagnostisch is.
* De `body` bevat gegevens over de diagnostische gebeurtenis. In dit geval bevat het bericht de hoofdtekst, omdat de gebeurtenis `MediaSessionEstablished` is.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Aanvullende directe methoden aanroepen om op te schonen

Roep directe methoden aan om de grafiek eerst te deactiveren en deze vervolgens te verwijderen.

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate aanroepen

Roep de directe methode `GraphInstanceDeactivate` aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

De statuscode 200 geeft aan dat het graafexemplaar is gedeactiveerd.

Probeer vervolgens `GraphInstanceGet` aan te roepen zoals eerder in dit artikel beschreven. Bekijk de waarde `state`.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete aanroepen

Roep de directe methode `GraphInstanceDelete` aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Een statuscode 200 geeft aan dat de grafiek is verwijderd.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete aanroepen

Roep de directe methode `GraphTopologyDelete` aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Binnen een paar seconden ziet u in het venster **UITVOER** het volgende antwoord verschijnen.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Een statuscode 200 geeft aan dat de grafiektopologie is verwijderd.

Voer de volgende stappen uit:

1. Roep `GraphTopologyList` aan en controleer of de module geen grafiektopologieën bevat.
1. Roep `GraphInstanceList` aan met dezelfde nettolading als `GraphTopologyList`. Houd er rekening mee dat er geen grafieken worden opgesomd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de resources die u in deze quickstart hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [opnemen van video met behulp van Live Video Analytics in IoT Edge](continuous-video-recording-tutorial.md).
* Meer informatie over [diagnostische berichten](monitoring-logging.md).