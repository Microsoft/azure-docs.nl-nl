---
title: Wat is ruimtelijke analyse?
titleSuffix: Azure Cognitive Services
description: In dit document worden de basis concepten en functies van een Computer Vision container voor ruimtelijke analyse beschreven.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284539"
---
# <a name="what-is-spatial-analysis"></a>Wat is ruimtelijke analyse?

De spatiale analyse service helpt organisaties de waarde van hun fysieke ruimten te maximaliseren door de bewegingen en aanwezigheid van mensen binnen een bepaald gebied te weten te komen. Zo kunt u video opnemen van CCTV of surveillance camera's, AI-bewerkingen uitvoeren om inzichten uit de videostreams op te halen en gebeurtenissen te genereren die door andere systemen moeten worden gebruikt. Met invoer uit een camera stroom kan een AI-bewerking het aantal mensen dat een ruimte binnenkomt, tellen of de naleving van het gezichts masker en de distancing-richt lijnen meten.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Wat het doet

De kern bewerkingen van ruimtelijke analyse zijn allemaal gebaseerd op een pijp lijn die video opneemt, mensen in de video detecteert en de mensen bijhoudt wanneer ze in de loop van de tijd bewegen en gebeurtenissen genereren als mensen communiceren met interessante regio's.

## <a name="spatial-analysis-features"></a>Functies voor ruimtelijke analyse

| Functie | Definitie |
|------|------------|
| **Detectie van personen** | Dit onderdeel beantwoordt de vraag ' waar bevinden zich de personen in deze installatie kopie? ' Er worden personen in een installatie kopie gevonden en een selectie kader door gegeven met de locatie van elke persoon naar het onderdeel voor het bijhouden van personen. |
| **Bijhouden van personen** | Met dit onderdeel worden de detecties van mensen in de loop van de tijd in de buurt van elkaar uitgevoerd. Er wordt gebruikgemaakt van tijdelijke logica over hoe mensen doorgaans verplaatsen en basis informatie over het algehele uiterlijk van de personen. Er worden geen personen bijgehouden op meerdere camera's. Als een persoon het veld van de weer gave langer dan ongeveer een minuut verlaat en vervolgens de camera weergave weer inschakelt, wordt dit door het systeem als een nieuwe persoon beschouwd. Het bijhouden van personen identificeert geen unieke personen op verschillende camera's. Het maakt geen gebruik van gezichts herkenning of Gait tracking. |
| **Gezichts masker detectie** | Dit onderdeel detecteert de locatie van het gezicht van een persoon in het weergave veld van de camera en identificeert de aanwezigheid van een Face-masker. Met de AI-bewerking worden afbeeldingen uit video gescand; Wanneer een gezicht wordt gedetecteerd, biedt de service een omsluitend kader rondom het gezicht. Met behulp van de mogelijkheden voor object detectie wordt de aanwezigheid van gezichts maskers in het selectie kader aangeduid. Bij Detectie van gezichts maskers is het niet mogelijk om één gezicht te onderscheiden van een ander gezicht, het voors pellen of classificeren van gezichts kenmerken of het uitvoeren van gezichts herkenning. |
| **Interesse gebied** | Dit is een door de gebruiker gedefinieerde zone of regel in het invoer video frame. Wanneer een persoon communiceert met deze regio in de video, genereert het systeem een gebeurtenis. Voor de bewerking PersonCrossingLine is bijvoorbeeld een regel gedefinieerd in de video. Wanneer een persoon die lijn bijsnijdt, wordt een gebeurtenis gegenereerd. |
| **Gebeurtenis** | Een gebeurtenis is de primaire uitvoer van ruimtelijke analyse. Elke bewerking verzendt periodiek een specifieke gebeurtenis (bijvoorbeeld eenmaal per minuut) of wanneer een specifieke trigger plaatsvindt. De gebeurtenis bevat informatie over wat er is gebeurd in de invoer video, maar bevat geen afbeeldingen of video. De bewerking PeopleCount kan bijvoorbeeld een gebeurtenis met het bijgewerkte aantal genereren elke keer dat het aantal persoons wijzigingen (trigger) of één keer per minuut (periodiek) wordt uitgevoerd. |

## <a name="get-started"></a>Aan de slag

### <a name="public-preview-gating"></a>Open bare preview-beperking

Om ervoor te zorgen dat ruimtelijke analyse wordt gebruikt voor scenario's waarvoor het is ontworpen, maken we deze technologie beschikbaar voor klanten via een toepassings proces. Als u toegang wilt krijgen tot ruimtelijke analyse, moet u beginnen met het invullen van ons online innemen formulier. [Start hier uw toepassing](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

De toegang tot de open bare preview van ruimtelijke analyse is onderhevig aan de enige keuze van micro soft, gebaseerd op onze geschiktheids criteria, het hebben-proces en beschik baarheid om een beperkt aantal klanten te ondersteunen tijdens deze geteste preview. In de open bare preview kijken we naar klanten die een belang rijke relatie met micro soft hebben, zijn geïnteresseerd in het samen werken met ons aan de aanbevolen gebruiks voorbeelden en aanvullende scenario's die bij onze onderhanden AI-toezeg gingen blijven staan.

### <a name="follow-a-quickstart"></a>Een Snelstartgids volgen

Zodra u toegang hebt gekregen tot ruimtelijke analyse, volgt u de [Snelstartgids](spatial-analysis-container.md) om de container in te stellen en te beginnen met het analyseren van de video.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Verantwoordelijk gebruik van ruimtelijke analyse technologie

Zie de [Opmerking over transparantie](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)als u wilt weten hoe u een degelijke ruimtelijke analyse technologie kunt gebruiken. De transparantie notities van micro soft zijn bedoeld om u te helpen begrijpen hoe onze AI-technologie werkt, de keuzes van systeem eigen aren kunnen de prestaties en het gedrag van het systeem beïnvloeden en het belang van het hele systeem, met inbegrip van de technologie, de personen en de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstartgids: container voor ruimtelijke analyse](spatial-analysis-container.md)' ' ' ' ' ' '