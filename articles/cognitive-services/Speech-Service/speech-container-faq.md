---
title: Veelgestelde vragen over Speech Service-containers
titleSuffix: Azure Cognitive Services
description: Spraakcontainers installeren en uitvoeren. spraak-naar-tekst transcribert audiostromen in realtime naar tekst die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. Tekst-naar-spraak converteert invoertekst naar menselijke gesynthetiseerde spraak.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387952"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Veelgestelde vragen over Speech Service-containers

Wanneer u de Speech-service met containers gebruikt, moet u vertrouwen op deze verzameling veelgestelde vragen voordat u naar ondersteuning escaleert. In dit artikel worden vragen met verschillende niveaus beschreven, van algemeen tot technisch. Klik op de vraag om een antwoord uit te vouwen.

## <a name="general-questions"></a>Algemene vragen

<details>
<summary>
<b>Hoe werken Spraakcontainers en hoe stel ik ze in?</b>
</summary>

**Antwoord:** Bij het instellen van het productiecluster zijn er verschillende zaken waar u rekening mee moet houden. Ten eerste is het instellen van één taal, meerdere containers, op dezelfde computer, geen groot probleem. Als u problemen ondervindt, kan het een hardwareprobleem zijn. Daarom kijken we eerst naar de resource, dat wil zeggen: CPU- en geheugenspecificaties.

Denk even na over de `ja-JP` container en het meest recente model. Het akoestische model is het meest veeleisende stukje CPU, terwijl het taalmodel het meeste geheugen vereist. Wanneer we een benchmark voor het gebruik hebben gemaakt, duurt het ongeveer 0,6 CPU-kernen om één spraak-naar-tekst-aanvraag te verwerken wanneer audio in realtime binnenstroomt (zoals via de microfoon). Als u sneller audio gebruikt dan realtime (zoals vanuit een bestand), kan dat gebruik worden verdubbeld (1,2 x kernen). Ondertussen is het geheugen dat hieronder wordt vermeld operationeel geheugen voor het decoderen van spraak. Er wordt *geen* rekening gehouden met de werkelijke volledige grootte van het taalmodel, dat zich in de bestandscache bevindt. Voor `ja-JP` is dat nog eens 2 GB; voor kan dit meer zijn `en-US` (6-7 GB).

Als u een computer hebt met weinig geheugen en u er meerdere talen op wilt implementeren, is het mogelijk dat de bestandscache vol is en dat het besturingssysteem modellen in- en uit pagina's moet plaatsen. Voor een lopende transcriptie kan dat slecht zijn, wat kan leiden tot vertragingen en andere gevolgen voor de prestaties.

Daarnaast verpakken we uitvoerbare bestanden vooraf voor machines met de [AVX2-instructieset (Advanced Vector Extension).](speech-container-howto.md#advanced-vector-extension-support) Voor een machine met de AVX512-instructieset is het genereren van code vereist voor dat doel. Het starten van 10 containers voor 10 talen kan de CPU tijdelijk uitputten. Een bericht zoals dit wordt weergegeven in de docker-logboeken:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

U kunt het aantal decoders dat u in één *container* wilt instellen met behulp van `DECODER MAX_COUNT` een variabele. Daarom moeten we beginnen met uw SKU (CPU/geheugen) en kunnen we voorstellen hoe u het beste uit deze SKU kunt halen. Een goed uitgangspunt is het verwijzen naar de aanbevolen resourcespecificaties van de hostmachine.

<br>
</details>

<details>
<summary>
<b>Kunt u helpen met capaciteitsplanning en kostenraming van on-prem spraak-naar-tekst-containers?</b>
</summary>

**Antwoord:** Voor containercapaciteit in de batchverwerkingsmodus kan elke decoder 2-3x in realtime verwerken, met twee CPU-kernen, voor één herkenning. We raden u niet aan om meer dan twee gelijktijdige herkenningen per container-instantie te bewaren, maar raden u aan meer exemplaren van containers uit te laten uitvoeren om redenen van betrouwbaarheid/beschikbaarheid, achter een load balancer.

We kunnen echter elke container-instantie uitvoeren met meer decoders. We kunnen bijvoorbeeld 7 decoders per containerinvoer instellen op een computer met acht kernen (bij meer dan 2x elk), wat een doorvoer van 15 keer oplevert. Er is een -param `DECODER_MAX_COUNT` waar u rekening mee moet houden. In het extreme geval doen zich problemen met de betrouwbaarheid en latentie voor, met een aanzienlijke toename van de doorvoer. Voor een microfoon is dit 1x realtime. Het algehele gebruik moet ongeveer één kern zijn voor één herkenning.

Voor een scenario met een verwerking van 1.000.000 uur in de batchverwerkingsmodus kunnen in extreme gevallen drie VM's dit binnen 24 uur verwerken, maar niet gegarandeerd. Voor het afhandelen van piekdagen, failover, bijwerken en om een minimale back-up/BCP te bieden, raden we 4-5 machines aan in plaats van 3 per cluster en met meer dan 2 clusters.

Voor hardware gebruiken we standaard Azure-VM als referentie (elke kern moet `DS13_v2` 2,6 GHz of beter zijn, met AVX2-instructieset ingeschakeld).

| Exemplaar  | vCPU('s) | RAM    | Tijdelijke opslag | Betalen per uur met AHB | 1-jaarsreserve met AHB (% besparingen) | 3-jaar gereserveerd met AHB (% Besparingen) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $ 0,598/uur            | $0,3528/uur (~41%)                 | $0,2333/uur (~61%)                  |

Op basis van de ontwerpverwijzing (twee clusters van 5 VM's voor het verwerken van 1.000.000 audiobatchverwerking per dag), zijn de hardwarekosten voor één jaar:

> 2 (clusters) * 5 (VM's per cluster) * $ 0,3528/uur * 365 (dagen) * 24 (uur) = $ 31.000/jaar

Bij toewijzing aan fysieke machine is een algemene schatting 1 vCPU = 1 fysieke CPU-kern. In werkelijkheid is 1vCPU krachtiger dan één kern.

On-prem spelen al deze aanvullende factoren een rol:

- Wat voor type de fysieke CPU is en hoeveel kernen er op staan
- Hoeveel CPU's worden samen uitgevoerd op dezelfde doos/computer
- Hoe VM's worden ingesteld
- Hoe hyperthreading/multi-threading wordt gebruikt
- Hoe geheugen wordt gedeeld
- Het besturingssysteem, enzovoort.

Normaal gesproken is de omgeving niet zo goed afgestemd op Azure. Gezien andere overhead, zou ik zeggen dat een veilige schatting 10 fysieke CPU-kernen = 8 Azure vCPU is. Hoewel populaire CPU's slechts acht kernen hebben. Bij on-prem-implementatie zijn de kosten hoger dan bij het gebruik van Azure-VM's. Houd ook rekening met het afschrijvingspercentage.

De servicekosten zijn hetzelfde als die van de onlineservice: $ 1/uur voor spraak-naar-tekst. De kosten voor de Speech-service zijn:

> $ 1 * 1000 * 365 = $365.000

De onderhoudskosten die aan Microsoft worden betaald, zijn afhankelijk van het serviceniveau en de inhoud van de service. Het is een bedrag van $ 29,99/maand voor het basisniveau tot honderdduizenden als het om een on-site service gaat. Een ruw getal is $ 300/uur voor service/onderhoud. Kosten voor personen zijn niet inbegrepen. Andere infrastructuurkosten (zoals opslag, netwerken en load balancers) zijn niet inbegrepen.

<br>
</details>

<details>
<summary>
<b>Waarom ontbreekt interpunctie in de transcriptie?</b>
</summary>

**Antwoord:** De `speech_recognition_language=<YOUR_LANGUAGE>` moet expliciet worden geconfigureerd in de aanvraag als ze de Carbon-client gebruiken.

Bijvoorbeeld:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Dit is de uitvoer:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Kan ik een aangepast akoestisch model en taalmodel gebruiken met een Speech-container?</b>
</summary>

We kunnen momenteel slechts één model-id doorgeven, ofwel een aangepast taalmodel of een aangepast akoestisch model.

**Antwoord:** Er is besloten *om niet* tegelijkertijd akoestische en taalmodellen te ondersteunen. Dit blijft van kracht totdat er een uniforme id wordt gemaakt om API-onderbrekingen te verminderen. Helaas wordt dit op dit moment niet ondersteund.

<br>
</details>

<details>
<summary>
<b>Kunt u deze fouten uitleggen vanuit de aangepaste container voor spraak-naar-tekst?</b>
</summary>

**Fout 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Antwoord 1:** Als u traint met het meest recente aangepaste model, wordt dat momenteel niet ondersteund. Als u traint met een oudere versie, is het mogelijk om te gebruiken. Er wordt nog gewerkt aan de ondersteuning van de nieuwste versies.

In wezen bieden de aangepaste containers geen ondersteuning voor Akoestische modellen op basis van Stageide of ONNX (dit is de standaardinstelling in de portal voor aangepaste training). Dit komt doordat aangepaste modellen niet zijn versleuteld en we onnx-modellen echter niet beschikbaar willen maken. taalmodellen zijn prima. De klant moet expliciet een ouder niet-ONNX-model selecteren voor aangepaste training. De nauwkeurigheid wordt niet beïnvloed. De modelgrootte kan groter zijn (met 100 MB).

> Ondersteuningsmodel > 20190220 (v4.5 Unified)

**Fout 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Antwoord 2:** U moet de juiste spraaknaam in de aanvraag verstrekken. Dit is een gevalgevoelige naam. Raadpleeg de volledige servicenaamtoewijzing.

**Fout 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Antwoord 3:** U wilt een Speech-resource maken, niet een Cognitive Services resource.


<br>
</details>

<details>
<summary>
<b>Welke API-protocollen worden ondersteund, REST of WS?</b>
</summary>

**Antwoord:** Voor containers voor spraak-naar-tekst en aangepaste spraak-naar-tekst ondersteunen we momenteel alleen het protocol op basis van websocket. De SDK biedt alleen ondersteuning voor aanroepen in WS, maar niet voor REST. Er is een plan om REST-ondersteuning toe te voegen, maar op dit moment niet aan ETA. Raadpleeg altijd de officiële documentatie, zie [Eindpunten voor queryvoorspelling.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>

<details>
<summary>
<b>Wordt CentOS ondersteund voor spraakcontainers?</b>
</summary>

**Antwoord:** CentOS 7 wordt nog niet ondersteund door de Python SDK, ook Ubuntu 19.04 wordt niet ondersteund.

Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
- **Windows** - x64 en x86
- **Mac** - macOS X versie 10.12 of hoger
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 op x64

Zie Installatie van Python-platform voor meer informatie over het instellen [van de omgeving.](quickstarts/setup-platform.md?pivots=programming-language-python) Op dit moment is Ubuntu 18.04 de aanbevolen versie.

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik fouten bij het aanroepen van LUIS-voorspellings eindpunten?</b>
</summary>

Ik gebruik de LUIS-container in een IoT Edge implementatie en ik probeer het LUIS-voorspellings-eindpunt aan te roepen vanuit een andere container. De LUIS-container luistert op poort 5001 en de URL die ik gebruik, is dit:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

De fout die ik krijg is:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Ik zie de aanvraag in de LUIS-containerlogboeken en het bericht geeft het volgende weer:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Wat betekent dit? Wat ontbreekt er? Ik volg het voorbeeld voor de Speech-SDK, vanaf [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Het scenario is dat we de audio rechtstreeks vanaf de microfoon van de pc detecteren en proberen de intentie te bepalen op basis van de LUIS-app die we hebben getraind. Het voorbeeld dat ik heb gekoppeld aan doet precies dat. En het werkt goed met de cloudservice van LUIS. Het gebruik van de Speech-SDK lijkt ons te besparen op het maken van een afzonderlijke expliciete aanroep naar de spraak-naar-tekst-API en vervolgens een tweede aanroep naar LUIS.

Ik probeer dus alleen maar over te schakelen van het scenario van het gebruik van LUIS in de cloud naar het gebruik van de LUIS-container. Ik kan me niet voorstellen of de Speech SDK voor de ene SDK werkt, het werkt niet voor de andere.

**Antwoord:** De Speech-SDK mag niet worden gebruikt voor een LUIS-container. Voor het gebruik van de LUIS-container moet de LUIS-SDK of LUIS-REST API worden gebruikt. Speech SDK moet worden gebruikt voor een spraakcontainer.

Een cloud is anders dan een container. Een cloud kan bestaan uit meerdere geaggregeerde containers (ook wel microservices genoemd). Er is dus een LUIS-container en vervolgens is er een Speech-container: twee afzonderlijke containers. De Speech-container doet alleen spraak. De LUIS-container doet alleen LUIS. In de cloud, omdat beide containers bekend zijn om te worden geïmplementeerd en het slechte prestaties zijn voor een externe client om naar de cloud te gaan, spraak uit te geven, terug te gaan, vervolgens weer naar de cloud te gaan en LUIS te gebruiken, bieden we een functie waarmee de client naar Spraak kan gaan, in de cloud kan blijven, naar LUIS kan gaan en vervolgens terug kan naar de client. Dus zelfs in dit scenario gaat de Speech SDK naar de Speech-cloudcontainer met audio, waarna de spraakcloudcontainer met tekst spreekt met de LUIS-cloudcontainer. De LUIS-container heeft geen concept van het accepteren van audio (het zou niet logisch zijn als LUIS-container streaming audio accepteert - LUIS is een op tekst gebaseerde service). Met on-premises hebben we er niet zeker van dat onze klant beide containers heeft geïmplementeerd. We gaan er niet van uit om te orkestreren tussen containers in de locatie van onze klanten. Als beide containers on-premises zijn geïmplementeerd, omdat ze meer lokaal zijn voor de client, is het niet een last om eerst naar de SR te gaan, terug te gaan naar de client en de klant vervolgens die tekst te laten nemen en naar LUIS te gaan.

<br>
</details>

<details>
<summary>
<b>Waarom krijgen we fouten met macOS, de Speech-container en de Python SDK?</b>
</summary>

Wanneer we een *WAV-bestand* verzenden dat moet worden getranscriscribeerd, wordt het resultaat als volgt:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

We weten dat de websocket correct is ingesteld.

**Antwoord:** Als dat het geval is, bekijkt u [dit GitHub-probleem.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) We hebben een oplossing die hier [wordt voorgesteld.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

Carbon heeft dit opgelost bij versie 1.8.


<br>
</details>

<details>
<summary>
<b>Wat zijn de verschillen in de eindpunten van de Speech-container?</b>
</summary>

Kunt u de volgende metrische testgegevens invullen, waaronder welke functies u wilt testen en hoe u de SDK en REST API's kunt testen? Met name verschillen in 'interactief' en 'gesprek', die ik niet in een bestaand document/voorbeeld heb gezien.

| Eindpunt                                                | Functionele test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Tekst synthetiseren (tekst-naar-spraak)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-prem dictation v1 websocket endpoint        | Ja | Nee       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Het Cognitive Services on-prem interactive v1 websocket-eindpunt  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Het websocket-eindpunt cognitive services on-prem conversation v1 |     |          |

**Antwoord:** Dit is een samenvoeging van:
- Mensen die het dicteer-eindpunt voor containers proberen te gebruiken (ik weet niet hoe ze die URL hebben gekregen)
- Het eindpunt<sup>van één partij</sup> is het eindpunt in een container.
- Het eindpunt van<sup>de 1e</sup> partij retourneert speech.fragment-berichten in plaats van de berichten die de eindpunten van het derde deel retourneren voor het `speech.hypothesis` dicteer-eindpunt.<sup></sup>
- De Carbon-quickstarts gebruiken allemaal `RecognizeOnce` (interactieve modus)
- Carbon heeft een assertie dat voor berichten waarvoor ze zijn `speech.fragment` vereist, niet worden geretourneerd in de interactieve modus.
- Carbon met de assertie brand in release-builds (het proces wordt geseed).

De tijdelijke oplossing is om over te schakelen naar continue herkenning in uw code of (sneller) verbinding te maken met de interactieve of continue eindpunten in de container.
Stel voor uw code het eindpunt in op `host:port` /speech/recognition/interactive/cognitiveservices/v1

Zie Spraakmodi - zie hieronder voor de verschillende modi:

## <a name="speech-modes---interactive-conversation-dictation"></a>Spraakmodi : interactief, gesprek, dicteren

[!INCLUDE [speech-modes](includes/speech-modes.md)]

De juiste oplossing wordt beschikbaar gemaakt met SDK 1.8, die on-prem ondersteuning biedt (kiest het juiste eindpunt, zodat we niet slechter zijn dan onlineservice). In de tussentijd is er een voorbeeld voor continue herkenning. Waarom wijzen we er niet naar?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Welke modus moet ik gebruiken voor verschillende audiobestanden?</b>
</summary>

**Antwoord:** Hier is een [quickstart met python](./get-started-speech-to-text.md?pivots=programming-language-python). U vindt de andere talen die zijn gekoppeld op de docs-site.

Ter verduidelijking van de interactieve, gespreks- en dicteerspeling; Dit is een geavanceerde manier om de specifieke manier op te geven waarop onze service de spraakaanvraag verwerkt. Helaas moeten we voor de on-prem-containers de volledige URI opgeven (omdat deze de lokale computer bevat), zodat deze informatie uit de abstractie is gelekt. We werken samen met het SDK-team om dit in de toekomst bruikbaarder te maken.

<br>
</details>

<details>
<summary>
<b>Hoe kunnen we een ruwe meting van transacties per seconde/kern benchmarken?</b>
</summary>

**Antwoord:** Hier zijn enkele van de ruwe getallen die u kunt verwachten van het bestaande model (zal voor een betere wijziging in het model dat we in ga verzenden):

- Voor bestanden wordt de beperking in de speech-SDK op 2x. De eerste vijf seconden audio worden niet beperkt. Decoder kan ongeveer 3x in realtime worden gedaan. Hiervoor is het totale CPU-gebruik dicht bij 2 kernen voor één herkenning.
- Voor mic is dit 1x realtime. Het totale gebruik moet ongeveer 1 kern zijn voor één herkenning.

Dit kan allemaal worden geverifieerd vanuit de docker-logboeken. We dumpen de regel eigenlijk met sessie- en woordgroeps-/utterancestatistieken, en die de RTF-nummers bevat.

<br>
</details>

<details>
<summary>
<b>Hoe kan ik meerdere containers uitvoeren op dezelfde host?</b>
</summary>

In het document staat dat u een andere poort wilt gebruiken, wat ik wel doe, maar dat de LUIS-container nog steeds luistert op poort 5000?

**Antwoord:** Probeer `-p <outside_unique_port>:5000` . Bijvoorbeeld `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technische vragen

<details>
<summary>
<b>Hoe kan ik niet-batch-API's krijgen om audio &lt; 15 seconden lang te verwerken?</b>
</summary>

**Antwoord:** `RecognizeOnce()` In de interactieve modus worden slechts maximaal 15 seconden audio verwerkt, omdat de modus is bedoeld voor spraakopdracht, waarbij utterances naar verwachting kort zijn. Als u gebruikt `StartContinuousRecognition()` voor dicteren of gesprekken, is er geen limiet van 15 seconden.


<br>
</details>

<details>
<summary>
<b>Wat zijn de aanbevolen resources, CPU en RAM; voor 50 gelijktijdige aanvragen?</b>
</summary>

Hoeveel gelijktijdige aanvragen worden door een 4-core, 4 GB RAM-geheugen verwerkt? Als we bijvoorbeeld 50 gelijktijdige aanvragen moeten verwerken, hoeveel core en RAM wordt dan aanbevolen?

**Antwoord:** In realtime 8 met onze meest recente , dus raden we u aan meer `en-US` Docker-containers dan 6 gelijktijdige aanvragen te gebruiken. Het wordt sneller dan 16 kernen en het knooppunt is niet-uniform geheugentoegang (NUMA). In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Speech-container beschreven.

# <a name="speech-to-text"></a>[Spraak-naar-tekst](#tab/stt)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Spraak naar tekst | 2 kernen, 2 GB geheugen | 4 kernen, 4 GB geheugen |

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Aangepaste spraak-naar-tekst | 2 kernen, 2 GB geheugen | 4 kernen, 4 GB geheugen |

# <a name="text-to-speech"></a>[Text-to-speech](#tab/tts)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Tekst naar spraak | 1 kern, 2 GB geheugen | 2 kernen, 3 GB geheugen |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst naar spraak](#tab/ctts)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Aangepaste tekst naar spraak | 1 kern, 2 GB geheugen | 2 kernen, 3 GB geheugen |

***

- Elke kern moet ten minste 2,6 GHz of sneller zijn.
- Voor bestanden vindt de beperking plaats in de Speech SDK, bij 2x (de eerste 5 seconden wordt de audio niet beperkt).
- De decoder kan ongeveer 2-3 keer in realtime worden gedaan. Hiervoor is het totale CPU-gebruik dicht bij twee kernen voor één herkenning. Daarom raden we niet aan om meer dan twee actieve verbindingen per container-instantie te bewaren. De extreme kant zou zijn om ongeveer 10 decoders in 2x realtime te zetten in een computer met acht kernen, zoals `DS13_V2` . Voor containerversie 1.3 en hoger is er een -param die u kunt `DECODER_MAX_COUNT=20` instellen.
- Voor de microfoon is dit 1x realtime. Het algehele gebruik moet ongeveer één kern zijn voor één herkenning.

Houd rekening met het totale aantal uren audio dat u hebt. Als het aantal groot is om de betrouwbaarheid/beschikbaarheid te verbeteren, raden we u aan meer exemplaren van containers uit te werken, ofwel in één vak of op meerdere vakken, achter een load balancer. Orchestration kan worden uitgevoerd met Behulp van Kubernetes (K8S) en Helm, of met Docker Compose.

Om bijvoorbeeld 1000 uur/24 uur af te handelen, hebben we geprobeerd 3-4 VM's in te stellen, met 10 exemplaren/decoders per VM.

<br>
</details>

<details>
<summary>
<b>Ondersteunt de spraakcontainer leestekens?</b>
</summary>

**Antwoord:** Er is een ITN (capitalization) beschikbaar in de on-prem-container. Interpunctie is taalafhankelijk en wordt niet ondersteund voor sommige talen, waaronder Chinees en Japans.

We *hebben* impliciete en eenvoudige leestekens voor de bestaande containers, maar dit is `off` standaard. Dit betekent dat u het teken in uw voorbeeld kunt `.` krijgen, maar niet het `。` teken. Als u deze impliciete logica wilt inschakelen, ziet u hier een voorbeeld van hoe u dit in Python doet met behulp van onze Speech SDK (dit zou in andere talen vergelijkbaar zijn):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik 404-fouten bij het plaatsen van gegevens in een container voor spraak-naar-tekst?</b>
</summary>

Hier is een voorbeeld van HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Antwoord:** We bieden geen ondersteuning REST API in een van de containers voor spraak-naar-tekst. WebSockets wordt alleen ondersteund via de Speech SDK. Raadpleeg altijd de officiële documentatie, zie [Eindpunten voor queryvoorspelling.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>


<details>
<summary>
<b> Waarom wordt de container uitgevoerd als een niet-hoofdgebruiker? Welke problemen kunnen hierdoor optreden?</b>
</summary>

**Antwoord:** Houd er rekening mee dat de standaardgebruiker in de container een niet-hoofdgebruiker is. Dit biedt bescherming tegen processen die de container escapen en geëscaleerde machtigingen verkrijgen op het host-knooppunt. Standaard doen sommige platforms, zoals het OpenShift Container Platform, dit al door containers uit te werken met behulp van een willekeurig toegewezen gebruikers-id. Voor deze platformen moet de niet-hoofdgebruiker machtigingen hebben om te schrijven naar een extern toe te passen volume dat schrijfrechten vereist. Bijvoorbeeld een map voor logboekregistratie of een aangepaste map voor het downloaden van modellen.
<br>
</details>

<details>
<summary>
<b>Waarom krijg ik deze fout wanneer ik de service spraak-naar-tekst gebruik?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Antwoord:** Dit gebeurt meestal wanneer u de audio sneller invoert dan de spraakherkenningscontainer kan gebruiken. Clientbuffers raken vol en de annulering wordt geactiveerd. U moet de gelijktijdigheid en de RTF bepalen waarop u de audio verzendt.

<br>
</details>

<details>
<summary>
<b>Kunt u deze tekst-naar-spraak-containerfouten uit de C++-voorbeelden uitleggen?</b>
</summary>

**Antwoord:** Als de containerversie ouder is dan 1.3, moet deze code worden gebruikt:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Oudere containers hebben niet het vereiste eindpunt voor Carbon om met de API te `FromHost` kunnen werken. Als de containers die worden gebruikt voor versie 1.3, moet deze code worden gebruikt:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Hieronder vindt u een voorbeeld van het gebruik van de `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 De `SetSpeechSynthesisVoiceName` functie wordt aangeroepen omdat voor de containers met een bijgewerkte engine voor tekst-naar-spraak de spraaknaam is vereist.

<br>
</details>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](speech-container-howto.md)
