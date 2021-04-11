---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 9d6d6ec36d5ab7d29b3050dafda5fd711b01f1f4
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108840"
---
In deze quickstart maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Python-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren.

```Python
pip install azure-cognitiveservices-speech
```

Als u macOS heeft en er problemen met de installatie optreden, moet u deze opdracht mogelijk eerst uitvoeren.

```Python
python3 -m pip install --upgrade pip
```

Voeg nadat de Speech-SDK is geïnstalleerd de volgende importinstructies bovenaan het script toe.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voorbeeld maakt u een [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) met behulp van een abonnementssleutel en regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u een [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer)-object, waarmee tekst-naar-spraakconversies en uitvoer naar luidsprekers, bestanden of andere uitvoerstromen worden uitgevoerd. De [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) accepteert als parameters het [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)-object dat in de vorige stap is gemaakt en tevens een [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig)-object dat aangeeft hoe uitvoerresultaten moeten worden verwerkt.

Eerst maakt u een `AudioOutputConfig` om de uitvoer automatisch naar een `.wav`-bestand te schrijven met behulp van de constructorparameter `filename`.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Vervolgens instantieert u een `SpeechSynthesizer` door het `speech_config`-object en het `audio_config`-object als parameters door te geven. Daarna is het uitvoeren van spraaksynthese en het schrijven naar een bestand net zo eenvoudig als het uitvoeren van `speak_text_async()` met een regel tekst.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Voer het programma uit. Er wordt een gesynthetiseerd `.wav`-bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het eenvoudigste gebruik, maar hierna gaat u de uitvoer aanpassen en de uitvoerrespons verwerken als een in-memory stroom om met aangepaste scenario's te kunnen werken.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar de uitvoer van de luidspreker

In sommige gevallen kunt u gesynthetiseerde spraak rechtstreeks naar een luidspreker uitvoeren. Gebruik hiervoor het voorbeeld in de vorige sectie, maar wijzig de `AudioOutputConfig` door de parameter `filename` te verwijderen en `use_default_speaker=True` in te stellen. Hiermee wordt de uitvoer naar het huidige, actieve uitvoerapparaat gestuurd.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-memory stroom

Voor veel scenario's in het ontwikkelen van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens als een in-memory stroom nodig, in plaats dat u rechtstreeks naar een bestand gaat schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* Vat de resulterende bytematrix samen als een doorzoekbare stroom voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, schrijf aangepaste `.wav`-headers, enzovoort.

U kunt deze wijziging eenvoudig met behulp van het voorgaande voorbeeld uitvoeren. Verwijder eerst de `AudioConfig`, omdat u het uitvoergedrag vanaf dit punt handmatig gaat beheren voor een betere controle. Geef vervolgens `None` door voor de `AudioConfig` in de `SpeechSynthesizer`-constructor.

> [!NOTE]
> Als `None` voor de `AudioConfig` wordt doorgegeven in plaats van dat deze wordt weggelaten, zoals in het bovenstaande voorbeeld met de luidspreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoerapparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult)-variabele. De eigenschap `audio_data` bevat een `bytes`-object van de uitvoergegevens. U kunt handmatig met dit object werken, of u kunt de [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream)-klasse gebruiken om de stroom in het geheugen te beheren. In dit voorbeeld gebruikt u de constructor `AudioDataStream` om een stroom van het resultaat op te halen.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Hierna kunt u aangepast gedrag implementeren met behulp van het resulterende `stream`-object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u kenmerken van audio-uitvoer kunt aanpassen, zoals:

* Audiobestandstype
* Samplefrequentie
* Bitdiepte

Als u de audio-indeling wilt wijzigen, gebruikt u de functie `set_speech_synthesis_output_format()` op het `SpeechConfig`-object. Deze functie verwacht een `enum` van het type [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat), die u gebruikt om de uitvoerindeling te selecteren. Zie de naslagdocumenten voor een [lijst met audio-indelingen](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) die beschikbaar zijn.

Voor verschillende bestandstypen zijn er diverse opties mogelijk, afhankelijk van uw vereisten. Houd er rekening mee dat raw-indelingen als `Raw24Khz16BitMonoPcm` geen audio-headers bevatten. Gebruik raw-indelingen alleen als u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers handmatig te bouwen op basis van bitdiepte, samplefrequentie, aantal kanalen, enzovoort.

In dit voorbeeld geeft u een RIFF-indeling met hoge kwaliteit `Riff24Khz16BitMonoPcm` op door `SpeechSynthesisOutputFormat` in te stellen voor het `SpeechConfig`-object. Net als in het voorbeeld in de vorige sectie, gebruikt u [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) om een stroom in het geheugen van het resultaat te verkrijgen en vervolgens naar een bestand te schrijven.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Als u het programma opnieuw uitvoert, wordt een aangepast `.wav`-bestand naar het opgegeven pad geschreven.

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

Vervolgens moet u de aanvraag voor spraaksynthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de functie `speak_text_async()` gebruikt u `speak_ssml_async()`. Deze functie verwacht een XML-tekenreeks, dus u moet uw SSML-configuratie eerst lezen als een tekenreeks. Hier is het resultaatobject precies hetzelfde als in de vorige voorbeelden.

> [!NOTE]
> Als uw `ssml_string` aan het begin van de tekenreeks `ï»¿` bevat, moet u de BOM-indeling verwijderen, anders wordt er een foutbericht weergegeven. U doet dit door de parameter `encoding` als volgt in te stellen: `open("ssml.xml", "r", encoding="utf-8-sig")`.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> Als u de stem wilt wijzigen zonder SSML te gebruiken, kunt u de eigenschap instellen op de met `SpeechConfig` behulp van `SpeechConfig.speech_synthesis_voice_name = "en-US-AriaNeural"`

## <a name="get-facial-pose-events"></a>Gebeurtenissen voor gezichts pose ophalen

Speech is een goede manier om de animatie van gezichts expressies te testen.
Vaak worden [visemes](../../../how-to-speech-synthesis-viseme.md) gebruikt om de sleutel in waargenomen spraak te vertegenwoordigen, zoals de positie van de lippen, jaw en tong bij het produceren van een bepaalde foneem.
U kunt de viseme-gebeurtenis in Speech SDK abonneren.
Vervolgens kunt u viseme-gebeurtenissen Toep assen om het gezicht van een teken te animeren wanneer spraak geluid wordt afgespeeld.
Meer informatie [over het ophalen van viseme-gebeurtenissen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
