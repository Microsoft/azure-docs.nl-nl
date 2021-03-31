---
title: Versterking van het onderwijs-persoonlijker
titleSuffix: Azure Cognitive Services
description: Personaler maakt gebruik van informatie over acties en de huidige context om betere suggesties voor de rang schikking te maken. De informatie over deze acties en context zijn kenmerken of eigenschappen die functies worden genoemd.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132735"
---
# <a name="what-is-reinforcement-learning"></a>Wat is versterking van het onderwijs?

Educatie leren is een benadering van machine learning die gedrag leert door feedback te krijgen van het gebruik ervan.
 
Educatief leren werkt door:

* Het bieden van een verkoop kans of vrijheids graad voor het nemen van een gedrag, zoals het maken van beslissingen of keuzes.
* Contextuele informatie over de omgeving en keuzen opgeven.
* Feedback geven over hoe goed het gedrag een bepaald doel afhaalt.

Hoewel er veel subtypen en stijlen voor het versterken van het onderwijs zijn, is dit de manier waarop het concept werkt in Personaler:

* Uw toepassing biedt de mogelijkheid om één stukje inhoud uit een lijst met alternatieven weer te geven.
* Uw toepassing bevat informatie over elke alternatieve en de context van de gebruiker.
* Uw toepassing berekent een _belonings Score_.

In tegens telling tot een aantal benaderingen van het versterken van educatie, is voor Personaler geen simulatie vereist om in te werken. De leer algoritmen zijn ontworpen om te reageren op een buiten wereld (en deze te beheren) en te leren van elk gegevens punt met een inzicht dat het een unieke kans is dat de tijd en het geld wordt gemaakt en dat er sprake is van een niet-nul (verlies van mogelijke beloning) als er suboptimale prestaties optreden.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Welk type algoritme voor versterking van leer van persoonlijke gegevens wordt gebruikt?

De huidige versie van Personaler maakt gebruik van **contextuele Bandits**, een benadering voor het versterken van het leren van beslissingen of keuzes tussen discrete acties, in een bepaalde context.

Het _beslissings geheugen_, het model dat is getraind voor het vastleggen van de best mogelijke beslissing, op basis van een context, maakt gebruik van een set lineaire modellen. Deze hebben herhaalde bedrijfs resultaten en zijn een bewezen benadering, deels omdat ze zeer snel van de praktijk kunnen leren, zonder dat ze training met meerdere gebruikers hoeven te hoeven doen, en dat ze gedeeltelijk kunnen worden uitgebreid met bewaakte leer modellen en diepe Neural-netwerk modellen.

De toewijzing van het gebruik van een verkennen/exploit-verkeer wordt op wille keurige wijze uitgevoerd volgens het percentage dat is ingesteld voor verkennen en het standaard algoritme voor verkennen is Epsilon-Greedy.

### <a name="history-of-contextual-bandits"></a>Geschiedenis van contextuele Bandits

John Langford heeft de naam contextuele Bandits (Langford en Zhang [2007]) gemunt om een Subtractieve subset van versterking van het onderwijs te beschrijven en heeft met een halve dozijn gewerkte kennis verbeterd over de manier waarop u in dit paradigma kunt leren werken:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer en Langford [2009]
* Li et al. [2010]

John heeft ook enkele zelf studies gegeven die eerder in de onderwerpen zijn behandeld, zoals gezamenlijke voor spelling (ICML 2015), contextuele Bandit theorie (NIPS 2013), actief leren (ICML 2009) en voor beelden van complexiteits grenzen (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Wat machine learning Frameworks maakt gebruik van persoonlijker?

Personaler maakt momenteel gebruik van [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) als basis voor de machine learning. Dit kader biedt maximale door Voer en een laagste latentie bij het maken van een persoonlijke classificatie en het model trainen met alle gebeurtenissen.

## <a name="references"></a>Referenties

* [Contextuele beslissingen nemen met weinig technische schulden](https://arxiv.org/abs/1606.03966)
* [Een verlagings benadering van een billijke classificatie](https://arxiv.org/abs/1803.02453)
* [Efficiënte contextuele Bandits in niet-stationaire werelden](https://arxiv.org/abs/1708.01799)
* [Rest verlies voor spelling: versterking: leren zonder incrementele feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Instructies en visuele waarnemingen toewijzen aan acties met versterking van het onderwijs](https://arxiv.org/abs/1704.08795)
* [Leer beter zoeken dan uw docent](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Volgende stappen

[Offline-evaluatie](concepts-offline-evaluation.md) 