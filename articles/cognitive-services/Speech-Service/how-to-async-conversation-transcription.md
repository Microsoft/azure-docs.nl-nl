---
title: Asynchrone conversatie transcriptie-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van asynchrone conversatie-transcriptie met behulp van de speech-service. Alleen beschikbaar voor Java en C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934635"
---
# <a name="asynchronous-conversation-transcription"></a>Asynchrone gesprektranscriptie

In dit artikel wordt asynchroon gesprek transcriptie gedemonstreerd met behulp van de **RemoteConversationTranscriptionClient** -API. Als u conversatie transcriptie hebt geconfigureerd om asynchrone transcriptie uit te voeren en een hebt `conversationId` , kunt u de transcriptie verkrijgen die is gekoppeld aan die met `conversationId` de **RemoteConversationTranscriptionClient** -API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroon versus realtime + asynchroon

Met asynchrone transcriptie streamt u de conversatie audio, maar hoeft er in realtime geen transcriptie te worden geretourneerd. Na het verzenden van de audio gebruikt u in plaats daarvan de `conversationId` van `Conversation` om de status van de asynchrone transcriptie te zoeken. Wanneer de asynchrone transcriptie klaar is, krijgt u een `RemoteConversationTranscriptionResult` .

Met realtime plus asynchroon krijgt u de transcriptie in realtime, maar krijgt u ook de transcriptie door een query uit te geven op basis van het `conversationId` scenario (vergelijkbaar met asynchroon).

Er zijn twee stappen vereist voor het uitvoeren van asynchrone transcriptie. De eerste stap is het uploaden van de audio, het kiezen van alleen asynchroon of realtime plus asynchroon. De tweede stap bestaat uit het ophalen van de transcriptie-resultaten.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
