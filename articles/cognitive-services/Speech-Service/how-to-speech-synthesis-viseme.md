---
title: Over het verkrijgen van gezichts pose-gebeurtenissen voor lip-sync
titleSuffix: Azure Cognitive Services
description: De Speech-SDK ondersteunt de gebeurtenis viseme in spraak synthese, die wordt gebruikt om de sleutel in waargenomen spraak te vertegenwoordigen, zoals de positie van de lippen, jaw en tong bij het produceren van een bepaalde foneem.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643900"
---
# <a name="get-facial-pose-events"></a>Gebeurtenissen voor gezichts pose ophalen

> [!NOTE]
> Viseme werkt nu alleen voor `en-US-AriaNeural` spraak.

Een viseme is de visuele beschrijving van een foneem in een gesp roken taal.
Hiermee wordt de positie van het gezicht en de mond gedefinieerd wanneer een woord wordt gesp roken.
Elke viseme laat zien hoe belang rijke gezichts een specifieke set fonemen.
Er is geen-op-een-correspondentie tussen visemes en fonemen.
Vaak zijn er verschillende fonemen die overeenkomen met één viseme, omdat meerdere fonemen er op het gezicht hetzelfde uitzien wanneer ze worden geproduceerd, zoals `s` en `z` .
Zie de [tabel toewijzing tussen visemes en fonemen](#map-phonemes-to-visemes).

Met behulp van visemes kunt u meer natuurlijke en intelligent News broadcast-assistent maken, meer interactieve gaming-en strip-tekens en meer intuïtieve taal onderwijs Video's. Mensen met een gehoor-bijzondere waardevermindering kunnen ook visueel geluid ophalen en ' lip-lezen ' spraak inhoud die visemes op een animatie gezicht laat zien.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Viseme-gebeurtenissen ophalen met de Speech SDK

Om viseme-gebeurtenissen te maken, converteren we invoer tekst naar een set foneem-reeksen en hun bijbehorende viseme-reeksen. We schatten de begin tijd van elke viseme in de spraak-audio. Viseme-gebeurtenissen bevatten een reeks Viseme-Id's, elk met een offset in de audio waar de Viseme wordt weer gegeven. Deze gebeurtenissen kunnen klauwzeer animaties die een persoon simuleren die de invoer tekst spreekt.

| Parameter | Beschrijving |
|-----------|-------------|
| Viseme-ID | Een geheel getal dat een viseme opgeeft. In het Engels (Verenigde Staten) bieden we 22 verschillende visemes om de mond vormen voor een specifieke set fonemen te laten zien. Zie de [tabel toewijzing tussen Viseme-id en fonemen](#map-phonemes-to-visemes).  |
| Audio-offset | De begin tijd van elke viseme, in Ticks (100 nano seconden). |

Als u viseme-gebeurtenissen wilt ophalen, abonneert u zich op de `VisemeReceived` gebeurtenis in Speech SDK.
De volgende code fragmenten laten zien hoe u zich abonneert op de viseme-gebeurtenis.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Fonemen toewijzen aan visemes

Visemes variëren per taal. Elke taal heeft een set visemes die overeenkomt met de specifieke fonemen. In de volgende tabel ziet u de correspondentie tussen internationaal fonetische alfabet (IPA) fonemen en viseme-Id's voor Engels (Verenigde Staten).

| IPA | Voorbeeld | Viseme-ID |
|-----|---------|-----------|
| i   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **een** te | 4 |
| ɛ | **e** zeer | 4 |
|æ  |   **een** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **OO** k          |4|
|oʊ |   **o** ld           |8|
|h  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **OE** t           |9|
|ɔɪ |   **Oi** l           |10|
|ju |   **Yu**, ma          |[6, 7]|
|ə  |   **een** go           |1|
|ɪɹ |   **oor**          |[6, 13]|
|ɛɹ |   **vlieg** tuig      |[4, 13]|
|ʊɹ |   c **uw** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ierland**-land   |[11, 13]|
|aʊ(ə)ɹ |   **uur** s     |[9, 13]|
|ɔɹ |   **of** Ange        |[3, 13]|
|ɑɹ |   **AR** Tist        |[2, 13]|
|ɝ  |   **oor**         |[5, 13]|
|ɚ  |   alle **Gy**       |[1, 13]|
|w  |   **w** i, s **UE** de   |7|
|v  |   **y**-ARD, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   **b** IG           |21|
|t  |   **t** alk          |19|
|d  |   **d** IG           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **m** at, s **m**-as    |21|
|n  |   **n** tot en met **n toestaan**      |19|
|ŋ  |   Li **n** k          |20|
|f  |   **f** ORK          |18|
|v  |   **v**-aarde         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z**-AP           |15|
|ʃ  |   **v**-e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** Elp          |12|
|tʃ |   **CH** in          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l**-id, g **l** AD     |14|
|ɹ  |   **r** Ed, b **r** ING    |13|


## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
