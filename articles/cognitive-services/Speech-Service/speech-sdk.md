---
title: Over de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: De speech Software Development Kit (SDK) toont veel van de mogelijkheden van de spraak service, waardoor het eenvoudiger wordt om toepassingen met spraak functionaliteit te ontwikkelen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: afba973570d75eace8cae8d1ed6ed470db21ef0e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043206"
---
# <a name="about-the-speech-sdk"></a>Info over de Speech-SDK

De speech Software Development Kit (SDK) toont veel van de mogelijkheden van de spraak service, om u te ondersteunen bij het ontwikkelen van toepassingen met spraak herkenning. De Speech SDK is beschikbaar in veel programmeer talen en op alle platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenario mogelijkheden

De Speech-SDK maakt veel functies van de speech-service beschikbaar, maar niet alle. De mogelijkheden van de Speech SDK zijn vaak gekoppeld aan scenario's. De Speech SDK is ideaal voor realtime-en niet-real-time scenario's, met behulp van lokale apparaten, bestanden, Azure Blob-opslag en zelfs invoer-en uitvoer stromen. Wanneer een scenario niet kan worden behaald met de Speech SDK, zoekt u naar een REST API alternatief.

### <a name="speech-to-text"></a>Spraak naar tekst

Met [spraak naar tekst](speech-to-text.md) (ook wel *spraak herkenning* genoemd) worden audio stromen getranscribeerd naar tekst die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](../luis/index.yml) om gebruikersintentie af te leiden uit getranscribeerde spraak en om te reageren op spraakopdrachten. Gebruik [spraak omzetting](speech-translation.md) om spraak invoer te vertalen naar een andere taal met één aanroep. Zie [basis beginselen van spraak naar tekst](./get-started-speech-to-text.md)voor meer informatie.

De **spraak herkenning (SR), woordgroepen lijst, intentie, vertaling en on-premises containers** zijn beschikbaar op de volgende platformen:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET core)/Windows & UWP & unit & Xamarin & Linux & macOS
  - Java (jre en Android)
  - Java script (browser en NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (alleen SR)

### <a name="text-to-speech"></a>Tekst naar spraak

[Tekst-naar-spraak](text-to-speech.md) (ook wel *spraak-synthese* genoemd) converteert tekst naar humane-achtige gesynthesizerde spraak. De invoer tekst is letterlijke teken reeksen of het gebruik van de [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Zie voor meer informatie over de standaard-of Neural stemmen [tekst-naar-spraak-taal en spraak ondersteuning](language-support.md#text-to-speech).

**Tekst-naar-spraak (TTS)** is beschikbaar op de volgende platformen:

  - C++/Windows & Linux
  - C#/Windows & UWP & eenheid
  - Java (jre en Android)
  - Python
  - Swift
  - Objective-C
  - TTS-REST API kunnen worden gebruikt in elke andere situatie.

### <a name="voice-assistants"></a>Spraakassistenten

Met spraak [assistenten](voice-assistants.md) die gebruikmaken van de Speech-SDK kunt u natuurlijke, menselijke-achtige gespreks interfaces maken voor uw toepassingen en ervaringen. De Speech SDK biedt snelle, betrouw bare interactie met spraak-naar-tekst-, tekst-naar-spraak-en gespreks gegevens over één verbinding. Uw implementatie kan het directe lijn spraak kanaal of de geïntegreerde service voor het volt ooien van de taak gebruiken. Daarnaast kunnen spraak assistenten aangepaste stemmen gebruiken die zijn gemaakt in de [aangepaste Voice Portal](https://aka.ms/customvoice) om een unieke spraak-uitvoer ervaring toe te voegen.

Ondersteuning voor de **Voice-assistent** is beschikbaar op de volgende platforms:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech apparaten SDK)
  - Go

#### <a name="keyword-spotting"></a>Tref woord herkennen

Het concept van [trefwoord herkennen](./custom-keyword-basics.md) wordt ondersteund in de Speech SDK. Trefwoord herkennen is de handeling van het identificeren van een sleutel woord in spraak, gevolgd door een actie na het horen van het sleutel woord. Bijvoorbeeld: "Hey Cortana" zou de Cortana-assistent activeren.

**Trefwoord herkennen (KWS)** is beschikbaar op de volgende platformen:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech-apparaten SDK)
  - De functionaliteit van trefwoord herkennen (KWS) kan worden gebruikt voor elk type microfoon, maar de ondersteuning van officiële KWS is momenteel beperkt tot de microfoon matrices die zijn gevonden in de Azure Kinect DK-hardware of de speech-apparaten SDK

### <a name="meeting-scenarios"></a>Scenario's voor vergaderingen

De Speech SDK is perfect voor het overzetten van Vergader scenario's, hetzij van een apparaat of een conversatie met meerdere apparaten.

#### <a name="conversation-transcription"></a>Gesprektranscriptie

[Conversatie transcriptie](conversation-transcription.md) maakt spraak herkenning in realtime (en asynchroon) mogelijk, waarbij elke spreker (ook wel bekend als *diarization*) wordt toegeschreven. Het is ideaal voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om de sprekers te onderscheiden.

**Conversation transcriptie** is beschikbaar op de volgende platformen:

  - C++/Windows & Linux
  - C# (Framework & .NET core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech-apparaten SDK)

#### <a name="multi-device-conversation"></a>Gesprek via meerdere apparaten

Met een [gesprek met meerdere](multi-device-conversation.md)apparaten kunt u meerdere apparaten of clients in een gesprek verbinden om berichten op basis van spraak of tekst te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling.

**Multi-device-conversatie** is beschikbaar op de volgende platforms:

  - C++/Windows
  - C# (Framework & .NET core)/Windows

### <a name="custom--agent-scenarios"></a>Aangepaste/Agent scenario's

De Speech-SDK kan worden gebruikt voor het transcriberen van oproep centrum scenario's, waar telefoon gegevens worden gegenereerd.

#### <a name="call-center-transcription"></a>Callcentertranscriptie

[Call Center transcriptie](call-center-transcription.md) is een veelvoorkomend scenario voor spraak naar tekst voor het transcriberen van grote hoeveel heden telefoon gegevens die afkomstig kunnen zijn van verschillende systemen, zoals Interactive Voice Response (IVR). De nieuwste spraakherkennings modellen van de speech-service Excel bij het transcriberen van deze telefoon gegevens, zelfs in gevallen waarin de gegevens moeilijk te begrijpen zijn.

**Call Center transcriptie** is beschikbaar via de batch speech-service via de rest API en kan in elke situatie worden gebruikt.

### <a name="codec-compressed-audio-input"></a>Door codec gecomprimeerde audio-invoer

Diverse spraak-SDK-programmeer talen ondersteunen codec gecomprimeerde audio-invoer stromen. Zie <a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">gecomprimeerde audio-invoer indelingen gebruiken </a>voor meer informatie.

De **gecomprimeerde audio-invoer** voor de codec is beschikbaar op de volgende platforms:

  - C++/Linux
  - C#-/Linux
  - Java/Linux, Android en iOS

## <a name="rest-api"></a>REST-API

Hoewel de spraak-SDK een groot aantal functies van de spraak service omvat, kunt u voor sommige scenario's de REST API gebruiken.

### <a name="batch-transcription"></a>Batchtranscriptie

[Batch transcriptie](batch-transcription.md) maakt asynchrone spraak-naar-tekst-transcriptie met grote hoeveel heden gegevens mogelijk. Batch-transcriptie is alleen mogelijk vanuit het REST API. Met batch spraak naar tekst kunt u niet alleen spraak-audio omzetten naar tekst, maar ook diarization-en sentiment-analyses.

## <a name="customization"></a>Aanpassing

De speech-service biedt uitstekende functionaliteit met de standaard modellen voor spraak naar tekst, tekst naar spraak en spraak omzetting. Soms wilt u mogelijk de basislijn prestaties verhogen zodat deze nog beter werken met uw unieke gebruiks case. De speech-service beschikt over een aantal hulpprogram ma's waarmee u geen code kunt aanpassen en waarmee u een competitief voor deel kunt maken met aangepaste modellen op basis van uw eigen gegevens. Deze modellen zijn alleen beschikbaar voor u en uw organisatie.

### <a name="custom-speech-to-text"></a>Aangepaste spraak-naar-tekst

Wanneer u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal en uitspraak modellen maken en trainen om omgevings lawaai of branchespecifieke woorden lijsten te verhelpen. Het maken en beheren van Custom Speech modellen zonder code is beschikbaar via de [Custom speech Portal](https://aka.ms/customspeech). Zodra het Custom Speech model is gepubliceerd, kan het worden gebruikt door de spraak-SDK.

### <a name="custom-text-to-speech"></a>Aangepaste tekst-naar-spraak

Aangepaste tekst-naar-spraak, ook wel bekend als aangepaste spraak, is een set online hulpprogram ma's waarmee u een herken bare, een-van-een-stem kunt maken voor uw merk. Het maken en beheren van aangepaste spraak modellen zonder code is beschikbaar via de [aangepaste Voice Portal](https://aka.ms/customvoice). Zodra het aangepaste spraak model is gepubliceerd, kan het worden gebruikt door de spraak-SDK.

## <a name="get-the-speech-sdk"></a>De Speech SDK ophalen

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Browser](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een gratis Azure-account maken](https://azure.microsoft.com/free/cognitive-services/)
* [Zie spraak herkennen in C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)