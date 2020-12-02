---
title: 'Quickstart: Speech SDK voor JavaScript (browser) platform instellen: Speech-service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van JavaScript (browser) met de Speech-service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188284"
---
In deze handleiding wordt uitgelegd hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor JavaScript installeert voor gebruik met een webpagina.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Een nieuwe websitemap maken

Maak een nieuwe, lege map. Als u de voorbeeldtoepassing op een webserver wilt hosten, moet u ervoor zorgen dat de webserver toegang heeft tot de map.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>De Speech SDK voor JavaScript in die map uitpakken

Download de Speech SDK als een [ZIP-pakket](https://aka.ms/csspeech/jsbrowserpackage) en pak dit uit in de zojuist gemaakte map. Er worden vijf bestanden uitgepakt:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` Een door mensen leesbare versie van de Speech SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Een toewijzingsbestand voor het opsporen van fouten in SDK-code.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` Objectdefinities voor gebruik met TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Een geminimaliseerde versie van de Speech SDK.
* `speech-processor.js` Code voor het verbeteren van de prestaties in sommige browsers.

## <a name="create-an-indexhtml-page"></a>Een index.html-pagina maken

Maak een nieuw bestand in de map met de naam `index.html` en open dit bestand met een teksteditor.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]