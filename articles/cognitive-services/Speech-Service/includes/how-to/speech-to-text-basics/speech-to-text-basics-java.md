---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 07d118073a73f539df8ea9c5428562a8cdd26db3
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912117"
---
Een van de belangrijkste functies van de Speech-service is de mogelijkheid om menselijke spraak te herkennen en te transcriberen (ook wel spraak-naar-tekst genoemd). In deze quickstart leert u meer over het gebruik van de Speech-SDK in uw apps en producten om spraak-naar-tekst-conversie van hoge kwaliteit uit te voeren.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Java-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken. Maak een [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) met behulp van uw sleutel en regio. Zie de pagina [Sleutels en regio zoeken](../../../overview.md#find-keys-and-region) om uw sleutel-regiopaar te zoeken.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Er zijn een paar andere manieren waarop u een [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) kunt initialiseren:

* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

## <a name="recognize-from-microphone"></a>Herkennen vanaf de microfoon

Als u spraak wilt herkennen met de microfoon van uw apparaat, maakt u een `AudioConfig` aan met behulp van `fromDefaultMicrophoneInput()`. Initialiseer vervolgens een[`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer), waarbij uw `audioConfig` en `config` worden doorgegeven.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Als u een *specifiek* audio-invoerapparaat wilt gebruiken, moet u de apparaat-id opgeven in de `AudioConfig`. Meer informatie over [het ophalen van de apparaat-id](../../../how-to-select-audio-input-devices.md) voor het audio-invoerapparaat.

## <a name="recognize-from-file"></a>Herkennen vanuit bestand

Als u spraak wilt herkennen vanuit een audiobestand in plaats van met een microfoon, moet u nog steeds een `AudioConfig` aanmaken. Wanneer u echter de [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) maakt in plaats van `fromDefaultMicrophoneInput()` aan te roepen, roept u `fromWavFileInput()` aan en geeft u het bestandspad door.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Foutafhandeling

In de vorige voorbeelden wordt de herkende tekst gewoon opgehaald met `result.getText()`, maar om fouten en andere antwoorden af te handelen, moet u code schrijven om het resultaat te verwerken. In het volgende voorbeeld wordt de eigenschap [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) geëvalueerd en:

* Het herkenningsresultaat afdrukken: `ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `ResultReason.NoMatch`
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>Continue herkenning

In de vorige voorbeelden wordt een eenmalige herkenning gebruikt, die één uiting herkent. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.

Continue herkenning wordt daarentegen gebruikt wanneer u wilt **bepalen** wanneer het herkennen moet stoppen. U moet zich abonneren op de gebeurtenissen `recognizing`, `recognized`en `canceled` om de herkenningsresultaten op te halen. Als u de herkenning wilt stoppen, moet u [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)aanroepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Vervolgens maken we een variabele voor het beheren van de status van spraakherkenning. We declareren een `Semaphore` in het klassebereik.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

We nemen een abonnement op de gebeurtenissen die vanuit de [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) worden verzonden.

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Nu alles is ingesteld, kunnen we [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync) aanroepen.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation)-methode op uw [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Frans. Zoek uw [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) in uw code en voeg deze regel daar direct onder toe.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) is een parameter die een tekenreeks als argument gebruikt. U kunt elke waarde in de lijst met ondersteunde [landinstellingen/talen](../../../language-support.md) opgeven.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Als u een lijst met zinsdelen opgeeft, verbetert u de nauwkeurigheid van spraakherkenning.

Als u bijvoorbeeld als mogelijke uitspraken een opdracht 'Move to' en een mogelijke bestemming 'Ward' hebt, kunt u een vermelding van 'Move to Ward' toevoegen. Door een woordgroep toe te voegen, wordt de kans groter dat een geluidsfragment wordt herkend als 'Move to Ward' in plaats van 'Move toward'.

Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een woordgroepenlijst gebruikt om herkenning van woorden en zinsdelen in de lijst te verbeteren, zelfs wanneer vermeldingen in het midden van een utterance voorkomen. 

> [!IMPORTANT]
> De functie Woordgroepenlijst is beschikbaar in de volgende talen: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Wijzigingen in [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Als u uw frasenlijst wilt wissen: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../custom-speech-overview.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)