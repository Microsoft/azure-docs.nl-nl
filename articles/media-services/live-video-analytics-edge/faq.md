---
title: Veelgestelde vragen over live video Analytics op IoT Edge-Azure
description: In dit onderwerp vindt u antwoorden op live video Analytics op IoT Edge Veelgestelde vragen.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401573"
---
# <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

In dit onderwerp vindt u antwoorden op live video Analytics op IoT Edge Veelgestelde vragen.

## <a name="general"></a>Algemeen

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Wat zijn de systeem variabelen die kunnen worden gebruikt in de definitie van de grafiek topologie?

|Variabele   |Beschrijving|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Vertegenwoordigt een directe UTC-tijd, meestal uitgedrukt als datum en tijd van de dag (Basic-weer gave yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Vertegenwoordigt een UTC-exemplaar voor datum en tijd in ISO8601 bestands indeling met milliseconden (Basic reyyyyMMddTHHmmss. fffZ).|
|System. GraphTopologyName   |Vertegenwoordigt een media grafiek topologie, bevat de blauw druk van een grafiek.|
|System. GraphInstanceName|  Vertegenwoordigt een exemplaar van een media grafiek, bevat parameter waarden en verwijst naar de topologie.|

## <a name="configuration-and-deployment"></a>Configuratie en implementatie

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Kan ik de module media Edge implementeren op een Windows 10-apparaat?

Ja. Zie het artikel over [Linux-containers in Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Vastleggen van IP-camera en RTSP-instellingen

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Moet ik een speciale SDK op mijn apparaat gebruiken om een videostream te verzenden?

Nee. Live video Analytics op IoT Edge ondersteunt het vastleggen van media met het RTSP video streaming protocol (dat wordt ondersteund op de meeste IP-camera's).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Kan ik media pushen naar live video Analytics op IoT Edge met behulp van RTMP of Smooth (zoals een Media Services live-evenement)?

* Nee. Live video Analytics ondersteunt alleen RTSP voor het vastleggen van video van IP-camera's.
* Elke camera die RTSP-streaming via TCP/HTTP ondersteunt, zou moeten werken. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Kan ik de RTSP-bron-URL in een grafiekexemplaar opnieuw instellen of bijwerken?

Ja, wanneer het exemplaar van de grafiek de status inactief heeft.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Is er een RTSP-Simulator beschikbaar voor gebruik tijdens het testen en ontwikkelen?

Ja. Er is een [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-module beschikbaar voor gebruik in de Quick Start en zelf studies ter ondersteuning van het leer proces. Deze module wordt geleverd als best-effort en is mogelijk niet altijd beschikbaar. Het wordt ten zeerste aangeraden deze niet langer dan een paar uur te gebruiken. Voordat u plannen maakt voor een productie-implementatie, moet u investeren in tests met uw werkelijke RTSP-bron.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Bieden jullie ondersteuning voor ONVIF-detectie van IP-camera's aan de rand?

Nee, er is geen ondersteuning voor ONVIF-detectie van apparaten aan de rand.

## <a name="streaming-and-playback"></a>Streaming en afspelen

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Kunnen assets die zijn geregistreerd voor AMS van de Edge, worden afgespeeld met Media Services streaming-technologieën zoals HLS of DASH Board?

Ja. De vastgelegde activa kunnen worden gestreamd zoals andere assets in Azure Media Services. Als u de inhoud wilt streamen, moet u een streaming-eind punt hebben gemaakt en de status wordt uitgevoerd. Als u het standaard proces voor het maken van streaming-locators gebruikt, krijgt u toegang tot een HLS-of een DASH-manifest voor streaming naar elk mogelijk Player-Framework. Zie [dynamische verpakking](../latest/dynamic-packaging-overview.md)voor meer informatie over het maken van publicatie-HLS of-streepjes manifesten.

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Kan ik de standaard functies voor beveiliging van inhoud en DRM van Media Services op een gearchiveerde Asset gebruiken?

Ja. Alle standaard functies voor dynamische versleuteling van inhoud en DRM zijn beschikbaar voor gebruik in de assets die zijn opgenomen in een media grafiek.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Welke spelers kan ik gebruiken om inhoud van de geregistreerde assets weer te geven?

Alle standaard spelers die ondersteuning bieden voor compatibele Apple HTTP Live Streaming (HLS) versie 3 of versie 4 worden ondersteund. Daarnaast wordt elke speler die compatibel is met MPEG-DASH Play, ook ondersteund.

De aanbevolen spelers voor testen zijn onder andere:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Schuda-speler](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Edge, Chrome of Safari gebouwd in HTML5-Video speler
* Commerciële spelers die ondersteuning bieden voor HLS of onderbroken afspelen

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Wat zijn de limieten voor het streamen van een media Graph-Asset?

Het streamen van een live-of opgenomen activum vanuit een media grafiek maakt gebruik van hetzelfde hoogwaardige infra structuur en streaming-eind punt dat Media Services ondersteunt voor on-demand en live streamen voor media & entertainment, OTT en het verzenden van klanten. Dit betekent dat u snel en eenvoudig de Azure CDN, Verizon of Akamai kunt inschakelen voor het leveren van uw inhoud aan een publiek als een klein aantal kijkers, of tot miljoenen, afhankelijk van uw scenario.

Inhoud kan worden geleverd met Apple HTTP Live Streaming (HLS) of MPEG-DASH.

## <a name="design-your-ai-model"></a>Uw AI-model ontwerpen 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Ik heb meerdere AI-modellen ingepakt in een docker-container. Hoe moet ik deze gebruiken met live video Analytics? 

Oplossingen verschillen, afhankelijk van het communicatie protocol dat door de server voor afleiding wordt gebruikt om te communiceren met live video-analyses. Hieronder volgen enkele manieren om dit te doen.

#### <a name="http-protocol"></a>HTTP-protocol:

* Eén container (enkelvoudige lvaExtension):  

   In uw server voor afleiding kunt u één poort gebruiken, maar ook verschillende eind punten voor verschillende AI-modellen. Voor een python-voor beeld kunt u bijvoorbeeld verschillende `route` s per model gebruiken als: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   En in uw live video Analytics-implementatie, wanneer u een exemplaar maakt van grafieken, stelt u de URL voor de server voor afleiding voor elk exemplaar in als: 

   1e instantie: Server-URL voor uitgevallen =`http://lvaExtension:44000/score/face_detection`<br/>
   2e instantie: Server-URL voor uitgevallen =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > U kunt ook uw AI-modellen beschikbaar maken op verschillende poorten en deze aanroepen wanneer u een exemplaar maakt van grafieken.  

* Meerdere containers: 

   Elke container wordt geïmplementeerd met een andere naam. Op dit moment hebben we in de documentatieset live video Analytics geleerd hoe u een uitbrei ding implementeert met de naam: **lvaExtension**. Nu kunt u twee verschillende containers ontwikkelen. Elke container heeft dezelfde HTTP-interface (dat wil zeggen hetzelfde `/score` eind punt). Implementeer deze twee containers met verschillende namen en zorg ervoor dat beide worden geluisterd op **verschillende poorten**. 

   Bijvoorbeeld: een container met de naam `lvaExtension1` luistert naar de poort `44000` , de andere container met de naam `lvaExtension2` luistert naar de poort `44001` . 

   In uw live video Analytics-topologie maakt u twee grafieken met verschillende Afleidings-Url's, zoals: 

   Eerste instantie: Server-URL voor uitgevallen = `http://lvaExtension1:44001/score`    
   Tweede exemplaar: Server-URL voor uitgevallen = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC-Protocol: 

Met de live video Analytics-module 1,0, wanneer u een gRPC-protocol gebruikt, zou de enige manier zijn als de gRPC-server verschillende AI-modellen heeft blootgesteld via verschillende poorten. In [dit voor beeld](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)is er één poort, 44000, die alle Yolo modellen weergeeft. In theorie kan de Yolo gRPC-server worden herschreven om een aantal modellen op 45000 44000 beschikbaar te maken. 

Met de live video Analytics-module 2,0 wordt een nieuwe eigenschap toegevoegd aan het gRPC extension-knoop punt. Deze eigenschap heet **extensionConfiguration** . Dit is een optionele teken reeks die kan worden gebruikt als onderdeel van het gRPC-contract. Wanneer u meerdere AI-modellen hebt ingepakt in één server voor het afleiden van een storing, hoeft u geen knoop punt voor elk AI-model beschikbaar te maken. In plaats daarvan kunt u voor een instantie van een grafiek definiëren hoe de verschillende AI-modellen moeten worden geselecteerd met behulp van de eigenschap **extensionConfiguration** en tijdens de uitvoering, waarna live video Analytics deze teken reeks doorgeeft aan de server voor het afwijzen van de gegevens die kan worden gebruikt om het gewenste AI-model aan te roepen. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Ik bouw een gRPC-server rond een AI-model en wil dat deze kan worden gebruikt door meerdere camera's/grafische instanties. Hoe kan ik mijn server bouwen? 

 Zorg er eerst voor dat uw server meer dan één aanvraag tegelijk kan verwerken. Of zorg ervoor dat uw server werkt in parallelle threads. 

In een voor beeld van een [Live video Analytics-GRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)is er een standaard aantal parallelle kanalen ingesteld. Zie: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

In de bovenstaande gRPC-server is het mogelijk dat de server slechts drie kanalen per camera (per grafiek topologie-exemplaar) tegelijk kan openen. Probeer niet meer dan drie instanties te verbinden met de server. Als u probeert meer dan drie kanalen te openen, zijn aanvragen in behandeling totdat er een bestaande wordt neergezet.  

De bovenstaande gRPC-Server implementatie wordt gebruikt in onze python-voor beelden. Ontwikkel aars kunnen hun eigen servers implementeren of in de bovenstaande standaard implementatie kunt u het nummer van de werk nemer verhogen dat is ingesteld op het aantal camera's dat wordt gebruikt om de video-feed van op te halen. 

Ontwikkel aars kunnen meerdere camera's instellen en gebruiken, maar u kunt ook meerdere exemplaren van de grafiek topologie instantiëren waarbij elk exemplaar dat verwijst naar dezelfde of een andere server voor het afwijzen van de afleiding (bijvoorbeeld server vermeld in de bovenstaande alinea). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Ik wil meerdere frames van de upstream ontvangen voordat ik een afnemende beslissing neemt. Hoe kan ik dit inschakelen? 

Huidige [standaard voorbeelden](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) werken in de modus stateless. In dit voor beeld wordt de status van de vorige aanroepen niet gehandhaafd en zelfs wie aangeroepen is (dat wil zeggen dat meerdere topologie-instanties dezelfde Afleidings server kunnen aanroepen, en de server kan geen onderscheid maken tussen wie de aanroep en de status per oproeper is) 

#### <a name="http-protocol"></a>HTTP-protocol

Bij gebruik van HTTP-protocol: 

Om de status te hand haven, roept elke oproepende server (Graph-topologie-instantie) de Afleidings functie aan met de HTTP-query parameter uniek voor de aanroeper. U kunt bijvoorbeeld het URL-adres van de server voor  

1e topologie-exemplaar = `http://lvaExtension:44000/score?id=1`<br/>
2e topologie-exemplaar = `http://lvaExtension:44000/score?id=2`

… 

Aan de kant van de server weet de Score route wie er aanroept. Als ID = 1, dan kan de status voor die aanroeper (Graph-topologie-instantie) afzonderlijk worden bewaard. U kunt de ontvangen video frames in een buffer (bijvoorbeeld matrix of een woorden lijst met datum-en tijd code) en de waarde is het kader. vervolgens kunt u de server definiëren die moet worden verwerkt (afleiden) nadat x-frames zijn ontvangen. 

#### <a name="grpc-protocol"></a>GRPC-Protocol 

Bij gebruik van het gRPC-Protocol: 

Met een gRPC-extensie is elke sessie voor een invoer van één camera, zodat u geen ID hoeft op te geven. Nu met de eigenschap extensionConfiguration kunt u de video frames in een buffer opslaan en de server die moet worden verwerkt (afleiden) definiëren nadat x-frames zijn ontvangen. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Worden alle ProcessMediaStreams op een bepaalde container hetzelfde AI-model uitgevoerd? 

Nee.  

Het starten/stoppen van aanroepen van de eind gebruiker van een Graph-exemplaar vormt een sessie, of er kan een camera worden losgekoppeld/opnieuw verbonden. Het doel is één sessie persistent te maken als de camera video gaat streamen. 

* Twee camera's die video verzenden voor verwerking, maakt twee sessies. 
* Eén camera die naar een grafiek gaat met twee gRPCExtension-knoop punten, maakt twee sessies. 

Elke sessie is een volledige duplex verbinding tussen live video Analytics en de gRPC-server en elke sessie kan een ander model/een andere pijp lijn hebben. 

> [!NOTE]
> Als een camera de verbinding verbreekt/opnieuw verbinding maakt (terwijl de camera offline gaat gedurende een periode van meer dan de tolerantie limieten), wordt met live video Analytics een nieuwe sessie geopend met de gRPC-server. Er is geen vereiste voor de server om de status over deze sessies bij te houden. 

Live video Analytics heeft ook ondersteuning toegevoegd voor meerdere gRPC-uitbrei dingen voor één camera in een Graph-exemplaar. U kunt deze gRPC-extensies gebruiken om de AI-verwerking sequentieel of parallel uit te voeren, of zelfs een combi natie van beide. 

> [!NOTE]
> Wanneer meerdere uitbrei dingen parallel worden uitgevoerd, heeft dit gevolgen voor uw hardwarebronnen. u moet er rekening mee houden bij het kiezen van de hardware die aan uw reken behoeften voldoet. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Wat is het maximale aantal gelijktijdige ProcessMediaStreams? 

Er is geen limiet die van toepassing is op live video analyses.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Hoe kan ik bepalen of mijn server voor deserverren CPU of GPU of een andere hardware Accelerator moet gebruiken? 

Dit is volledig afhankelijk van hoe complex het AI-model is ontwikkeld en hoe de ontwikkelaar de CPU-en hardware Accelerators wil gebruiken. Tijdens het ontwikkelen van het AI-model kunnen de ontwikkel aars opgeven welke resources door het model moeten worden gebruikt om de acties uit te voeren. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Hoe kan ik afbeeldingen met selectie vakjes opslaan na verwerking? 

Momenteel bieden we coördinaten voor selectie kaders als alleen berichten afnemen. Ontwikkel aars kunnen een aangepaste MJPEG-streamer bouwen die deze berichten kan gebruiken en de selectie kaders via de video frames kunnen bedekken.  

## <a name="grpc-compatibility"></a>gRPC-compatibiliteit 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Hoe weet ik wat de verplichte velden voor de media stream-descriptor zijn? 

Alle veld waarden die niet worden opgegeven, krijgen een standaard waarde [zoals opgegeven door gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live video Analytics maakt gebruik van de **proto3** -versie van de taal protocol buffer. Alle protocol buffer gegevens die door live video Analytics-contracten worden gebruikt, zijn beschikbaar in de protocol buffer bestanden die [hier zijn gedefinieerd](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Hoe moet ik ervoor zorgen dat ik de meest recente protocol buffer bestanden gebruik? 

U kunt hier de meest recente protocol buffer bestanden [verkrijgen](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Telkens wanneer de contract bestanden worden bijgewerkt, worden deze weer gegeven op deze locatie. Hoewel er geen onmiddellijke planning is voor het bijwerken van de protocol bestanden, zoekt u naar de pakket naam boven aan de bestanden om de versie te kennen. Dit moet als volgt worden gelezen: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Eventuele updates van deze bestanden worden de ' v-waarde ' aan het einde van de naam verhoogd. 

> [!NOTE]
> Aangezien live video Analytics gebruikmaakt van een proto3-versie van de taal, zijn de velden optioneel, waardoor deze achterwaarts en compatibel is. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Welke gRPC-functies zijn beschikbaar voor gebruik met live video Analytics? Welke functies zijn verplicht en welke zijn optioneel? 

Alle gRPC-functies aan de server zijde kunnen worden gebruikt, vooropgesteld dat aan het protobuf-contract is voldaan. 

## <a name="monitoring-and-metrics"></a>Bewaking en metrische gegevens

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Kan ik de media grafiek op de rand bewaken met behulp van Event Grid?

Ja. U kunt de metrische gegevens voor Prometheus gebruiken en deze publiceren in Event grid. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Kan ik Azure Monitor gebruiken om de status, metrische gegevens en prestaties van mijn media grafieken in de Cloud of aan de rand weer te geven?

Ja. Dit wordt ondersteund. Meer informatie over [het gebruik van Azure monitor metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Zijn er hulpprogram ma's waarmee u de Media Services IoT Edge module gemakkelijker kunt bewaken?

Visual Studio code ondersteunt de extensie ' Azure IoT tools ' waarmee u de LVAEdge-module-eind punten eenvoudig kunt bewaken. U kunt dit hulp programma gebruiken om snel te beginnen met het bewaken van het IoT Hub ingebouwde eind punt voor "events" en de Afleidings berichten te zien die vanuit het edge-apparaat naar de cloud worden gerouteerd. 

Daarnaast kunt u deze extensie gebruiken om de module te bewerken voor de module LVAEdge om de instellingen voor de media grafiek te wijzigen.

Zie het artikel over [bewaking en logboek registratie](monitoring-logging.md) voor meer informatie.

## <a name="billing-and-availability"></a>Facturering en beschik baarheid

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Hoe wordt live video Analytics op IoT Edge gefactureerd?

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Quickstart: Over Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
