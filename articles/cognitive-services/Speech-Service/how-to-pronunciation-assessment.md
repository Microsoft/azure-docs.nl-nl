---
title: Spraak-SDK gebruiken voor het beoordelen van de uitspraak
titleSuffix: Azure Cognitive Services
description: De Speech SDK ondersteunt de uitspraak beoordeling, waarmee de uitspraak kwaliteit van spraak invoer wordt beoordeeld, met indica toren van nauw keurigheid, Fluency, volledigheid, enzovoort.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 1cc313daf0e76ddd14865959410b07d9bdc189d7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984918"
---
# <a name="pronunciation-assessment"></a>Beoordeling van uitspraak

Beoordeling van de uitspraak evalueert de stem op spraak en geeft sprekers feedback over de nauw keurigheid en fluency van gesp roken audio.
Met het beoordelen van uitspraak kunnen taal kennisers oefenen, direct feedback krijgen en de uitspraak verbeteren zodat ze kunnen praten en presen teren.
Docenten kunnen de mogelijkheid gebruiken om de uitspraak van meerdere sprekers in realtime te evalueren.

In dit artikel vindt u informatie over het instellen `PronunciationAssessmentConfig` en ophalen van het `PronunciationAssessmentResult` gebruik van de spraak-SDK.

> [!NOTE]
> De functie voor het beoordelen van uitspraak is momenteel alleen beschikbaar in regio's `westus` `eastasia` en `centralindia` ondersteunt alleen talen `en-US` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Uitspraak beoordeling met de Speech SDK

In de onderstaande voor beelden maakt u een `PronunciationAssessmentConfig` en past u deze toe op een `SpeechRecognizer` .

De volgende code fragmenten laten zien hoe u automatische taal detectie in uw apps kunt gebruiken:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Configuratie parameters voor beoordeling van uitspraak

Deze tabel bevat de configuratie parameters voor de uitspraak beoordeling.

| Parameter | Beschrijving | Vereist? |
|-----------|-------------|---------------------|
| ReferenceText | De tekst waarmee de uitspraak wordt geëvalueerd. | Vereist |
| GradingSystem | Het punt systeem voor de Score kalibratie. Het `FivePoint` systeem geeft een drijvende-komma Score van 0-5 en `HundredMark` geeft een 0-100 drijvende-komma Score. Standaard: `FivePoint`. | Optioneel |
| Granulariteit | De granulatie van de evaluatie. Geaccepteerde waarden zijn `Phoneme` , waarmee de score wordt weer gegeven op de volledige tekst, het woord-en foneem-niveau, `Word` waarin de Score van het volledige tekst-en woord niveau wordt weer gegeven, `FullText` waarin de score op het volledige tekst niveau wordt weer geven. Standaard: `Phoneme`. | Optioneel |
| EnableMiscue | Hiermee wordt de miscue-berekening ingeschakeld. Als deze functie is ingeschakeld, worden de uitgesp roken woorden vergeleken met de verwijzings tekst en worden deze gemarkeerd met weglating/invoegen op basis van de vergelijking. Geaccepteerde waarden zijn `False` en `True` . Standaard: `False`. | Optioneel |
| ScenarioId | Een GUID die een aangepast punt systeem aangeeft. | Optioneel |

### <a name="pronunciation-assessment-result-parameters"></a>Resultaten van de beoordeling van de uitspraak

Deze tabel bevat de resultaat parameters van de uitspraak beoordeling.

| Parameter | Beschrijving |
|-----------|-------------|
| `AccuracyScore` | Nauw keurigheid van de uitspraak van de spraak. Nauw keurigheid geeft aan hoe nauw keurig de fonemen overeenkomt met de uitspraak van de systeem eigen spreker. De nauw keurigheid van het woord en het volledige tekst niveau worden geaggregeerd van de nauwkeurigheids Score van het foneem niveau. |
| `FluencyScore` | Fluency van de gegeven spraak. Fluency geeft aan hoe nauw keurig de spraak overeenkomt met het gebruik van stille onderbrekingen van de eigen spreker tussen woorden. |
| `CompletenessScore` | Volledigheid van de spraak, bepaald door het berekenen van de verhouding van uitgesp roken woorden om naar tekst invoer te verwijzen. |
| `PronunciationScore` | Algemene score die de uitspraak kwaliteit van de opgegeven spraak aangeeft. Deze wordt samengesteld op basis `AccuracyScore` van `FluencyScore` en `CompletenessScore` met gewicht. |
| `ErrorType` | Deze waarde geeft aan of een woord wordt wegge laten, wordt ingevoegd of verkeerd is uitgesp roken, vergeleken met `ReferenceText` . Mogelijke waarden zijn `None` (wat betekent dat er geen fout is in dit woord), `Omission` `Insertion` en `Mispronunciation` . |

## <a name="next-steps"></a>Volgende stappen

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
