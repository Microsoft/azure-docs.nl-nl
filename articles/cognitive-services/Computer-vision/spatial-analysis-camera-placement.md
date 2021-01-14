---
title: Plaatsing van camera voor ruimtelijke analyse
titleSuffix: Azure Cognitive Services
description: Meer informatie over het instellen van een camera voor gebruik met ruimtelijke analyse
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 849afe54125588cb7664ee221e4f229e83be8f96
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185475"
---
# <a name="camera-placement-guide"></a>Gids voor camera plaatsing

In dit artikel vindt u aanbevelingen voor camera plaatsing voor ruimtelijke analyse (open bare preview). Het bevat algemene richt lijnen, evenals specifieke aanbevelingen voor de hoogte, hoek en camera-naar-focus punt-afstand voor alle opgenomen bewerkingen. 

> [!NOTE]
> Deze hand leiding is ontworpen voor de as M3045-V-camera. Deze camera gebruikt resolutie 1920, het horizontale veld weer gave van 106 graden, het verticale weergave veld van 59 graden en een vaste lengte van 2,8 mm. De onderstaande principes zijn van toepassing op alle camera's, maar specifieke richt lijnen rond de camera hoogte en camera-naar-brand punt afstand moeten worden aangepast voor gebruik met andere camera's. 

## <a name="general-guidelines"></a>Algemene richtlijnen

Houd rekening met de volgende algemene richt lijnen bij het plaatsen van camera's voor ruimtelijke analyse:

* **Belichtings hoogte.** Plaats camera's onder belichtings aansluitingen zodat de aansluitingen de camera's niet blok keren.
* **Obstakels.** Om te voor komen dat er camera weergaven worden weer gegeven, noteert u obstakels zoals palen, aantekening, rekken, wanden en bestaande LP-camera's.
* **Achtergrond verlichting.** Achtergrond verlichting heeft invloed op de kwaliteit van camera beelden. Vermijd het omleiden van camera's op externe Windows-en glas deuren om ernstige achtergrond verlichting te voor komen.
* **Lokale privacy-regels en-voor Schriften.** Lokale voor schriften kunnen beperken welke camera's kunnen vastleggen. Zorg ervoor dat u de lokale regels en voor Schriften begrijpt voordat u camera's plaatst.
* **Structuur maken.** AIRCO, sprinklers en bestaande bedrading kunnen de vaste montage van camera's beperken.
* **Kabel beheer.** Zorg ervoor dat u een Ethernet-kabel van geplande camera-koppelings locaties kunt routeren naar de Switch Power over Internet (PoE).

## <a name="height-focal-point-distance-and-angle"></a>Hoogte, brandpuntsafstand en hoek

U moet drie dingen overwegen wanneer u bepaalt hoe u een camera installeert voor ruimtelijke analyse:
- Camera hoogte
- Camera-naar-scherpere punt afstand
- De hoek van de camera ten opzichte van het vloer vlak

Het is ook belang rijk dat u weet in welke richting het meren deel van de mensen (aflopende richting) ten opzichte van het camera veld, indien mogelijk. Deze richting is belang rijk voor de systeem prestaties.

![Afbeelding van een persoon die in een richting doorloopt](./media/spatial-analysis/person-walking-direction.png)

In de volgende afbeelding ziet u de verhogings weergave voor de richting van de afronding van de persoon.

![Uitbrei ding en plan weergave](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Camera hoogte

Over het algemeen moeten camera's 12-14 poten van het wegdek worden bevestigd. Voor de detectie van gezichts masker raden we aan dat camera's 8-12 poten van het wegdek worden bevestigd. Wanneer u de camera in dit bereik hebt gekoppeld, kunt u obstakels (bijvoorbeeld rekken, hangende lichten, aflopende aanmeldingen en weer gaven) overwegen die van invloed kunnen zijn op de camera weergave en vervolgens de hoogte aanpassen als dat nodig is.

## <a name="camera-to-focal-point-distance"></a>Camera-naar-scherpere punt afstand

_Camera-naar-brandpunt afstand_ is de lineaire afstand van het brand punt (of het midden van de camera afbeelding) naar de camera die op het wegdek wordt gemeten.

![Camera-naar-focus punt-afstand](./media/spatial-analysis/camera-focal-point.png)

Deze afstand wordt gemeten op het vloer vlak.

![Hoe de camera-naar-focus punt-afstand wordt gemeten vanaf de vloer](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Van boven ziet het er als volgt uit:

![Hoe een camera-naar-scherp punt afstand wordt gemeten](./media/spatial-analysis/camera-focal-point-above.png)

Gebruik de onderstaande tabel om de afstand van de camera te bepalen van het brand punt op basis van de specifieke koppelings hoogte. Deze afstanden zijn voor optimale plaatsing. De tabel biedt richt lijnen onder de aanbeveling 12 '-14 ', aangezien sommige plafonds de hoogte kunnen beperken. Voor de detectie van gezichts maskers is de aanbevolen camera-naar-brand punt afstand (min/max) 4 '-10 ' voor de camera hoogte tussen 8 ' en 12 '.

| Camera hoogte | Camera-naar-brandpuntsafstand-punt-afstand (min/max) |  
| ------------- | ---------------------------------------- |  
| achtste            | 10 '-13 '                                  |  
| 6           | 7 '-13 '                                   |  
| 12           | 10 '-17 '                                  |  
| 15           | 11 '-18 '                                  |  
| 18           | 12 '-22 '                                  |  
| 20           | 15 '-30 '                                  |  

In de volgende afbeelding worden camera weergaven gesimuleerd op basis van de meest overeenkomende camera-naar-focus punt afstanden.

| Meeste                                      | Verder                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Dichtstbijzijnde afstand van camera-naar-scherpere punt](./media/spatial-analysis/focal-point-closest.png) | ![De meest linkse camera-naar-brand punt afstand](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Bereik van camera hoek koppelen

In deze sectie worden geldige bereiken voor het koppelen van camera hoeken beschreven. In deze koppel reeksen wordt het acceptabele bereik weer gegeven voor de optimale plaatsing.

### <a name="line-configuration"></a>Lijn configuratie

De volgende tabel bevat aanbevelingen voor camera's die zijn geconfigureerd voor de bewerking **cognitiveservices. Vision. spatialanalysis-personcrossingline** . Voor detectie van gezichts maskers is +/-30 graden de optimale camera montage hoek voor de camera hoogte tussen 8 en 12.

| Camera hoogte | Camera-naar-scherpere punt afstand | Optimale camera montage hoek (min/max) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| achtste            | 9,4                             | +/-40 °                                     |  
| 6           | 6                            | +/-30 °                                     |  
| 12           | 13                            | +/-20 °                                     |  
| 18           | 18,0                            | +/-10 °                                     |  
| 20           | 21                            | +/-10 °                                     |  

In de volgende afbeelding worden de camera weergaven gesimuleerd met behulp van de meest linkse (-) en meest rechtse (+)-montage hoek aanbevelingen voor het gebruik van **cognitiveservices. Vision. spatialanalysis-personcrossingline** om te beginnen met tellen.

| Meest linkse weer gave                | Weer gave uiterst rechts                |  
| ---------------------------- | ----------------------------- |  
| ![Camera hoek uiterst links](./media/spatial-analysis/camera-angle-left.png) | ![Camera hoek uiterst rechts](./media/spatial-analysis/camera-angle-right.png) |  

In de volgende afbeelding ziet u de plaatsing en koppelings hoeken van de camera van een vogel weergave.

![Overzicht](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Zone configuratie

We raden u aan om camera's op 10 meter of meer boven het wegdek te plaatsen om te garanderen dat de gedekte ruimte groot genoeg is. 

Wanneer de zone zich bevindt als een muur of plank, koppelt u camera's in de opgegeven afstand van het doel binnen het acceptabele bereik van 120 graden, zoals wordt weer gegeven in de volgende afbeelding.

![Aanvaard bare camera hoek](./media/spatial-analysis/camera-angle-acceptable.png)

De volgende afbeelding bevat simulaties voor de linker-en rechter camera weergaven van een gebied naast een rek.

| Weer gave links        | Rechter weergave        |  
| ---------------- | ----------------- |  
| ![Weer gave links](./media/spatial-analysis/end-cap-left.png) | ![Rechter weergave](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Wachtrijen

De **cognitiveservices. Vision. spatialanalysis-personcount**, **cognitiveservices. Vision. spatialanalysis-persondistance** en **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon-** vaardig heden kunnen worden gebruikt voor het bewaken van wacht rijen. Voor een optimale kwaliteit van de wachtrij gegevens worden intrek barrières de voor keur gegeven om bedekking van de mensen in de wachtrij te minimaliseren en ervoor te zorgen dat de locatie van de wacht rijen consistent is.

![Inklap bare band wachtrij](./media/spatial-analysis/retractable-belt-queue.png)

Dit type barrière geniet de voor keur boven dekkende belemmeringen voor wachtrij vorming om de nauw keurigheid van de inzichten van het systeem te maximaliseren.

Er zijn twee typen wacht rijen: lineair en zigzag.

In de volgende afbeelding ziet u aanbevelingen voor lineaire wacht rijen:

![Aanbeveling lineaire wachtrij](./media/spatial-analysis/camera-angle-linear-queue.png)

De volgende afbeelding bevat simulaties voor de linker-en rechter camera weergaven van lineaire wacht rijen. Houd er rekening mee dat u de camera aan de andere kant van de wachtrij kunt koppelen.

| Weer gave links                          | Rechter weergave                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Linkerhoek voor lineaire wachtrij](./media/spatial-analysis/camera-angle-linear-left.png) | ![Rechter hoek voor lineaire wachtrij](./media/spatial-analysis/camera-angle-linear-right.png) |  

Voor zigzag wacht rijen is het het beste om te voor komen dat de camera rechtstreeks op de wachtrij regel richting wordt geplaatst, zoals wordt weer gegeven in de volgende afbeelding. Houd er rekening mee dat elk van de vier voorbeeld camera posities in de afbeelding de ideale weer gave biedt met een acceptabele afwijking van +/-15 graden in elke richting.

In de volgende afbeeldingen wordt de weer gave gesimuleerd van een camera die is geplaatst op de ideale locaties voor een zigzag-volgende wachtrij.

| Weer geven 1        | Weer gave 2        |  
| ------------- | ------------- |  
| ![Weer geven 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Weer gave 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Weer geven 3 |  Weer geven 4 |  
| ---- | ----  |
| ![Weer geven 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Weer geven 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Organische wacht rijen

Biologische wachtrij regels vormen biologisch. Deze stijl van de wachtrij is acceptabel als wacht rijen niet meer dan 2-3 personen en de lijn formulieren binnen de zone definitie vormen. Als de wachtrij lengte meestal meer is dan 2-3 personen, raden we u aan een inklap bare riem te gebruiken om de richting van de wachtrij te begeleiden en de lijn formulieren binnen de zone definitie te controleren.

## <a name="next-steps"></a>Volgende stappen

* [Een web-app voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Ruimtelijke-analyse bewerkingen configureren](./spatial-analysis-operations.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Hand leiding voor zone-en lijn plaatsing](spatial-analysis-zone-line-placement.md)
