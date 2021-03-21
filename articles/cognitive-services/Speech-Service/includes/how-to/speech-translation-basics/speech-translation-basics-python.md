---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 679104a1941e677dd78190fb80964a5d39813e61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445531"
---
Een van de belangrijkste functies van de speech-service is de mogelijkheid om menselijke spraak te herkennen en te vertalen naar andere talen. In deze Snelstartgids leert u hoe u de Speech SDK in uw apps en producten kunt gebruiken om spraak vertalingen van hoge kwaliteit uit te voeren. In deze Quick Start vindt u onderwerpen, waaronder:

* Omzetten van spraak naar tekst
* Spraak omzetten naar meerdere doel talen
* Direct omzetten van spraak naar spraak uitvoeren

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Python-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies in de sectie <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">de SDK voor spraak ophalen</a> van het artikel _over Speech SDK_ .

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, voegt u de volgende `import` instructies toe boven aan het python-code bestand.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevings variabelen

De voorbeeld bron code in dit artikel is afhankelijk van omgevings variabelen voor het opslaan van gevoelige gegevens, zoals de sleutel van het abonnement voor spraak bronnen en de regio. Het python-code bestand bevat twee waarden die zijn toegewezen uit de omgevings variabelen van de hostcomputer, te weten `SPEECH__SUBSCRIPTION__KEY` en `SPEECH__SERVICE__REGION` . Beide variabelen bevinden zich in het globale bereik, waardoor ze toegankelijk zijn in de functie definitie van het code bestand. Zie [omgevings variabelen en toepassings configuratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevings variabelen.

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Een configuratie voor spraak omzetting maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechTranslationConfig`][config] maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!TIP]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechTranslationConfig`][config] kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechTranslationConfig`][config] wordt gemaakt met behulp van een sleutel en regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak van spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoertaal wijzigt in Italiaans. In uw code, communiceert u met het [`SpeechTranslationConfig`][config] exemplaar en wijst u deze toe aan de `speech_recognition_language` eigenschap.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

De eigenschap [`speech_recognition_language`][recognitionlang] verwacht een indelingstekenreeks voor taal-landinstellingen. U kunt in de lijst met ondersteunde [Landinstellingen en talen](../../../language-support.md) een willekeurige waarde opgeven in de kolom **Landinstelling**.

## <a name="add-translation-language"></a>Vertaal taal toevoegen

Een andere veelvoorkomende taak van spraak omzetting is het opgeven van talen voor doel omzetting, ten minste één is vereist, maar meerdere worden ondersteund. In het volgende code fragment worden zowel Frans als Duits ingesteld als Vertaal taal doelen.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Bij elke aanroep van [`add_target_language`][addlang] wordt een nieuwe doel taal voor vertalen opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de bron taal, is elke doel omzetting beschikbaar als onderdeel van de resulterende Vertaal bewerking.

## <a name="initialize-a-translation-recognizer"></a>Een omzettings herkenning initialiseren

Nadat u een [`SpeechTranslationConfig`][config] hebt gemaakt, is de volgende stap het initialiseren van een [`TranslationRecognizer`][recognizer]. Wanneer u een [`TranslationRecognizer`][recognizer] initialiseert, moet u er uw `translation_config` aan doorgeven. Het configuratie object bevat de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaardmicrofoon van uw apparaat, ziet u hier hoe de [`TranslationRecognizer`][recognizer] eruit moet zien:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Als u het audio-invoerapparaat wilt opgeven, moet u een [`AudioConfig`][audioconfig] maken en de parameter `audio_config` opgeven bij het initialiseren van uw [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u `AudioConfig` als volgt naar het object:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Wanneer u echter een maakt [`AudioConfig`][audioconfig] in plaats van aan te roepen `use_default_microphone=True` , roept u aan `filename="path-to-file.wav"` en geeft u de `filename` para meter op.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te kunnen vertalen, is de spraak-SDK afhankelijk van een microfoon of een audio bestand invoer. Spraak herkenning vindt plaats voordat spraak wordt vertaald. Wanneer alle objecten zijn geïnitialiseerd, roept u de functie recognize Once aan en haalt u het resultaat op.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Zie [basis beginselen van spraak herkenning](../../../get-started-speech-to-text.md)voor meer informatie over spraak naar tekst.

## <a name="synthesize-translations"></a>Vertalingen voor synthesizer

Na een geslaagde spraak herkenning en-omzetting bevat het resultaat alle vertalingen in een woorden lijst. De [`translations`][translations] woordenlijst sleutel is de taal voor de doel omzetting en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens in een andere taal (spraak naar spraak) worden gesynthesizerd.

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object toont een `Synthesizing` gebeurtenis. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme voor het ophalen van de gesynthesizerde audio uit het resultaat van de vertalings herkenning. Zie [hand matige synthese](#manual-synthesis)als u naar meerdere talen wilt vertalen. Geef de synthese stem op door een [`voice_name`][voicename] gebeurtenis-handler toe te wijzen en de `Synthesizing` audio op te halen. In het volgende voor beeld wordt de vertaalde audio opgeslagen als *WAV* -bestand.

> [!IMPORTANT]
> De op gebeurtenissen gebaseerde synthese werkt alleen met één vertaling, Voeg **geen** meerdere talen voor doel omzetting toe. Daarnaast moet het [`voice_name`][voicename] dezelfde taal zijn als die van de doel taal van de vertaling, bijvoorbeeld; `"de"` kan worden toegewezen aan `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Hand matige synthese

De [`translations`][translations] woorden lijst kan worden gebruikt om de audio van de Vertaal tekst te defragmenteren. Herhaal elke vertaling en synthesizer de vertaling. Bij het maken van een `SpeechSynthesizer` exemplaar `SpeechConfig` moet de eigenschap van het object [`speech_synthesis_voice_name`][speechsynthesisvoicename] op de gewenste stem zijn ingesteld. In het volgende voor beeld worden de vijf talen omgezet en elke vertaling wordt vervolgens op een audio bestand in de bijbehorende Neural-taal gesynthesizerd.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Zie [basis beginselen van spraak synthese](../../../get-started-text-to-speech.md)voor meer informatie over spraak synthese.

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name