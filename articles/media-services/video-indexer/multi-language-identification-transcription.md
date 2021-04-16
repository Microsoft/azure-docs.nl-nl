---
title: Automatisch inhoud in meerdere talen identificeren en transcriberen met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp wordt gedemonstreerd hoe u inhoud in meerdere talen automatisch identificeert en transcribert met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532917"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Inhoud in meerdere talen automatisch identificeren en transcriberen

Video Indexer ondersteunt automatische taalidentificatie en transcriptie in inhoud in meerdere talen. Dit proces omvat het automatisch identificeren van de gesproken taal in verschillende audiosegmenten, het verzenden van elk segment van het mediabestand dat moet worden transcriberen en het combineren van de transcriptie terug naar één uniforme transcriptie. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Meertalige identificatie kiezen bij indexeren met portal

U kunt detectie **in meerdere talen kiezen bij** het uploaden en indexeren van uw video. U kunt ook detectie in **meerdere talen kiezen wanneer**  u uw video opnieuw indexeert. In de volgende stappen wordt beschreven hoe u opnieuw indexeert:

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Ga naar de **pagina Bibliotheek** en beweeg de muisaanwijzer over de naam van de video die u opnieuw wilt indexeren. 
1. Klik in de rechteronderhoek op de knop **Video opnieuw indexeren.** 
1. Kies in **het dialoogvenster Video opnieuw indexeren** de optie Detectie van meerdere talen in de vervolgkeuzevak  **Videobrontaal.**

    * Wanneer een video wordt geïndexeerd als meerdere talen, bevat de inzichtpagina die optie en wordt er een extra inzichttype weergegeven, zodat de gebruiker kan zien welk segment is getrscribeerd in welke taal 'Gesproken taal'.
    * Vertaling naar alle talen is volledig beschikbaar via de transcriptie in meerdere talen.
    * Alle andere inzichten worden weergegeven in de gedetecteerde mastertaal. Dit is de taal die het meest in de audio werd weergegeven.
    * Ondertiteling op de speler is ook beschikbaar in meerdere talen.

![Portalervaring](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Meertalige identificatie kiezen bij indexeren met API

Wanneer u een video [indexeert of opnieuw indexeert](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) met behulp van de API, kiest u `multi-language detection` de optie in de parameter `sourceLanguage` .

### <a name="model-output"></a>Uitvoer van model

Het model haalt alle talen op die in de video in één lijst zijn gedetecteerd

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Daarnaast bevat elk exemplaar in de transcriptiesectie de taal waarin het is transcriberen

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Richtlijnen en beperkingen

* Set ondersteunde talen: Engels, Frans, Duits, Spaans.
* Ondersteuning voor inhoud in meerdere talen met maximaal drie ondersteunde talen.
* Als de audio andere talen bevat dan de bovenstaande ondersteunde lijst, is het resultaat onverwacht.
* Minimale segmentlengte die voor elke taal moet worden gedetecteerd: 15 seconden.
* De offset voor taaldetectie is gemiddeld 3 seconden.
* Spraak is naar verwachting continu. Regelmatige verschillen tussen talen kunnen van invloed zijn op de prestaties van het model.
* Spraak van niet-native sprekers kan invloed hebben op de prestaties van het model (bijvoorbeeld wanneer sprekers hun eigen sen gebruiken en ze overschakelen naar een andere taal).
* Het model is ontworpen voor het herkennen van een conversatiespraak met redelijke audio-akoestiek (geen spraakopdrachten, tijdens de uitvoering, enzovoort).
* Het maken en bewerken van projecten is momenteel niet beschikbaar voor video's in meerdere talen.
* Aangepaste taalmodellen zijn niet beschikbaar bij het gebruik van detectie in meerdere talen.
* Het toevoegen van trefwoorden wordt niet ondersteund.
* Bij het exporteren van ondertitelingsbestanden wordt de taalindicatie niet weergegeven.
* De api voor transcriptie van updates biedt geen ondersteuning voor bestanden in meerdere talen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
