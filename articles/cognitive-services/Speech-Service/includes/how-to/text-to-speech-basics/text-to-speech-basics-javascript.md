---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 4d228388d951314b03ecd950052f76a20b6e4166
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108884"
---
In deze quickstart maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [JavaScript-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service resource hebt. Als u geen account en resource hebt, [kunt u de spraak service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech-SDK installeren voor JavaScript</a>. Gebruik de volgende instructies, afhankelijk van uw platform:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser </a>

Afhankelijk van de doelomgeving gebruikt u daarnaast een van de volgende opties:

# <a name="script"></a>[script](#tab/script)

Down load en pak de <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK voor Java script</a> - *microsoft.cognitiveservices.speech.sdk.bundle.js* bestand uit en plaats het in een map die toegankelijk is voor uw HTML-bestand.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Als u een webbrowser als doel hebt en de tag `<script>` gebruikt, is het voorvoegsel `sdk` niet nodig. Het voorvoegsel `sdk` is een alias die wordt gebruikt om de `require`-module te benoemen.

# <a name="import"></a>[import](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Zie <a href="https://javascript.info/import-export" target="_blank">export en import</a> voor meer informatie over `import`.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Zie <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Wat is require?</a> voor meer informatie over `require`.

---


## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) maken. Deze klasse bevat informatie over uw resource, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) kunt initialiseren:

* Met een resource: Geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voor beeld maakt u een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) met een resource sleutel en-regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free). U schrijft ook wat eenvoudige standaardcode voor gebruik in de rest van dit artikel. U gaat de code voor verschillende aanpassingen wijzigen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u een [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer)-object, waarmee tekst-naar-spraakconversies en uitvoer naar luidsprekers, bestanden of andere uitvoerstromen worden uitgevoerd. De [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) accepteert als parameters het [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)-object dat in de vorige stap is gemaakt en tevens een [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig)-object dat aangeeft hoe uitvoerresultaten moeten worden verwerkt.

Eerst maakt u een `AudioConfig` om de uitvoer automatisch naar een `.wav`-bestand te schrijven met behulp van de statische functie `fromAudioFileOutput()`.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Vervolgens instantieert u een `SpeechSynthesizer`, terwijl het `speechConfig`-object en het `audioConfig`-object als parameters worden doorgegeven. Daarna is het uitvoeren van spraaksynthese en het schrijven naar een bestand net zo eenvoudig als het uitvoeren van `speakTextAsync()` met een regel tekst. De resulterende callback is een goede plaats om `synthesizer.close()` aan te roepen, omdat deze aanroep is vereist om de synthese goed te laten functioneren.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Voer het programma uit. Er wordt een gesynthetiseerd `.wav`-bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het eenvoudigste gebruik, maar hierna gaat u de uitvoer aanpassen en de uitvoerrespons verwerken als een in-memory stroom om met aangepaste scenario's te kunnen werken.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar de uitvoer van de luidspreker

In sommige gevallen kunt u gesynthetiseerde spraak rechtstreeks naar een luidspreker uitvoeren. Als u dit wilt doen, moet u `AudioConfig` instantiëren met behulp van de statische functie `fromDefaultSpeakerOutput()`. Hiermee wordt de uitvoer naar het huidige, actieve uitvoerapparaat gestuurd.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-memory stroom

Voor veel scenario's in het ontwikkelen van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens als een in-memory stroom nodig, in plaats dat u rechtstreeks naar een bestand gaat schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* Vat de resulterende bytematrix samen als een doorzoekbare stroom voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, schrijf aangepaste `.wav`-headers, enzovoort.

U kunt deze wijziging eenvoudig met behulp van het voorgaande voorbeeld uitvoeren. Verwijder eerst het `AudioConfig`-blok, omdat u het uitvoergedrag vanaf dit punt handmatig gaat beheren voor een betere controle. Geef vervolgens `undefined` door voor de `AudioConfig` in de `SpeechSynthesizer`-constructor.

> [!NOTE]
> Als `undefined` voor de `AudioConfig` wordt doorgegeven in plaats van dat deze wordt weggelaten, zoals in het bovenstaande voorbeeld met de luidspreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoerapparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult)-variabele. De `SpeechSynthesisResult.audioData` eigenschap retourneert een `ArrayBuffer` van de uitvoer gegevens, het standaard type browser stroom. Converteer voor de server code de arrayBuffer naar een buffer stroom.

De volgende code werkt voor code aan de client zijde.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Hierna kunt u aangepast gedrag implementeren met behulp van het resulterende `ArrayBuffer`-object. De ArrayBuffer is een gemeen schappelijk type dat in een browser kan worden ontvangen en vanuit deze indeling kan worden afgespeeld.

Als u in plaats van een ArrayBuffer de gegevens als een stroom wilt gebruiken, moet u voor elke server code het object converteren naar een stroom.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u kenmerken van audio-uitvoer kunt aanpassen, zoals:

* Audiobestandstype
* Samplefrequentie
* Bitdiepte

Als u de audio-indeling wilt wijzigen, gebruikt u de eigenschap `speechSynthesisOutputFormat` voor het `SpeechConfig`-object. Deze eigenschap verwacht een `enum` van het type [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), dat u gebruikt om de uitvoerindeling te selecteren. Zie de naslagdocumenten voor een [lijst met audio-indelingen](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) die beschikbaar zijn.

Voor verschillende bestandstypen zijn er diverse opties mogelijk, afhankelijk van uw vereisten. Houd er rekening mee dat raw-indelingen als `Raw24Khz16BitMonoPcm` geen audio-headers bevatten. Gebruik raw-indelingen alleen als u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers handmatig te bouwen op basis van bitdiepte, samplefrequentie, aantal kanalen, enzovoort.

In dit voorbeeld geeft u een RIFF-indeling met hoge kwaliteit `Riff24Khz16BitMonoPcm` op door `speechSynthesisOutputFormat` in te stellen voor het `SpeechConfig`-object. Net als in het voorbeeld in de vorige sectie, kunt u de `ArrayBuffer`-audiogegevens ophalen en ermee werken.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Als u het programma opnieuw uitvoert, wordt een `.wav`-bestand naar het opgegeven pad geschreven.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML gebruiken om spraakkenmerken aan te passen

Met SSML (Speech Synthesis Markup Language) kunt u de toonhoogte, de uitspraak, de spreeksnelheid, het volume en meer van tekst-naar-spraakuitvoer nauwkeuriger instellen door uw aanvragen vanaf een XML-schema te verzenden. In deze sectie ziet u een voor beeld van het wijzigen van de spraak, maar voor een meer gedetailleerde hand leiding raadpleegt u het [artikel SSMLe How-to](../../../speech-synthesis-markup.md). 

Als u SSML voor aanpassing wilt gaan gebruiken, brengt u een eenvoudige wijziging aan die de stem wisselt.
Maak eerst een nieuw XML-bestand voor de SSML-configuratie in de hoofdmap van het project, in dit voorbeeld `ssml.xml`. Het hoofdelement is altijd `<speak>`, en wanneer u de tekst in een `<voice>`-element verpakt, kunt u de stem wijzigen met behulp van de parameter `name`. Bekijk de [volledige lijst](../../../language-support.md#neural-voices) met ondersteunde **Neural** stemmen. 

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Vervolgens moet u de aanvraag voor spraaksynthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de functie `speakTextAsync()` gebruikt u `speakSsmlAsync()`. Deze functie verwacht een XML-tekenreeks, dus eerst maakt u een functie om een XML-bestand te laden en als een tekenreeks te retourneren.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Zie <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js-bestandssysteem</a> voor meer informatie over `readFileSync`. Hier is het resultaatobject precies hetzelfde als in de vorige voorbeelden.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

> [!NOTE]
> Als u de stem wilt wijzigen zonder SSML te gebruiken, kunt u de eigenschap instellen op de met `SpeechConfig` behulp van `SpeechConfig.speechSynthesisVoiceName = "en-US-AriaNeural";`

## <a name="get-facial-pose-events"></a>Gebeurtenissen voor gezichts pose ophalen

Speech is een goede manier om de animatie van gezichts expressies te testen.
Vaak worden [visemes](../../../how-to-speech-synthesis-viseme.md) gebruikt om de sleutel in waargenomen spraak te vertegenwoordigen, zoals de positie van de lippen, jaw en tong bij het produceren van een bepaalde foneem.
U kunt de viseme-gebeurtenis in Speech SDK abonneren.
Vervolgens kunt u viseme-gebeurtenissen Toep assen om het gezicht van een teken te animeren wanneer spraak geluid wordt afgespeeld.
Meer informatie [over het ophalen van viseme-gebeurtenissen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
