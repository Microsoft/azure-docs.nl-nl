---
title: Wat is Computer Vision?
titleSuffix: Azure Cognitive Services
description: De Computer Vision-service geeft u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision-toepassingen, computer vision-service
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286131"
---
# <a name="what-is-computer-vision"></a>Wat is Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Computer Vision-service van Azure geeft u toegang tot geavanceerde algoritmen waarmee afbeeldingen worden verwerkt en informatie wordt geretourneerd op basis van de visuele kenmerken waarin u geïnteresseerd bent. 

| Service|Beschrijving|
|---|---|
|[Optische tekenherkenning (OCR)](overview-ocr.md)|De OCR-service (Optical Character Recognition) haalt tekst uit afbeeldingen op. U kunt de nieuwe Lees-API gebruiken om gedrukte en handgeschreven tekst uit Foto's en documenten te extra heren. Het maakt gebruik van uitgebreide modellen en werkt met tekst op verschillende Opper vlakken en achtergronden. Dit zijn onder andere bedrijfs documenten, facturen, bevestigingen, posters, visite kaartjes, brieven en White boards. De OCR-Api's bieden ondersteuning voor het extra heren van gedrukte tekst in [verschillende talen](./language-support.md). Volg de [Quick](quickstarts-sdk/client-library.md) start van OCR om aan de slag te gaan.|
|[Analyse van de afbeelding](overview-image-analysis.md)| De installatie kopie analyse service extraheert veel visuele functies van afbeeldingen, zoals objecten, gezichten, inhoud voor volwassenen en automatisch gegenereerde tekst beschrijvingen. Volg de [Snelstartgids](quickstarts-sdk/image-analysis-client-library.md) om aan de slag te gaan.|
| [Ruimtelijke analyse](intro-to-spatial-analysis-public-preview.md)| De spatiale analyse service analyseert de aanwezigheid en verplaatsing van personen in een video-feed en produceert gebeurtenissen waarmee andere systemen kunnen reageren. Installeer de [container voor ruimtelijke analyse](spatial-analysis-container.md) om aan de slag te gaan.|

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision voor beheer van digitale assets

Met Computer Vision kunt u tal van DAM-scenario's (Digitaal Asset Management) ondersteunen. DAM is het bedrijfsproces voor het organiseren, opslaan en ophalen van geavanceerde media-assets en het beheren van digitale rechten en machtigingen. Het is bijvoorbeeld mogelijk dat een bedrijf afbeeldingen wil groeperen en identificeren op basis van zichtbare logo's, gezichten, objecten, kleuren, enzovoort. U kunt ook automatisch [bijschriften voor afbeeldingen genereren](./Tutorials/storage-lab-tutorial.md) en trefwoorden toevoegen zodat ze zoekbaar zijn. Zie de handleiding [Knowledge Mining Solution Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) (Oplossingsverbetering voor kennisanalyse) op GitHub voor een alles-in-eenoplossing met Cognitive Services, Azure Cognitive Search en intelligente rapportage. Zie de opslagplaats voor [Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) (Oplossingssjablonen van Computer Vision) voor andere DAM-voorbeelden.

## <a name="image-requirements"></a>Vereisten voor installatiekopieën

Met Computer Vision kunt u afbeeldingen analyseren die voldoen aan de volgende vereisten:

- De afbeelding moet worden weergegeven in de JPEG-, PNG-, GIF- of BMP-indeling
- De afbeelding moet kleiner zijn dan 4 megabyte (MB)
- De afmetingen van de afbeelding moet groter zijn dan 50 x 50 pixels
  - Voor de Read-API moet de afbeelding tussen de 50 x 50 en 10000 x 10000 pixels groot zijn.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg een Snelstartgids om een service te implementeren en uit te voeren in uw voorkeurs taal voor ontwikkeling.

* [Snelstartgids: optische teken herkenning (OCR)](quickstarts-sdk/client-library.md)
* [Snelstartgids: afbeeldings analyse](quickstarts-sdk/image-analysis-client-library.md)
* [Snelstartgids: container voor ruimtelijke analyse](spatial-analysis-container.md)
