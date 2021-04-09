---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98947473"
---
De container biedt Api's voor query-eind punten op basis van websockets die worden geopend via de [Speech SDK](../index.yml). De Speech SDK maakt standaard gebruik van online spraak Services. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen.

> [!TIP]
> Wanneer u de Speech SDK met containers gebruikt, hoeft u de Azure speech resource [Subscription-sleutel of een verificatie Bearer-token](../rest-speech-to-text.md#authentication)niet op te geven.

Zie de onderstaande voorbeelden.

# <a name="c"></a>[C#](#tab/csharp)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Deze aanroep met de container [host](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Deze aanroep met het [eind punt](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)van de container gebruiken:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
