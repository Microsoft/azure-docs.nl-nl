---
title: Quickstart voor tekst-naar-spraak - Speech-service
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Speech-SDK gebruikt om spraak-naar-tekst te converteren. In deze quickstart leert u meer over het bouwen van objecten en ontwerppatronen, ondersteunde audio-uitvoerindelingen, de spraak-CLI en aangepaste configuratieopties voor spraaksynthese.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: tekst naar spraak
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428201"
---
# <a name="get-started-with-text-to-speech"></a>Aan de slag met tekst-naar-spraak

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Positie-informatie ophalen

Het is mogelijk dat het project moet weten wanneer een woord wordt gesp roken door spraak naar tekst, zodat het specifieke actie kan uitvoeren op basis van die timing. Als u bijvoorbeeld woorden wilt markeren als ze zijn gesp roken, moet u weten wat u wilt markeren, wanneer u deze markeert en hoe lang het moet worden gemarkeerd.

U kunt dit doen met behulp `WordBoundary` van de gebeurtenis die beschikbaar is in `SpeechSynthesizer` . Deze gebeurtenis treedt op het begin van elk nieuw gesp roken woord op en geeft een tijd verschuiving in de gesp roken stroom en een tekst verschuiving binnen de invoer prompt.

* `AudioOffset` Hiermee wordt de verstreken tijd van de uitvoer audio gerapporteerd tussen het begin van de synthese en het begin van het volgende woord. Dit wordt gemeten in honderden nano seconden units (HNS) met 10.000 HNS gelijk aan 1 milliseconde.
* `WordOffset` Hiermee wordt de teken positie in de invoer teken reeks (oorspronkelijke tekst of [SSML](speech-synthesis-markup.md)) direct vóór het woord dat gaat worden gesp roken, gerapporteerd.

> [!NOTE]
> `WordBoundary` Er worden gebeurtenissen gegenereerd wanneer de uitvoer audio gegevens beschikbaar worden, wat sneller is dan afspelen naar een uitvoer apparaat. De time-out voor het streamen van gegevens naar real time moet worden uitgevoerd door de aanroeper.

Voor beelden van gebruik `WordBoundary` in de [tekst-naar-spraak](https://aka.ms/csspeech/samples) -voor beelden vindt u op github.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Custom Voice](how-to-custom-voice.md)
* [Synthese verbeteren met SSML](speech-synthesis-markup.md)
* Meer informatie over het gebruik van de [Long Audio API](long-audio-api.md) voor grote tekstvoorbeelden zoals boeken en nieuwsartikelen
* Bekijk de [Quickstart-voorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) op GitHub
