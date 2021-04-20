---
title: Codec gecomprimeerde audio streamen met de Speech SDK - Speech-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het streamen van gecomprimeerde audio naar de Speech-service met de Speech SDK. Beschikbaar voor C++, C# en Java voor Linux, Java in Android en Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: f02a9a3b493ed0f3068e6e0ccd2daa40850a4fb6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726174"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Codec gecomprimeerde audio-invoer gebruiken met de Speech SDK

De Speech Service SDK biedt een manier om gecomprimeerde audio-indelingen rechtstreeks naar de Speech-service te verzenden met behulp van een of (beide benaderingen worden niet rechtstreeks naar de back-end gestreamd, er wordt nog steeds een onbewerkte PCM naar de `PullStream` service verzonden). `PushStream`

Platform | Talen | Ondersteunde GStreamer-versie
| :--- | ---: | :---:
Windows (met uitzondering van UWP)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [ondersteunde Linux-distributies en doelarchitectarchitecten](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Speech SDK-versie vereist voor gecomprimeerde audio-invoer
* Speech SDK versie 1.10.0 of hoger is vereist voor RHEL 8 en CentOS 8
* Speech SDK versie 1.11.0 of hoger is vereist voor Windows.
* Speech SDK versie 1.16.0 of hoger voor de nieuwste gstreamer in Windows en Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer vereist voor het verwerken van gecomprimeerde audio

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Voorbeeldcode met codec gecomprimeerde audio-invoer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het herkennen van spraak](./get-started-speech-to-text.md)