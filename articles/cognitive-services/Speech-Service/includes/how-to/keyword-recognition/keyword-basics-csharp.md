---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 407906727332f3db8d3d0a6840d0c865c6b33ff7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609514"
---
Laad eerst uw trefwoorden model bestand met behulp van de `FromFile()` statische functie. dit retourneert een `KeywordRecognitionModel` . Gebruik het pad naar het `.table` bestand dat u hebt gedownload vanuit speech Studio. Daarnaast maakt u een `AudioConfig` met de standaard microfoon en vervolgens een nieuw exemplaar `KeywordRecognizer` van de audio configuratie.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Vervolgens wordt het uitvoeren van trefwoord herkenning uitgevoerd met één aanroep naar `RecognizeOnceAsync()` door uw model object door te geven. Hiermee wordt een sessie voor het herkennen van een tref woord gestart totdat het sleutel woord is herkend. Daarom gebruikt u dit ontwerp patroon doorgaans in toepassingen met meerdere threads of in gevallen waarin u voor onbepaalde tijd kunt wachten op een Wake-woord.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> In het voor beeld dat hier wordt weer gegeven, wordt gebruikgemaakt van lokale woord herkenning, omdat hiervoor geen `SpeechConfig` object voor verificatie context is vereist en er geen contact wordt gemaakt met de back-end. U kunt echter zowel trefwoord herkenning als verificatie uitvoeren met [een directe back-endverbinding](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Continue herkenning

Andere klassen in de Speech SDK ondersteunen doorlopende herkenning (voor spraak-en intentie herkenning) met trefwoord herkenning. Zo kunt u dezelfde code gebruiken die u normaal gesp roken gebruikt voor continue herkenning, met de mogelijkheid om te verwijzen naar een `.table` bestand voor uw trefwoord model.

Voor spraak naar tekst volgt u hetzelfde ontwerp patroon dat wordt weer gegeven in de [Quick](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) start om doorlopende herkenning in te stellen. Vervang vervolgens de aanroep door door `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` en geef uw object door `KeywordRecognitionModel` . Als u doorlopende herkenning met trefwoord herkenning wilt stoppen, gebruikt u `recognizer.StopKeywordRecognitionAsync()` in plaats van `recognizer.StopContinuousRecognitionAsync()` .

De intentie herkenning gebruikt een identiek patroon met de [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) functies en.
