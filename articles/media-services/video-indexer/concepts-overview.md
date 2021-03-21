---
title: Video Indexer concepten-Azure
titleSuffix: Azure Media Services Video Indexer
description: Dit artikel bevat een kort overzicht van Azure Media Services Video Indexer terminologie en concepten.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633854"
---
# <a name="video-indexer-concepts"></a>Video Indexer concepten

Dit artikel bevat een kort overzicht van Azure Media Services Video Indexer terminologie en concepten.

## <a name="audiovideocombined-insights"></a>Audio/video/gecombineerde inzichten

Wanneer u uw Video's uploadt naar Video Indexer, analyseert deze zowel visuele elementen als audio door verschillende AI-modellen uit te voeren. Als Video Indexer analyseert u uw video, de inzichten die worden geëxtraheerd door de AI-modellen. Zie [overzicht](video-indexer-overview.md)voor meer informatie.

## <a name="confidence-scores"></a>Betrouwbaarheids scores

Met de betrouwbaarheids score wordt het vertrouwen in een inzicht aangeduid. Het is een getal tussen 0,0 en 1,0. Hoe hoger de score, hoe groter het vertrouwen in het antwoord is. Bijvoorbeeld: 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Inhouds toezicht

Gebruik tekstuele en visuele toezicht modellen voor inhoud om uw gebruikers veilig te houden van ongepaste inhoud en te controleren of de inhoud die u publiceert, overeenkomt met de waarden van uw organisatie. U kunt bepaalde Video's automatisch blok keren of uw gebruikers waarschuwen over de inhoud. Zie voor meer informatie [inzichten: visuele en tekstuele inhoud toezicht](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Block   

Blokken zijn bedoeld om het gemakkelijker te maken om de gegevens te door lopen. Een blok kan bijvoorbeeld worden opgesplitst op basis van wanneer er tussen sprekers wordt gewisseld of als er een lange pauze plaatsvindt.  

## <a name="project-and-editor"></a>Project en editor

Met de [video indexer](https://www.videoindexer.ai/) website kunt u de grondige inzichten van uw video gebruiken om: Zoek de juiste media-inhoud, zoek de onderdelen die u wilt gebruiken en gebruik de resultaten om een volledig nieuw project te maken. Nadat het project is gemaakt, kan het worden gerenderd en gedownload van Video Indexer en worden gebruikt in uw eigen bewerkings toepassingen of downstream-werk stromen.

Enkele scenario's waarin deze functie nuttig is, zijn: 

* Film hooglichten voor aanhang wagens maken.
* Het gebruik van oude video clips in Nieuws casts.
* Kortere inhoud maken voor sociale media.

Zie [Editor gebruiken om projecten te maken](use-editor-create-project.md)voor meer informatie.

## <a name="keyframes"></a>Keyframes

Video Indexer selecteert de frame (s) die elke opname het beste vertegenwoordigen. Keyframes zijn de representatieve frames die zijn geselecteerd uit de volledige video op basis van esthetische eigenschappen (bijvoorbeeld contrast en stabiele gegevens). Zie [scènes, afbeeldingen en keyframes](scenes-shots-keyframes.md)voor meer informatie.

## <a name="time-range-vs-adjusted-time-range"></a>tijds bereik versus gecorrigeerd tijd bereik   

Time Range is het tijds bereik in de oorspronkelijke video. AdjustedTimeRange is het tijds bereik ten opzichte van de huidige afspeel lijst. Omdat u een afspeel lijst kunt maken op basis van verschillende lijnen van verschillende Video's, kunt u een video van 1 uur nemen en er slechts één regel van gebruiken, bijvoorbeeld 10:00-10:15. In dat geval hebt u een afspeel lijst met 1 regel, waarbij het tijds bereik 10:00-10:15 is, maar de adjustedTimeRange is 00:00-00:15. 

## <a name="widgets"></a>Widgets

Video Indexer ondersteunt het insluiten van widgets in uw apps. Zie [video indexer widgets insluiten in uw apps](video-indexer-embed-widgets.md)voor meer informatie.

## <a name="summarized-insights"></a>Overzicht van inzichten  

Samenvatd Insights bevatten een geaggregeerde weer gave van de gegevens: gezichten, topics, emoties. Zo kunt u in plaats van elke duizenden Peri odes te gaan gebruiken en te controleren welke gezichten er zich bevinden. het overzicht bevat alle gezichten en voor elk van de getoonde inzichten, het tijds bereik dat wordt weer gegeven in en het percentage van de tijd dat het wordt weer gegeven.  

## <a name="next-steps"></a>Volgende stappen

- [overzicht](video-indexer-overview.md)
- [Inzichten](video-indexer-output-json-v2.md)
