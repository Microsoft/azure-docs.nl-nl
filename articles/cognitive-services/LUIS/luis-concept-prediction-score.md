---
title: Voorspellings scores-LUIS
description: Een Voorspellings score geeft de mate van betrouw baarheid van de LUIS API-service voor Voorspellings resultaten op basis van een gebruikers utterance.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316457"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Voorspellings scores geven de nauw keurigheid van de voor spelling voor intentie en entiteiten aan

Een Voorspellings score geeft aan dat de mate van betrouw baarheid die LUIS heeft voor de Voorspellings resultaten van een utterance van de gebruiker.

Een Voorspellings Score ligt tussen nul (0) en een (1). Een voor beeld van een zeer zeker LUISe Score is 0,99. Een voor beeld van een Score van een lage betrouw baarheid is 0,01.

|Score waarde|Betrouwbaarheid|
|--|--|
|1|definitieve overeenkomst|
|0,99|hoge betrouw baarheid|
|0,01|lage betrouw baarheid|
|0|afwijkende fout|

## <a name="top-scoring-intent"></a>Belangrijkste Score intentie

Elke utterance-voor spelling retourneert een doel voor het bovenste Score. Deze voor spelling is een numerieke vergelijking van de Voorspellings scores.

## <a name="proximity-of-scores-to-each-other"></a>Nabijheid van scores aan elkaar

De bovenste twee scores kunnen een zeer klein verschil hebben. LUIS geeft deze nabijheid niet aan, anders dan het retour neren van de bovenste Score.

## <a name="return-prediction-score-for-all-intents"></a>Voorspellings score voor alle intenties retour neren

Een test-of eindpunt resultaat kan alle intenties bevatten. Deze configuratie wordt ingesteld op het eind punt met de juiste teken reeks naam/waarde-paar.

|Voorspellings-API|Query naam|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Intenties met vergelijk bare scores controleren

Het controleren van de score voor alle doel stellingen is een goede manier om te controleren of niet alleen de juiste intentie is geïdentificeerd, maar dat de volgende geïdentificeerde intenten significant en consistent zijn voor uitingen.

Als meerdere doel einden Voorspellings scores hebben, op basis van de context van een utterance, kan LUIS scha kelen tussen de intenties. Om deze situatie op te lossen, gaat u door met het toevoegen van uitingen aan elke intentie met een breder scala aan contextuele verschillen of kunt u de client toepassing, zoals een chat-bot, programmatische keuzes maken over het afhandelen van de twee belangrijkste intenties.

De twee intenties, die te nauwkeurig zijn, kunnen worden omgekeerd door **niet-deterministische training**. De hoogste score kan de tweede boven zijn en de tweede hoogste score zou de eerste hoogste score kunnen worden. Om deze situatie te voor komen, voegt u bijvoorbeeld uitingen toe aan elk van de twee bovenste doel stellingen voor die utterance met de keuze en context van het woord dat de 2 intentie onderscheidt. De twee doel stellingen moeten ongeveer hetzelfde aantal voorbeeld uitingen hebben. Een regel voor het scheiden van een schei ding om te voor komen dat de inversie wordt veroorzaakt door trainingen, is een verschil van 15% in scores.

U kunt de **niet-deterministische training** uitschakelen door te [trainen op alle gegevens](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Verschillen met voor spellingen tussen verschillende trainings sessies

Wanneer u hetzelfde model in een andere app traint en de scores zijn niet hetzelfde, is dit verschil omdat er **niet-deterministische training** (een element van wille keurigheid) is. Ten tweede betekent een overlap ping van een utterance aan meer dan één intentie dat de beste intentie voor dezelfde utterance kan worden gewijzigd op basis van training.

Als uw chat-bot een specifieke LUIS Score vereist om het vertrouwen in een intentie aan te geven, moet u het Score verschil tussen de twee beste doel stellingen gebruiken. Deze situatie biedt mogelijkheden voor variaties in training.

U kunt de **niet-deterministische training** uitschakelen door te [trainen op alle gegevens](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (exponent) notatie

Voor Voorspellings scores kunnen de notatie exponent gebruiken die boven het 0-1-bereik wordt _weer gegeven_ , zoals `9.910309E-07` . Deze score geeft een indicatie van een zeer **klein** getal.

|E-notatie Score |Werkelijke Score|
|--|--|
|9.910309 e-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Toepassingsinstellingen

Gebruik [Toepassings instellingen](luis-reference-application-settings.md) om te bepalen hoe diakritische tekens en lees tekens van invloed zijn op de Voorspellings scores.

## <a name="next-steps"></a>Volgende stappen

Zie [entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw Luis-app.
