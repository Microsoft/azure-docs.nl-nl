---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: ad32204739d728006362ef55657a2f433be7aefc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97978856"
---
Als u de spraak-SDK wilt configureren voor het accepteren van gecomprimeerde audio-invoer, maakt u `PullAudioInputStream` of `PushAudioInputStream` . Maak vervolgens een `AudioConfig` van een exemplaar van uw Stream-klasse, waarbij u de compressie-indeling van de stroom opgeeft.

We gaan ervan uit dat u een invoer stroom klasse hebt aangeroepen `pushStream` en gebruikmaakt van Opus/OGG. Uw code kan er als volgt uitzien:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
