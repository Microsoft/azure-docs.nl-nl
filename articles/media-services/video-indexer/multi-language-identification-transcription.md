---
title: Meerdere talen inhoud automatisch identificeren en transcriberen met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp wordt gedemonstreerd hoe u inhoud met meerdere talen automatisch kunt identificeren en transcriberen met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015552"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Inhoud in meerdere talen automatisch identificeren en transcriberen

Video Indexer ondersteunt automatische taal identificatie en transcriptie in inhoud met meerdere talen. Dit proces omvat het automatisch identificeren van de gesp roken taal in verschillende segmenten van audio, waarbij elk segment van het Media bestand dat moet worden getranscribeerd, wordt verzonden en de transcriptie opnieuw kan worden gecombineerd tot één Unified transcriptie. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Meertalige identificatie voor indexering met portal kiezen

U kunt de **meertalige detectie** kiezen bij het uploaden en indexeren van uw video. U kunt ook een detectie op **meerdere talen**  kiezen wanneer u de video opnieuw wilt indexeren. In de volgende stappen wordt beschreven hoe u opnieuw indexeert:

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Ga naar de **bibliotheek** pagina en beweeg de muis aanwijzer over de naam van de video die u opnieuw wilt indexeren. 
1. Klik in de rechter bovenhoek op de knop **video opnieuw indexeren** . 
1. Kies in het dialoog venster **video opnieuw indexeren** de optie **detectie op meerdere talen** in de vervolg keuzelijst **video bron taal** .

    * Wanneer een video wordt geïndexeerd als meerdere talen, bevat de Insight-pagina deze optie en wordt er een extra inzicht type weer gegeven, zodat de gebruiker kan zien welk segment is getranscribeerd in welke taal ' gesp roken taal '.
    * De vertaling naar alle talen is volledig beschikbaar vanuit de transcripten in meerdere talen.
    * Alle andere inzichten worden weer gegeven in de gedetecteerde Master taal. Dit is de taal die het meest voor komt in de audio.
    * Ondertiteling in de speler is ook beschikbaar in meerdere talen.

![Portalervaring](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Meertalige identificatie voor indexering met API kiezen

Wanneer u een video indexeert of opnieuw [indexeert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) met behulp van de API, kiest u de `multi-language detection` optie in de `sourceLanguage` para meter.

### <a name="model-output"></a>Uitvoer van model

In het model worden alle talen opgehaald die in de video in één lijst zijn gedetecteerd

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Daarnaast bevat elke instantie in de sectie transcriptie de taal waarin deze is getranscribeerd

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

## <a name="guidelines-and-limitations"></a>Richt lijnen en beperkingen

* Set ondersteunde talen: Engels, Frans, Duits, Spaans.
* Ondersteuning voor inhoud met meerdere talen met Maxi maal drie ondersteunde talen.
* Als de audio andere talen dan de hierboven vermelde lijst bevat, is het resultaat onverwacht.
* Minimale segment lengte die moet worden gedetecteerd voor elke taal – 15 seconden.
* De taal detectie offset is 3 seconden gemiddeld.
* Spraak wordt naar verwachting doorlopend. Veelvuldige verschillen tussen talen kunnen de prestaties van modellen beïnvloeden.
* Spraak van niet-systeem eigen sprekers kan van invloed zijn op de prestaties van het model (bijvoorbeeld wanneer luid sprekers hun eigen tong gebruiken en ze overschakelen naar een andere taal).
* Het model is ontworpen om een spontane spreek spraak te herkennen met redelijke geluids akoestische (geen spraak opdrachten, zingen, enzovoort).
* Het maken en bewerken van het project is momenteel niet beschikbaar voor Video's met meerdere talen.
* Aangepaste taal modellen zijn niet beschikbaar wanneer u meerdere talen detectie gebruikt.
* Het toevoegen van tref woorden wordt niet ondersteund.
* Wanneer u ondertitelings bestanden exporteert, wordt de taal aanduiding niet weer gegeven.
* De update transcript-API biedt geen ondersteuning voor meerdere taal bestanden.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
