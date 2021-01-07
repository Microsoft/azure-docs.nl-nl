---
title: Inleiding tot Computer Vision ruimtelijke analyse
titleSuffix: Azure Cognitive Services
description: In dit document worden de basis concepten en functies van een Computer Vision container voor ruimtelijke analyse beschreven.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 158a5e5f859749ec2ca20bfa4783fe32cc17ee0e
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964606"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Inleiding tot Computer Vision ruimtelijke analyse

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

## <a name="example-use-cases-for-spatial-analysis"></a>Voor beelden van use cases voor ruimtelijke analyse

Hieronder ziet u enkele voor beelden van gevallen waarin we de ruimtelijke analyse hadden ontworpen en getest.

**Naleving van sociale Distancing** : een kantoor ruimte heeft verschillende camera's die ruimtelijke analyse gebruiken om de naleving van sociale Distancing te bewaken door de afstand tussen mensen te meten. De faciliteiten beheerder kan Heatmaps gebruiken om statistische statistieken over de sociale distancing-naleving in de loop van de tijd weer te geven om de werk ruimte aan te passen en de sociale distancing eenvoudiger te maken.

Inkoop **analyse** : een boodschappen archief maakt gebruik van camera's die op product schermen worden weer gegeven om de impact van de merchandising wijzigingen in het opslag verkeer te meten. Met het systeem kan de Store Manager bepalen welke nieuwe producten het meest worden gewijzigd in engagement.

**Wachtrij beheer** : camera's die zijn opgenomen in wacht rijen voor uitchecken bieden waarschuwingen aan managers wanneer de wacht tijd te lang duurt, waardoor ze meer regels kunnen openen. Historische gegevens over het afbreken van de wachtrij bieden inzicht in het gedrag van de consument.

**Naleving van gezichts masker** : in retail winkels kunnen camera's worden gebruikt die verwijzen naar de Store-voor waarden om te controleren of klanten die in de Store worden onderzocht, gezichts maskers hebben om de veiligheid te garanderen en statistische gegevens te analyseren om inzicht te krijgen in trends in het masker gebruik. 

**Buil ding & Analysis** : een kantoor gebouw maakt gebruik van camera's die zijn gericht op ingangen op basis van belang rijke ruimten om Footfall te meten en hoe mensen de werk plek gebruiken. Met Insights kan de bouw Manager service en lay-out aanpassen om inzittenden beter te kunnen bedienen.

**Minimale mede werkers detectie** : in een Data Center kunnen camera's een activiteit rond servers bewaken. Wanneer werk nemers een fysiek herstel van gevoelige apparatuur hebben, moeten ze om veiligheids redenen altijd aanwezig zijn tijdens de reparatie. Camera's worden gebruikt om te controleren of deze richt lijn wordt gevolgd.

**Bewerkings optimalisatie** : in een snel en informe restaurant worden camera's in de keuken gebruikt voor het genereren van geaggregeerde informatie over werk stroom van workflow. Dit wordt door managers gebruikt voor het verbeteren van processen en training voor het team.

## <a name="considerations-when-choosing-a-use-case"></a>Overwegingen bij het kiezen van een use-case

**Kritieke veiligheids waarschuwingen voor komen** : ruimtelijke analyse is niet ontworpen voor kritieke veiligheids waarschuwingen in realtime. Het mag niet worden vertrouwd voor scenario's waarin real-time waarschuwingen nodig zijn om de interventie te voor komen, zoals het uitschakelen van een zware machine wanneer er een persoon aanwezig is. Het kan worden gebruikt voor risico beperking met statistieken en interventies om riskant gedrag te beperken, zoals mensen die een beperkt/verboden gebied invoeren.

**Vermijd het gebruik van beslissingen met betrekking tot werk nemers** : ruimtelijke analyse biedt Probabilistic metrische gegevens over de locatie en verplaatsing van mensen binnen een ruimte. Deze gegevens kunnen nuttig zijn voor het verbeteren van de proces verbetering, maar de gegevens zijn geen goede indicator van de prestaties van afzonderlijke werk nemers en mogen niet worden gebruikt voor het maken van beslissingen met betrekking tot het werk.

**Vermijd het gebruik van beslissingen met betrekking tot gezondheids zorg** : ruimtelijke analyse biedt Probabilistic en gedeeltelijke gegevens met betrekking tot de bewegingen van personen. De gegevens zijn niet geschikt voor het maken van aan status gerelateerde beslissingen.

**Vermijd het gebruik in beveiligde Spaces** : Beveilig de privacy van personen door de camera locaties en posities te evalueren, de hoeken en de regio van de interesses aan te passen, zodat ze geen beveiligde gebieden zoals restrooms hebben.

**Overweeg zorgvuldig te gebruiken in scholen of** betrouwbaar zorg faciliteiten: ruimtelijke analyse heeft niet sterk getest met gegevens die minder jarigen bevatten dan 18 jaar of volwassenen in leeftijd van 65. We raden klanten aan om de fout tarieven voor hun scenario grondig te evalueren in omgevingen waarin deze leeftijden predominate.

**Overweeg zorgvuldig te gebruiken in open bare ruimten** : Evalueer camera locaties en-posities, pas de hoeken en de regio van belangen aan om de verzameling van open bare ruimten te minimaliseren. Verlichting en weers omstandigheden in open bare ruimten, zoals straten en parken, zijn aanzienlijk van invloed op de prestaties van het ruimtelijke analyse systeem en het is zeer moeilijk om effectief openbaar making te bieden in open bare ruimten.

## <a name="spatial-analysis-gating-for-public-preview"></a>Ruimtelijke analyse beperking voor open bare preview

Om ervoor te zorgen dat ruimtelijke analyse wordt gebruikt voor scenario's waarvoor het is ontworpen, maken we deze technologie beschikbaar voor klanten via een toepassings proces. Als u toegang wilt krijgen tot ruimtelijke analyse, moet u beginnen met het invullen van ons online innemen formulier. [Start hier uw toepassing](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

De toegang tot de open bare preview van ruimtelijke analyse is onderhevig aan de enige keuze van micro soft, gebaseerd op onze geschiktheids criteria, het hebben-proces en beschik baarheid om een beperkt aantal klanten te ondersteunen tijdens deze geteste preview. In de open bare preview kijken we naar klanten die een belang rijke relatie met micro soft hebben, zijn geïnteresseerd in het samen werken met ons aan de aanbevolen gebruiks voorbeelden en aanvullende scenario's die in overeenstemming zijn met onze verantwoordelijke AI-toezeg gingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kenmerken en beperkingen voor ruimtelijke analyse](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)
