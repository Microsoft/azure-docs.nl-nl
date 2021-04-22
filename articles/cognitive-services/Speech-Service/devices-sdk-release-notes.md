---
title: Documentatie voor Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: De release-opmerkingen bieden een logboek met updates, verbeteringen, oplossingen voor fouten en wijzigingen in de Speech Devices SDK. Dit artikel wordt bijgewerkt met elke release van de Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 5214d914c104fdf6df302c7879230bba2b3d2928
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891838"
---
# <a name="release-notes-speech-devices-sdk"></a>Opmerkingen bij de release: Speech Devices SDK

In de volgende secties worden wijzigingen in de meest recente releases vermeld.

## <a name="speech-devices-sdk-1160"></a>Speech Devices SDK 1.16.0:

- [Github-probleem opgelost #22](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues/22).
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.16.0. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-1150"></a>Speech Devices SDK 1.15.0:

- Bijgewerkt naar nieuwe Microsoft Audio Stack (MAS) met verbeterde balken en ruisvermindering voor spraak.
- De binaire grootte is met wel 70% verlaagd, afhankelijk van het doel.
- Ondersteuning voor [Azure Percept Audio met](../../azure-percept/overview-azure-percept-audio.md) binaire [release](https://aka.ms/sdsdk-download-APAudio).
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.15.0. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-1110"></a>Speech Devices SDK 1.11.0:

- Ondersteuning voor [willekeurige microfoon matrixgeometratrices](how-to-devices-microphone-array-configuration.md) en het instellen van de werkhoek via een [configuratiebestand](https://aka.ms/sdsdk-micarray-json).
- Ondersteuning voor [Urbetter DDK](http://www.urbetter.com/products_56/278.html).
- Er zijn binaire bestanden uitgebracht voor de [GGEC-spreker die](https://aka.ms/sdsdk-download-speaker) wordt gebruikt in ons [Spraakassistent-voorbeeld.](https://aka.ms/sdsdk-speaker)
- Binaire bestanden uitgebracht voor [Linux ARM32 en](https://aka.ms/sdsdk-download-linux-arm32) [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) voor Raspberry Pi en vergelijkbare apparaten.
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.11.0. Zie de opmerkingen bij de [release voor meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-190"></a>Speech Devices SDK 1.9.0:

- Er worden initiële binaire [bestanden voor Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) verstrekt.
- Roobo v1 gebruikt nu Maven voor de Speech SDK
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.9.0. Zie de opmerkingen bij de [release voor meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-170"></a>Speech Devices SDK 1.7.0:

- Linux ARM wordt nu ondersteund.
- Initiële binaire bestanden [voor Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) worden opgegeven (Linux ARM64).
- Windows-gebruikers kunnen `AudioConfig.fromDefaultMicrophoneInput()` of gebruiken om de microfoon op te geven die moet worden `AudioConfig.fromMicrophoneInput(deviceName)` gebruikt.
- De grootte van de bibliotheek is geoptimaliseerd.
- Ondersteuning voor herkenning met meerdere draaiingen met hetzelfde spraak-/intentieherkenningsobject.
- Los af en toe een probleem op waarbij het proces niet meer reageert tijdens het stoppen van de herkenning.
- Voorbeeld-apps bevatten nu een voorbeeldbestand participants.properties om de indeling van het bestand te demonstreren.
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.7.0. Zie de opmerkingen bij de [release voor meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

- Ondersteuning [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) windows en Linux met algemene [voorbeeldtoepassing](./speech-devices-sdk.md)
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.6.0. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

- Neem [gesprektranscriptie op](./conversation-transcription.md) in de voorbeeld-app.
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.5.1. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0: release van 2019-mei

- Speech Devices SDK is nu ga en geen gated preview meer.
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.5.0. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)
- Nieuwe sleutelwoordtechnologie brengt aanzienlijke kwaliteitsverbeteringen met zich mee. Zie Belangrijke wijzigingen.
- Nieuwe pijplijn voor audioverwerking voor verbeterde ver-veldherkenning.

**Wijzigingen die fouten veroorzaken**

- Vanwege de nieuwe sleutelwoordtechnologie moeten alle trefwoorden opnieuw worden gemaakt in onze verbeterde sleutelwoordportal. Als u oude trefwoorden volledig wilt verwijderen van het apparaat, verwijdert u de oude app.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0: 2019-Apr release

- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.4.0. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1: release van 2019-maart

- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.3.1. Zie de release-opmerkingen voor [meer informatie.](./releasenotes.md)
- Bijgewerkte verwerking van trefwoorden. Zie Belangrijke wijzigingen.
- Voorbeeldtoepassing voegt de taalkeuze toe voor zowel spraakherkenning als vertaling.

**Wijzigingen die fouten veroorzaken**

- [Het installeren van een trefwoord](./custom-keyword-basics.md) is vereenvoudigd en maakt nu deel uit van de app en heeft geen afzonderlijke installatie op het apparaat nodig.
- De herkenning van trefwoorden is gewijzigd en er worden twee gebeurtenissen ondersteund.
  - `RecognizingKeyword,` geeft aan dat het spraakresultaat trefwoordtekst bevat (niet-geverifieerd).
  - `RecognizedKeyword`, geeft aan dat sleutelwoordherkenning is voltooid met het herkennen van het opgegeven trefwoord.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0: release van 2018-November

- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.1.0. Zie de opmerkingen bij de [release voor meer informatie.](./releasenotes.md)
- De nauwkeurigheid van Far Field Speech-herkenning is verbeterd met ons verbeterde algoritme voor audioverwerking.
- Voorbeeldtoepassing: ondersteuning voor Chinese spraakherkenning toegevoegd.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1: release van 2018-okt

- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 1.0.1. Zie de opmerkingen bij [de release voor meer informatie.](./releasenotes.md)
- De nauwkeurigheid van spraakherkenning wordt verbeterd met ons verbeterde algoritme voor audioverwerking
- Er is een fout in een audiosessie met continue herkenning opgelost.

**Wijzigingen die fouten veroorzaken**

- Met deze release worden een aantal belangrijke wijzigingen geïntroduceerd. Raadpleeg deze [pagina voor](https://aka.ms/csspeech/breakingchanges_1_0_0) meer informatie over de API's.
- De bestanden van het model voor sleutelwoordherkenning zijn niet compatibel met Speech Devices SDK 1.0.1. De bestaande sleutelwoordbestanden worden verwijderd nadat de nieuwe sleutelwoordbestanden naar het apparaat zijn geschreven.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0: release van 2018 aug

- Verbeterde nauwkeurigheid van spraakherkenning door een fout in de audioverwerkingscode op te verhelpen.
- Het [Speech SDK-onderdeel](./speech-sdk.md) is bijgewerkt naar versie 0.5.0. Zie de release-opmerkingen voor [meer informatie.](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733: 2018 -Mei release

De eerste openbare preview-versie van de Cognitive Services Speech Devices SDK.