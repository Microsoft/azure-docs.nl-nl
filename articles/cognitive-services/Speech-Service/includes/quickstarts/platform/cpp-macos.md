---
title: 'Snelstart: Speech SDK voor instellen van C++ (macOS)-platform - Speech-service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van C++ onder macOS met de Speech-service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188245"
---
In deze gids ontdekt u hoe u de [Speech-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor C++ kunt installeren voor macOS 10.13 en hoger.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

macOS 10.13 en hoger

## <a name="install-speech-sdk"></a>Speech SDK installeren

1. Kies een map waar de bestanden van de Speech SDK moeten worden uitgepakt en laat de `SPEECHSDK_ROOT`-omgevingsvariabele naar die map verwijzen. Met deze variabele kunt u in toekomstige opdrachten eenvoudig naar de map verwijzen. Als u de map `speechsdk` bijvoorbeeld wilt gebruiken in de basismap, gebruik dan een opdracht als de volgende:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Maak de map als deze nog niet bestaat.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Download en extraheer het `.zip`-archief met de binaire bestanden voor het Speech SDK-framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valideer de inhoud van de map op het hoogste niveau van het uitgepakte pakket:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   De map moet de kennisgeving en licentiebestanden van derden bevatten, evenals een `MicrosoftCognitiveServicesSpeech.framework`-map.

U kunt nu verdergaan met [Volgende stappen](#next-steps) hieronder.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
