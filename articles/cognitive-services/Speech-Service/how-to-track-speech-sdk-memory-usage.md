---
title: Het bijhouden van het geheugen gebruik van Speech SDK-spraak service
titleSuffix: Azure Cognitive Services
description: De Speech Service SDK ondersteunt talloze programmeer talen voor conversie van spraak naar tekst en tekst naar spraak, samen met spraak omzetting. In dit artikel vindt u informatie over het hulp programma voor geheugen beheer dat is ingebouwd in de SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934137"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Het bijhouden van het geheugen gebruik van Speech SDK

De Speech SDK is gebaseerd op een systeem eigen code basis die is geprojecteerd in meerdere programmeer talen via een reeks interoperabiliteits lagen. Elke taalspecifieke projectie heeft idiomatically juiste functies voor het beheren van de levens cyclus van het object. Daarnaast bevat de spraak-SDK hulp middelen voor geheugen beheer voor het bijhouden van resource gebruik met object logboek registratie en object limieten. 

## <a name="how-to-read-object-logs"></a>Object logboeken lezen

Als [spraak-SDK-logboek registratie is ingeschakeld](how-to-use-logging.md), worden tracking Tags verzonden om historische object waarnemingen mogelijk te maken. Deze tags zijn onder andere: 

* `TrackHandle` of `StopTracking` 
* Het object type
* Het huidige aantal objecten dat het type van het object en het huidige nummer wordt bijgehouden.

Hier volgt een voor beeld van een logboek: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Een waarschuwings drempel instellen

U hebt de optie om een waarschuwings drempel te maken en als de drempel waarde wordt overschreden (uitgaande logboek registratie is ingeschakeld), wordt er een waarschuwings bericht vastgelegd. Het waarschuwings bericht bevat een dump van alle objecten in aanwezigheid, samen met het aantal. Deze informatie kan worden gebruikt om problemen beter te begrijpen. 

Als u een waarschuwings drempel wilt inschakelen, moet u deze opgeven voor een `SpeechConfig` object. Dit object wordt gecontroleerd wanneer een nieuwe herkenner wordt gemaakt. In de volgende voor beelden wordt ervan uitgegaan dat u een instantie hebt gemaakt met de `SpeechConfig` naam `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> De standaard waarde voor deze eigenschap is 10.000.

## <a name="set-an-error-threshold"></a>Een drempel waarde voor fouten instellen 

Met de Speech SDK kunt u het maximum aantal objecten instellen dat op een bepaald moment is toegestaan. Als deze instelling is ingeschakeld, zullen pogingen om nieuwe Recognizer-objecten te maken mislukken wanneer het maximum aantal wordt bereikt. Bestaande objecten blijven werken.

Hier volgt een voorbeeld fout:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Als u een drempel waarde voor fouten wilt inschakelen, moet u deze opgeven voor een `SpeechConfig` object. Dit object wordt gecontroleerd wanneer een nieuwe herkenner wordt gemaakt. In de volgende voor beelden wordt ervan uitgegaan dat u een instantie hebt gemaakt met de `SpeechConfig` naam `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> De standaard waarde voor deze eigenschap is de platformspecifieke maximum waarde voor een `size_t` gegevens type. Er wordt een typische herkenning gebruikt tussen 7 en 10 interne objecten.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de Speech SDK](speech-sdk.md)