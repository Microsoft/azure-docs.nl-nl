---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88682123"
---
Wanneer u de mediagrafiek uitvoert, gaan de resultaten van het knooppunt van de bewegingsdetectorprocessor via het knooppunt van de IoT Hub-sink naar de IoT-hub. De berichten die u ziet in het **UITVOER**-venster van Visual Studio Code bevatten een sectie `body` en een sectie `applicationProperties`. Zie [IoT Hub-berichten maken en lezen](../../../../../iot-hub/iot-hub-devguide-messages-construct.md) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagrafiek wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live555-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

In de voorgaande uitvoer geldt het volgende: 

* Het bericht is een diagnostische gebeurtenis, `MediaSessionEstablished`. Het geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In `applicationProperties` verwijst `subject` naar het knooppunt in de graaftopologie vanwaaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een diagnostische gebeurtenis is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden.
* De `body`-sectie bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="recordingstarted-event"></a>RecordingStarted-gebeurtenis

Het processorknooppunt van de signaalpoort wordt geactiveerd wanneer er beweging wordt gedetecteerd, en het bestandssinkknooppunt begint een MP4-bestand te schrijven in de mediagrafiek. Het bestandssinkknooppunt verzendt een operationele gebeurtenis. Het `type` is ingesteld op `motion` om aan te geven dat het om een resultaat van de bewegingsdetectieprocessor gaat. De waarde `eventTime` is het tijdstip (UTC) waarop de beweging heeft plaatsgevonden. Bekijk de sectie [Overzicht](#overview) van deze quickstart voor meer informatie over dit proces.

Hier volgt een voorbeeld van dit bericht:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

In het voorgaande bericht geldt het volgende: 

* In `applicationProperties` verwijst `subject` naar het knooppunt in de mediagraaf vanwaaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het bestandssinkknooppunt.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis operationeel is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden. Dit tijdstip is 5 tot 6 seconden na 1 en na het begin van de videostroom. Dit tijdstip komt overeen met het punt op 5 seconden toen de [auto het parkeerterrein kwam oprijden](#review-the-sample-video).
* De sectie `body` bevat gegevens over de operationele gebeurtenis. In dit geval bestaan de gegevens uit `outputType` en `outputLocation`.
* De variabele `outputType` geeft aan dat dit gegevens zijn over het bestandspad.
* De waarde `outputLocation` is de locatie van het MP4-bestand in de edge-module.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped- en RecordingAvailable-gebeurtenissen

Als u de eigenschappen van het signaalpoortprocessorknoopunt in de [graaftopologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json) bekijkt, ziet u dat de activeringstijden zijn ingesteld op 5 seconden. Ongeveer 5 seconden nadat de `RecordingStarted`-gebeurtenis is ontvangen, krijgt u:

* Een `RecordingStopped`-gebeurtenis, waarmee wordt aangegeven dat de opname is gestopt.
* Een `RecordingAvailable`-event, waarmee wordt aangegeven dat het MP4-bestand nu kan worden bekeken.

De twee gebeurtenissen worden doorgaans enkele seconden na elkaar verzonden.
