---
title: Naslag informatie over Azure IoT Hub-gegevens bewaken
description: Belang rijk referentie materiaal dat nodig is wanneer u Azure IoT Hub bewaakt
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570965"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Naslag informatie over Azure IoT Hub-gegevens bewaken

Zie [azure IOT hub bewaken](monitor-iot-hub.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure IOT hub.

## <a name="metrics"></a>Metrische gegevens

In deze sectie vindt u alle automatisch verzamelde platform gegevens voor Azure IoT Hub. De naam ruimte van de resource provider voor IoT Hub metrische gegevens is **micro soft. devices** en de type naam ruimte is **IoTHubs**.

In de volgende subsecties worden de IoT Hub platform metrieken per algemene categorie en weer gegeven op basis van de weergave naam die ze in de Azure Portal met behulp van hebben. Informatie wordt ook verstrekt die relevant is voor de metrische gegevens die in elke Subsectie worden weer gegeven.

U kunt ook een enkele tabel vinden met een lijst met alle IoT Hub platform metrieken per metrische naam onder [micro soft. devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs) in de Azure monitor-documentatie. Houd er rekening mee dat deze tabel niet alle informatie bevat, zoals [ondersteunde aggregaties](#supported-aggregations) voor bepaalde metrische gegevens, die in dit artikel beschikbaar zijn.

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/essentials/metrics-supported.md)voor meer informatie over metrische gegevens die door andere Azure-Services worden ondersteund.

**Onderwerpen in dit gedeelte**

- [Naslag informatie over Azure IoT Hub-gegevens bewaken](#monitoring-azure-iot-hub-data-reference)
  - [Metrische gegevens](#metrics)
    - [Ondersteunde aggregaties](#supported-aggregations)
    - [Metrische gegevens van de opdracht Cloud naar apparaat](#cloud-to-device-command-metrics)
    - [Metrische gegevens voor directe methoden van de Cloud naar een apparaat](#cloud-to-device-direct-methods-metrics)
    - [Metrische gegevens van de Cloud naar het apparaat dubbele bewerkingen](#cloud-to-device-twin-operations-metrics)
    - [Metrische configuratie gegevens](#configurations-metrics)
    - [Metrische gegevens per dag quotum](#daily-quota-metrics)
    - [Metrische gegevens van apparaat](#device-metrics)
    - [Metrische gegevens over de telemetrie van apparaten](#device-telemetry-metrics)
    - [Gegevens van het apparaat naar de Cloud voor dubbele bewerkingen](#device-to-cloud-twin-operations-metrics)
    - [Metrische gegevens van gebeurtenis raster](#event-grid-metrics)
    - [Metrische gegevens van taken](#jobs-metrics)
    - [Routerings metrieken](#routing-metrics)
    - [Dubbele metrische query gegevens](#twin-query-metrics)
  - [Metrische dimensies](#metric-dimensions)
  - [Resourcelogboeken](#resource-logs)
    - [Verbindingen](#connections)
    - [Apparaattelemetrie](#device-telemetry)
    - [Cloud-naar-apparaat-opdrachten](#cloud-to-device-commands)
    - [Bewerkingen voor apparaat-id's](#device-identity-operations)
    - [Bestandsuploadbewerkingen](#file-upload-operations)
    - [Routes](#routes)
    - [Dubbele bewerkingen van het apparaat naar de Cloud](#device-to-cloud-twin-operations)
    - [Dubbele bewerkingen van het Cloud naar het apparaat](#cloud-to-device-twin-operations)
    - [Dubbele query's](#twin-queries)
    - [Taakbewerkingen](#jobs-operations)
    - [Directe methoden](#direct-methods)
    - [Gedistribueerde tracering (preview-versie)](#distributed-tracing-preview)
      - [IoT Hub D2C-Logboeken (apparaat-naar-Cloud)](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub ingangs logboeken](#iot-hub-ingress-logs)
      - [IoT Hub uitgangs logboeken](#iot-hub-egress-logs)
    - [Configuraties](#configurations)
    - [Apparaatversleuteling (preview-versie)](#device-streams-preview)
  - [Tabellen Azure Monitor logboeken](#azure-monitor-logs-tables)
  - [Zie ook](#see-also)

### <a name="supported-aggregations"></a>Ondersteunde aggregaties

De kolom **aggregatie type** in elke tabel komt overeen met de standaard aggregatie die wordt gebruikt wanneer de metriek voor een grafiek of waarschuwing wordt geselecteerd.

   ![Scherm afbeelding met aggregatie voor metrische gegevens](./media/monitor-iot-hub-reference/aggregation-type.png)

Voor de meeste metrische gegevens zijn alle aggregatie typen geldig. bij metrische waarden tellen, die met de waarde **aantal** van een **eenheids** kolom, zijn echter slechts enkele aggregaties geldig. Metrische gegevens over het aantal kunnen een van de volgende twee typen zijn:

* Voor metrische gegevens van **één punt** telt IOT hub een enkel data-punt registreren--in feite een 1--elke keer dat de gemeten bewerking plaatsvindt. Azure Monitor worden deze gegevens punten vervolgens op basis van de opgegeven granulariteit opgeteld. Voor beelden van metrische gegevens met **één punt** zijn *telemetriegegevens* die zijn verzonden en *C2D bericht leveringen zijn voltooid*. Voor deze metrische gegevens is het enige relevante samenvoegings type totaal (Sum). Met de portal kunt u minimum, maximum en gemiddeld kiezen. deze waarden zullen echter altijd 1 zijn.

* Voor de metrische gegevens van **moment opnamen** tellen IOT hub een totaal aantal registreren wanneer de gemeten bewerking plaatsvindt. Op dit moment zijn er drie **moment opname** -metrische gegevens gegenereerd door IOT hub: het *totale aantal gebruikte berichten*, de *totale hoeveelheid apparaten (preview)* en *verbonden apparaten (preview)*. Omdat deze metrische gegevens elke keer dat ze worden verzonden, een totale hoeveelheid bevatten, is het opsommen van de opgegeven granulatie niet zinvol. Azure Monitor kunt u het gemiddelde, het minimum en het maximum voor het aggregatie type voor deze metrische gegevens selecteren.

### <a name="cloud-to-device-command-metrics"></a>Metrische gegevens van de opdracht Cloud naar apparaat

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|C2D-berichten verlopen (preview-versie)|C2DMessagesExpired|Aantal|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|Geen|
|C2D-bericht leveringen voltooid|C2D. commands. OUTuitgang. complete. geslaagd|Aantal|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|Geen|
|C2D-berichten zijn afgebroken|C2D. commands. uitgangs. Abandon. geslaagd|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|Geen|
|Geweigerde C2D-berichten|C2D. commands. uitgangs. reject. geslaagd|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="cloud-to-device-direct-methods-metrics"></a>Metrische gegevens voor directe methoden van de Cloud naar een apparaat

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Mislukte directe aanroepen van methode|C2D. methods. failure|Aantal|Totaal|Het aantal mislukte direct-methode aanroepen.|Geen|
|Aanvraag grootte van directe-methode aanroepen|C2D. methods. requestSize|Bytes|Gemiddeld|Het aantal voltooide direct-methode aanvragen.|Geen|
|Antwoord grootte van directe methode aanroepen|C2D. methods. responseSize|Bytes|Gemiddeld|Het aantal voltooide direct-methode reacties.|Geen|
|Geslaagde directe aanroepen van de methode|C2D. methods. geslaagd|Aantal|Totaal|Het aantal voltooide direct-methode aanroepen.|Geen|

Voor metrische gegevens met een **eenheids** waarde **van het totale aantal (** Sum) is aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="cloud-to-device-twin-operations-metrics"></a>Metrische gegevens van de Cloud naar het apparaat dubbele bewerkingen

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Mislukte dubbele Lees bewerkingen van back-end|C2D. dubbele. Read. failure|Aantal|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|Mislukte dubbele updates van back-end|C2D. dubbele. update. failure|Aantal|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Geen|
|Reactie grootte van dubbele Lees bewerkingen van de back-end|C2D. dubbele. Lees. grootte|Bytes|Gemiddeld|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|Grootte van dubbele updates van back-end|C2D. dubbele. update. grootte|Bytes|Gemiddeld|De totale grootte van alle geslaagde back-end-geïnitieerde twee-updates.|Geen|
|Geslaagde dubbele Lees bewerkingen van back-end|C2D. dubbele. lezen. geslaagd|Aantal|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|Geslaagde dubbele updates van back-end|C2D. dubbele. update. geslaagd|Aantal|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="configurations-metrics"></a>Metrische configuratie gegevens

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Metrische configuratie gegevens|configuraties|Aantal|Totaal|Het totale aantal ruwe verwerkingen dat is uitgevoerd voor apparaatconfiguratie en IoT Edge implementatie op een set doel apparaten. Dit omvat ook het aantal bewerkingen dat het apparaat dubbel of de module heeft gewijzigd vanwege deze configuraties.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="daily-quota-metrics"></a>Metrische gegevens per dag quotum

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Totale hoeveelheid gegevens gebruik van apparaat|deviceDataUsage|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|Totaal gebruik van apparaatgegevens (preview-versie)|deviceDataUsageV2|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|Totaal aantal gebruikte berichten|dailyMessageQuotaUsed|Count|Gemiddeld|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Geen|

Voor het *totale aantal gebruikte berichten* worden alleen de minimum-, maximum-en gemiddelde aggregaties ondersteund. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="device-metrics"></a>Metrische gegevens van apparaat

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Totaal aantal apparaten (afgeschaft)|apparaten. totalDevices|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|Verbonden apparaten (afgeschaft) |apparaten. connectedDevices. allProtocol|Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|Totaal aantal apparaten (preview-versie)|totalDeviceCount|Count|Gemiddeld|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|Verbonden apparaten (preview-versie)|connectedDeviceCount|Count|Gemiddeld|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|

Voor het *totale aantal apparaten (afgeschaft)* en *verbonden apparaten (afgeschaft)* is alleen totale (Sum) aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

Voor het *totale aantal apparaten (preview)* en *verbonden apparaten (preview)* zijn alleen minimum-, maximum-en gemiddelde aggregaties geldig. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

*Verbonden apparaten (preview)* en *totale aantal apparaten (preview)* kunnen niet worden geëxporteerd via Diagnostische instellingen.

### <a name="device-telemetry-metrics"></a>Metrische gegevens over de telemetrie van apparaten

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal beperkings fouten|D2C. telemetrie. ingress. sendThrottle|Aantal|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Geen|
|Verzend pogingen voor telemetrie-berichten|D2C. telemetrie. ingress. allProtocol|Aantal|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Geen|
|Verzonden telemetriegegevens|D2C. telemetrie. ingress. geslaagd|Aantal|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="device-to-cloud-twin-operations-metrics"></a>Gegevens van het apparaat naar de Cloud voor dubbele bewerkingen

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Mislukte dubbele Lees bewerkingen van apparaten|D2C. dubbele. Read. failure|Aantal|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Geen|
|Mislukte dubbele updates van apparaten|D2C. dubbele. update. failure|Aantal|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Geen|
|Reactie grootte van dubbele Lees bewerkingen van apparaten|D2C. dubbele. Lees. grootte|Bytes|Gemiddeld|Het aantal gestarte dubbele Lees bewerkingen voor alle geslaagde apparaten.|Geen|
|Grootte van dubbele updates van apparaten|D2C. dubbele. update. grootte|Bytes|Gemiddeld|De totale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|Geslaagde dubbele Lees bewerkingen van apparaten|D2C. dubbele. lezen. geslaagd|Aantal|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Geen|
|Geslaagde dubbele updates van apparaten|D2C. dubbele. update. geslaagd|Aantal|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="event-grid-metrics"></a>Metrische gegevens van gebeurtenis raster

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Event Grid leveringen (preview-versie)|EventGridDeliveries|Aantal|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer ( https://aka.ms/ioteventgrid) .|Resultaat,<br/>EventType<br>*Zie [metrische dimensies](#metric-dimensions)voor meer informatie*.|
|Event Grid latentie (preview-versie)|EventGridLatency|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|EventType<br>*Zie [metrische dimensies](#metric-dimensions)voor meer informatie*.|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="jobs-metrics"></a>Metrische gegevens van taken

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Voltooide taken|Jobs. voltooid|Aantal|Totaal|Het aantal voltooide taken.|Geen|
|Mislukte aanroepen naar lijst taken|Jobs. listJobs. failure|Aantal|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Geen|
|Kan geen aanroepen van methode aanroep taken uitvoeren|Jobs. createDirectMethodJob. failure|Aantal|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Geen|
|Kan geen dubbele update taken uitvoeren|Jobs. createTwinUpdateJob. failure|Aantal|Totaal|Het aantal mislukte het maken van dubbele update taken.|Geen|
|Mislukte taak annuleringen|Jobs. cancelJob. failure|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen|
|Mislukte taak query's|Jobs. queryJobs. failure|Aantal|Totaal|Het aantal mislukte aanroepen naar query taken.|Geen|
|Mislukte taken|Jobs. mislukt|Aantal|Totaal|Het aantal mislukte taken.|Geen|
|Geslaagde aanroepen naar lijst taken|Jobs. listJobs. geslaagd|Aantal|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Geen|
|Geslaagde creatie van methode aanroep taken|Jobs. createDirectMethodJob. geslaagd|Aantal|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Geen|
|Geslaagde creatie van dubbele update taken|Jobs. createTwinUpdateJob.<br>voltooid|Aantal|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Geen|
|Voltooide taak annuleringen|Jobs. cancelJob. geslaagd|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen|
|Geslaagde taak query's|Jobs. queryJobs. geslaagd|Aantal|Totaal|Het aantal geslaagde aanroepen naar query taken.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="routing-metrics"></a>Routerings metrieken

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
| Bezorgings pogingen routeren (preview) |RoutingDeliveries | Aantal | Totaal |Dit is de metrische gegevens van de route ring. Gebruik de dimensies om de leverings status voor een bepaald eind punt of voor een specifieke routerings bron te identificeren.| Resultaat,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Zie [metrische dimensies](#metric-dimensions)voor meer informatie*. |
| Grootte van bezorgings gegevens in bytes (preview-versie)|RoutingDataSizeInBytesDelivered| Bytes | Totaal |Het totale aantal bytes dat wordt gerouteerd door IoT Hub naar het aangepaste eind punt en het ingebouwde eind punt. Gebruik de dimensies om gegevens grootte te identificeren die naar een bepaald eind punt of voor een specifieke routerings bron worden doorgestuurd.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Zie [metrische dimensies](#metric-dimensions)voor meer informatie*.|
| Routerings latentie (preview-versie) |RoutingDeliveryLatency| Milliseconden | Gemiddeld |Dit is de metrische gegevens van de bezorgings latentie van de route ring. Gebruik de dimensies om de latentie voor een bepaald eind punt of voor een specifieke routerings bron te identificeren.| RoutingSource,<br>EndpointType,<br>EndpointName<br>*Zie [metrische dimensies](#metric-dimensions)voor meer informatie*.|
|Route ring: blobs die aan de opslag worden geleverd|D2C. endpoints. outwaar. storage. blobs|Aantal|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Geen|
|Route ring: gegevens worden geleverd aan de opslag|D2C. endpoints. out. storage. bytes|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Geen|
|Route ring: bericht latentie voor Event hub|D2C. endpoints. latentie. Event hubs|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) tussen bericht binnenkomend naar IoT Hub en het bericht wordt binnengebracht in aangepaste eind punten van het type Event hub. Dit omvat geen berichten routes naar het ingebouwde eind punt (gebeurtenissen).|Geen|
|Route ring: bericht latentie voor Service Bus wachtrij|D2C. endpoints. latentie. serviceBusQueues|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een Service Bus wachtrij-eind punt.|Geen|
|Route ring: bericht latentie voor Service Bus onderwerp|D2C. endpoints. latentie. serviceBusTopics|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een eind punt van een Service Bus onderwerp.|Geen|
|Route ring: bericht latentie voor berichten/gebeurtenissen|D2C. endpoints. latentie. builtIn. Events|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in het ingebouwde eind punt (berichten/gebeurtenissen) en de terugval route.|Geen|
|Route ring: bericht latentie voor opslag|D2C. endpoints. latentie. opslag|Milliseconden|Gemiddeld|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een opslag eindpunt.|Geen|
|Route ring: berichten worden bezorgd bij Event hub|D2C. endpoints. uitgangs. Event hubs|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan aangepaste eind punten van het type Event hub. Dit omvat geen berichten routes naar het ingebouwde eind punt (gebeurtenissen).|Geen|
|Route ring: berichten worden bezorgd bij Service Bus wachtrij|D2C. endpoints. uitgangs. serviceBusQueues|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Geen|
|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|D2C. endpoints. uitgangs. serviceBusTopics|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Geen|
|Route ring: berichten worden bezorgd bij terugval|D2C. telemetrie.. fallback|Aantal|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Geen|
|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|D2C. endpoints. uitgangs punt. builtIn. Events|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen) en terugval route.|Geen|
|Route ring: berichten worden bezorgd bij de opslag|D2C. endpoints. outwaarde. Storage|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Geen|
|Route ring: telemetrie-berichten|D2C. telemetrie. uitgangs. geslaagd|Aantal|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen|
|Route ring: telemetrie-berichten verwijderd |D2C. telemetrie. uitgangs. verwijderd|Aantal|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Geen|
|Route ring: telemetrie-berichten incompatibel|D2C. telemetrie. uitgangs. ongeldig|Aantal|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Een bericht is niet compatibel met een eind punt wanneer IOT hub het bericht aan een eind punt probeert af te leveren en mislukt met een niet-tijdelijke fout. Ongeldige berichten worden niet opnieuw geprobeerd. Deze waarde omvat geen nieuwe pogingen.|Geen|
|Route ring: telemetriegegevens van zwevende berichten |D2C. telemetrie.. zwevend|Aantal|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring omdat deze niet overeenkomen met een routerings query wanneer terugval route is uitgeschakeld.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

### <a name="twin-query-metrics"></a>Dubbele metrische query gegevens

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Mislukte dubbele query's|twinQueries. failure|Aantal|Totaal|Het aantal mislukte dubbele query's.|Geen|
|Geslaagde dubbele query's|twinQueries. geslaagd|Aantal|Totaal|Het aantal geslaagde dubbele query's.|Geen|
|Resultaat grootte van dubbele query's|twinQueries.resultSize|Bytes|Gemiddeld|Het totaal van de resultaat grootte van alle geslaagde dubbele query's.|Geen|

Voor metrische gegevens met een **eenheids** waarde van het **aantal**, is alleen totaal (Sum)-aggregatie geldig. De minimum-, maximum-en gemiddelde aggregaties retour neren altijd 1. Zie [ondersteunde aggregaties](#supported-aggregations)voor meer informatie.

## <a name="metric-dimensions"></a>Metrische dimensies

Azure IoT Hub heeft de volgende dimensies die zijn gekoppeld aan een aantal metrische gegevens over de route ring en het gebeurtenis raster.

|Dimensie naam | Beschrijving|
|---|---|
||
|**EndpointName**| De naam van het eind punt.|
|**EndpointType**|Een van de volgende: **Event hubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **Builtin** of **blobStorage**.|
|**EventType**| Een van de volgende Event Grid gebeurtenis typen: **micro soft. devices. DeviceCreated**. **Micro soft. devices. DeviceDeleted**, **micro soft. devices. DeviceConnected**, **micro soft. devices. DeviceDisconnected**, of **micro soft. devices. DeviceTelemetry**. Zie [gebeurtenis typen](iot-hub-event-grid.md#event-types)voor meer informatie.|
|**FailureReasonCategory**| Een van de volgende waarden: **ongeldig**, **verwijderd**, **zwevend** of **Null**.|
|**Resultaat**| **Geslaagd** of **mislukt**.|
|**RoutingSource**| Apparaat-berichten<br>Dubbele wijzigings gebeurtenissen<br>Levenscyclus gebeurtenissen van apparaat|

Zie voor meer informatie over metrische dimensies [multi-dimensionale metrische gegevens](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Resourcelogboeken

In deze sectie vindt u een lijst met alle bron logboek categorie typen en schema's die zijn verzameld voor Azure IoT Hub. De resource provider en het type voor alle IoT Hub Logboeken is [micro soft. devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs).

**Onderwerpen in dit gedeelte**

- [Naslag informatie over Azure IoT Hub-gegevens bewaken](#monitoring-azure-iot-hub-data-reference)
  - [Metrische gegevens](#metrics)
    - [Ondersteunde aggregaties](#supported-aggregations)
    - [Metrische gegevens van de opdracht Cloud naar apparaat](#cloud-to-device-command-metrics)
    - [Metrische gegevens voor directe methoden van de Cloud naar een apparaat](#cloud-to-device-direct-methods-metrics)
    - [Metrische gegevens van de Cloud naar het apparaat dubbele bewerkingen](#cloud-to-device-twin-operations-metrics)
    - [Metrische configuratie gegevens](#configurations-metrics)
    - [Metrische gegevens per dag quotum](#daily-quota-metrics)
    - [Metrische gegevens van apparaat](#device-metrics)
    - [Metrische gegevens over de telemetrie van apparaten](#device-telemetry-metrics)
    - [Gegevens van het apparaat naar de Cloud voor dubbele bewerkingen](#device-to-cloud-twin-operations-metrics)
    - [Metrische gegevens van gebeurtenis raster](#event-grid-metrics)
    - [Metrische gegevens van taken](#jobs-metrics)
    - [Routerings metrieken](#routing-metrics)
    - [Dubbele metrische query gegevens](#twin-query-metrics)
  - [Metrische dimensies](#metric-dimensions)
  - [Resourcelogboeken](#resource-logs)
    - [Verbindingen](#connections)
    - [Apparaattelemetrie](#device-telemetry)
    - [Cloud-naar-apparaat-opdrachten](#cloud-to-device-commands)
    - [Bewerkingen voor apparaat-id's](#device-identity-operations)
    - [Bestandsuploadbewerkingen](#file-upload-operations)
    - [Routes](#routes)
    - [Dubbele bewerkingen van het apparaat naar de Cloud](#device-to-cloud-twin-operations)
    - [Dubbele bewerkingen van het Cloud naar het apparaat](#cloud-to-device-twin-operations)
    - [Dubbele query's](#twin-queries)
    - [Taakbewerkingen](#jobs-operations)
    - [Directe methoden](#direct-methods)
    - [Gedistribueerde tracering (preview-versie)](#distributed-tracing-preview)
      - [IoT Hub D2C-Logboeken (apparaat-naar-Cloud)](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub ingangs logboeken](#iot-hub-ingress-logs)
      - [IoT Hub uitgangs logboeken](#iot-hub-egress-logs)
    - [Configuraties](#configurations)
    - [Apparaatversleuteling (preview-versie)](#device-streams-preview)
  - [Tabellen Azure Monitor logboeken](#azure-monitor-logs-tables)
  - [Zie ook](#see-also)

### <a name="connections"></a>Verbindingen

De categorie verbindingen houdt het apparaat Connect en verbreekt gebeurtenissen van een IoT-hub en van fouten. Deze categorie is handig voor het identificeren van niet-geautoriseerde Verbindings pogingen en of waarschuwingen wanneer u de verbinding met apparaten verliest.

> [!NOTE]
> Voor een betrouw bare verbindings status van apparaten controleert u de heartbeat van het [apparaat](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Apparaattelemetrie

De telemetrie-categorie van het apparaat houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de telemetrie-pijp lijn. Deze categorie bevat fouten die optreden bij het verzenden van telemetrie-gebeurtenissen (zoals het beperken) en het ontvangen van telemetrie-gebeurtenissen (zoals niet-geautoriseerde lezer). Deze categorie kan geen fouten ondervangen die worden veroorzaakt door code die op het apparaat wordt uitgevoerd.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Cloud-naar-apparaat-opdrachten

De categorie Cloud-naar-apparaat-opdrachten houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de Cloud-naar-apparaat-berichten pijplijn. Deze categorie bevat fouten die zich voordoen vanaf:

* Cloud-naar-apparaat-berichten verzenden (zoals niet-geautoriseerde Sender fouten),
* Cloud-naar-apparaat-berichten ontvangen (zoals het aantal leverings fouten is overschreden), en
* Cloud-naar-apparaat-bericht feedback ontvangen (zoals verlopen feedback fouten).

In deze categorie worden geen fouten onderschept wanneer het Cloud-naar-apparaat-bericht wordt bezorgd, maar vervolgens niet goed wordt verwerkt door het apparaat.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Bewerkingen voor apparaat-id's

De categorie apparaat-id bewerkingen houdt fouten bij die optreden wanneer u probeert een vermelding te maken, bij te werken of te verwijderen in het id-REGI ster van uw IoT-hub. Het bijhouden van deze categorie is handig voor het inrichten van scenario's.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Bestandsuploadbewerkingen

De categorie bestand uploaden houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de functionaliteit voor het uploaden van bestanden. Deze categorie omvat:

* Fouten die zich voordoen met de SAS-URI, zoals wanneer deze verlopen voordat een apparaat de hub van een voltooide upload ontvangt.

* Mislukte uploads die zijn gerapporteerd door het apparaat.

* Fouten die optreden wanneer een bestand niet wordt gevonden in de opslag tijdens het maken van IoT Hub meldings bericht.

Deze categorie kan geen fouten ondervangen die rechtstreeks optreden terwijl het apparaat een bestand naar de opslag uploadt.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Routes

In de categorie [bericht routering](./iot-hub-devguide-messages-d2c.md) worden fouten bijgehouden die optreden tijdens de bericht route-evaluatie en de eindpunt status, zoals wordt waargenomen door IOT hub. Deze categorie bevat gebeurtenissen zoals:

* Een regel resulteert in "niet-gedefinieerd",
* IoT Hub markeert een eind punt als Dead of
* Eventuele fouten die zijn ontvangen van een eind punt.

Deze categorie bevat geen specifieke fouten voor de berichten zelf (zoals storingen in het apparaat), die worden gerapporteerd in de categorie telemetrie van het apparaat.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Hier vindt u meer informatie over route ring van resource logboeken:

* [Lijst met fout codes voor het routerings bron logboek](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lijst met routerings bron logboeken operationNames](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Dubbele bewerkingen van het apparaat naar de Cloud

Met de categorie apparaat-naar-Cloud dubbele bewerkingen worden door het apparaat geïnitieerde gebeurtenissen op apparaat apparaatdubbels bijgehouden. Tot deze bewerkingen behoren onder andere ophalen, gerapporteerde eigenschappen bijwerken en abonneren op gewenste eigenschappen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Dubbele bewerkingen van het Cloud naar het apparaat

De categorie met dubbele bewerkingen van Cloud naar apparaat houdt de door de service geïnitieerde gebeurtenissen op apparaat apparaatdubbels. Deze bewerkingen kunnen bestaan uit het ophalen van dubbele, update of vervangen Tags en het bijwerken of vervangen van gewenste eigenschappen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Dubbele query's

De categorie dubbele query's rapporteert over query aanvragen voor apparaatdubbels die in de cloud worden gestart.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Taakbewerkingen

De categorie taken bewerkingen rapporteert over taak aanvragen voor het bijwerken van apparaatdubbels of het aanroepen van directe methoden op meerdere apparaten. Deze aanvragen worden gestart in de Cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Directe methoden

In de categorie directe methoden worden aanvraag-antwoord interacties bijgehouden die naar afzonderlijke apparaten worden verzonden. Deze aanvragen worden gestart in de Cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Gedistribueerde tracering (preview-versie)

De categorie gedistribueerde tracering houdt de correlatie-Id's bij voor berichten die de header van de tracering context bevatten. Als u deze logboeken volledig wilt inschakelen, moet de client code worden bijgewerkt door het [end-to-end van IOT-toepassingen te analyseren en te diagnosticeren met IOT hub Distributed tracing (preview)](iot-hub-distributed-tracing.md).

Houd er rekening mee dat `correlationId` u voldoet aan het voor stel van de [W3C-traceer context](https://github.com/w3c/trace-context) , waarbij het een `trace-id` en een bevat `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C-Logboeken (apparaat-naar-Cloud)

IoT Hub registreert dit logboek wanneer een bericht met geldige traceer eigenschappen arriveert bij IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Hier `durationMs` wordt niet berekend wanneer de klok van het IOT hub mogelijk niet synchroon is met de klok van het apparaat en daarom kan de duur van de berekening misleidend zijn. U wordt aangeraden logica te schrijven met behulp van de tijds tempels in de `properties` sectie om pieken vast te leggen in latentie van apparaat naar Cloud.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Geheel getal | De grootte van het apparaat-naar-Cloud bericht in bytes |
| **deviceId** | Teken reeks van ASCII 7-bits alfanumerieke tekens | De identiteit van het apparaat |
| **callerLocalTimeUtc** | UTC-tijds tempel | De aanmaak tijd van het bericht zoals gerapporteerd door de lokale klok van het apparaat |
| **calleeLocalTimeUtc** | UTC-tijds tempel | De tijd van de aankomst van het bericht op de gateway van de IoT Hub, zoals gerapporteerd door de IoT Hub klok van de service |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub ingangs logboeken

IoT Hub registreert dit logboek wanneer een bericht met geldige traceer eigenschappen naar een interne of ingebouwde Event hub schrijft.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

In de `properties` sectie bevat dit logboek extra informatie over berichten binnenkomend.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Tekenreeks | Waar of onwaar, geeft aan of bericht routering is ingeschakeld in de IoT Hub |
| **parentSpanId** | Tekenreeks | De [reeks-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, die de D2C-bericht tracering in dit geval zou zijn |

#### <a name="iot-hub-egress-logs"></a>IoT Hub uitgangs logboeken

IoT Hub registreert dit logboek wanneer [route ring](iot-hub-devguide-messages-d2c.md) is ingeschakeld en het bericht naar een [eind punt](iot-hub-devguide-endpoints.md)wordt geschreven. Als route ring niet is ingeschakeld, wordt dit logboek IoT Hub niet geregistreerd.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

In de `properties` sectie bevat dit logboek extra informatie over berichten binnenkomend.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Tekenreeks | De naam van het eind punt van de route ring |
| **endpointType** | Tekenreeks | Het type van het eind punt van de route ring |
| **parentSpanId** | Tekenreeks | De [reeks-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, dat de IOT hub bericht tracering in dit geval zou zijn |

### <a name="configurations"></a>Configuraties

In IoT Hub configuratie logboeken worden gebeurtenissen en fouten bijgehouden voor de functie automatisch Apparaatbeheer instellen.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Apparaatversleuteling (preview-versie)

De categorie Device streams traceert aanvraag-antwoord interacties die naar afzonderlijke apparaten worden verzonden.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabellen Azure Monitor logboeken
<!-- REQUIRED. Please keep heading in this order -->

In deze sectie wordt verwezen naar alle Azure Monitor-tabellen Kusto die relevant zijn voor Azure IoT Hub en die beschikbaar zijn voor query's door Log Analytics. Zie [IOT hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) in de tabel verwijzing Azure monitor logboeken voor een lijst met deze tabellen en koppelingen naar meer informatie over het resource type IOT hub.

Zie de [Naslag informatie over Azure monitor logboek tabel](/azure/azure-monitor/reference/tables/tables-resourcetype)voor een verwijzing naar Azure monitor logboeken/log Analytics tabellen.

## <a name="see-also"></a>Zie ook

* Zie [azure IOT hub bewaken](monitor-iot-hub.md) voor een beschrijving van het controleren van Azure IOT hub.
* Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
