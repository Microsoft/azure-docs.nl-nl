---
title: Interpreteer van modellen in Azure Machine Learning (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over & uitleggen hoe uw machine learning model voor spellingen doet tijdens de training & afwijzen met behulp van de python-SDK van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 2c61cfaf0e97f7d483239a23e5eea52b51c6a126
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690206"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpreteer van modellen in Azure Machine Learning (preview-versie)


## <a name="model-interpretability-overview"></a>Overzicht van het model interpreteren

Model Interpretation is van cruciaal belang voor gegevens wetenschappers, Audi tors en zakelijke besluit vormers om te zorgen voor naleving van het bedrijfs beleid, de industrie normen en regerings voorschriften:

+ Gegevens wetenschappers hebben de mogelijkheid nodig om hun modellen te uitleggen aan leidinggevenden en belanghebbenden, zodat ze inzicht hebben in de waarde en nauw keurigheid van hun bevindingen. Ze moeten ook interpreteren om fouten in hun modellen op te sporen en weloverwogen beslissingen te nemen over hoe ze kunnen worden verbeterd. 

+ Juridische controleurs hebben hulpprogram ma's nodig om modellen te valideren met betrekking tot de naleving van de regelgeving en te controleren hoe de beslissingen van de modellen van invloed zijn op de mens. 

+ Zakelijke besluit vormers hebben gemoeds rust door de mogelijkheid om transparantie voor eind gebruikers te bieden. Op die manier kunnen ze vertrouwens relaties verdienen en onderhouden.

Het inschakelen van de mogelijkheid om een machine learning model uit te leggen, is belang rijk tijdens twee belang rijke fasen van model ontwikkeling:

+ Tijdens de trainings fase kunnen model ontwerpers en evaluatoren de interpretatieve uitvoer van een model gebruiken om hypo Thesen te controleren en om vertrouwen te bouwen met belanghebbenden. Ze gebruiken ook de inzichten in het model voor het opsporen van fouten, het valideren van het model gedrag aan hun doel stellingen en het controleren op modelloze of onbeduidende functies.

+ Tijdens de versterkings fase is het met transparantie rond geïmplementeerde modellen het mogelijk dat leidinggevenden begrijpen "wanneer geïmplementeerd" hoe het model werkt en hoe de beslissingen in werkelijkheid worden behandeld en wat van invloed is op mensen in het leven. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretiteit met Azure Machine Learning

De klassen interpreteer baarheid van modellen worden beschikbaar gesteld via het volgende SDK-pakket: (informatie over het [installeren van SDK-pakketten voor Azure machine learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py))

* `azureml.interpret`, bevat functionaliteiten die door micro soft worden ondersteund.

Gebruiken `pip install azureml-interpret` voor algemeen gebruik.

## <a name="how-to-interpret-your-model"></a>Het model interpreteren

Met de klassen en methoden in de SDK kunt u het volgende doen:
+ Uitleg over model voorspellingen door het genereren van belang rijke waarden voor het hele model en/of afzonderlijke data Points. 
+ Behaal de model interpreteer baarheid van de gegevens sets op schaal, tijdens de training en de interferentie.
+ Gebruik een interactief visualisatie dashboard om patronen in gegevens en uitleg te ontdekken tijdens de trainings tijd

In machine learning zijn **functies** de gegevens velden die worden gebruikt om een doel gegevens punt te voors pellen. Bijvoorbeeld, om het krediet risico te voors pellen, kunnen gegevens velden voor leeftijd, account grootte en account leeftijd worden gebruikt. In dit geval zijn de leeftijd, de account grootte en de account duur **functies**. Functie belang vertelt u hoe elk gegevens veld de voor spellingen van het model beïnvloedt. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voor spelling, terwijl de grootte van het account en de leeftijd geen invloed hebben op de Voorspellings waarden. Met dit proces kunnen gegevens wetenschappers de resulterende voor spellingen uitleggen, zodat de belanghebbenden inzicht hebben in de functies die het belangrijkst zijn in het model.

## <a name="supported-interpretability-techniques"></a>Ondersteunde technieken voor interpretatie

 `azureml-interpret` maakt gebruik van de technieken voor interpretaties die zijn ontwikkeld in [interprete-Community](https://github.com/interpretml/interpret-community/), een open source python-pakket voor het interpreteren van modellen en helpen bij het uitleggen van blackbox AI-systemen. [Interpreteren: Community](https://github.com/interpretml/interpret-community/) fungeert als host voor de ondersteunde uitlegers van deze SDK en ondersteunt momenteel de volgende methoden voor interpretatie:

|Geïnterpreteerde techniek|Beschrijving|Type|
|--|--|--------------------|
|Uitleg over de SHAP-structuur| De boom uitleg van de [Shap](https://github.com/slundberg/shap), die gericht is op een polynomiale, snelle Shap waarde schattings algoritme die specifiek is voor **bomen en ensembles van structuren**.|Model-specifiek|
|Uitgebreide uitleg van SHAP| Op basis van de uitleg van SHAP is diepe uitleg een uiterst snelle benaderings algoritme voor SHAP-waarden in diepe leer modellen die zijn gebaseerd op een verbinding met DeepLIFT beschreven in het [Shap NIPS-papier](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **Tensor flow** -modellen en **Keras** -modellen met behulp van de tensor flow-backend worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch) '.|Model-specifiek|
|SHAP lineaire uitleg| De lineaire uitleg van SHAP berekent SHAP-waarden voor een **lineair model**, eventueel administratief voor correlaties tussen functies.|Model-specifiek|
|SHAP-kernel-uitleg| De kernel-uitleg van SHAP maakt gebruik van een speciaal gewogen lokale lineaire regressie om SHAP-waarden voor **elk model** te schatten.|Model-neutraal|
|Nabooter (globaal surrogaat)| Nabooter is gebaseerd op het idee van [globale surrogaat modellen](https://christophm.github.io/interpretable-ml-book/global.html) met training om blackbox modellen te simuleren. Een globaal surrogaat model is een intrinsiek interpretable model dat is getraind om de voor spellingen van **een zwart box-model** zo nauw keurig mogelijk te benaderen. Gegevens wetenschappers kunnen het surrogaat model interpreteren om conclusies over het zwarte box-model te tekenen. U kunt een van de volgende verwerkte modellen gebruiken als surrogaat model: LightGBM (LGBMExplainableModel), lineaire regressie (LinearExplainableModel), stochastische Gradient Daal verklarend model (SGDExplainableModel) en beslissings structuur (DecisionTreeExplainableModel).|Model-neutraal|
|Beschrijving van de permutatie functie (PFI)| Het belang van de permutatie functie is een techniek die wordt gebruikt om classificatie-en regressie modellen te verklaren die zijn geïnspireerd op [het breiman van een wille keurige bossen](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (zie sectie 10). Op hoog niveau kan de manier waarop het werkt, worden uitgevoerd door in wille keurige volg orde de gegevens per functie op te nemen voor de hele gegevensset en te berekenen hoeveel de prestatie metriek van de interesses is gewijzigd. Hoe groter de verandering, des te belang rijker deze functie is. PFI kan het algemene gedrag van **elk onderliggend model** uitleggen, maar verklaart geen afzonderlijke voor spellingen. |Model-neutraal|

Naast de methoden voor het interpreteren die hierboven worden beschreven, ondersteunen we nog een op SHAP gebaseerde uitleg, ook wel genoemd `TabularExplainer` . Afhankelijk van het model `TabularExplainer` gebruikt een van de ondersteunde Shap-uitleg:

* TreeExplainer voor alle modellen op basis van een structuur
* DeepExplainer voor DNN-modellen
* LinearExplainer voor lineaire modellen
* KernelExplainer voor alle andere modellen

`TabularExplainer` heeft ook aanzienlijke verbeteringen aangebracht in de functie en prestaties ten opzichte van de directe SHAP-uitleg:

* **Samen vatting van de initialisatie-gegevensset**. In gevallen waarin de snelheid van uitleg het belangrijkst is, vatten we de initialisatie-gegevensset samen en genereren ze een kleine set representatieve voor beelden, waardoor de generatie van de belang rijke waarden van de algemene en afzonderlijke onderdelen wordt versneld.
* **Bemonstert u de set evaluatie gegevens**. Als de gebruiker een grote set evaluatie voorbeelden heeft door gegeven, maar niet alle moet worden geëvalueerd, kan de para meter sample worden ingesteld op True om de berekening van de algehele model verklaringen te versnellen.

In het volgende diagram ziet u de huidige structuur van ondersteunde uitlegers.

[![Architectuur van Machine Learning-interpretaties](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Ondersteunde machine learning modellen

Het `azureml.interpret` pakket van de SDK ondersteunt modellen die zijn getraind met de volgende indelingen voor gegevensset:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

De uitleg functies accepteren zowel modellen als pijp lijnen als invoer. Als er een model wordt gegeven, moet het model de Voorspellings functie implementeren `predict` of `predict_proba` voldoet aan de Scikit-Conventie. Als uw model dit niet ondersteunt, kunt u uw model inpakken in een functie die hetzelfde resultaat genereert als `predict` of `predict_proba` in Scikit en die wrapper-functie gebruiken met de geselecteerde uitleger. Als er een pijp lijn wordt gegeven, wordt ervan uitgegaan dat het actieve pijplijn script een voor spelling retourneert. Het gebruik van deze terugloop techniek `azureml.interpret` kan modellen ondersteunen die zijn getraind via PyTorch, tensor flow en Kerase diep leer frameworks en klassieke machine learning modellen.

## <a name="local-and-remote-compute-target"></a>Lokaal en extern Compute-doel

Het `azureml.interpret` pakket is ontworpen om te werken met zowel lokale als externe Compute-doelen. Als de SDK-functies lokaal worden uitgevoerd, worden er geen contact opgenomen met Azure-Services. 

U kunt uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleg informatie vastleggen in de Azure Machine Learning uitvoerings geschiedenis service. Zodra deze informatie is geregistreerd, zijn rapporten en visualisaties van de uitleg beschikbaar op Azure Machine Learning Studio voor gebruikers analyse.


## <a name="next-steps"></a>Volgende stappen

- Zie de [procedure](how-to-machine-learning-interpretability-aml.md) voor het inschakelen van de functie voor interpretering voor model training zowel lokaal als op Azure machine learning externe Compute-resources. 
- Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) voor aanvullende scenario's. 
- Zie Interpreting [-Text](https://github.com/interpretml/interpret-text), een gerelateerde open source-opslag plaats om te interpreteren [-Community](https://github.com/interpretml/interpret-community/), voor de interpretatieve technieken voor NLP, als u geïnteresseerd bent in de interpretatie van tekst scenario's. `azureml.interpret` het pakket biedt momenteel geen ondersteuning voor deze technieken, maar u kunt aan de slag met een [voorbeeld notitieblok in tekst classificatie](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).