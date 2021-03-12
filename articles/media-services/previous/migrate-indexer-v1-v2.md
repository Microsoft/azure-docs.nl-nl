---
title: Migreren van Indexeer functie v1 en v2 naar Azure Media Services Video Indexer | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u migreert van Azure Media Indexer v1 en v2 naar Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: eb4f662a4b34d748da0e97da9b64a386be3f5c49
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016420"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migreren van Media Indexer en Media Indexer 2 naar Video Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Het wordt aanbevolen dat klanten migreren vanuit Indexeer functie v1 en Indexeer functie v2 naar het gebruik van de [Media Services v3 AudioAnalyzerPreset Basic-modus](../latest/analyzing-video-audio-files-concept.md). De media processor van [Azure media indexer](media-services-index-content.md) media en [Azure media indexer 2 Preview](./legacy-components.md) worden buiten gebruik gesteld. Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Azure Media Services Video Indexer is gebaseerd op Azure Media Analytics, Azure Cognitive Search, Cognitive Services (zoals de Face-API, micro soft Translator, de Computer Vision-API en Custom Speech Service). Hiermee kunt u inzichten ophalen uit uw video's met Video Indexer-modellen voor audio en video. Zie [video indexer video-en audio modellen](../video-indexer/video-indexer-overview.md)om te zien welke scenario's video indexer kunnen worden gebruikt in, welke functies worden aangeboden en hoe u aan de slag kunt gaan. 

U kunt inzichten uit uw video-en audio bestanden extra heren met behulp van de [Azure Media Services v3 Analyzer-voor instellingen](../latest/analyzing-video-audio-files-concept.md) of rechtstreeks met behulp van de [video indexer-api's](https://api-portal.videoindexer.ai/). Er is momenteel een overlap ping tussen de functies die worden geboden door de Video Indexer Api's en de Media Services v3-Api's.

> [!NOTE]
> Bekijk het [vergelijkings document](../video-indexer/compare-video-indexer-with-media-services-presets.md)om inzicht te krijgen in de verschillen tussen de voor instellingen van Video Indexer versus Media Services Analyzer.

In dit artikel worden de stappen beschreven voor het migreren van Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migratieopties

|Als dat nodig is  |dan |
|---|---|
|een oplossing die een spraak-naar-tekst transcriptie biedt voor elke indeling van een media bestand in een ondertitelings bestand met de volgende indelingen: VTT, SRT of TTML<br/>evenals extra geluids inzichten zoals tref woorden, afnemen van onderwerpen, akoestische gebeurtenissen, sprekers diarization, extractie en vertaling van entiteiten| werk uw toepassingen bij om de mogelijkheden van Azure Video Indexer te gebruiken via de Video Indexer v2-REST API of de Azure Media Services v3-audio analyse vooraf.|
|functies voor spraak naar tekst| Gebruik de Cognitive Services Speech API direct.|  

## <a name="getting-started-with-video-indexer"></a>Aan de slag met Video Indexer

In het volgende gedeelte vindt u relevante koppelingen: [Hoe ga ik aan de slag met video indexer?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Aan de slag met Media Services v3-Api's

Met Azure Media Services v3 API kunt u inzichten uit uw video-en audio bestanden ophalen via de [voor instellingen van de Azure Media Services v3 Analyzer](../latest/analyzing-video-audio-files-concept.md).

Met **AudioAnalyzerPreset** kunt u meerdere geluids inzichten uit een audio-of video bestand ophalen. De uitvoer bevat een VTT-of TTML-bestand voor de transcripten van de audio en een JSON-bestand (met alle extra audio inzichten). De geluids inzichten bevatten tref woorden, het indexeren van sprekers en de analyse van spraak sentiment. AudioAnalyzerPreset biedt ook ondersteuning voor taal detectie voor specifieke talen. Zie [trans formaties](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)voor gedetailleerde informatie.

### <a name="get-started"></a>Aan de slag

Om aan de slag te gaan, gaat u naar:

* [Zelfstudie](../latest/analyze-videos-tutorial-with-api.md)
* Voor beelden van AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) of [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Voor beelden van VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) of [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Aan de slag met Cognitive Services speech Services

[Azure Cognitive Services](../../cognitive-services/index.yml) biedt een service voor spraak naar tekst waarmee audio stromen naar tekst in realtime worden getranscribeerd die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. U kunt spraak naar tekst gebruiken om [uw eigen akoestische model, taal model of uitspraak model](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)aan te passen. Zie [Cognitive Services voor spraak naar tekst](../../cognitive-services/speech-service/speech-to-text.md)voor meer informatie. 

> [!NOTE] 
> De spraak-naar-tekst-service heeft geen video bestands indelingen en heeft alleen [bepaalde audio-indelingen](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Zie [Wat is spraak-naar-tekst?](../../cognitive-services/speech-service/speech-to-text.md) voor meer informatie over de tekst-naar-spraak-service en hoe u aan de slag kunt gaan.

## <a name="known-differences-from-deprecated-services"></a>Bekende verschillen van afgeschafte Services

U zult merken dat Video Indexer, Azure Media Services v3 AudioAnalyzerPreset en Cognitive Services speech services-Services betrouwbaarder zijn en betere kwaliteit levert dan de buiten gebruik gestelde Azure Media Indexer 1 en Azure Media Indexer 2-processors.  

Enkele bekende verschillen zijn onder andere:

* Cognitive Services speech Services biedt geen ondersteuning voor het uitpakken van tref woorden. Video Indexer en Media Services v3 AudioAnalyzerPreset bieden echter een robuustere set sleutel woorden in JSON-bestands indeling.

## <a name="support"></a>Ondersteuning

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)