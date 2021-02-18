---
title: Overzicht van ruimtelijke analyse
titleSuffix: Azure Cognitive Services
description: In dit document worden de basis concepten en functies van een Computer Vision container voor ruimtelijke analyse beschreven.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575365"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Overzicht van Computer Vision ruimtelijke analyse

Computer Vision ruimtelijke analyse is een nieuwe functie van Azure Cognitive Services Computer Vision waarmee organisaties de waarde van hun fysieke ruimten kunnen maximaliseren door te zien wat de bewegingen en aanwezigheid van mensen binnen een bepaald gebied zijn. Zo kunt u video opnemen van CCTV of surveillance camera's, AI-bewerkingen uitvoeren om inzichten uit de videostreams op te halen en gebeurtenissen te genereren die door andere systemen moeten worden gebruikt. Met invoer uit een camera stroom kan een AI-bewerking het aantal mensen dat een ruimte binnenkomt, tellen of de naleving van het gezichts masker en de distancing-richt lijnen meten.

## <a name="the-basics-of-spatial-analysis"></a>De basis beginselen van ruimtelijke analyse

Vandaag de kern bewerkingen van ruimtelijke analyse zijn gebaseerd op een pijp lijn die video opneemt, mensen in de video detecteert en de mensen bijhoudt wanneer ze in de loop van de tijd worden verplaatst en gebeurtenissen worden gegenereerd als mensen communiceren met interessante regio's.

## <a name="spatial-analysis-terms"></a>Voor waarden voor ruimtelijke analyse

| Termijn | Definitie |
|------|------------|
| Detectie van personen | Dit onderdeel beantwoordt de vraag "waar bevinden zich de mensen in deze afbeelding"? Er wordt gezocht naar mensen in een installatie kopie en er wordt een omsluitend kader door gegeven met de locatie van elke persoon naar het onderdeel voor het bijhouden van personen. |
| Bijhouden van personen | Met dit onderdeel worden de detecties van mensen in de loop van de tijd verbonden, omdat de mensen zich voor een camera bewegen. Er wordt gebruikgemaakt van tijdelijke logica over hoe mensen doorgaans verplaatsen en basis informatie over het algehele uiterlijk van de mensen om dit te doen. Er worden geen personen bijgehouden op meerdere camera's. Als een persoon het veld met de weer gave van een camera langer dan ongeveer een minuut bevindt en vervolgens de camera weergave weer inschakelt, wordt dit door het systeem als een nieuwe persoon beschouwd. Het bijhouden van personen identificeert geen unieke personen op verschillende camera's. Het maakt geen gebruik van gezichts herkenning of Gait tracking. |
| Gezichts masker detectie | Dit onderdeel detecteert de locatie van het gezicht van een persoon in het weergave veld van de camera en identificeert de aanwezigheid van een Face-masker. Om dit te doen, scant de AI-bewerking afbeeldingen van video. Wanneer een gezicht wordt gedetecteerd, biedt de service een omsluitend kader rondom het gezicht. Met behulp van de mogelijkheden voor object detectie wordt de aanwezigheid van gezichts maskers in het selectie kader aangeduid. Bij Detectie van gezichts maskers is het niet mogelijk om één gezicht te onderscheiden van een ander gezicht, het voors pellen of classificeren van gezichts kenmerken of het uitvoeren van gezichts herkenning. |
| Interesse gebied | Dit is een zone of regel die is gedefinieerd in de video invoeren als onderdeel van de configuratie. Wanneer een persoon communiceert met de regio van de video, genereert het systeem een gebeurtenis. Voor de bewerking PersonCrossingLine is bijvoorbeeld een regel gedefinieerd in de video. Wanneer een persoon die lijn bijsnijdt, wordt een gebeurtenis gegenereerd. |
| Gebeurtenis | Een gebeurtenis is de primaire uitvoer van ruimtelijke analyse. Elke bewerking verzendt periodiek een specifieke gebeurtenis (bijvoorbeeld eenmaal per minuut) of wanneer een specifieke trigger optreedt. De gebeurtenis bevat informatie over wat er is gebeurd in de invoer video, maar bevat geen afbeeldingen of video. De bewerking PeopleCount kan bijvoorbeeld een gebeurtenis met het bijgewerkte aantal genereren elke keer dat het aantal persoons wijzigingen (trigger) of één keer per minuut (periodiek) wordt uitgevoerd. |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Verantwoordelijk gebruik van ruimtelijke analyse technologie

Zie de [Opmerking over transparantie](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)als u wilt weten hoe u een degelijke ruimtelijke analyse technologie kunt gebruiken. De transparantie notities van micro soft zijn bedoeld om u te helpen begrijpen hoe onze AI-technologie werkt, de keuzes van systeem eigen aren kunnen de prestaties en het gedrag van het systeem beïnvloeden en het belang van het hele systeem, met inbegrip van de technologie, de personen en de omgeving.

## <a name="spatial-analysis-gating-for-public-preview"></a>Ruimtelijke analyse beperking voor open bare preview

Om ervoor te zorgen dat ruimtelijke analyse wordt gebruikt voor scenario's waarvoor het is ontworpen, maken we deze technologie beschikbaar voor klanten via een toepassings proces. Als u toegang wilt krijgen tot ruimtelijke analyse, moet u beginnen met het invullen van ons online innemen formulier. [Start hier uw toepassing](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

De toegang tot de open bare preview van ruimtelijke analyse is onderhevig aan de enige keuze van micro soft, gebaseerd op onze geschiktheids criteria, het hebben-proces en beschik baarheid om een beperkt aantal klanten te ondersteunen tijdens deze geteste preview. In de open bare preview kijken we naar klanten die een belang rijke relatie met micro soft hebben, zijn geïnteresseerd in het samen werken met ons aan de aanbevolen gebruiks voorbeelden en aanvullende scenario's die in overeenstemming zijn met onze verantwoordelijke AI-toezeg gingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met container voor ruimtelijke analyse](spatial-analysis-container.md)