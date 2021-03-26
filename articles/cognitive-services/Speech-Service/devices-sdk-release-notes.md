---
title: SDK-documentatie voor speech-apparaten
titleSuffix: Azure Cognitive Services
description: De release opmerkingen bieden een logboek van updates, verbeteringen, fout oplossingen en wijzigingen aan de SDK voor spraak apparaten. Dit artikel wordt bijgewerkt met elke release van de SDK voor spraak apparaten.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: f9215469b1436ce9dc95c140e6969366d80d1ced
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607130"
---
# <a name="release-notes-speech-devices-sdk"></a>Release opmerkingen: SDK voor spraak apparaten

De volgende secties bevatten een lijst met wijzigingen in de meest recente releases.

## <a name="speech-devices-sdk-1150"></a>Speech-apparaten SDK 1.15.0:

- Geüpgraded naar nieuwe micro soft audio stack (MAS) met verbeterde beamforming en ruis reductie voor spraak.
- De binaire grootte wordt met 70% verminderd, afhankelijk van het doel.
- Ondersteuning voor [Azure percept-audio](../../azure-percept/overview-azure-percept-audio.md) met [binaire versie](https://aka.ms/sdsdk-download-APAudio).
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.15.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-1110"></a>Speech-apparaten SDK 1.11.0:

- Ondersteuning voor [wille keurige microfoon matrix geometrie](how-to-devices-microphone-array-configuration.md) en het instellen van de werk hoek via een [configuratie bestand](https://aka.ms/sdsdk-micarray-json).
- Ondersteuning voor [URBETTER DDK](http://www.urbetter.com/products_56/278.html).
- Uitgebrachte binaire bestanden voor de [GGEC-spreker](https://aka.ms/sdsdk-download-speaker) die wordt gebruikt in het voor beeld van de [Voice-assistent](https://aka.ms/sdsdk-speaker).
- Uitgebrachte binaire bestanden voor [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) en [Linux arm 64](https://aka.ms/sdsdk-download-linux-arm64) voor Raspberry Pi en vergelijk bare apparaten.
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.11.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-190"></a>Speech-apparaten SDK 1.9.0:

- De initiële binaire bestanden voor [URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) worden gegeven.
- Roobo v1 maakt nu gebruik van Maven voor de Speech SDK
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.9.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-170"></a>Speech-apparaten SDK 1.7.0:

- Linux ARM wordt nu ondersteund.
- Er worden initiële binaire bestanden voor [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) gegeven (Linux ARM64).
- Windows-gebruikers kunnen `AudioConfig.fromDefaultMicrophoneInput()` of gebruiken `AudioConfig.fromMicrophoneInput(deviceName)` om de microfoon op te geven die moet worden gebruikt.
- De grootte van de tape wisselaar is geoptimaliseerd.
- Ondersteuning voor multi-turn-herkenning met hetzelfde spraak/intentie Recognizer-object.
- Een incidenteel probleem oplossen waarbij het proces niet meer reageert tijdens het stoppen van de herkenning.
- Voor beeld-apps bevatten nu een voor beeld van een deel nemers. eigenschappen bestand om de indeling van het bestand te demonstreren.
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.7.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-160"></a>Speech-apparaten SDK 1.6.0:

- Ondersteuning voor [Azure KINECT DK](https://azure.microsoft.com/services/kinect-dk/) op Windows en Linux met een gemeen schappelijke [voorbeeld toepassing](./speech-devices-sdk.md)
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.6.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-151"></a>Speech-apparaten SDK 1.5.1:

- Neem de [conversatie-transcriptie](./conversation-transcription.md) op in de voor beeld-app.
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.5.1. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech-apparaten SDK 1.5.0:2019-mei release

- De SDK voor spraak apparaten is nu beschikbaar en is niet langer een geteste preview.
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.5.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.
- Nieuwe trefwoord technologie brengt belang rijke verbeteringen met zich mee.
- Nieuwe pijp lijn voor de verwerking van audio voor verbeterde herkenning van het ver-veld.

**Wijzigingen die fouten veroorzaken**

- Vanwege de nieuwe trefwoord technologie moeten alle tref woorden opnieuw worden gemaakt in onze verbeterde trefwoord Portal. Oude tref woorden volledig verwijderen van het apparaat verwijderen van de oude app.
  - ADB uninstall com. micro soft. cognitiveservices. speech. samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech-apparaten SDK 1.4.0:2019-apr release

- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.4.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech-apparaten SDK 1.3.1:2019-mrt release

- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.3.1. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.
- De verwerking van tref woorden is bijgewerkt, zie wijzigingen afbreken.
- Voorbeeld toepassing voegt taal keuze voor zowel spraak herkenning als omzetting toe.

**Wijzigingen die fouten veroorzaken**

- Het [installeren van een tref woord](./custom-keyword-basics.md) is vereenvoudigd, het maakt nu deel uit van de app en vereist geen afzonderlijke installatie op het apparaat.
- De herkenning van het tref woord is gewijzigd en twee gebeurtenissen worden ondersteund.
  - `RecognizingKeyword,` Hiermee wordt aangegeven dat het resultaat van de spraak tekst (niet-geverifieerd) is.
  - `RecognizedKeyword`geeft aan dat het sleutel woord herkenning heeft opgegeven dat het sleutel woord is herkend.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech-apparaten SDK 1.1.0:2018-nov release

- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.1.0. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.
- De nauw keurigheid van spraak herkenning in het verre veld is verbeterd met ons uitgebreide algoritme voor geluids verwerking.
- Voorbeeld toepassing toegevoegd Chinese spraak herkenning ondersteuning.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech-apparaten SDK 1.0.1:2018-okt release

- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 1.0.1. Zie de [release opmerkingen](./releasenotes.md)voor meer informatie.
- Nauw keurigheid van spraak herkenning is verbeterd met onze verbeterde algoritme voor geluids verwerking
- Er is een probleem met een continue opname van audio sessie opgelost.

**Wijzigingen die fouten veroorzaken**

- Met deze release wordt een aantal belang rijke wijzigingen geïntroduceerd. Raadpleeg [Deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie over de api's.
- De bestanden voor het herkennings model voor het tref woord zijn niet compatibel met Speech-apparaten SDK 1.0.1. De bestaande trefwoord bestanden worden verwijderd nadat de nieuwe trefwoord bestanden naar het apparaat zijn geschreven.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech-apparaten SDK 0.5.0:2018-aug release

- De nauw keurigheid van spraak herkenning is verbeterd door een fout in de code voor audio verwerking te corrigeren.
- Het [Speech SDK](./speech-sdk.md) -onderdeel is bijgewerkt naar versie 0.5.0. Zie de [release opmerkingen](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)voor meer informatie.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech-apparaten SDK 0.2.12733:2018-mei release

De eerste open bare preview-versie van de SDK voor Cognitive Services speech-apparaten.