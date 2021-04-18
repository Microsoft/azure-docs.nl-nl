---
title: Gebruik Video Indexer om automatisch gesproken talen te identificeren - Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe Video Indexer taalidentificatiemodel wordt gebruikt om automatisch de gesproken taal in een video te identificeren.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600634"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>De gesproken taal automatisch identificeren met het taalidentificatiemodel

Video Indexer ondersteunt automatische taalidentificatie (LID). Dit is het proces van het automatisch identificeren van de gesproken taal van audio en het verzenden van het mediabestand dat moet worden getranscrimineerd in de dominante geïdentificeerde taal. 

Momenteel ondersteunt LID: Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chinees, Japans, Russisch en Portugees (Brazilië). 

Lees de sectie [Richtlijnen en beperkingen](#guidelines-and-limitations) hieronder.

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatische taalidentificatie kiezen bij indexeren

Wanneer u een video indexeert of [opnieuw indexeert](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) met behulp van de API, kiest u `auto detect` de optie in de parameter `sourceLanguage` .

Wanneer u de portal gebruikt, gaat u naar uw **accountvideo's** op de Video Indexer startpagina en houdt u de muisaanwijzer boven de naam van de video die u opnieuw wilt indexeren. [](https://www.videoindexer.ai/) Klik in de rechteronderhoek op de knop Opnieuw indexeren. Kies in **het dialoogvenster Video opnieuw indexeren** de optie Automatisch *detecteren* in de vervolgkeuzevak **Videobrontaal.**

![automatisch detecteren](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Uitvoer van model

Video Indexer de video transcriberen volgens de meest waarschijnlijke taal als het vertrouwen voor die taal `> 0.6` is. Als de taal niet met vertrouwen kan worden geïdentificeerd, wordt ervan uitgenomen dat de gesproken taal Engels is. 

De dominante taal van het model is beschikbaar in de JSON van inzichten als het `sourceLanguage` kenmerk (onder root/videos/insights). Een bijbehorende betrouwbaarheidsscore is ook beschikbaar onder het `sourceLanguageConfidence` kenmerk .

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

## <a name="guidelines-and-limitations"></a>Richtlijnen en beperkingen

* Automatische taalidentificatie (LID) ondersteunt de volgende talen: 

    Engels, Spaans, Frans, Duits, Italiaans, Mandarijns- en Japans, Russisch en Portugees (Brazilië).
* Hoewel Video Indexer (Modern Standard en Levantine), Hindi en Koreaans ondersteunt, worden deze talen niet ondersteund in LID.
* Als de audio andere talen bevat dan de bovenstaande ondersteunde lijst, is het resultaat onverwacht.
* Als Video Indexer taal niet kan identificeren met een hoge betrouwbaarheid ( `>0.6` ), is de terugvaltaal Engels.
* Er is momenteel geen ondersteuning voor bestand met audio in gemengde talen. Als de audio gemengde talen bevat, is het resultaat onverwacht. 
* Audio van lage kwaliteit kan van invloed zijn op de modelresultaten.
* Voor het model is ten minste één minuut spraak in de audio vereist.
* Het model is ontworpen om een conversatiespraak te herkennen (niet spraakopdrachten, zoals een programma, enzovoort).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht](video-indexer-overview.md)
* [Inhoud in meerdere talen automatisch identificeren en transcriberen](multi-language-identification-transcription.md)
