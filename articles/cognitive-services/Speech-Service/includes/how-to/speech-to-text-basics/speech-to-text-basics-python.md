---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 8f84de2454baf0689f95422a95f9dda84a01b8b2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551716"
---
Een van de belangrijkste functies van de Speech-service is de mogelijkheid om menselijke spraak te herkennen en te transcriberen (ook wel spraak-naar-tekst genoemd). In deze quickstart leert u meer over het gebruik van de Speech-SDK in uw apps en producten om spraak-naar-tekst-conversie van hoge kwaliteit uit te voeren.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Python-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat:

* U een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>De Speech-SDK installeren en importeren

Voordat u iets kunt doen, moet u de Speech SDK installeren.

```Python
pip install azure-cognitiveservices-speech
```

Als u macOS heeft en er problemen met de installatie optreden, moet u deze opdracht mogelijk eerst uitvoeren.

```Python
python3 -m pip install --upgrade pip
```

Nadat de Speech-SDK is geïnstalleerd, importeert u deze in uw Python-project.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken. Maak een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) met behulp van uw sleutel en regio. Zie de pagina [Sleutels en regio zoeken](../../../overview.md#find-keys-and-region) om uw sleutel-regiopaar te zoeken.

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

Er zijn een paar andere manieren waarop u een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) kunt initialiseren:

* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

## <a name="recognize-from-microphone"></a>Herkennen vanaf de microfoon

Als u spraak wilt herkennen met de microfoon van uw apparaat, maakt u gewoon een `SpeechRecognizer` aan zonder een `AudioConfig` door te geven en geeft u uw `speech_config` door.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

Als u een *specifiek* audio-invoerapparaat wilt gebruiken, moet u de apparaat-ID in een `AudioConfig` opgeven en deze door geven aan de `audio_config`-parameter van de `SpeechRecognizer`-constructor. Meer informatie over [het ophalen van de apparaat-id](../../../how-to-select-audio-input-devices.md) voor het audio-invoerapparaat.

## <a name="recognize-from-file"></a>Herkennen vanuit bestand

Als u spraak wilt herkennen vanuit een audiobestand in plaats van met een microfoon, maakt u een [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig) aan en gebruikt u de parameter `filename`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>Foutafhandeling

In de vorige voorbeelden wordt de herkende tekst gewoon opgehaald uit `result.text`, maar om fouten en andere antwoorden af te handelen, moet u code schrijven om het resultaat te verwerken. Met de volgende code wordt de [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason)-eigenschap geëvalueerd en:

* Het herkenningsresultaat afdrukken: `speechsdk.ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `speechsdk.ResultReason.NoMatch `
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>Continue herkenning

In de vorige voorbeelden wordt een eenmalige herkenning gebruikt, die één uiting herkent. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.

Continue herkenning wordt daarentegen gebruikt wanneer u wilt **bepalen** wanneer het herkennen moet stoppen. Hiervoor moet u verbinding maken met de `EventSignal` om de herkennings resultaten op te halen en om de herkenning te stoppen, dient u [stop_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) of [stop_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition-async--)aan te roepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Vervolgens maken we een variabele voor het beheren van de status van spraakherkenning. Om te beginnen, stellen we dit in op `False`, omdat we bij het begin van de herkenning zonder twijfel kunnen aannemen dat deze niet is voltooid.

```Python
done = False
```

Nu gaan we een callback aanmaken om continue herkenning te stoppen wanneer een `evt` wordt ontvangen. Er zijn enkele dingen die u moet onthouden.

* Wanneer een `evt` wordt ontvangen, wordt het `evt`-bericht afgedrukt.
* Nadat een `evt` is ontvangen, wordt [stop_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) aangeroepen om de herkenning te stoppen.
* De herkenningsstatus is gewijzigd in `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Dit codevoorbeeld toont hoe u callbacks verbindt met gebeurtenissen die vanuit het [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#start-continuous-recognition--)worden verzonden.

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognizing): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-started): Signaal voor gebeurtenissen die het begin van een herkenningssessie (bewerking) aangeven.
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#canceled): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Als alles is ingesteld, kunnen we [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped) aanroepen.

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#enable-dictation--)-methode op uw [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Duits. Zoek uw SpeechConfig in uw code en voeg deze regel daar direct onder toe.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-recognition-language) is een parameter die een tekenreeks als argument gebruikt. U kunt elke waarde in de lijst met ondersteunde [landinstellingen/talen](../../../language-support.md) opgeven.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Als u een lijst met zinsdelen opgeeft, verbetert u de nauwkeurigheid van spraakherkenning.

Als u bijvoorbeeld als mogelijke uitspraken een opdracht 'Move to' en een mogelijke bestemming 'Ward' hebt, kunt u een vermelding van 'Move to Ward' toevoegen. Door een woordgroep toe te voegen, wordt de kans groter dat een geluidsfragment wordt herkend als 'Move to Ward' in plaats van 'Move toward'.

Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een woordgroepenlijst gebruikt om herkenning van woorden en zinsdelen in de lijst te verbeteren, zelfs wanneer vermeldingen in het midden van een utterance voorkomen. 

> [!IMPORTANT]
> De functie Woordgroepenlijst is beschikbaar in de volgende talen: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar#addphrase-phrase--str-).

Wijzigingen in [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Als u uw frasenlijst wilt wissen: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../custom-speech-overview.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)
