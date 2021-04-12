---
title: Machine learning-verdeling (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over machine learning verdeling en hoe u het python-pakket van Fairlearn kunt gebruiken om de oneerlijkheid te beoordelen en te beperken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 04/02/2021
ms.custom: responsible-ml
ms.openlocfilehash: 45c63780eac021301e5b01c0ff8abea7d704a4cf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220518"
---
# <a name="machine-learning-fairness-preview"></a>Machine learning-verdeling (preview-versie)

Meer informatie over machine learning verdeling en hoe het open-source python-pakket van [Fairlearn](https://fairlearn.github.io/) u kan helpen bij het beoordelen en oplossen van problemen met oneerlijkheid in machine learning modellen. 

## <a name="what-is-machine-learning-fairness"></a>Wat is machine learning verdeling?

Kunst matige intelligentie en machine learning systemen kunnen oneerlijke gedrag tonen. Een manier om oneerlijk gedrag te definiëren, is het nadeel van het probleem of gevolgen voor mensen. Er zijn veel soorten schade die AI-systemen kunnen aanleiding geven tot. Raadpleeg de [NeurIPS 2017-speech van Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) voor meer informatie.

Twee veelvoorkomende typen AI-oorzaken zijn:

- Beschadiging van de toewijzing: een AI-systeem breidt of inhoudt verkoop kansen, resources of informatie voor bepaalde groepen uit. Voor beelden zijn onder andere huur, school toelatingen en leningen waarbij een model veel beter kan zijn bij het verzamelen van goede kandidaten over een specifieke groep personen dan onder andere groepen.

- Beschadiging van Quality-of-service: een AI-systeem werkt ook niet voor één groep mensen, zoals het voor een andere. Een voor beeld: een systeem voor spraak herkenning werkt mogelijk niet goed voor vrouwen als voor mannen.

Om oneerlijke werking in AI-systemen te verminderen, moet u deze Risico's beoordelen en beperken.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Verdeling-evaluatie en-oplossing met Fairlearn

Fairlearn is een open-source python-pakket dat machine learning systeem ontwikkelaars in staat stelt hun systemen verdeling te beoordelen en de oneerlijkheid te beperken.

>[!NOTE]
> Verdeling is een sociaal-Technical uitdaging. Veel aspecten van verdeling, zoals rechtvaardigheid en het verval proces, worden niet vastgelegd in kwantitatieve verdeling-metrische gegevens. Daarnaast kunnen veel kwantitatieve verdeling-metrische gegevens niet tegelijkertijd aan de criteria voldoen. Het doel van het open-source pakket voor Fairlearn is om mensen te helpen verschillende impact-en risico strategieën te beoordelen. Uiteindelijk is het goed voor de menselijke gebruikers om kunst matige intelligentie en machine learning modellen te bouwen, zodat deze geschikt zijn voor hun scenario.

Het open-source pakket Fairlearn heeft twee onderdelen:

- Beoordelings Dashboard: een Jupyter notebook-widget waarmee wordt beoordeeld hoe de voor spellingen van een model van invloed zijn op verschillende groepen. Er kunnen ook meerdere modellen worden vergeleken met behulp van verdeling-en prestatie gegevens.
- Beperkende algoritmen: een set algoritmen voor het beperken van oneerlijkheid in binaire classificatie en regressie.

Met deze onderdelen kunnen gegevens wetenschappers en bedrijfs leiders alle trans acties tussen verdeling en prestaties door lopen en de risico beperkings strategie selecteren die het beste bij hun behoeften past.

## <a name="assess-fairness-in-machine-learning-models"></a>Verdeling evalueren in machine learning modellen

In het open-source pakket Fairlearn wordt verdeling geconceptueeleerd, maar ook wel **groeps verdeling**, waarin wordt gevraagd: welke groepen personen risico lopen voor het optreden van het probleem? De relevante groepen, ook wel subpopulaties genoemd, worden gedefinieerd door middel van **gevoelige functies** of gevoelige kenmerken. Gevoelige functies worden door gegeven aan een estimator in het open-source pakket Fairlearn als een vector of matrix met de naam  `sensitive_features` . De term impliceert dat de systeem ontwerper gevoelig moet zijn voor deze functies bij het bepalen van de groeps verdeling. 

Als mindful is, is het van belang of deze functies privacy-implicaties hebben vanwege persoonlijke gegevens. Maar het woord ' gevoelig ' impliceert niet dat deze functies mogen worden gebruikt voor het maken van voor spellingen.

>[!NOTE]
> Een verdeling-evaluatie is geen louter technische oefening.  Het open-source pakket Fairlearn kan u helpen bij het beoordelen van de verdeling van een model, maar zal de evaluatie niet voor u uitvoeren.  Het open-source pakket Fairlearn helpt kwantitatieve metrische gegevens te identificeren voor het beoordelen van verdeling, maar ontwikkel aars moeten ook een kwalitatieve analyse uitvoeren om de verdeling van hun eigen modellen te evalueren.  De hierboven vermelde gevoelige functies zijn een voor beeld van dit soort kwalitatieve analyse.     

Tijdens de evaluatie fase wordt verdeling gekwantificeerd via de metrische gegevens over de pariteit. Met **metrische gegevens** over de pariteit kan het model gedrag van de verschillende groepen worden geëvalueerd en vergeleken, zoals de verhoudingen of verschillen. Het open-source pakket Fairlearn ondersteunt twee klassen van metrische gegevens over de pariteit:


- Verschillen in model prestaties: met deze sets metrische gegevens wordt het verschil in de waarden van de geselecteerde prestatie gegevens over verschillende subgroepen berekend. Voorbeelden zijn:

  - de nauw keurigheid van de pariteit
  - fout frequentie van verschillen
  - verschillen in precisie
  - dispariteit in intrekken
  - depariteit in MAE
  - veel andere

- Verschillen in selectie criterium: deze metriek bevat het verschil in selectie verhouding tussen verschillende subgroepen. Een voor beeld hiervan is het verschil in de goedkeurings frequentie voor leningen. Selectie percentage betekent de Fractie van data Points in elke klasse die is geclassificeerd als 1 (in binaire classificatie) of distributie van voorspellings waarden (in regressie).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Oneerlijkheid in machine learning modellen beperken

### <a name="parity-constraints"></a>Pariteits beperkingen

Het open-source pakket Fairlearn bevat een groot aantal oneerlijke beperkende algoritmen. Deze algoritmen bieden ondersteuning voor een set met beperkingen voor het gedrag van de Voorspellings functie met de naam **pariteits beperkingen** of criteria. Pariteits beperkingen vereisen dat bepaalde aspecten van het gedrag van de voor spellingen vergelijkbaar zijn met de groepen die gevoelige functies definiëren (bijvoorbeeld verschillende races). De beperkende algoritmen in het open-source pakket Fairlearn gebruiken dergelijke pariteits beperkingen om de waargenomen verdeling problemen te verhelpen.

>[!NOTE]
> Het beperken van de oneerlijkheid in een model betekent dat de oneerlijkheid wordt verminderd, maar deze technische oplossing kan deze oneerlijkheid niet volledig elimineren.  De beperkings algoritmen voor oneerlijkheid in het open-source pakket Fairlearn kunnen voorgestelde beperkende strategieën bieden om de onbillijkheid in een machine learning model te verminderen, maar ze zijn geen oplossingen om de oneerlijkheid volledig op te lossen.  Er zijn mogelijk andere pariteits beperkingen of-criteria die moeten worden overwogen voor elk machine learning model van de ontwikkelaar. Ontwikkel aars die Azure Machine Learning gebruiken, moeten zichzelf bepalen wanneer de risico beperking een onbillijkheid in hun beoogde gebruik en implementatie van machine learning modellen elimineert.  

Het open-source pakket Fairlearn ondersteunt de volgende typen pariteits beperkingen: 

|Pariteits beperking  | Doel  |Machine learning-taak  |
|---------|---------|---------|
|Demografische pariteit     |  Beperking van de toewijzing is ongedaan | Binaire classificatie, regressie |
|Gelijke conflicteert  | Vaststellen van de toewijzing en de kwaliteit van de service | Binaire classificatie        |
|Gelijke kans | Vaststellen van de toewijzing en de kwaliteit van de service | Binaire classificatie        |
|Verlies gebonden groep     |  Problemen met de kwaliteit van de service beperken | Regressie |

### <a name="mitigation-algorithms"></a>Beperkende algoritmen

Het open-source pakket Fairlearn biedt postprocessing en reductie algoritmen voor het beperken van oneerlijkheid:

- Reductie: deze algoritmen hebben een standaard Black-Box machine learning Estimator (bijvoorbeeld een LightGBM-model) en genereren een set met retrainde modellen met behulp van een reeks hergewogen trainings gegevens sets. Aanvragers van een bepaald geslacht kunnen bijvoorbeeld worden verkleind of omlaag worden gereduceerd om modellen opnieuw te trainen en de verschillen in verschillende groepen geslacht te verminderen. Gebruikers kunnen vervolgens een model kiezen dat de beste verhouding biedt tussen nauw keurigheid (of andere prestatie gegevens) en de verschillen, wat doorgaans moet worden gebaseerd op bedrijfs regels en kosten berekeningen.  
- Na het verwerken: deze algoritmen hebben een bestaande classificatie en de gevoelige functie als invoer. Vervolgens wordt een trans formatie van de voor spelling van de classificatie afgeleid om de opgegeven verdeling-beperkingen af te dwingen. Het grootste voor deel van de drempel optimalisatie is de eenvoud en flexibiliteit, omdat het model niet hoeft te worden getraind. 

| Algoritme | Beschrijving | Machine learning-taak | Gevoelige functies | Ondersteunde pariteits beperkingen | Algoritme type |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Black-Box-benadering voor een billijke classificatie die wordt beschreven in [een verlagings benadering van een billijke classificatie](https://arxiv.org/abs/1803.02453) | Binaire classificatie | Categorische gegevens | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Passen |
| `GridSearch` | Black-Box-benadering beschreven in [een verlagings benadering van een billijke classificatie](https://arxiv.org/abs/1803.02453)| Binaire classificatie | Binair | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Passen |
| `GridSearch` | Black-Box-benadering waarmee een grid-Zoek variant van een billijke regressie wordt geïmplementeerd met het algoritme voor beperkt groeps verlies dat wordt beschreven in [eerlijke regressie: kwantitatieve definities en algoritmen op basis van reductie](https://arxiv.org/abs/1905.12843) | Regressie | Binair | [Verlies gebonden groep](#parity-constraints) | Passen |
| `ThresholdOptimizer` | Postprocessing-algoritme op basis van het papieren [voor het leren van de verkoop kansen in Super visie](https://arxiv.org/abs/1610.02413). Deze techniek neemt als invoer een bestaande classificatie en de gevoelige functie en maakt een monotone trans formatie van de voor spelling van de classificatie om de opgegeven pariteits beperkingen af te dwingen. | Binaire classificatie | Categorische gegevens | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Na verwerking |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van de verschillende onderdelen door de Fairlearn van de [github](https://github.com/fairlearn/fairlearn/), de [Gebruikers handleiding](https://fairlearn.github.io/main/user_guide/index.html), [voor beelden](https://fairlearn.github.io/main/auto_examples/index.html)en de [voorbeeld notitieblokken](https://github.com/fairlearn/fairlearn/tree/master/notebooks)te controleren.
- Meer informatie [over het](how-to-machine-learning-fairness-aml.md) inschakelen van verdeling-evaluatie van machine learning-modellen in azure machine learning.
- Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) voor aanvullende verdeling-evaluatie scenario's in azure machine learning. 
