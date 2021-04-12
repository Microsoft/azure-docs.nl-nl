---
title: Ruimtelijke analyse zone en regel plaatsing
titleSuffix: Azure Cognitive Services
description: Meer informatie over het instellen van zones en regels met ruimtelijke analyse
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: cfd3bc406407298c6daf7723cb684911d7c9a9cf
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284665"
---
# <a name="zone-and-line-placement-guide"></a>Hand leiding voor zone-en lijn plaatsing

Dit artikel bevat richt lijnen voor het definiëren van zones en regels voor ruimtelijke analyse bewerkingen om een nauw keurige analyse van de verplaatsingen van volkeren in een ruimte te krijgen. Dit geldt voor alle bewerkingen. 

Zones en regels worden gedefinieerd met behulp van de JSON-SPACEANALYSIS_CONFIG para meter. Zie het artikel over [ruimtelijke analyse bewerkingen](spatial-analysis-operations.md) voor meer informatie.

## <a name="guidelines-for-drawing-zones"></a>Richt lijnen voor teken zones

Houd er rekening mee dat elke spatie anders is. u moet de positie of grootte bijwerken, afhankelijk van uw behoeften.

Als u een specifieke sectie van de camera weergave wilt zien, maakt u de grootste zone die u kunt best rijken, die betrekking heeft op het specifieke vloer gebied waarin u bent geïnteresseerd, maar niet met andere gebieden waarin u niet geïnteresseerd bent. Dit verhoogt de nauw keurigheid van de verzamelde gegevens en voor komt fout-positieven van gebieden die u niet wilt bijhouden. Wees voorzichtig bij het plaatsen van de hoeken van uw veelhoek en zorg ervoor dat ze niet buiten het gebied vallen dat u wilt bijhouden.  

### <a name="example-of-a-well-shaped-zone"></a>Voor beeld van een goed gevormde zone

De zone moet groot genoeg zijn om te voorzien in drie mensen die aan elke rand zijn gericht en die op het betrokken gebied kunnen worden aangepast. Ruimtelijke analyse identificeert mensen die in de zone worden geplaatst, dus als u zones op de 2D-afbeelding tekent, stelt u de zone in als een tapijt op de vloer.

![Goed gevormde zone](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Voor beelden van zones die niet goed gevormd zijn

In de volgende voor beelden ziet u slecht gevormde zones. In deze voor beelden is het interesse gebied de ruimte die voor de *Game tijd* wordt weer gegeven.

**De zone bevindt zich niet op de vloer.**

![Slecht gevormde zone](./media/spatial-analysis/zone-not-on-floor.png) 

**De zone is te klein.**

![de zone is te klein](./media/spatial-analysis/zone-too-small.png)

**Zone het gebied rond de weer gave niet volledig vastlegt.**

![zone legt het gebied niet volledig vast over de eind markering](./media/spatial-analysis/zone-bad-capture.png)

**De zone bevindt zich te dicht bij de rand van de camera afbeelding en legt de juiste weer gave niet vast.**

![de zone bevindt zich te dicht bij de rand van de camera afbeelding en legt de juiste weer gave niet vast](./media/spatial-analysis/zone-edge.png)

**De zone wordt gedeeltelijk geblokkeerd door het schap, zodat mensen en vloeren niet volledig zichtbaar zijn.**

![de zone is gedeeltelijk geblokkeerd, zodat mensen niet volledig zichtbaar zijn](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Voor beeld van een goed gevormde lijn

De regel moet lang genoeg zijn om aan de volledige start te kunnen voldoen. Ruimtelijke analyse identificeert mensen met een footer die de lijn snijdt, dus wanneer u regels in de 2D-afbeelding tekent dat u ze wilt tekenen alsof ze zich op de vloer bevinden. 

Als dat mogelijk is, breidt u de lijn die breder is dan de werkelijke start uit. Als dit niet resulteert in extra kruisingen (zoals in de onderstaande afbeelding wanneer de lijn zich op een muur bevindt), breidt u deze uit.

![Goed gevormde lijn](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Voor beelden van lijnen die niet goed gevormd zijn

In de volgende voor beelden ziet u slecht gedefinieerde regels.

**De lijn bevindt zich niet op de volledige invoer van de vloer.**

![Lijn bevindt zich niet op de volledige invoer van de vloer](./media/spatial-analysis/zone-line-bad-coverage.png)

**De lijn is te hoog en bevindt zich niet helemaal in de lucht.**

![De lijn is te hoog en bevindt zich niet volledig van de deur](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Volgende stappen

* [Een web-app voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Ruimtelijke-analyse bewerkingen configureren](./spatial-analysis-operations.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Gids voor camera plaatsing](spatial-analysis-camera-placement.md)
