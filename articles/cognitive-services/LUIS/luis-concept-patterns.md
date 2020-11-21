---
title: Help voor voor spelling van patronen-LUIS
titleSuffix: Azure Cognitive Services
description: Een patroon biedt u de mogelijkheid om nauw keuriger te zijn voor een intentie zonder veel meer uitingen te bieden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025956"
---
# <a name="patterns-improve-prediction-accuracy"></a>Nauw keurigheid van de voor spelling verbeteren
Patronen zijn ontworpen om de nauw keurigheid te verbeteren wanneer verschillende uitingen zeer vergelijkbaar zijn.  Een patroon biedt u de mogelijkheid om nauw keuriger te zijn voor een intentie zonder veel meer uitingen te bieden.

## <a name="patterns-solve-low-intent-confidence"></a>Patronen voor minder betrouw baarheid oplossen
Overweeg een Human resources-app die rapporteert over het organigram in relatie tot een werk nemer. Op basis van de naam en relatie van een werk nemer LUIS worden de betrokken werk nemers geretourneerd. Overweeg een werk nemer, Tom, met de naam van een manager en een team van ondergeschikten met de naam Michael, Rebecca en Carl.

![Afbeelding van organigram](./media/luis-concept-patterns/org-chart.png)

|Utterances|Intentie voor speld|Intente Score|
|--|--|--|
|Wie is de ondergeschikte van Tom?|GetOrgChart|.30|
|Wie is de ondergeschikte van Tom?|GetOrgChart|.30|

Als een app tussen 10 en 20 uitingen met verschillende lengten van zinnen, een andere woord volgorde en zelfs andere woorden (synoniemen, ' beheer ', ' rapport ') heeft, kan LUIS een lage betrouwbaarheids Score retour neren. Maak een patroon om LUIS inzicht te krijgen in het belang van de woord volgorde.

Met patronen worden de volgende situaties opgelost:

* De intentie Score is laag
* De juiste intentie is niet de hoogste score, maar te dicht bij de bovenste Score.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Patronen zijn geen garantie van intentie
Patronen gebruiken een combi natie van voorspellings technologieën. Het instellen van een intentie voor een sjabloon utterance in een patroon is geen garantie voor de intentie voorspelling, maar is een sterk signaal.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>De entiteits detectie van de machine learning wordt niet verbeterd door patronen

Een patroon is hoofd zakelijk bedoeld om de voor spelling van intenties en rollen te helpen. Het _patroon. elke_ entiteit wordt gebruikt om vrije-vorm entiteiten te extra heren. Hoewel patronen gebruikmaken van entiteiten, kan een patroon niet helpen bij het detecteren van een machine learning-entiteit.

Het is niet raadzaam om verbeterde entiteits voorspellingen te zien als u meerdere uitingen samenvouwt tot één patroon. Voor eenvoudige entiteiten die moeten worden gestart, moet u uitingen toevoegen of de lijst entiteiten gebruiken. anders wordt uw patroon niet geactiveerd.

## <a name="patterns-use-entity-roles"></a>Patronen gebruiken entiteits rollen
Als twee of meer entiteiten in een patroon contextuele gerelateerde zijn, gebruiken patronen entiteits [rollen](./luis-concept-entity-types.md) om contextuele informatie over entiteiten te extra heren.

## <a name="prediction-scores-with-and-without-patterns"></a>Voorspellings scores met en zonder patronen
Gezien voldoende voor beeld uitingen, kan LUIS de Voorspellings betrouwbaarheid zonder patronen verg Roten. Patronen verhogen de betrouwbaarheids Score zonder zoveel uitingen te bieden.

## <a name="pattern-matching"></a>Patroon vergelijking
Er wordt een patroon vergeleken op basis van het detecteren van de entiteiten in het patroon, waarna de rest van de woorden en de woord volgorde van het patroon worden gevalideerd. Entiteiten zijn vereist in het patroon voor een patroon dat overeenkomt met. Het patroon wordt toegepast op token niveau, niet op het teken niveau.

## <a name="pattern-only-apps"></a>Alleen patroon-apps
U kunt een app bouwen met intenties die geen voor beeld uitingen hebben, zolang er voor elke intentie een patroon is. Het patroon mag voor een alleen-patroon app geen machine learning-entiteiten bevatten, omdat hiervoor bijvoorbeeld uitingen nodig zijn.

## <a name="patternany-entity"></a>Pattern.any-entiteit

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Patroonsyntaxis

Meer informatie over de syntaxis van patronen in de syntaxis van de [patroon verwijzing](reference-pattern-syntax.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Een patroon toevoegen. elke entiteit](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntaxis van patronen](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van patronen in deze zelf studie](luis-tutorial-pattern.md)
