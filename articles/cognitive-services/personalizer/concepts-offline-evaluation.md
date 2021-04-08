---
title: De offline-evaluatie methode gebruiken-persoonlijker
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u offline-evaluatie kunt gebruiken om de effectiviteit van uw app te meten en uw leer proces te analyseren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132752"
---
# <a name="offline-evaluation"></a>Offline-evaluatie

Offline-evaluatie is een methode waarmee u de effectiviteit van de Personaler service kunt testen en evalueren zonder uw code te wijzigen of de gebruikers ervaring te beïnvloeden. Offline-evaluatie maakt gebruik van vroegere gegevens, verzonden vanuit uw toepassing naar de Rank-en belonings-Api's om te vergelijken hoe verschillende posities zijn uitgevoerd.

Offline-evaluatie wordt uitgevoerd op een datum bereik. Het bereik kan worden voltooid zo laat als de huidige tijd. Het begin van het bereik kan niet groter zijn dan het aantal dagen dat is opgegeven voor het [bewaren van gegevens](how-to-settings.md).

Met de offline-evaluatie kunt u de volgende vragen beantwoorden:

* Hoe effectief is het persoonlijke karakter voor een geslaagde personalisatie?
    * Wat zijn de gemiddelde beloningen die zijn behaald door de Personaler online machine learning-beleid?
    * Hoe vergelijkt Personaler zich met de effectiviteit van wat de toepassing standaard zou hebben uitgevoerd?
    * Wat zou de effectiviteit van een wille keurige keuze voor personalisatie zouden zijn?
    * Wat zou zijn als de effectiviteit van het hand matig opgeven van een ander leer beleid?
* Welke functies van de context zijn meer of minder voor een succes volle personalisatie?
* Welke functies van de acties zijn meer of minder voor een succes volle personalisatie?

Daarnaast kunt u offline-evaluatie gebruiken om meer geoptimaliseerde leer beleid te ontdekken dat persoonlijker kan gebruiken om de resultaten in de toekomst te verbeteren.

Offline-evaluaties bieden geen richt lijnen voor het percentage gebeurtenissen dat moet worden gebruikt voor het verkennen.

## <a name="prerequisites-for-offline-evaluation"></a>Vereisten voor offline-evaluatie

Hier volgen enkele belang rijke aandachtspunten voor de representatieve offline-evaluatie:

* Voldoende gegevens hebben. Het aanbevolen minimum is ten minste 50.000 gebeurtenissen.
* Gegevens verzamelen uit Peri Oden met representatief gebruikers gedrag en verkeer.

## <a name="discovering-the-optimized-learning-policy"></a>Het geoptimaliseerde leer beleid detecteren

Personaler kan het offline-evaluatie proces gebruiken om automatisch een betrouwbaarder leer beleid te detecteren.

Nadat u de offline-evaluatie hebt uitgevoerd, kunt u de vergelijkende effectiviteit van Personaler voor het nieuwe beleid bekijken vergeleken met het huidige online beleid. U kunt dat leer beleid vervolgens Toep assen om het direct van kracht te laten zijn door het te downloaden en te uploaden in het deel venster modellen en beleid. U kunt het ook downloaden voor toekomstige analyses of voor gebruik.

Huidige beleids regels die zijn opgenomen in de evaluatie:

| Leer instellingen | Doel|
|--|--|
|**Online beleid**| Het huidige leer beleid dat wordt gebruikt in Personaler |
|**Basislijn**|De standaard waarde van de toepassing (zoals bepaald door de eerste actie die wordt verzonden in Rangings aanroepen)|
|**Wille keurig beleid**|Een imaginair positie gedrag dat altijd een wille keurige keuze van de acties retourneert uit de opgegeven records.|
|**Aangepast beleid**|Er zijn extra leer beleid geüpload bij het starten van de evaluatie.|
|**Geoptimaliseerd beleid**|Als de evaluatie is gestart met de optie om een geoptimaliseerd beleid te detecteren, wordt het ook vergeleken en kunt u het downloaden of het online leer beleid maken, waarbij de huidige wordt vervangen.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Meer informatie over de relevantie van offline-evaluatie resultaten

Wanneer u een offline-evaluatie uitvoert, is het belang rijk om de _vertrouwens grenzen_ van de resultaten te analyseren. Als ze breed zijn, betekent dit dat uw toepassing niet voldoende gegevens heeft ontvangen die nauw keurig of significant zijn. Naarmate er meer gegevens in het systeem worden verzameld en u offline-evaluaties uitvoert gedurende langere Peri Oden, worden de betrouwbaarheids intervallen smaller.

## <a name="how-offline-evaluations-are-done"></a>Hoe offline-evaluaties worden uitgevoerd

Offline-evaluaties worden uitgevoerd met behulp van een methode met de naam **counterfactual Evaluation**.

Personaler is gebouwd op basis van de veronderstelling dat het gedrag van gebruikers (en dus beloningen) niet op een andere manier kan voors pellen (Personaler kan niet weten wat er zou zijn als de gebruiker iets anders zou hebben weer gegeven dan het had zien) en alleen om te leren van gemeten beloningen.

Dit is het conceptuele proces dat wordt gebruikt voor evaluaties:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

De offline-evaluatie maakt alleen gebruik van het geobserveerde gebruikers gedrag. Met dit proces worden grote hoeveel heden gegevens verwijderd, met name als uw toepassing classificatie aanroept met een groot aantal acties.


## <a name="evaluation-of-features"></a>Evaluatie van functies

Offline-evaluaties kunnen informatie geven over de hoeveelheid van de specifieke functies voor acties of context wegens meer beloningen. De informatie wordt berekend aan de hand van de evaluatie op basis van de opgegeven tijds periode en gegevens en kan variëren met de tijd.

We raden u aan functie-evaluaties te bekijken en te vragen:

* Wat andere, extra, functies kunnen uw toepassing of systeem volgen langs de regels die effectiever zijn?
* Welke functies kunnen worden verwijderd vanwege een geringe effectiviteit? Met functies met weinig effectiviteit voegt u _ruis_ toe aan de machine learning.
* Zijn er onbedoeld onderdelen die per ongeluk zijn opgenomen? Voor beelden hiervan zijn: door de gebruiker geïdentificeerde informatie, dubbele Id's, enzovoort.
* Zijn er ongewenste functies die niet mogen worden gebruikt om te personaliseren als gevolg van regelgevende of verantwoordelijke overwegingen voor het gebruik? Zijn er functies die een proxy kunnen hebben (dat wil zeggen, goed spie gelen of correleren met) ongewenste functies?


## <a name="next-steps"></a>Volgende stappen

[Persoonlijker configureren](how-to-settings.md) 
 [Offline-evaluaties uitvoeren](how-to-offline-evaluation.md) Meer informatie [over de werking van personaler](how-personalizer-works.md)
