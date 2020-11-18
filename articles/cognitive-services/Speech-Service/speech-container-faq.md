---
title: Veelgestelde vragen over Speech-Service containers
titleSuffix: Azure Cognitive Services
description: Installeer en voer spraak containers uit. met spraak naar tekst worden audio stromen naar tekst getranscribeerd in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Tekst-naar-spraak zet invoer tekst om in humane-achtige, gesynthesizerde spraak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e4a6d9180d2a9949cebc40cf30edffac73ef9d0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653535"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Veelgestelde vragen over Speech-Service containers

Wanneer u de speech-service met containers gebruikt, moet u vertrouwen op deze verzameling Veelgestelde vragen voordat u naar ondersteuning gaat. In dit artikel worden vragen van verschillende mate, van algemeen naar technisch, vastgelegd. Als u een antwoord wilt uitbreiden, klikt u op de vraag.

## <a name="general-questions"></a>Algemene vragen

<details>
<summary>
<b>Hoe werken spraak containers en hoe kan ik deze instellen?</b>
</summary>

**Antwoord:** Bij het instellen van het productie cluster zijn er verschillende zaken die u moet overwegen. Ten eerste, het instellen van één taal, meerdere containers op dezelfde computer, mag geen groot probleem zijn. Als u problemen ondervindt, kan dit een hardwareprobleem zijn, dus we moeten eerst de resource bekijken. CPU-en geheugen specificaties.

Neem even de tijd, de `ja-JP` container en het meest recente model. Het akoestische model is het meest veeleisende gedeelte CPU-and, terwijl het taal model het meeste geheugen vereist. Toen we het gebruik hebben gebenchmarkd, neemt het ongeveer 0,6 CPU-kernen in beslag om één spraak-naar-tekst-aanvraag te verwerken wanneer audio in realtime wordt doorgelopen (bijvoorbeeld van de microfoon). Als u audio sneller doorstuurt dan in realtime (zoals bij een bestand), kan dat gebruik dubbele (1,2 x kernen) zijn. Ondertussen is het geheugen dat hieronder wordt weer gegeven, werk geheugen voor het decoderen van spraak. Er wordt *geen* rekening gehouden met de werkelijke volledige grootte van het taal model, dat zich in de bestands cache bevindt. Voor `ja-JP` een extra 2 GB; voor is `en-US` het mogelijk meer (6-7 GB).

Als u een computer hebt waarop het geheugen schaar is en u er meerdere talen mee probeert te implementeren, is het mogelijk dat de bestands cache vol is en dat het besturings systeem wordt uitgevoerd op pagina modellen in en uit. Voor een actief transcriptie kan dat disastrous zijn, en kan dit leiden tot vertragingen en andere gevolgen voor de prestaties.

Bovendien verpakken we uitvoer bare bestanden voor machines met de [AVX2-instructie (Advanced vector extension)](speech-container-howto.md#advanced-vector-extension-support) . Voor een machine waarvoor de AVX512-instructies zijn ingesteld, is het genereren van code voor dat doel vereist en het starten van 10 containers voor 10 talen kan de CPU tijdelijk uitgeput raken. Een bericht zoals dit wordt weer gegeven in de docker-logboeken:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

U kunt het aantal decoders instellen dat u wilt binnen *één* container met behulp van een `DECODER MAX_COUNT` variabele. In principe moeten we beginnen met uw SKU (CPU/geheugen), en we kunnen Voorst Ellen hoe u deze optimaal kunt benutten. Een geweldig start punt verwijst naar de aanbevolen resource specificaties van de host.

<br>
</details>

<details>
<summary>
<b>Kunt u helpen bij het plannen van de capaciteit en de schatting van de kosten voor on-premises spraak-naar-tekst containers?</b>
</summary>

**Antwoord:** Voor container capaciteit in de batch verwerkings modus kan elke decoder 2-3x in realtime verwerken, met twee CPU-kernen voor één herkenning. Het is niet raadzaam om meer dan twee gelijktijdige Recognitions per container exemplaar te bewaren, maar het wordt aangeraden om meer exemplaren van containers te maken voor betrouw baarheid/beschikbaarheids redenen, achter een load balancer.

Hoewel er al een container exemplaar met meer decoders kan worden uitgevoerd. Het is bijvoorbeeld mogelijk dat er op een acht kern machine 7-decoders per container exemplaar worden ingesteld (op meer dan 2x elke), waardoor de 15x-door Voer wordt verkregen. Er is een para meter `DECODER_MAX_COUNT` waarmee u rekening moet houden. In het uitzonderlijke geval ontstaan er problemen met de betrouw baarheid en latentie bij een aanzienlijke toename van de door voer. Voor een microfoon wordt deze in 1x real time. Het totale gebruik moet ongeveer één kern zijn voor één herkenning.

Voor het scenario van het verwerken van 1 K uur per dag in de batch verwerkings modus in een extreem geval kunnen drie Vm's binnen 24 uur worden verwerkt, maar niet gegarandeerd. Voor het afhandelen van piek dagen, failover, update en het bieden van minimale back-ups/BCP raden wij 4-5-computers aan in plaats van 3 per cluster en met 2 + clusters.

Voor hardware gebruiken we standaard Azure VM `DS13_v2` als referentie (elke kern moet 2,6 GHz of beter zijn, waarbij de AVX2-instructiesset is ingeschakeld).

| Exemplaar  | vCPU (s) | RAM    | Tijdelijke opslag | Betalen per gebruik met AHB | 1 jaar reserve met AHB (% besparing) | 3 jaar gereserveerd met AHB (% besparing) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/uur            | $0.3528/uur (~ 41%)                 | $0.2333/uur (~ 61%)                  |

Op basis van de ontwerp verwijzing (twee clusters van vijf Vm's voor het afhandelen van een batch verwerking van 1 K uur per dag), zijn 1 jaar de volgende hardware-kosten:

> 2 (clusters) * 5 (Vm's per cluster) * $0.3528/uur * 365 (dagen) * 24 (uur) = $31K/jaar

Bij toewijzing aan fysieke machine is een algemene schatting 1 vCPU = 1 fysieke CPU-kern. In werkelijkheid is 1vCPU krachtiger dan één kern.

Voor on-premises komen al deze extra factoren in het spel:

- Op welk type de fysieke CPU is en hoeveel kern geheugens er op zijn
- Hoeveel Cpu's er samen worden uitgevoerd op hetzelfde vak/dezelfde computer
- Hoe Vm's worden ingesteld
- Hoe Hyper-Threading/meerdere threads worden gebruikt
- Hoe geheugen wordt gedeeld
- Het besturings systeem, enzovoort.

Normaal gesp roken is het niet zo goed afgestemd als Azure de omgeving. Als er andere overhead wordt overwogen, is een veilige schatting 10 fysieke CPU-kernen = 8 Azure vCPU. Populaire Cpu's hebben echter slechts acht kernen. Bij een on-premises implementatie zijn de kosten hoger dan het gebruik van virtuele machines van Azure. Houd ook rekening met het afschrijvings bedrag.

Service kosten zijn hetzelfde als de online service: $1/uur voor spraak naar tekst. De kosten van de speech-service zijn:

> $1 * 1000 * 365 = $365K

De onderhouds kosten die aan micro soft worden betaald, zijn afhankelijk van het service niveau en de inhoud van de service. Dit is een aantal van $29.99/month voor het basis niveau tot honderd duizenden als de on-site service betrokken is. Een ruw getal is $300/uur voor service/onderhoud. De kosten voor personen zijn niet inbegrepen. Andere kosten voor de infra structuur (zoals opslag, netwerken en load balancers) zijn niet inbegrepen.

<br>
</details>

<details>
<summary>
<b>Waarom ontbreekt er Lees tekens in de transcriptie?</b>
</summary>

**Antwoord:** De `speech_recognition_language=<YOUR_LANGUAGE>` moet expliciet worden geconfigureerd in de aanvraag als deze gebruikmaakt van een Carbon client.

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
<b>Kan ik een aangepast akoestisch model en taal model met spraak container gebruiken?</b>
</summary>

We kunnen momenteel slechts één model-ID, een aangepast taal model of een aangepast geluids model door geven.

**Antwoord:** De *beslissing om zowel akoestische als taal* modellen gelijktijdig te ondersteunen, is uitgevoerd. Dit blijft van kracht totdat een uniforme id is gemaakt om API-onderbrekingen te verminderen. Helaas wordt dit nu niet ondersteund.

<br>
</details>

<details>
<summary>
<b>Kunt u deze fouten uitleggen van de aangepaste spraak-naar-tekst-container?</b>
</summary>

**Fout 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Antwoord 1:** Als u het meest recente aangepaste model gebruikt, wordt dit momenteel niet ondersteund. Als u traint met een oudere versie, moet u deze kunnen gebruiken. Er wordt nog steeds gewerkt aan de ondersteuning van de meest recente versies.

In wezen bieden de aangepaste containers geen ondersteuning voor halide-of ONNX-gebaseerde akoestische modellen (dit is de standaard instelling in de aangepaste trainings Portal). Dit wordt veroorzaakt door aangepaste modellen die niet worden versleuteld en we willen geen ONNX-modellen beschikbaar maken. taal modellen zijn prima. De klant moet expliciet een ouder niet-ONNX-model selecteren voor aangepaste training. De nauw keurigheid wordt niet beïnvloed. De grootte van het model kan groter zijn (met 100 MB).

> Ondersteunings model > 20190220 (v 4.5 Unified)

**Fout 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Antwoord 2:** U moet de juiste spraak naam opgeven in de aanvraag. Dit is hoofdletter gevoelig. Raadpleeg de volledige toewijzing van de service naam. U moet gebruiken `en-US-JessaRUS` , omdat deze `en-US-JessaNeural` nu niet beschikbaar is in container versie van tekst naar spraak.

**Fout 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Antwoord 3:** U reed een spraak bron maken, niet een Cognitive Services bron.


<br>
</details>

<details>
<summary>
<b>Welke API-protocollen worden ondersteund, REST of WS?</b>
</summary>

**Antwoord:** Voor spraak naar tekst-en aangepaste spraak-naar-tekst-containers ondersteunen we momenteel alleen het WebSocket-protocol. De SDK ondersteunt alleen het aanroepen van WS, maar niet REST. Er is een plan om REST-ondersteuning toe te voegen, maar dit is nog niet zo gebeurd. Raadpleeg altijd de officiële documentatie voor meer informatie over de voor [Spelling-eind punten van query's](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Wordt CentOS ondersteund voor spraak containers?</b>
</summary>

**Antwoord:** CentOS 7 wordt nog niet ondersteund door python SDK, maar Ubuntu 19,04 wordt niet ondersteund.

Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
- **Windows** -x64 en x86
- **Mac** -macOS X versie 10,12 of hoger
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 op x64

Zie [python platform Setup](quickstarts/setup-platform.md?pivots=programming-language-python)(Engelstalig) voor meer informatie over het instellen van de omgeving. Voor nu is Ubuntu 18,04 de aanbevolen versie.

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik fouten bij het aanroepen van LUIS-Voorspellings eindpunten?</b>
</summary>

Ik gebruik de LUIS-container in een IoT Edge-implementatie en probeert het LUIS prediction-eind punt vanuit een andere container aan te roepen. De LUIS-container luistert op poort 5001 en de URL die ik gebruik:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

De volgende fout is opgetreden:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Ik zie de aanvraag in de LUIS-container logboeken en het bericht:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Wat betekent dit? Wat ontbreekt er? Ik volg het voor beeld voor de spraak- [SDK.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Het scenario is dat we de audio rechtstreeks van de PC-microfoon detecteren en proberen de intentie te bepalen, op basis van de LUIS-app die we hebben opgeleid. Het voor beeld dat u hebt gekoppeld, heeft precies dat. En werkt goed samen met de LUIS-Cloud service. Door gebruik te maken van de Speech SDK leek het opslaan van een afzonderlijke expliciete aanroep van de spraak-naar-tekst-API en vervolgens een tweede aanroep naar LUIS.

Daarom is alle ik probeer over te stappen van het scenario voor het gebruik van LUIS in de cloud om de LUIS-container te gebruiken. Ik kan niet aan de slag met de spraak-SDK voor een van de andere.

**Antwoord:** De Speech SDK mag niet worden gebruikt voor een LUIS-container. Voor het gebruik van de LUIS-container moet de LUIS-SDK of LUIS REST API worden gebruikt. Speech SDK moet worden gebruikt voor een spraak container.

Een Cloud wijkt af van een container. Een cloud kan bestaan uit meerdere geaggregeerde containers (ook wel micro Services genoemd). Er is dus een LUIS-container en vervolgens is er een spraak container-twee afzonderlijke containers. De spraak container heeft alleen spraak. De LUIS-container heeft alleen LUIS. Omdat beide containers bekend zijn om te worden geïmplementeerd in de Cloud, en de prestaties van een externe client naar de Cloud kunnen gaan, moet u spraak doen, terugkomen en vervolgens naar de Cloud teruggaan en LUIS, we bieden een functie waarmee de client naar spraak kan gaan, in de Cloud gaat. Ga vervolgens terug naar de client. Zelfs in dit scenario gaat de spraak-SDK naar een spraak-Cloud container met audio, en wordt de spraak-Cloud container gesp roken naar LUIS Cloud container met tekst. De LUIS-container heeft geen idee voor het accepteren van audio (het is niet zinvol dat LUIS-container streaming audio accepteert, LUIS is een service op basis van tekst). Met on-premises hebben we niet de zekerheid dat onze klant beide containers heeft geïmplementeerd. als beide containers on-premises zijn geïmplementeerd, is het geen last om de SR eerste, terug naar de client te gaan en de klant vervolgens naar LUIS te gaan. dit doet u door de gebruiker te laten opdoen.

<br>
</details>

<details>
<summary>
<b>Waarom worden er fouten in macOS, de spraak container en de python-SDK opgehaald?</b>
</summary>

Wanneer we een *. WAV* -bestand verzenden dat moet worden getranscribeerd, wordt het resultaat weer opgehaald:

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

We weten dat de WebSocket juist is ingesteld.

**Antwoord:** Als dat het geval is, raadpleegt u [Dit github-probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Er is een tijdelijke oplossing, die [hier wordt voorgesteld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Deze kool is opgelost met versie 1,8.


<br>
</details>

<details>
<summary>
<b>Wat zijn de verschillen in de eind punten van de spraak container?</b>
</summary>

Helpt u bij het invullen van de volgende metrische test gegevens, waaronder welke functies moeten worden getest en hoe u de SDK en REST-Api's kunt testen? Met name verschillen in ' Interactive ' en ' Conversation ', wat ik niet heb zien van het bestaande doc/sample.

| Eindpunt                                                | Functionele test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Tekst van de synthesizer (tekst-naar-spraak)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-premises dicteren v1 WebSocket-eind punt        | Ja | Nee       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Het Cognitive Services on-premises interactieve v1 WebSocket-eind punt  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Het on-premises RIP-WebSocket-eind punt van de cognitieve Services |     |          |

**Antwoord:** Dit is een fusie van:
- Personen die het dicteer eindpunt voor containers proberen, (ik weet niet hoe ze een URL hebben gekregen)
- Het eind punt van<sup>de partij van</sup> de eerste is in een container.
- Het eind punt van<sup>de partij die</sup> spraak. fragmenteert, stuurt berichten in plaats van de `speech.hypothesis` berichten die door de drie eind punten van de<sup>extern bureau blad</sup> -onderdelen worden geretourneerd voor het dicteer eindpunt.
- Alle gebruik van Carbon Quick starts `RecognizeOnce` (interactieve modus)
- Carbon met een bevestiging dat voor `speech.fragment` berichten die worden vereist, niet worden geretourneerd in de interactieve modus.
- Kool waarbij de bevestigingen in de release worden geactiveerd (het proces wordt gedoden).

De tijdelijke oplossing is overschakelen naar het gebruik van doorlopende herkenning in uw code of (sneller) verbinding maken met de interactieve of doorlopende eind punten in de container.
Voor uw code stelt u het eind punt in op `host:port` /Speech/Recognition/Interactive/cognitiveservices/v1

Zie voor de verschillende modi spraak modi-Zie hieronder:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

De juiste oplossing is beschikbaar in SDK 1,8, die on-premises ondersteuning heeft (het juiste eind punt wordt gekozen, dus we zullen niet erger zijn dan online service). In de tussen tijd is er een voor beeld voor doorlopend erkennen, waarom is dat niet het geval?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Welke modus moet ik voor verschillende audio bestanden gebruiken?</b>
</summary>

**Antwoord:** Hier volgt een [Snelstartgids](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)voor het gebruik van python. U kunt de andere talen vinden die zijn gekoppeld aan de docs-site.

Net duidelijk voor de interactieve, conversatie en dictering; Dit is een geavanceerde manier om de specifieke manier op te geven waarop de spraak aanvraag wordt verwerkt door de service. Voor de on-premises containers moeten we de volledige URI opgeven (aangezien deze lokale machine bevat), zodat deze gegevens uit de abstractie worden gelekt. We werken samen met het SDK-team om dit in de toekomst bruikbaarder te maken.

<br>
</details>

<details>
<summary>
<b>Hoe kunnen we een ruwe maat eenheid van trans acties/seconde/kern benchmarken?</b>
</summary>

**Antwoord:** Hier volgen enkele van de ruwe cijfers die u kunt verwachten van het bestaande model (wordt gewijzigd voor een betere grootte in de pagina die we in GA gaan verzenden):

- Voor bestanden is de beperking in de Speech SDK, op 2x. De eerste vijf seconden aan audio worden niet beperkt. Decoder kan ongeveer 3x real time doen. Hiervoor is het totale CPU-gebruik bijna twee kernen voor één herkenning.
- Voor Mic geldt een periode van 1x. Het totale gebruik moet ongeveer 1 kern zijn voor één herkenning.

Dit kan allemaal worden gecontroleerd vanuit de docker-Logboeken. We dumpen de regel met de statistieken voor de sessie en zinsdelen/utterance en bevatten de RTF-nummers.


<br>
</details>

<details>
<summary>
<b>Is het gebruikelijk om audio bestanden te splitsen in chucks voor het gebruik van een spraak container?</b>
</summary>

Mijn huidige plan is om een bestaand audio bestand te maken en te splitsen in tien tweede segmenten en deze via de container te verzenden. Is dat een aanvaardbaar scenario?  Is er een betere manier om grotere audio bestanden met de container te verwerken?

**Antwoord:** U hoeft alleen de spraak-SDK te gebruiken en het bestand te geven. Dit is het juiste effect. Waarom moet het bestand worden gesegmenteerd?


<br>
</details>

<details>
<summary>
<b>Hoe kan ik er meerdere containers op dezelfde host worden uitgevoerd?</b>
</summary>

In het document wordt aangegeven dat er een andere poort beschikbaar is, maar de LUIS-container luistert nog steeds op poort 5000?

**Antwoord:** Probeer het opnieuw `-p <outside_unique_port>:5000` . Bijvoorbeeld `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technische vragen

<details>
<summary>
<b>Hoe kan ik voor komen dat niet-batch-Api's &lt; 15 seconden lang worden afgehandeld?</b>
</summary>

**Antwoord:** `RecognizeOnce()` in de interactieve modus worden alleen Maxi maal 15 seconden audio verwerkt, omdat de modus is bedoeld voor spraak opdrachten waarbij uitingen naar verwachting worden beperkt. Als u `StartContinuousRecognition()` voor dicteer of gesprek gebruikt, is er geen limiet van 15 seconden.


<br>
</details>

<details>
<summary>
<b>Wat zijn de aanbevolen resources, de CPU en het RAM-geheugen. voor 50 gelijktijdige aanvragen?</b>
</summary>

Hoeveel gelijktijdige aanvragen wordt een 4-core, 4 GB RAM-ingang? Als we een voor beeld hebben van 50 gelijktijdige aanvragen, hoeveel kern geheugen en RAM wordt aanbevolen?

**Antwoord:** In realtime, 8 met onze nieuwste `en-US` , raden we u aan om meer docker-containers te gebruiken dan zes gelijktijdige aanvragen. De Crazier wordt groter dan 16 kernen en wordt niet-uniforme NUMA-knoop punt (Non-Uniform Memory Access). In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke spraak container beschreven.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Spraak naar tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="custom-speech-to-text"></a>[Custom Speech-naar-tekst](#tab/cstt)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Custom Speech-naar-tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="text-to-speech"></a>[Tekst-naar-spraak](#tab/tts)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Tekst naar spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Aangepaste tekst-naar-spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

**_

- Elke kern moet ten minste 2,6 GHz of sneller zijn.
- Voor bestanden is de beperking in de Speech SDK, op 2x (eerste 5 seconden aan audio worden niet beperkt).
- De decoder kan twee tot drie keer in realtime worden uitgevoerd. Hiervoor wordt het totale CPU-gebruik dicht bij twee kernen voor één herkenning. Daarom raden we u aan om meer dan twee actieve verbindingen te houden per container exemplaar. De meeste kant is om ongeveer 10 decoders op 2x realtime te plaatsen op een acht kern computer zoals `DS13_V2` . Voor container versie 1,3 en hoger is er een para meter die u kunt proberen in te stellen `DECODER_MAX_COUNT=20` .
- Voor microfoons is deze in 1x real time. Het totale gebruik moet ongeveer één kern zijn voor één herkenning.

Houd rekening met het totale aantal uur aan audio dat u hebt. Als het aantal groot is, om de betrouw baarheid/Beschik baarheid te verbeteren, wordt u aangeraden meer exemplaren van containers uit te voeren, hetzij op één doos of op meerdere vakjes achter een load balancer. Indeling kan worden uitgevoerd met behulp van Kubernetes (K8S) en helm, of met docker opstellen.

Voor een voor beeld: 1000 uur/24 uur, hebben we geprobeerd 3-4 Vm's in te stellen, met 10 instanties/decoders per VM.

<br>
</details>

<details>
<summary>
<b>Ondersteunt de spraak container interpunctie?</b>
</summary>

_ *Antwoord:** er is een hoofdletter gebruik (ITN) beschikbaar in de on-premises container. Interpunctie is taal afhankelijk en wordt niet ondersteund voor sommige talen, waaronder Chinees en Japans.

We *hebben* impliciete en eenvoudige interpunctie ondersteuning voor de bestaande containers, maar dit is `off` standaard. Dat betekent dat u het `.` teken in uw voor beeld kunt ophalen, maar niet het `。` teken. Als u deze impliciete logica wilt inschakelen, volgt u hier een voor beeld van hoe u dit in python kunt doen met behulp van onze spraak-SDK (dit is vergelijkbaar in andere talen):

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
<b>Waarom krijg ik 404 fouten bij het plaatsen van gegevens naar een tekst container?</b>
</summary>

Hier volgt een voor beeld van een HTTP POST:

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

**Antwoord:** REST API in een van de spraak-naar-tekst-container wordt niet ondersteund. we ondersteunen alleen websockets via de Speech SDK. Raadpleeg altijd de officiële documentatie voor meer informatie over de voor [Spelling-eind punten van query's](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Waarom wordt de container uitgevoerd als een niet-hoofd gebruiker? Welke problemen kunnen optreden vanwege dit?</b>
</summary>

**Antwoord:** Houd er rekening mee dat de standaard gebruiker binnen de container een niet-hoofd gebruiker is. Dit biedt beveiliging tegen procedures voor het afleiden van de container en het verkrijgen van geëscaleerd machtigingen op het host-knoop punt. Standaard voeren sommige platforms zoals het open Shift container platform dit al uit door containers uit te voeren met een wille keurig toegewezen gebruikers-ID. Voor deze platforms moet de niet-hoofd gebruiker gemachtigd zijn om te schrijven naar een extern toegewezen volume waarvoor schrijf bewerkingen vereist zijn. Een voor beeld van een map voor logboek registratie of een aangepaste downloadmap voor het model.
<br>
</details>

<details>
<summary>
<b>Waarom krijg ik deze fout bij het gebruik van de service voor spraak naar tekst?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Antwoord:** Dit gebeurt meestal wanneer u de audio sneller infeedt dan de spraakherkennings container kan doen. De client buffers worden opgevuld en de annulering wordt geactiveerd. U moet de gelijktijdigheid en de RTF-indeling beheren waarmee u de audio verzendt.

<br>
</details>

<details>
<summary>
<b>Kunt u deze tekst-naar-spraak-container fouten in de C++-voor beelden uitleggen?</b>
</summary>

**Antwoord:** Als de container versie ouder is dan 1,3, moet deze code worden gebruikt:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Oudere containers hebben niet het vereiste eind punt voor Carbon om met de `FromHost` API te werken. Als de containers voor versie 1,3 worden gebruikt, moet deze code worden gebruikt:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Hieronder ziet u een voor beeld van het gebruik van de `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 De `SetSpeechSynthesisVoiceName` functie wordt aangeroepen omdat voor de containers met een bijgewerkte tekst-naar-spraak-engine de naam van de spraak nodig is.

<br>
</details>

<details>
<summary>
<b>Hoe kan ik v 1.7 van de Speech SDK met een spraak container gebruiken?</b>
</summary>

**Antwoord:** Er zijn drie eind punten in de spraak container voor verschillende gebruiks methoden, die als spraak modi worden gedefinieerd. Zie hieronder:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Ze zijn voor verschillende doel einden en worden anders gebruikt.

Python-voor [beelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Zie voor eenmalige herkenning (interactieve modus) met een aangepast eind punt (dat wil zeggen, `SpeechConfig` met een para meter van een eind punt) `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- Zie voor continue herkenning (conversatie modus) en pas een aangepast eind punt te gebruiken als hierboven `speech_recognize_continuous_from_file()` .
- Als u in voor beelden zoals hierboven een dicteer functie wilt inschakelen (alleen als u deze echt nodig hebt), voegt u code toe nadat u deze hebt gemaakt `speech_config` `speech_config.enable_dictation()` .

In C# moet u de functie aanroepen `SpeechConfig.EnableDictation()` .

### <a name="fromendpoint-apis"></a>`FromEndpoint` APIs
| Taal | API-details |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Javascript | Wordt momenteel niet ondersteund en ook niet gepland. |

<br>
</details>

<details>
<summary>
<b>Hoe kan ik v 1.8 van de Speech SDK gebruiken met een spraak container?</b>
</summary>

**Antwoord:** Er is een nieuwe `FromHost` API. Hiermee worden bestaande Api's niet vervangen of gewijzigd. Er wordt alleen een alternatieve manier toegevoegd om een spraak configuratie te maken met behulp van een aangepaste host.

### <a name="fromhost-apis"></a>`FromHost` APIs

| Taal | API-details |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Javascript | Momenteel niet ondersteund |

> Para meters: host (verplicht), abonnements sleutel (optioneel, als u de service zonder IT kunt gebruiken).

De indeling voor de host is `protocol://hostname:port` `:port` optioneel (zie hieronder):
- Als de container lokaal wordt uitgevoerd, is de hostnaam `localhost` .
- Als de container wordt uitgevoerd op een externe server, gebruikt u de hostnaam of het IPv4-adres van die server.

Voor beelden van host-para meters voor spraak naar tekst:
- `ws://localhost:5000` -niet-beveiligde verbinding met een lokale container via poort 5000
- `ws://some.host.com:5000` -niet-beveiligde verbinding met een container die wordt uitgevoerd op een externe server

Python-voor beelden van bovenstaande, maar gebruik de `host` para meter in plaats van `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](speech-container-howto.md)
