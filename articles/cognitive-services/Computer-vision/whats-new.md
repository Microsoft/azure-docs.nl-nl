---
title: Wat is er nieuw in Computer Vision?
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat nieuws over Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 33987be39258adc74cf4f88dbb0544f7026f6086
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183350"
---
# <a name="whats-new-in-computer-vision"></a>Wat is er nieuw in Computer Vision?

Meer informatie over nieuwe functies in de service. Dit kunnen opmerkingen bij de release, video's, blogposts en andere soorten informatie zijn. Voeg een bladwijzer toe voor deze pagina om up-to-date te blijven over de service.

## <a name="january-2021"></a>Januari 2021

### <a name="spatial-analysis-container-update"></a>Update van de container voor ruimtelijke analyse

Er is een nieuwe versie van de [container voor ruimtelijke analyse](spatial-analysis-container.md) vrijgegeven met een nieuwe functieset. Met deze Docker-container kunt u realtime streaming-video analyseren om inzicht te krijgen in de ruimtelijke relaties tussen mensen en hun bewegingen door fysieke omgevingen. 

* [Bewerkingen voor ruimtelijke analyse](spatial-analysis-operations.md) kunnen nu worden geconfigureerd om te detecteren of een persoon gezichtsbedekkende bescherming draagt, zoals een masker. 
    * Voor de bewerkingen `personcount`, `personcrossingline` en `personcrossingpolygon` kan een maskerclassificatie worden ingeschakeld door parameter `ENABLE_FACE_MASK_CLASSIFIER` te configureren.
    * De kenmerken `face_mask` en `face_noMask` worden geretourneerd als metagegevens met een betrouwbaarheidsscore voor elke persoon die in de videostroom wordt gedetecteerd


## <a name="october-2020"></a>Oktober 2020

### <a name="computer-vision-api-v31-ga"></a>Algemene beschikbaarheid van Computer Vision API v3.1

De Computer Vision-API in Algemene beschikbaarheid is bijgewerkt naar v3.1.

## <a name="september-2020"></a>September 2020

### <a name="spatial-analysis-container-preview"></a>Ruimtelijke analyse-container (preview)

De [ruimtelijke analyse-container](spatial-analysis-container.md) is nu beschikbaar als preview-versie. Met de functie voor ruimtelijke analyse van Computer Vision kunt u realtime streaming-video analyseren om inzicht te krijgen in de ruimtelijke relaties tussen mensen en hun verplaatsing door fysieke omgevingen. Ruimtelijke analyse is een Docker-container die u on-premises kunt gebruiken. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Met de openbare preview-versie van Lees-API v3.1 wordt OCR voor Japans toegevoegd
Lees-API v3.1 (openbare preview-versie) voegt de volgende functies toe:
* OCR voor de Japanse taal
* Geef voor elke tekstregel aan of het met de hand geschreven of gedrukte tekst is, samen met een betrouwbaarheidsscore (alleen Latijnse talen).
* Extraheer alleen tekst voor de geselecteerde pagina's of het paginabereik voor een document met meerdere pagina's.

* Deze preview-versie van de lees-API ondersteunt de talen Engels, Nederlands, Frans, Duits, Italiaans, Japans, Portugees, vereenvoudigd Chinees en Spaans.

Zie het [Lees-API: overzicht](concept-recognizing-text.md) voor meer informatie.

> [!div class="nextstepaction"]
> [Meer informatie over de Lees-API v3.1 (openbare preview-versie 2)](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juli 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Lees-API v3.1 (openbare preview-versie) met OCR voor vereenvoudigd Chinees
Lees-API v3.1 (openbare preview-versie) voegt ondersteuning toe voor vereenvoudigd Chinees van Computer Vision.

* Deze preview-versie van de lees-API ondersteunt de talen Engels, Nederlands, Frans, Duits, Italiaans, Portugees, vereenvoudigd Chinees en Spaans.

Zie het [Lees-API: overzicht](concept-recognizing-text.md) voor meer informatie.

> [!div class="nextstepaction"]
> [Meer informatie over de Lees-API v3.1 (openbare preview-versie 1)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Mei 2020
Computer Vision API v3.0 is nu algemeen beschikbaar, met updates voor de [lees-API](concept-recognizing-text.md):

* Ondersteuning voor Engels, Nederlands, Frans, Duits, Italiaans, Portugees en Spaans
* Verbeterde nauwkeurigheid
* Betrouwbaarheidsscore voor elk geëxtraheerd woord
* Nieuwe uitvoerindeling

## <a name="march-2020"></a>Maart 2020

* TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen bij deze service. Zie [Beveiliging van Azure Cognitive Services](../cognitive-services-security.md) voor meer informatie.

## <a name="january-2020"></a>Januari 2020

### <a name="read-api-30-public-preview"></a>Openbare preview van lees-API 3.0

U hebt nu de mogelijkheid om versie 3.0 van de lees-API te gebruiken voor het extraheren van gedrukte of handgeschreven tekst uit afbeeldingen. Vergeleken met eerdere versies biedt 3.0 de volgende mogelijkheden:
* Verbeterde nauwkeurigheid
* Nieuwe uitvoerindeling
* Betrouwbaarheidsscore voor elk geëxtraheerd woord
* Ondersteuning voor Spaans en Engels met de extra taalparameter

Volg een [quickstart over tekstextractie](./quickstarts/csharp-hand-text.md?tabs=version-3) om aan de slag te gaan met de 3.0-API.

## <a name="cognitive-service-updates"></a>Updates van Cognitive Services

[Meldingen van Azure-updates voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)