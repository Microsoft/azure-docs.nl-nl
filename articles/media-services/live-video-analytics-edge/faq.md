---
title: Veelgestelde vragen over live video Analytics op IoT Edge-Azure
description: In dit artikel vindt u antwoorden op veelgestelde vragen over live video Analytics op IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880047"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Veelgestelde vragen over live video Analytics op IoT Edge

In dit artikel vindt u antwoorden op veelgestelde vragen over live video Analytics op Azure IoT Edge.

## <a name="general"></a>Algemeen

**Welke systeem variabelen kan ik gebruiken in de definitie van de grafiek topologie?**

| Variabele   |  Beschrijving  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Vertegenwoordigt een directe UTC-tijd, meestal uitgedrukt als een datum en tijd van de dag in de volgende notatie:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Vertegenwoordigt een UTC-exemplaar (Coordinated Universal Time) in een indeling met ISO8601 bestanden met milliseconden, in de volgende indeling:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. GraphTopologyName   | Vertegenwoordigt een media grafiek topologie en bevat de blauw druk van een grafiek. | 
| System. GraphInstanceName |    Vertegenwoordigt een exemplaar van een media grafiek, bevatten parameter waarden en verwijst naar de topologie. | 

## <a name="configuration-and-deployment"></a>Configuratie en implementatie

**Kan ik de module media Edge implementeren op een Windows 10-apparaat?**

Ja. Zie [Linux-containers in Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers)voor meer informatie.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Vastleggen van IP-camera en RTSP-instellingen

**Moet ik een speciale SDK op mijn apparaat gebruiken om een videostream te verzenden?**

Nee, live video Analytics op IoT Edge biedt ondersteuning voor het vastleggen van media door gebruik te maken van RTSP (Realtime Streaming Protocol) voor video-streaming, dat wordt ondersteund op de meeste IP-camera's.

**Kan ik media naar live video Analytics op IoT Edge pushen met behulp van het Real-Time Messa ging Protocol (RTMP) of Smooth Streaming Protocol (zoals een Media Services live-gebeurtenis)?**

Nee, live video Analytics ondersteunt alleen RTSP voor het vastleggen van video van IP-camera's. Elke camera die RTSP-streaming via TCP/HTTP ondersteunt, zou moeten werken. 

**Kan ik de RTSP-bron-URL in een Graph-exemplaar opnieuw instellen of bijwerken?**

Ja, wanneer het exemplaar van de grafiek de status *inactief* heeft.  

**Is een RTSP-Simulator beschikbaar voor gebruik tijdens het testen en ontwikkelen?**

Ja, er is een [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-module beschikbaar voor gebruik in de Quick starts en zelf studies ter ondersteuning van het leer proces. Deze module wordt geleverd als Best effort en is mogelijk niet altijd beschikbaar. Het is raadzaam om de Simulator *niet* langer dan een paar uur te gebruiken. Voordat u een productie-implementatie plant, moet u investeren in tests met uw werkelijke RTSP-bron.

**Bieden jullie ondersteuning voor ONVIF-detectie van IP-camera's aan de rand?**

Nee, er wordt geen ondersteuning geboden voor Open Network Video Interface Forum (ONVIF) detectie van apparaten aan de rand.

## <a name="streaming-and-playback"></a>Streaming en afspelen

**Kan ik assets die zijn geregistreerd op Azure Media Services van de Edge, afspelen met streaming-technologieën zoals HLS of DASH?**

Ja. U kunt vastgelegde assets streamen zoals andere activa in Azure Media Services. Als u de inhoud wilt streamen, moet u een streaming-eind punt hebben gemaakt en de status wordt uitgevoerd. Als u het standaard proces voor het maken van streaming-locators gebruikt, hebt u toegang tot een Apple HTTP Live Streaming (HLS) of dynamisch adaptief streamen via HTTP (DASH, ook wel MPEG-DASH)-manifest voor streamen naar elk mogelijk Player-Framework. Zie [dynamische verpakking](../latest/dynamic-packaging-overview.md)voor meer informatie over het maken en publiceren van HLS-of Dash-manifesten.

**Kan ik de standaard functies voor beveiliging van inhoud en DRM van Media Services op een gearchiveerde Asset gebruiken?**

Ja. Alle standaard functies voor dynamische versleuteling van inhoud en digital rights management (DRM) zijn beschikbaar voor het gebruik van assets die zijn vastgelegd in een media grafiek.

**Welke spelers kan ik gebruiken om inhoud van de geregistreerde assets weer te geven?**

Alle standaard spelers die ondersteuning bieden voor compatibele HLS versie 3 of versie 4, worden ondersteund. Daarnaast wordt elke speler die compatibel is met MPEG-DASH Play, ook ondersteund.

De aanbevolen spelers voor testen zijn onder andere:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Schuda-speler](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Ingebouwde HTML5-Video speler voor Edge, Chrome of Safari
* Commerciële spelers die ondersteuning bieden voor HLS of onderbroken afspelen

**Wat zijn de limieten voor het streamen van een media Graph-Asset?**

Het streamen van een live of opgenomen activum vanuit een media grafiek maakt gebruik van hetzelfde hoogwaardige infra structuur en streaming-eind punt dat Media Services ondersteunt voor on-demand en live streamen voor media & entertainment, aan de bovenkant (OTT) en voor het uitzenden van klanten. Dit betekent dat u Azure Content Delivery Network, Verizon of Akamai snel en eenvoudig kunt inschakelen om uw inhoud te leveren aan een publiek als een klein aantal kijkers of tot miljoenen, afhankelijk van uw scenario.

U kunt inhoud afleveren met Apple HLS of MPEG-DASH.

## <a name="design-your-ai-model"></a>Uw AI-model ontwerpen 

**Ik heb meerdere AI-modellen ingepakt in een docker-container. Hoe moet ik deze gebruiken met live video Analytics?** 

Oplossingen variëren, afhankelijk van het communicatie protocol dat wordt gebruikt door de server voor afleiding om te communiceren met live video-analyses. In de volgende secties wordt beschreven hoe elk protocol werkt.

*Het HTTP-protocol gebruiken*:

* Eén container (enkelvoudige lvaExtension):  

   In uw server voor afleiding kunt u één poort gebruiken, maar ook verschillende eind punten voor verschillende AI-modellen. Voor een python-voor beeld kunt u bijvoorbeeld verschillende `route` s per model gebruiken, zoals hier wordt weer gegeven: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   En vervolgens, in uw live video Analytics-implementatie, bij het instantiëren van grafieken, de URL voor de server voor het afschermen van gegevens voor elk exemplaar instellen, zoals hier wordt weer gegeven: 

   1e instantie: Server-URL voor uitgevallen =`http://lvaExtension:44000/score/face_detection`<br/>
   2e instantie: Server-URL voor uitgevallen =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > U kunt ook uw AI-modellen zichtbaar maken op verschillende poorten en deze aanroepen wanneer u een exemplaar maakt van grafieken.  

* Meerdere containers: 

   Elke container wordt geïmplementeerd met een andere naam. In de documentatieset live video Analytics hebt u eerder geleerd hoe u een uitbrei ding met de naam *lvaExtension* implementeert. U kunt nu twee verschillende containers ontwikkelen, elk met dezelfde HTTP-interface, wat betekent dat ze hetzelfde `/score` eind punt hebben. Implementeer deze twee containers met verschillende namen en zorg ervoor dat beide worden geluisterd op *verschillende poorten*. 

   Bijvoorbeeld: een container met `lvaExtension1` de naam luistert naar de poort `44000` en een tweede container met `lvaExtension2` de naam luistert naar de poort `44001` . 

   In uw live video Analytics-topologie maakt u twee grafieken met verschillende Afleidings-Url's, zoals hier wordt weer gegeven: 

   Eerste instantie: Server-URL voor uitgevallen = `http://lvaExtension1:44001/score`    
   Tweede exemplaar: Server-URL voor uitgevallen = `http://lvaExtension2:44001/score`
   
*Het gRPC-protocol gebruiken*: 

* Met de live video Analytics-module 1,0, wanneer u een gRPC-Protocol (Remote Procedure Call) gebruikt, is de enige manier om dit te doen als de gRPC-server verschillende AI-modellen via verschillende poorten beschikbaar stelt. In [Dit code voorbeeld](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)wordt met één poort, 44000, alle Yolo modellen weer gegeven. In theorie kan de Yolo gRPC-server worden herschreven om sommige modellen op poort 44000 en anderen beschikbaar te maken op poort 45000. 

* Met de live video Analytics-module 2,0 wordt een nieuwe eigenschap toegevoegd aan het gRPC extension-knoop punt. Deze eigenschap, **extensionConfiguration**, is een optionele teken reeks die kan worden gebruikt als onderdeel van het gRPC-contract. Wanneer u meerdere AI-modellen hebt ingepakt in één server voor het afleiden van een storing, hoeft u geen knoop punt voor elk AI-model beschikbaar te maken. In plaats daarvan kunt u, als uitbreidings provider, voor een instantie van een grafiek definiëren hoe de verschillende AI-modellen moeten worden geselecteerd met behulp van de eigenschap **extensionConfiguration** . Tijdens de uitvoering wordt deze teken reeks door live video Analytics door gegeven aan de server voor het afleiden van de gegevens, zodat deze kan worden gebruikt om het gewenste AI-model aan te roepen. 

**Ik bouw een gRPC-server rond een AI-model en ik wil het gebruik door meerdere camera's of grafische instanties kunnen ondersteunen. Hoe kan ik mijn server bouwen?** 

 Zorg er eerst voor dat uw server meer dan één aanvraag tegelijk kan verwerken of dat u in parallelle threads werkt. 

Zo is bijvoorbeeld een standaard aantal parallelle kanalen ingesteld in het volgende [Live video Analytics gRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)-voor beeld: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

In de voor gaande instantie van de gRPC-server kan de server slechts drie kanalen tegelijk openen per camera of per grafiek topologie-exemplaar. Probeer niet meer dan drie instanties te verbinden met de server. Als u probeert meer dan drie kanalen te openen, zijn aanvragen in behandeling totdat een bestaand kanaal wordt neergezet.  

De voor gaande gRPC-Server implementatie wordt gebruikt in onze python-voor beelden. Als ontwikkelaar kunt u uw eigen server implementeren of de voor gaande standaard implementatie gebruiken om het werknemernummer te verhogen dat u instelt op het aantal camera's dat u wilt gebruiken voor video-feeds. 

Als u meerdere camera's wilt instellen en gebruiken, kunt u meerdere exemplaren van de grafiek topologie instantiëren die verwijzen naar dezelfde of een andere Afleidings server (bijvoorbeeld de server die wordt vermeld in de vorige alinea). 

**Ik wil meerdere frames van de upstream ontvangen voordat ik een afnemende beslissing neemt. Hoe kan ik dit inschakelen?** 

Onze huidige [standaard voorbeelden](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) werken in een *stateless* modus. De status van de vorige aanroepen wordt niet gewijzigd of zelfs niet aangeroepen. Dit betekent dat meerdere topologie-exemplaren dezelfde Afleidings server kunnen aanroepen, maar de server kan niet onderscheiden wie de oproept of de status per oproeper. 

*Het HTTP-protocol gebruiken*:

Om de status, elk aanroepende of graph-topologie-exemplaar, wordt de server voor het afwijzen van een oproep aangeroepen met behulp van de HTTP-query parameter die uniek is voor de aanroeper. Zo worden de url's van de server-URL voor elke instantie hier weer gegeven:  

1e topologie-exemplaar = `http://lvaExtension:44000/score?id=1`<br/>
2e topologie-exemplaar = `http://lvaExtension:44000/score?id=2`

… 

Aan de kant van de server weet de Score route naar wie wordt gebeld. Als ID = 1, dan kan deze de status afzonderlijk voor die aanroeper-of graph-topologie-instantie laten staan. U kunt de ontvangen video frames vervolgens in een buffer laten staan. Gebruik bijvoorbeeld een matrix of een woorden lijst met een datum/tijd-sleutel en de waarde is het kader. U kunt vervolgens de server die moet worden verwerkt (afleiden) definiëren nadat *x* aantal frames is ontvangen. 

*Het gRPC-protocol gebruiken*: 

Met een gRPC-extensie is elke sessie voor één camera voeding, dus u hoeft geen ID op te geven. Met de eigenschap extensionConfiguration kunt u nu de video frames in een buffer opslaan en de server die moet worden verwerkt (afleiden) definiëren nadat *x* aantal frames is ontvangen. 

**Worden alle ProcessMediaStreams op een bepaalde container hetzelfde AI-model uitgevoerd?** 

Nee. Het starten of stoppen van aanroepen van de eind gebruiker in een Graph-exemplaar vormt een sessie, of er is mogelijk een camera verbinding verbreken of opnieuw verbinding maken. Het doel is één sessie persistent te maken als de camera video gaat streamen. 

* Twee camera's die video verzenden voor verwerking, maken twee sessies. 
* Een camera die naar een grafiek gaat met twee gRPC-extensie knooppunten, maakt twee sessies. 

Elke sessie is een volledige duplex verbinding tussen live video Analytics en de gRPC-server en elke sessie kan een ander model of een andere pijp lijn hebben. 

> [!NOTE]
> Als een camera de verbinding verbreekt of opnieuw verbinding maakt, wordt er met live video Analytics een nieuwe sessie met de gRPC-server geopend. Er is geen vereiste voor de server om de status van deze sessies bij te houden. 

Live video Analytics voegt ook ondersteuning toe voor meerdere gRPC-uitbrei dingen voor één camera in een Graph-exemplaar. U kunt deze gRPC-extensies gebruiken om de AI-verwerking opeenvolgend, parallel of als combi natie van beide uit te voeren. 

> [!NOTE]
> Wanneer meerdere extensies parallel worden uitgevoerd, heeft dit invloed op uw hardwarebronnen. Houd er rekening mee dat u de hardware kiest die aansluit bij uw reken behoeften. 

**Wat is het maximum aantal gelijktijdige ProcessMediaStreams?** 

Live video Analytics heeft geen limieten voor dit aantal.  

**Hoe kan ik bepalen of mijn server voor ingaand gebruik CPU of GPU of een andere hardware Accelerator moet gebruiken?** 

Uw beslissing is afhankelijk van de complexiteit van het ontwikkelde AI-model en hoe u de CPU-en hardware-accelerators wilt gebruiken. Tijdens het ontwikkelen van het AI-model kunt u opgeven welke resources door het model moeten worden gebruikt en welke acties moeten worden uitgevoerd. 

**Hoe kan ik afbeeldingen opslaan met behulp van selectie vakjes na verwerking?** 

Momenteel bieden we coördinaten voor selectie kaders als alleen berichten afnemen. U kunt een aangepaste MJPEG-streamer bouwen die deze berichten kan gebruiken en de selectie vakjes in de video frames bedekken.  

## <a name="grpc-compatibility"></a>gRPC-compatibiliteit 

**Hoe weet ik wat de verplichte velden voor de media stream-descriptor zijn?** 

Elk veld dat u geen waarde opgeeft, krijgt een [standaard waarde, zoals is opgegeven door gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live video Analytics maakt gebruik van de *proto3* -versie van de taal protocol buffer. Alle protocol buffer gegevens die door live video Analytics-contracten worden gebruikt, zijn beschikbaar in de [buffer bestanden](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)van het protocol. 

**Hoe kan ik ervoor zorgen dat ik de meest recente protocol buffer bestanden gebruik?** 

U kunt de meest recente protocol buffer bestanden verkrijgen op de [site contract bestanden](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Wanneer de contract bestanden worden bijgewerkt, bevinden deze zich op deze locatie. Er is geen onmiddellijke planning voor het bijwerken van de protocol bestanden, dus zoek naar de naam van het pakket boven aan de bestanden om de versie te kennen. Dit moet als volgt worden gelezen: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Bij updates van deze bestanden wordt de ' v-waarde ' aan het einde van de naam verhoogd. 

> [!NOTE]
> Omdat live video Analytics gebruikmaakt van de proto3-versie van de taal, zijn de velden optioneel en is de versie achterwaarts en voorwaarts compatibel. 

**Welke gRPC-functies zijn beschikbaar voor gebruik met live video Analytics? Welke functies zijn verplicht en zijn optioneel?** 

U kunt alle gRPC-functies aan de server zijde gebruiken, mits aan het protobuf-contract (protocol buffers) wordt voldaan. 

## <a name="monitoring-and-metrics"></a>Bewaking en metrische gegevens

**Kan ik de media grafiek op de rand bewaken met behulp van Azure Event Grid?**

Ja. U kunt metrische gegevens over Prometheus gebruiken en deze publiceren naar uw event grid. 

**Kan ik Azure Monitor gebruiken om de status, metrische gegevens en prestaties van mijn media grafieken in de Cloud of aan de rand weer te geven?**

Ja, we ondersteunen deze methode. Zie [Azure monitor metrische gegevens overzicht](../../azure-monitor/platform/data-platform-metrics.md)voor meer informatie.

**Zijn er hulpprogram ma's waarmee u de Media Services IoT Edge module gemakkelijker kunt bewaken?**

Visual Studio code ondersteunt de Azure IoT tools-extensie, waarmee u de LVAEdge-module-eind punten eenvoudig kunt bewaken. U kunt dit hulp programma gebruiken om snel aan de slag te gaan met het bewaken van het ingebouwde eind punt van de IoT hub voor "events" en het afleiden van berichten die vanuit het apparaat aan de rand worden doorgestuurd naar de Cloud. 

Daarnaast kunt u deze extensie gebruiken om de module te bewerken voor de module LVAEdge om de instellingen voor de media grafiek te wijzigen.

Zie het artikel over [bewaking en logboek registratie](monitoring-logging.md) voor meer informatie.

## <a name="billing-and-availability"></a>Facturering en beschik baarheid

**Hoe wordt live video Analytics op IoT Edge gefactureerd?**

Zie [Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/)voor facturerings details.

## <a name="next-steps"></a>Volgende stappen

[Snelstartgids: aan de slag met live video Analytics op IoT Edge](get-started-detect-motion-emit-events-quickstart.md)