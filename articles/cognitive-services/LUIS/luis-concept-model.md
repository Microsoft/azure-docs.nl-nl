---
title: Ontwerpen met modellen-LUIS
description: Language Standing biedt verschillende types modellen. Sommige modellen kunnen op meer dan één manier worden gebruikt.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316508"
---
# <a name="design-with-intent-and-entity-models"></a>Ontwerpen met intentie-en entiteits modellen

Language Standing biedt twee typen modellen waarmee u uw app-schema kunt definiëren. Uw app-schema bepaalt welke informatie u ontvangt van de voor spelling van een nieuwe gebruiker utterance.

Het app-schema is gebaseerd op modellen die u maakt met behulp van [machine onderwijs](#authoring-uses-machine-teaching):
* [Intenties](#intents-classify-utterances) classificeren gebruiker uitingen
* [Entiteiten](#entities-extract-data) halen gegevens op uit utterance

## <a name="authoring-uses-machine-teaching"></a>Ontwerpen maakt gebruik van machine onderwijs

Met de machine leer methodologie van LUIS kunt u eenvoudig concepten aan een machine geven. Het is niet nodig om LUIS te gebruiken. _machine learning_ In plaats daarvan communiceert u als docent met een concept voor LUIS door voor beelden te bieden van het concept en te verklaren hoe een concept moet worden gemodelleerd met andere gerelateerde concepten. Als docent kan het model van LUIS ook interactief verbeteren door Voorspellings fouten te identificeren en te verhelpen.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Intentie-uitingen classificeren

Een intentie classificeert bijvoorbeeld uitingen om LUIS te leren over het doel. Een voor beeld van een uitingen binnen een intentie wordt als positieve voor beeld van de utterance gebruikt. Deze zelfde uitingen worden als negatieve voor beelden in alle andere intenties gebruikt.

Overweeg een app die de bedoeling van een gebruiker moet bepalen om een boek te best Ellen en een app die het verzend adres voor de klant nodig heeft. Deze app heeft twee doel stellingen: `OrderBook` en `ShippingLocation` .

De volgende utterance is een **positief voor beeld** voor de `OrderBook` intentie en een **negatief voor beeld** voor de doel stellingen `ShippingLocation` `None` :

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Entiteitgegevens extraheren

Een entiteit vertegenwoordigt een hoeveelheid gegevens die u wilt ophalen uit de utterance. Een machine learning-entiteit is een entiteit op het hoogste niveau met subentiteiten, die ook instanties voor machine learning zijn.

Een voor beeld van een machine learning-entiteit is een bestelling voor een vlieg ticket. Conceptueel gezien is dit een enkele trans actie met veel kleinere gegevens eenheden, zoals datum, tijd, aantal stoelen, het type Seat, zoals de eerste klasse of de fase, de oorsprongs locatie, de doel locatie en de keuze van de maaltijd.

## <a name="intents-versus-entities"></a>Intenties versus entiteiten

Een intentie is het gewenste resultaat van het _hele_ utterance terwijl entiteiten gegevens uit de utterance zijn geëxtraheerd. Doorgaans zijn intenties gekoppeld aan acties, die de client toepassing moet uitvoeren. Entiteiten zijn informatie die nodig is om deze actie uit te voeren. Vanuit een programmeer perspectief wordt een methode aanroep geactiveerd en worden de entiteiten gebruikt als para meters voor die methode aanroep.

Deze utterance _moet_ een intentie hebben en _kan_ entiteiten hebben:

`Buy an airline ticket from Seattle to Cairo`

Dit utterance heeft één intentie:

* Een vlieg ticket kopen

Deze utterance _kan_ verschillende entiteiten hebben:

* Locaties van Seattle (oorsprong) en Cairo (bestemming)
* De hoeveelheid van een enkel ticket

## <a name="entity-model-decomposition"></a>Decompositie van entiteits model

LUIS ondersteunt _model ontleding_ met de ontwerp-api's, waarbij een concept in kleinere delen wordt opgesplitst. Zo kunt u uw modellen met vertrouwen bouwen in de manier waarop de verschillende onderdelen zijn geconstrueerd en gedicteerd.

Model ontleding heeft de volgende onderdelen:

* [intents](#intents-classify-utterances)
    * [functies](#features)
* [machine learning-entiteiten](reference-entity-machine-learned-entity.md)
    * subentiteiten (ook machine learning-entiteiten)
        * [functies](#features)
            * [woordgroepen lijst](luis-concept-feature.md)
            * [niet-machine learning-entiteiten](luis-concept-feature.md) zoals [reguliere expressies](reference-entity-regular-expression.md), [lijsten](reference-entity-list.md)en [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Functies

Een [functie](luis-concept-feature.md) is een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert. Machine learning-functies geven LUIS belang rijke aanwijzingen voor waar u kunt zoeken naar dingen die een concept zullen onderscheiden. Ze zijn hints die LUIS kunnen gebruiken, maar geen vaste regels. Deze hints worden gebruikt in combi natie met de labels om de gegevens te vinden.

## <a name="patterns"></a>Patronen

[Patronen](luis-concept-patterns.md) zijn ontworpen om de nauw keurigheid te verbeteren wanneer verschillende uitingen zeer vergelijkbaar zijn. Een patroon biedt u de mogelijkheid om nauw keuriger te zijn voor een intentie zonder veel meer uitingen te bieden.

## <a name="extending-the-app-at-runtime"></a>De app tijdens runtime uitbreiden

Het schema van de app (modellen en functies) wordt getraind en gepubliceerd naar het Voorspellings eindpunt. U kunt [nieuwe informatie](schema-change-prediction-runtime.md), samen met de utterance van de gebruiker, door geven aan het Voorspellings eindpunt om de voor spelling te verbeteren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [intenties](luis-concept-intent.md) en [entiteiten](luis-concept-entity-types.md).
* Meer informatie over [functies](luis-concept-feature.md)