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
ms.openlocfilehash: e42096fc32a504ae329d3b179004b6a123de4469
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365634"
---
# <a name="whats-new-in-computer-vision"></a>Wat is er nieuw in Computer Vision?

Meer informatie over nieuwe functies in de service. Dit kunnen opmerkingen bij de release, video's, blogposts en andere soorten informatie zijn. Voeg een bladwijzer toe voor deze pagina om up-to-date te blijven over de service.

## <a name="april-2021"></a>April 2021

### <a name="computer-vision-v32-ga"></a>Computer Vision v3.2 GA

De Computer Vision API v3.2 is nu algemeen beschikbaar met de volgende updates:
* Verbeterd model voor het taggen van afbeeldingen: analyseert visuele inhoud en genereert relevante tags op basis van objecten, acties en inhoud die in de afbeelding worden weergegeven. Dit is beschikbaar via de [Tag Image-API.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) Zie de handleiding en het [overzicht van Afbeeldingsanalyse](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) [voor](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) meer informatie.
* Bijgewerkt model voor inhoudsbeheer: detecteert de aanwezigheid van inhoud voor volwassenen en geeft vlaggen om afbeeldingen met inhoud voor volwassenen, racy en gory visuals te filteren. Dit is beschikbaar via de [Analyse-API.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) Zie de handleiding en het [overzicht van Afbeeldingsanalyse](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) [voor](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) meer informatie.
* [OCR (Lezen) is beschikbaar voor 73](./language-support.md#optical-character-recognition-ocr) talen, waaronder vereenvoudigd en traditioneel Chinees, Japans, Koreaans en Latijns.
* [OCR (Lezen)](./overview-ocr.md) is ook beschikbaar als [een distroloze container](./computer-vision-how-to-install-containers.md?tabs=version-3-2) voor on-premises implementatie.

> [!div class="nextstepaction"]
> [Zie Computer Vision v3.2 GA](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>Maart 2021

### <a name="computer-vision-32-public-preview-update"></a>Computer Vision 3.2 Openbare preview-update

De Computer Vision API v3.2 openbare preview is bijgewerkt. De preview-versie bevat alle Computer Vision samen met bijgewerkte Read and Analyze API's.

> [!div class="nextstepaction"]
> [Zie Computer Vision versie 3.2 van openbare preview 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Februari 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Openbare preview van Read API v3.2 met OCR-ondersteuning voor 73 talen
Computer Vision de openbare preview van de Read API v3.2, beschikbaar als cloudservice en Docker-container, bevat de volgende updates:
* [OCR voor 73 talen,](./language-support.md#optical-character-recognition-ocr) waaronder vereenvoudigd en traditioneel Chinees, Japans, Koreaans en Latijns.
* Natuurlijke leesorde voor de tekstregeluitvoer (alleen Latijnse talen)
* Classificatie van handschriftstijl voor tekstregels, samen met een betrouwbaarheidsscore (alleen Latijnse talen).
* Extraheren van alleen tekst voor geselecteerde pagina's voor een document met meerdere pagina's.
* Beschikbaar als een [distroloze container](./computer-vision-how-to-install-containers.md?tabs=version-3-2) voor on-premises implementatie.

Zie de [handleiding api lezen voor](Vision-API-How-to-Topics/call-read-api.md) meer informatie.

> [!div class="nextstepaction"]
> [De openbare preview van read-API v3.2 gebruiken](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Januari 2021

### <a name="spatial-analysis-container-update"></a>Containerupdate voor ruimtelijke analyse

Er is een nieuwe versie [van de spatial analysis-container](spatial-analysis-container.md) uitgebracht met een nieuwe functieset. Met deze Docker-container kunt u realtime streaming-video analyseren om inzicht te krijgen in de ruimtelijke relaties tussen mensen en hun bewegingen door fysieke omgevingen. 

* [Ruimtelijke analysebewerkingen](spatial-analysis-operations.md) kunnen nu worden geconfigureerd om te detecteren of een persoon een beschermend gezicht, zoals een masker, draagt. 
    * Voor de bewerkingen `personcount`, `personcrossingline` en `personcrossingpolygon` kan een maskerclassificatie worden ingeschakeld door parameter `ENABLE_FACE_MASK_CLASSIFIER` te configureren.
    * De kenmerken `face_mask` en `face_noMask` worden geretourneerd als metagegevens met een betrouwbaarheidsscore voor elke persoon die in de videostroom wordt gedetecteerd
* De *personcrossingpolygon-bewerking* is uitgebreid om de berekening toe te staan van de tijd die een persoon in een zone doorgeeft. U kunt de parameter in de zoneconfiguratie voor de bewerking instellen op en een nieuwe gebeurtenis van het `type` `zonedwelltime` type *personZoneDwellTimeEvent* bevat het veld dat wordt gevuld met het aantal milliseconden dat de persoon in de zone heeft `durationMs` uitgegeven.
* **Wijziging die een** wijziging doorbreekt: de naam van de *gebeurtenis personZoneEvent* is gewijzigd in *personZoneEnterExitEvent.* Deze gebeurtenis wordt veroorzaakt door de *personcrossingpolygon-bewerking* wanneer een persoon de zone binnenkomt of verlaat en directionele informatie biedt met de genummerde kant van de zone die is kruist.
* De URL van de video kan in alle bewerkingen worden opgegeven als 'Privéparameter/verdukt'. Ondesprekbaar maken is nu optioneel en werkt alleen als `KEY` en worden opgegeven als `IV` omgevingsvariabelen.
* Kalibratie is standaard ingeschakeld voor alle bewerkingen. Stel de in `do_calibration: false` om dit uit te schakelen.
* Ondersteuning toegevoegd voor automatische hercalibratie (standaard uitgeschakeld) via de parameter . Raadpleeg Spatial Analysis operations (Bewerkingen voor ruimtelijke `enable_recalibration` [analyse)](./spatial-analysis-operations.md) voor meer informatie
* Kalibratieparameters voor de `DETECTOR_NODE_CONFIG` camera. Raadpleeg Bewerkingen [voor ruimtelijke analyse voor](./spatial-analysis-operations.md) meer informatie.


## <a name="october-2020"></a>Oktober 2020

### <a name="computer-vision-api-v31-ga"></a>Algemene beschikbaarheid van Computer Vision API v3.1

De Computer Vision-API in Algemene beschikbaarheid is bijgewerkt naar v3.1.

## <a name="september-2020"></a>September 2020

### <a name="spatial-analysis-container-preview"></a>Voorbeeld van ruimtelijke analysecontainer

De [ruimtelijke analysecontainer](spatial-analysis-container.md) is nu beschikbaar als preview-versie. Met de functie ruimtelijke analyse van Computer Vision kunt u realtime streamingvideo analyseren om inzicht te krijgen in ruimtelijke relaties tussen personen en hun verplaatsing door fysieke omgevingen. Ruimtelijke analyse is een Docker-container die u on-premises kunt gebruiken. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Met de openbare preview-versie van Lees-API v3.1 wordt OCR voor Japans toegevoegd
Lees-API v3.1 (openbare preview-versie) voegt de volgende functies toe:
* OCR voor de Japanse taal
* Geef voor elke tekstregel aan of het met de hand geschreven of gedrukte tekst is, samen met een betrouwbaarheidsscore (alleen Latijnse talen).
* Extraheer alleen tekst voor de geselecteerde pagina's of het paginabereik voor een document met meerdere pagina's.

* Deze preview-versie van de lees-API ondersteunt de talen Engels, Nederlands, Frans, Duits, Italiaans, Japans, Portugees, vereenvoudigd Chinees en Spaans.

Zie de [handleiding api lezen voor](Vision-API-How-to-Topics/call-read-api.md) meer informatie.

> [!div class="nextstepaction"]
> [Meer informatie over de Lees-API v3.1 (openbare preview-versie 2)](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juli 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Lees-API v3.1 (openbare preview-versie) met OCR voor vereenvoudigd Chinees
Lees-API v3.1 (openbare preview-versie) voegt ondersteuning toe voor vereenvoudigd Chinees van Computer Vision.

* Deze preview-versie van de lees-API ondersteunt de talen Engels, Nederlands, Frans, Duits, Italiaans, Portugees, vereenvoudigd Chinees en Spaans.

Zie de [handleiding api lezen voor](Vision-API-How-to-Topics/call-read-api.md) meer informatie.

> [!div class="nextstepaction"]
> [Meer informatie over de Lees-API v3.1 (openbare preview-versie 1)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Mei 2020
Computer Vision API v3.0 is algemeen beschikbaar, met updates voor de Read-API:

* Ondersteuning voor Engels, Nederlands, Frans, Duits, Italiaans, Portugees en Spaans
* Verbeterde nauwkeurigheid
* Betrouwbaarheidsscore voor elk geëxtraheerd woord
* Nieuwe uitvoerindeling

Zie overzicht [van OCR](overview-ocr.md) voor meer informatie.

## <a name="march-2020"></a>Maart 2020

* TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen bij deze service. Zie [Beveiliging van Azure Cognitive Services](../cognitive-services-security.md) voor meer informatie.

## <a name="january-2020"></a>Januari 2020

### <a name="read-api-30-public-preview"></a>Openbare preview van lees-API 3.0

U hebt nu de mogelijkheid om versie 3.0 van de lees-API te gebruiken voor het extraheren van gedrukte of handgeschreven tekst uit afbeeldingen. Vergeleken met eerdere versies biedt 3.0 de volgende mogelijkheden:
* Verbeterde nauwkeurigheid
* Nieuwe uitvoerindeling
* Betrouwbaarheidsscore voor elk geëxtraheerd woord
* Ondersteuning voor Spaans en Engels met de extra taalparameter

Volg een [quickstart over tekstextractie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) om aan de slag te gaan met de 3.0-API.

## <a name="cognitive-service-updates"></a>Updates van Cognitive Services

[Meldingen van Azure-updates voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
