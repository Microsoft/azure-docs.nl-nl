---
title: Wat kan ik doen met Machine Learning Studio (klassiek) - Azure
description: Machine Learning Studio (klassiek) is een hulpprogramma waarmee u met slepen en neerzetten snel modellen kunt ontwikkelen met behulp van een kant-en-klare bibliotheek van algoritmen en modules.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 0a18f9adb8c3723619b44b30bfa334edf4300c0d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509485"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Wat kan ik doen met Machine Learning Studio (klassiek)?

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (klassiek) is een hulpprogramma met functionaliteit op basis van slepen en neerzetten waarmee u Machine Learning-modellen kunt ontwikkelen, testen en implementeren. Studio (klassiek) publiceert modellen als webservices die eenvoudig kunnen worden gebruikt door aangepaste apps of BI-hulpprogramma's zoals Excel.


## <a name="studio-classic--interactive-workspace"></a>Interactieve werkruimte voor Studio (klassiek)

Wanneer u een predictive analytics-model ontwikkelt, gebruikt u doorgaans gegevens uit een of meer bronnen. U transformeert en analyseert deze gegevens met diverse functies voor gegevensmanipulatie en statistische functies, en genereert een set resultaten. Het ontwikkelen van een model als dit is een iteratief proces. Terwijl u de verschillende functies en de bijbehorende parameters aanpast, worden de resultaten geconvergeerd tot u een afdoende getraind en doeltreffend model hebt.

Machine Learning Studio (klassiek) beschikt over een interactieve, visuele werkruimte om eenvoudig een predictive analytics-model te bouwen, te testen en te herhalen. U sleept **_gegevenssets_* _ en _*_modules_*_ voor analyse naar een interactief canvas en verbindt deze met elkaar om een _*_experiment_*_ op te zetten, dat u vervolgens uitvoert in Machine Learning Studio (klassiek). Als u het modelontwerp wilt herhalen, kunt u het experiment bewerken, desgewenst een kopie ervan opslaan en het opnieuw uitvoeren. Wanneer u klaar bent, kunt u het _*_trainingsexperiment_*_ converteren naar een _*_voorspellend experiment_*_ en dit vervolgens publiceren als een _*_webservice_*_, zodat het model ook voor anderen toegankelijk is.

U hoeft niets te programmeren. U hoeft alleen de gegevenssets en modules visueel met elkaar te verbinden om een predictive analytics-model op te zetten.

![Diagram voor Machine Learning Studio (klassiek): Zet experimenten op, lees gegevens uit verschillende bronnen, schrijf beoordeelde gegevens weg, maak modellen.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Het overzichtsdiagram voor ML Studio (klassiek) downloaden
Download het diagram _ *Overzicht van de mogelijkheden van Microsoft ML Studio (klassiek)* * voor een algemeen overzicht van de mogelijkheden van Machine Learning Studio (klassiek). Als u het diagram altijd bij de hand wilt hebben, kunt u het in A3-formaat afdrukken.

**Download het diagram hier: [Overzicht van de mogelijkheden van Microsoft Machine Learning Studio (klassiek)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Overzicht van de mogelijkheden van Microsoft Machine Learning Studio (klassiek)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Onderdelen van een experiment van studio (klassiek)
Een experiment bestaat uit gegevenssets die gegevens leveren aan analytische modules. Deze kunt u met elkaar verbinden in een predictive analytics-model. Een geldig experiment heeft de volgende kenmerken:

* Het experiment heeft minimaal één gegevensset en één module
* Gegevenssets mogen alleen worden verbonden met modules
* Modules mogen worden verbonden met gegevenssets of andere modules
* Alle ingangspoorten voor modules moeten een verbinding hebben met de gegevensstroom
* Voor elke module moeten alle vereiste parameters zijn ingesteld

U kunt een geheel nieuw experiment maken, maar u kunt ook een bestaand voorbeeldexperiment als sjabloon gebruiken. Zie [Voorbeeldexperimenten kopiëren om nieuwe experimenten voor Machine Learning te maken](sample-experiments.md) voor meer informatie.

Zie [Een experiment maken in Machine Learning Studio (klassiek)](create-experiment.md) voor een voorbeeld van het maken van een experiment.

Zie [Een predictive analytics-oplossing maken met Machine Learning Studio (klassiek)](tutorial-part1-credit-risk.md) voor de volledige procedure voor het maken van een predictive analytics-oplossing.

### <a name="datasets"></a>Gegevenssets
Een gegevensset bestaat uit gegevens die zijn geüpload naar Machine Learning Studio (klassiek), zodat ze kunnen worden gebruikt in het modelleringsproces. In Machine Learning Studio (klassiek) is een aantal voorbeeldgegevenssets opgenomen waarmee u kunt experimenteren. U kunt meer gegevenssets uploaden als dat nodig is. Hier volgen enkele voorbeelden van opgenomen gegevenssets:

* **MPG-gegevens voor verschillende auto's**: MPG-waarden (mijl per gallon) voor auto's, geïdentificeerd met het aantal cilinders, paardenkracht, enzovoort.
* **Borstkankergegevens**: gegevens voor borstkankerdiagnose.
* **Bosbrandgegevens**: omvang van bosbranden in het noordoosten van Portugal.

Wanneer u een experiment maakt, kunt u links in het canvas kiezen uit de lijst met beschikbare gegevenssets.

Zie [De voorbeeldgegevenssets in Machine Learning Studio (klassiek)](use-sample-datasets.md) gebruiken voor een lijst van voorbeeldgegevenssets die zijn opgenomen in Machine Learning Studio (klassiek).

### <a name="modules"></a>Modules
Een module is een algoritme dat u met uw gegevens kunt uitvoeren. Machine Learning Studio (klassiek) beschikt over diverse modules, variërend van functies voor inkomende gegevens tot processen voor training, beoordeling en validatie. Hier volgen enkele voorbeelden van opgenomen modules:

* [Converteren naar ARFF][convert-to-arff]: hiermee wordt een geserialiseerde .NET-gegevensset geconverteerd naar de indeling Attribute-Relation File Format (ARFF).
* [Elementaire statistieken berekenen][elementary-statistics]: hiermee worden elementaire statistieken berekend, zoals het gemiddelde, de standaardafwijking enzovoort.
* [Lineaire regressie][linear-regression]: hiermee wordt een online lineair regressiemodel met daalgradiënt gemaakt.
* [Scoremodel][score-model]: hier wordt een getraind classificatie- of regressiemodel beoordeeld.

Wanneer u een experiment maakt, kunt u links in het canvas kiezen uit de lijst met beschikbare modules.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de parameters van de module weergegeven in het deelvenster **Properties**, rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen.

Zie [Algoritmen kiezen voor Microsoft Machine Learning Studio (klassiek)](../how-to-select-algorithms.md) voor hulp bij het navigeren door de uitgebreide verzameling beschikbare machine learning-algoritmen.

## <a name="deploying-a-predictive-analytics-web-service"></a>Een predictive analytics-web service implementeren
Wanneer uw predictive analytics-model klaar is, kunt u het direct vanuit Machine Learning Studio (klassiek) implementeren als webservice. Zie [Een Azure Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md) voor meer informatie over dit proces.

## <a name="next-steps"></a>Volgende stappen
U kunt zich de basiskennis van predictive analytics en machine learning eigen maken aan de hand van een [Stapsgewijze quickstart](create-experiment.md) en [door voorbeelden verder uit te werken](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model