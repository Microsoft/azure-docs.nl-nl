---
title: Gebruik Video Indexer om gesp roken talen automatisch te identificeren-Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe het Video Indexer language Identification model wordt gebruikt om automatisch de gesp roken taal in een video te identificeren.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687126"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>De gesproken taal automatisch identificeren met het taalidentificatiemodel

Video Indexer ondersteunt automatische taal identificatie (deksel). Dit is het proces van het automatisch identificeren van de gesp roken taal van de audio en het verzenden van het Media bestand dat moet worden getranscribeerd in de taal die als dominant is geïdentificeerd. 

De huidige deksel ondersteunt: Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chinees, Japans, Russisch en Portugees (Brazilië). 

Controleer de sectie [richt lijnen en beperkingen](#guidelines-and-limitations) hieronder.

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatische taal identificatie voor indexering kiezen

Wanneer u een video met behulp van de API indexeert of [opnieuw indexeert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) , kiest u de `auto detect` optie in de `sourceLanguage` para meter.

Wanneer u Portal gebruikt, gaat u naar uw **account Video's** op de start pagina van [video indexer](https://www.videoindexer.ai/) en houdt u de muis aanwijzer over de naam van de video die u opnieuw wilt indexeren. Klik in de rechter bovenhoek op de knop opnieuw indexeren. Kies in het dialoog venster **video opnieuw indexeren** de optie *Automatische detectie* in de vervolg keuzelijst **video bron taal** .

![automatische detectie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Uitvoer van model

Video Indexer transcribeert de video aan de hand van de meest waarschijnlijke taal als het vertrouwen voor die taal is `> 0.6` . Als de taal niet met vertrouwen kan worden geïdentificeerd, wordt ervan uitgegaan dat de gesp roken taal Engels is. 

De dominante taal van het model is beschikbaar in de JSON Insights als het `sourceLanguage` kenmerk (onder hoofdmap/Video's/inzichten). Er is ook een bijbehorende betrouwbaarheids Score beschikbaar onder het- `sourceLanguageConfidence` kenmerk.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Richt lijnen en beperkingen

* Automatische taal identificatie (deksel) ondersteunt de volgende talen: 

    Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chin, Japans, Russisch en Portugees (Brazilië).
* Hoewel Video Indexer Arabisch (modern Standard en Levantine), Hindi en Koreaans ondersteunt, worden deze talen niet ondersteund in het deksel.
* Als de audio andere talen dan de hierboven vermelde lijst bevat, is het resultaat onverwacht.
* Als Video Indexer de taal niet kan identificeren met een hoge mate van betrouw baarheid ( `>0.6` ), is de terugval taal Engels.
* Er is geen huidige ondersteuning voor een bestand met audio voor gemengde talen. Als de audio gemengde talen bevat, is het resultaat onverwacht. 
* Audio van lage kwaliteit kan van invloed zijn op de model resultaten.
* Het model vereist ten minste één minuut spraak op de audio.
* Het model is ontworpen om een spontane spreek spraak te herkennen (geen spraak opdrachten, zingen, enzovoort).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht](video-indexer-overview.md)
* [Inhoud in meerdere talen automatisch identificeren en transcriberen](multi-language-identification-transcription.md)
