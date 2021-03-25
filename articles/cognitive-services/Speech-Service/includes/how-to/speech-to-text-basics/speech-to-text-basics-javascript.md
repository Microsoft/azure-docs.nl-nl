---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 7649f9c13db1a10bdb48d19f1019ec3b31244f45
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104378"
---
Een van de belangrijkste functies van de Speech-service is de mogelijkheid om menselijke spraak te herkennen en te transcriberen (ook wel spraak-naar-tekst genoemd). In deze quickstart leert u meer over het gebruik van de Speech-SDK in uw apps en producten om spraak-naar-tekst-conversie van hoge kwaliteit uit te voeren.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [JavaScript-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) op GitHub als u direct naar voorbeeldcode wilt gaan.

U kunt ook het voor [beeld reageren](https://github.com/Azure-Samples/AzureSpeechReactSample) raadplegen om te leren hoe u de Speech SDK kunt gebruiken in een op een browser gebaseerde omgeving.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren voor Node.js. Als u alleen de naam van het pakket wilt installeren, voert u uit `npm install microsoft-cognitiveservices-speech-sdk` . Zie het artikel [aan de slag](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs) voor instructies voor de begeleide installatie.

Gebruik de volgende `require` instructie om de SDK te importeren.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require`Zie de [documentatie vereisen](https://nodejs.org/en/knowledge/getting-started/what-is-require/)voor meer informatie.

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken. Maak een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) met behulp van uw sleutel en regio. Zie de pagina [Sleutels en regio zoeken](../../../overview.md#find-keys-and-region) om uw sleutel-regiopaar te zoeken.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Er zijn een paar andere manieren waarop u een [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) kunt initialiseren:

* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

## <a name="recognize-from-microphone-browser-only"></a>Herkennen met de microfoon (alleen browser)

Het herkennen van spraak van een microfoon wordt **niet ondersteund in Node.js** en wordt alleen ondersteund in een op een browser gebaseerde java script-omgeving. Zie het voor [beeld reageren](https://github.com/Azure-Samples/AzureSpeechReactSample) op github voor een overzicht [van de spraak-naar-tekst van de implementatie van de microfoon](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Als u een *specifiek* audio-invoerapparaat wilt gebruiken, moet u de apparaat-id opgeven in de `AudioConfig`. Meer informatie over [het ophalen van de apparaat-id](../../../how-to-select-audio-input-devices.md) voor het audio-invoerapparaat.

## <a name="recognize-from-file"></a>Herkennen vanuit bestand 

Als u spraak wilt herkennen vanuit een audio bestand, maakt `AudioConfig` u een using `fromWavFileInput()` waarmee een object wordt geaccepteerd `Buffer` . Initialiseer vervolgens een [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest), waarbij uw `audioConfig` en `speechConfig` worden doorgegeven.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Herkennen vanuit de gegevensstroom in het geheugen

In veel gevallen is het waarschijnlijk dat uw audio gegevens afkomstig zijn uit de Blob-opslag of dat deze al in het geheugen aanwezig zijn als een [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) of vergelijk bare onbewerkte gegevens structuur. De volgende code:

* Hiermee maakt u een push-stream met `createPushStream()` .
* Leest een `.wav` bestand dat `fs.createReadStream` wordt gebruikt voor demonstratie doeleinden, maar als u al audio gegevens in een hebt `ArrayBuffer` , kunt u direct door gaan om de inhoud naar de invoer stroom te schrijven.
* Hiermee maakt u een audio configuratie met behulp van de push-stream.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

Als u een push-stream gebruikt als invoer, wordt ervan uitgegaan dat de audiogegevens een onbewerkte PCM zijn, zoals het overslaan van headers.
De API werkt in bepaalde gevallen nog steeds als de header niet is overgeslagen, maar voor de beste resultaten kunt u het implementeren van logica voor het lezen van de headers overwegen, zodat de `fs` begint bij het *starten van de audiogegevens*.

## <a name="error-handling"></a>Foutafhandeling

In de vorige voorbeelden wordt de herkende tekst gewoon opgehaald uit `result.text`, maar om fouten en andere antwoorden af te handelen, moet u code schrijven om het resultaat te verwerken. Met de volgende code wordt de [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason)-eigenschap geëvalueerd en:

* Het herkenningsresultaat afdrukken: `ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `ResultReason.NoMatch`
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Continue herkenning

In de vorige voorbeelden wordt een eenmalige herkenning gebruikt, die één uiting herkent. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.

Continue herkenning wordt daarentegen gebruikt wanneer u wilt **bepalen** wanneer het herkennen moet stoppen. U moet zich abonneren op de gebeurtenissen `Recognizing`, `Recognized`en `Canceled` om de herkenningsresultaten op te halen. Als u de herkenning wilt stoppen, moet u [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync)aanroepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Begin met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Abonneer u vervolgens op de gebeurtenissen die vanuit de [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) worden verzonden.

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Als alles is ingesteld, roept u [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) aan om te beginnen met de herkenning.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--)-methode op uw [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Italiaans. Zoek uw [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) in uw code en voeg deze regel daar direct onder toe.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

De eigenschap [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) verwacht een indelingstekenreeks voor taal-landinstellingen. U kunt in de lijst met ondersteunde [Landinstellingen en talen](../../../language-support.md) een willekeurige waarde opgeven in de kolom **Landinstelling**.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Als u een lijst met zinsdelen opgeeft, verbetert u de nauwkeurigheid van spraakherkenning.

Als u bijvoorbeeld als mogelijke uitspraken een opdracht 'Move to' en een mogelijke bestemming 'Ward' hebt, kunt u een vermelding van 'Move to Ward' toevoegen. Door een woordgroep toe te voegen, wordt de kans groter dat een geluidsfragment wordt herkend als 'Move to Ward' in plaats van 'Move toward'.

Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een woordgroepenlijst gebruikt om herkenning van woorden en zinsdelen in de lijst te verbeteren, zelfs wanneer vermeldingen in het midden van een utterance voorkomen. 

> [!IMPORTANT]
> De functie Woordgroepenlijst is beschikbaar in de volgende talen: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-).

Wijzigingen in [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Als u uw frasenlijst wilt wissen:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../custom-speech-overview.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)