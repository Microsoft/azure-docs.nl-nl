---
title: Video Indexer scènes, opnamen en keyframes
titleSuffix: Azure Media Services
description: In dit onderwerp wordt een overzicht gegeven van de Video Indexer, opnamen en keyframes.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532420"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, opnamen en sleutelframes

Video Indexer het segmenteren van video's in tijdelijke eenheden op basis van structurele en semantische eigenschappen. Op deze manier kunnen klanten eenvoudig bladeren, beheren en hun video-inhoud bewerken op basis van verschillende granulariteiten. Bijvoorbeeld op basis van scènes, opnamen en keyframes, zoals beschreven in dit onderwerp.   

![Scènes, opnamen en sleutelframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scènedetectie  
 
Video Indexer bepaalt wanneer een scène in de video wordt gewijzigd op basis van visuele aanwijzingen. Een scène geeft één gebeurtenis weer en bestaat uit een reeks opeenvolgende opnamen, die semantisch gerelateerd zijn. Een scèneminiature is het eerste sleutelframe van de onderliggende opname. Video indexer segmenteert een video in scènes op basis van kleurseherentie tussen opeenvolgende opnamen en haalt de begin- en eindtijd van elke scène op. Scènedetectie wordt beschouwd als een uitdagende taak omdat het gaat om het kwantificeren van semantische aspecten van video's.

> [!NOTE]
> Dit is van toepassing op video's die ten minste drie scènes bevatten.

## <a name="shot-detection"></a>Opnamedetectie

Video Indexer bepaalt wanneer een opname in de video wordt gewijzigd op basis van visuele aanwijzingen, door zowel plotselinge als geleidelijke overgangen bij te houden in het kleurenschema van aangrenzende frames. De metagegevens van de opname bevatten een begin- en eindtijd, evenals de lijst met sleutelframes die in die opname zijn opgenomen. De opnamen zijn opeenvolgende frames die tegelijkertijd van dezelfde camera zijn genomen.

## <a name="keyframe-detection"></a>Detectie van sleutelframes

Video Indexer selecteert u de frame(s) die het beste voor elke opname staan. Keyframes zijn de representatieve frames die in de hele video zijn geselecteerd op basis van vormgevingseigenschappen (bijvoorbeeld contrast en stabiliteit). Video Indexer haalt een lijst met keyframe-ID's op als onderdeel van de metagegevens van de opname, op basis waarvan klanten het sleutelframe kunnen extraheren als afbeelding met een hoge resolutie.  

### <a name="extracting-keyframes"></a>Sleutelframes extraheren

Als u sleutelframes met hoge resolutie voor uw video wilt extraheren, moet u eerst de video uploaden en indexeren.

![Hoofdframes](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Met de Video Indexer website

Als u keyframes wilt extraheren met behulp van Video Indexer website, uploadt en indexeert u uw video. Zodra de indexerings taak is voltooid, klikt u op de **knop Downloaden** en selecteert u **Artefacten (ZIP)**. Hiermee downloadt u de map artefacten naar uw computer. 

![Schermopname van de vervolgkeuze met 'Artefacten' geselecteerd.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Open de map uit en uit. In de *_KeyframeThumbnail* map vindt u alle sleutelframes die uit uw video zijn geëxtraheerd. 

#### <a name="with-the-video-indexer-api"></a>Met de Video Indexer-API

Als u keyframes wilt op halen met behulp van Video Indexer API, uploadt en indexeert u uw video met behulp van de [aanroep Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) uploaden. Zodra de indexerings job is voltooid, roept u [Get Video Index aan.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) Hiermee krijgt u alle inzichten die u hebt Video Indexer uit uw inhoud in een JSON-bestand.  

U krijgt een lijst met keyframe-ID's als onderdeel van de metagegevens van elke opname. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

U moet nu elk van deze sleutelframe-ID's uitvoeren op de [aanroep Miniaturen](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) krijgen. Hiermee downloadt u elk van de keyframe-afbeeldingen naar uw computer. 

## <a name="editorial-shot-type-detection"></a>Detectie van het type in een hoofdartikel

Keyframes zijn gekoppeld aan opnamen in de uitvoer-JSON. 

Het type opname dat is gekoppeld aan een afzonderlijke opname in de JSON-inzichten vertegenwoordigt het type van de hoofdartikels. Mogelijk vindt u deze kenmerken van het schermafbeeldingstype nuttig bij het bewerken van video's in fragmenten, clip of bij het zoeken naar een specifieke stijl van keyframe voor speciale doeleinden. De verschillende typen worden bepaald op basis van de analyse van het eerste sleutelframe van elke opname. Opnamen worden geïdentificeerd door de schaal, grootte en locatie van de gezichten die in hun eerste sleutelframe worden weergegeven. 

De grootte en schaal van de foto worden bepaald op basis van de afstand tussen de camera en de gezichten die in het frame worden weergegeven. Met behulp van deze eigenschappen Video Indexer de volgende opnametypen gedetecteerd:

* Breed: toont het hele lichaam van een persoon.
* Gemiddeld: toont het hoofd- en gezichts hoofd- en gezichtsgezicht van een persoon.
* Close-up: toont voornamelijk het gezicht van een persoon.
* Uiterst close-up: toont het gezicht van een persoon die het scherm vult. 

Opgenomen typen kunnen ook worden bepaald door de locatie van de onderwerptekens met betrekking tot het midden van het frame. Deze eigenschap definieert de volgende opnametypen in Video Indexer:

* Linkergezicht: er wordt een persoon weergegeven aan de linkerkant van het frame.
* Middelste gezicht: een persoon wordt weergegeven in de centrale regio van het frame.
* Rechtergezicht: er wordt een persoon aan de rechterkant van het frame weergegeven.
* Buiten: een persoon wordt weergegeven in een buitenomgeving.
* Indoor: een persoon wordt weergegeven in een indoorinstelling.

Aanvullende kenmerken:

* Twee opnamen: toont gezichten van twee personen van gemiddelde grootte.
* Meerdere gezichten: meer dan twee personen.


## <a name="next-steps"></a>Volgende stappen

[De uitvoer Video Indexer de API bekijken](video-indexer-output-json-v2.md#scenes)
