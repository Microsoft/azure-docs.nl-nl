---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: b43c0da3303b4cdfd4941f9d76b663f8089a1417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104229"
---
Een van de belangrijkste functies van de speech-service is de mogelijkheid om menselijke spraak te herkennen en te vertalen naar andere talen. In deze Snelstartgids leert u hoe u de Speech SDK in uw apps en producten kunt gebruiken om spraak vertalingen van hoge kwaliteit uit te voeren. In deze Quick Start vindt u onderwerpen, waaronder:

* Omzetten van spraak naar tekst
* Spraak omzetten naar meerdere doel talen
* Direct omzetten van spraak naar spraak uitvoeren

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [JavaScript-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

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
import * from "microsoft-cognitiveservices-speech-sdk";
```

Zie <a href="https://javascript.info/import-export" target="_blank">export en import</a> voor meer informatie over `import`.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Zie <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Wat is require?</a> voor meer informatie over `require`.

---

## <a name="create-a-translation-configuration"></a>Een Vertaal configuratie maken

Als u de Vertaal service wilt aanroepen met behulp van de Speech SDK, moet u een maken [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.
Er zijn een paar manieren waarop u een [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) wordt gemaakt met behulp van een sleutel en regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Een vertaler initialiseren

Nadat u een [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) hebt gemaakt, is de volgende stap het initialiseren van een [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer). Wanneer u een [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) initialiseert, moet u er uw `speechTranslationConfig` aan doorgeven. Dit biedt de referenties die de Vertaal service nodig heeft om uw aanvraag te valideren.

Als u via de standaard microfoon van uw apparaat omgaat met spraak, ziet u het [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) volgende:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Als u het audio-invoerapparaat wilt opgeven, moet u een [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) maken en de parameter `audioConfig` opgeven bij het initialiseren van uw [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer).

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).
Verwijs als volgt naar het `AudioConfig`-object:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Dit kan echter alleen worden gedaan wanneer het doel **node.js** is en wanneer u een [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) maakt, in plaats van `fromDefaultMicrophoneInput` aan te roepen, roept u `fromWavFileOutput` aan en geeft u de parameter `filename` door.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Spraak vertalen

De [klasse TranslationRecognizer](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) voor de Speech SDK voor Java script bevat enkele methoden die u voor spraak omzetting kunt gebruiken.

* Eenmalige vertaling (asynchroon): voert een vertaling uit in een niet-blokkerende (asynchrone) modus. Hiermee wordt één utterance vertaald. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.
* Continue vertalingen (asynchroon): initieert asynchrone Vertaal bewerking. De gebruiker meldt zich aan bij gebeurtenissen en verwerkt verschillende toepassings statussen. Om asynchrone continue vertaling te stoppen, roept u aan [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Een doel taal opgeven

Als u wilt vertalen, moet u zowel een bron taal als ten minste één doel taal opgeven.
U kunt een bron taal kiezen met behulp van een land instelling die wordt weer gegeven in de [tabel voor spraak omzetting](../../../language-support.md#speech-translation). Zoek uw opties voor vertaalde taal op dezelfde koppeling. De opties voor doel talen verschillen als u tekst wilt weer geven of als u de vertaalde spraak wilt belui Steren. Als u wilt vertalen van Engels naar Duits, wijzigt u het Vertaal configuratie object:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Eenmalige herkenning

Hier volgt een voor beeld van asynchrone omzetting van één opname met behulp van [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

U moet code schrijven om het resultaat af te handelen. In dit voor beeld wordt het [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) voor een vertaling in het Duits geëvalueerd:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Uw code kan ook updates verwerken die zijn verschaft tijdens de verwerking van de vertaling.
U kunt deze updates gebruiken om visuele feedback te geven over de voortgang van de vertaling.
Zie [dit voor beeld van Java script Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) voor voorbeeld code waarin updates worden weer gegeven tijdens het Vertaal proces. In de volgende code worden ook de details weer gegeven die tijdens het Vertaal proces zijn geproduceerd.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Continue vertaling

Continue vertaling is een beetje meer betrokken dan eenmalige herkenning. U moet zich abonneren op de gebeurtenissen `recognizing`, `recognized`en `canceled` om de herkenningsresultaten op te halen. Als u de vertaling wilt stoppen, moet u bellen [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . Hier volgt een voor beeld van hoe continue vertalingen worden uitgevoerd op een audio-invoer bestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer):

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

We nemen een abonnement op de gebeurtenissen die vanuit de [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) worden verzonden.

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Signaal voor gebeurtenissen met tussenliggende Vertaal resultaten.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Signaal voor gebeurtenissen met definitieve Vertaal resultaten (wat een geslaagde omzettings poging aangeeft).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Signaal voor gebeurtenissen die het einde van een Vertaal sessie (bewerking) aangeven.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Signaal voor gebeurtenissen met Geannuleerde Vertaal resultaten (die een vertalings poging aanduidt die is geannuleerd als gevolg van een aanvraag of een rechtstreekse annulering of een Trans Port-of protocol fout).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

Nu alles is ingesteld, kunnen we [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) aanroepen.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Een bron taal kiezen

Een veelvoorkomende taak voor spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoertaal wijzigt in Italiaans. Zoek in uw code uw [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) en voeg vervolgens de volgende regel toe.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

De eigenschap [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) verwacht een indelingstekenreeks voor taal-landinstellingen. U kunt in de lijst met ondersteunde [Landinstellingen en talen](../../../language-support.md) een willekeurige waarde opgeven in de kolom **Landinstelling**.

## <a name="choose-one-or-more-target-languages"></a>Kies een of meer doel talen

De Speech SDK kan tegelijkertijd worden omgezet naar meerdere doel talen. De beschik bare doel talen verschillen enigszins van de lijst van de bron taal en u geeft doel talen op met behulp van een taal code in plaats van een land instelling.
Bekijk een lijst met taal codes voor tekst doelen in [de tabel voor spraak omzetting op de pagina taal ondersteuning](../../../language-support.md#speech-translation). U vindt hier ook informatie over vertalingen in geprogrammeerde talen.

Met de volgende code wordt Duits als doel taal toegevoegd:

```javascript
translationConfig.addTargetLanguage("de");
```

Omdat er meerdere vertalingen van de doel taal mogelijk zijn, moet uw code de doel taal opgeven bij het onderzoeken van het resultaat. Met de volgende code worden Vertaal resultaten voor Duits opgehaald.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```