---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 3d8558fbee05f663d72be64cb45ed4a2481513a3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105646"
---
Een van de belangrijkste functies van de Speech-service is de mogelijkheid om menselijke spraak te herkennen en te transcriberen (ook wel spraak-naar-tekst genoemd). In deze quickstart leert u meer over het gebruik van de Speech-SDK in uw apps en producten om spraak-naar-tekst-conversie van hoge kwaliteit uit te voeren.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [C++-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken. Maak een [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) met behulp van uw sleutel en regio. Zie de pagina [Sleutels en regio zoeken](../../../overview.md#find-keys-and-region) om uw sleutel-regiopaar te zoeken.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Er zijn een paar andere manieren waarop u een [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) kunt initialiseren:

* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

## <a name="recognize-from-microphone"></a>Herkennen vanaf de microfoon

Als u spraak wilt herkennen met de microfoon van uw apparaat, maakt u een `AudioConfig` aan met behulp van `FromDefaultMicrophoneInput()`. Initialiseer vervolgens een [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer), waarbij uw `audioConfig` en `config` worden doorgegeven.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Als u een *specifiek* audio-invoerapparaat wilt gebruiken, moet u de apparaat-id opgeven in de `AudioConfig`. Meer informatie over [het ophalen van de apparaat-id](../../../how-to-select-audio-input-devices.md) voor het audio-invoerapparaat.

## <a name="recognize-from-file"></a>Herkennen vanuit bestand

Als u spraak wilt herkennen vanuit een audiobestand in plaats van met een microfoon, moet u nog steeds een `AudioConfig` aanmaken. Wanneer u echter de [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) maakt in plaats van `FromDefaultMicrophoneInput()` aan te roepen, roept u `FromWavFileInput()` aan en geeft u het bestandspad door.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Spraak herkennen

De [Recognizer-klasse](/cpp/cognitive-services/speech/speechrecognizer) voor de Speech SDK voor C++ maakt een aantal methoden beschikbaar die u voor spraakherkenning kunt gebruiken.

### <a name="single-shot-recognition"></a>Eenmalige herkenning

Met eenmalige herkenning herkent u asynchroon één uiting. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt. Hier volgt een voor beeld van asynchrone eenmalige herkenning met [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

U moet code schrijven om het resultaat af te handelen. In dit voorbeeld wordt de [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason) geëvalueerd:

* Het herkenningsresultaat afdrukken: `ResultReason::RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `ResultReason::NoMatch`
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Continue herkenning

Continue herkenning is wat ingewikkelder dan eenmalige herkenning. U moet zich abonneren op de gebeurtenissen `Recognizing`, `Recognized`en `Canceled` om de herkenningsresultaten op te halen. Als u de herkenning wilt stoppen, moet u [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync) aanroepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Vervolgens maken we een variabele voor het beheren van de status van spraakherkenning. Om te beginnen, declareren we een `promise<void>`, omdat we bij het begin van de herkenning zonder twijfel kunnen aannemen dat deze niet is voltooid.

```cpp
promise<void> recognitionEnd;
```

We nemen een abonnement op de gebeurtenissen die vanuit de [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) worden verzonden.

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Nu alles is ingesteld, kunnen we [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) aanroepen.

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation)-methode op uw [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Duits. Zoek uw [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) in uw code en voeg deze regel daar direct onder toe.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) is een parameter die een tekenreeks als argument gebruikt. U kunt elke waarde in de lijst met ondersteunde [landinstellingen/talen](../../../language-support.md) opgeven.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Als u een lijst met zinsdelen opgeeft, verbetert u de nauwkeurigheid van spraakherkenning.

Als u bijvoorbeeld als mogelijke uitspraken een opdracht 'Move to' en een mogelijke bestemming 'Ward' hebt, kunt u een vermelding van 'Move to Ward' toevoegen. Door een woordgroep toe te voegen, wordt de kans groter dat een geluidsfragment wordt herkend als 'Move to Ward' in plaats van 'Move toward'.

Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een woordgroepenlijst gebruikt om herkenning van woorden en zinsdelen in de lijst te verbeteren, zelfs wanneer vermeldingen in het midden van een utterance voorkomen. 

> [!IMPORTANT]
> De functie Woordgroepenlijst is beschikbaar in de volgende talen: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Wijzigingen in [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Als u uw frasenlijst wilt wissen: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../custom-speech-overview.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)