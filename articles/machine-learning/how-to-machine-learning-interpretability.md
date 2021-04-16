---
title: Model interpreteerbaarheid in Azure Machine Learning (preview)
titleSuffix: Azure Machine Learning
description: Leer hoe u & hoe uw machine learning-model voorspellingen doet tijdens het trainen & de deferencing met behulp van de Python SDK Azure Machine Learning python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: c517cf2fc8491d62cf2379c87acd2eaadde8fe15
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576428"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Model interpreteerbaarheid in Azure Machine Learning (preview)


## <a name="model-interpretability-overview"></a>Overzicht van de interpreteerbaarheid van modellen

De interpreteerbaarheid van modellen is essentieel voor gegevenswetenschappers, auditors en besluitvormers om ervoor te zorgen dat het bedrijfsbeleid, industrienormen en overheidsvoorschriften worden nageleefd:

+ Gegevenswetenschappers moeten hun modellen kunnen uitleggen aan leidinggevenden en belanghebbenden, zodat ze de waarde en nauwkeurigheid van hun bevindingen kunnen begrijpen. Ze vereisen ook interpreteerbaarheid om fouten in hun modellen op te sporen en weloverwogen beslissingen te nemen over hoe ze deze kunnen verbeteren. 

+ Juridische auditors hebben hulpprogramma's nodig om modellen te valideren met betrekking tot naleving van regelgeving en om te controleren hoe beslissingen van modellen mensen beïnvloeden. 

+ Besluitvormers hebben behoefte aan een gerust hart door de mogelijkheid om eindgebruikers transparantie te bieden. Hierdoor kunnen ze vertrouwen verdienen en onderhouden.

Het inschakelen van de mogelijkheid om een machine learning model uit te leggen is belangrijk tijdens twee hoofdfasen van modelontwikkeling:

+ Tijdens de trainingsfase kunnen modelontwerpers en evaluators de uitvoer van de interpreteerbaarheid van een model gebruiken om hypothesen te verifiëren en vertrouwen te bouwen met belanghebbenden. Ze gebruiken ook de inzichten in het model voor het debuggen, valideren van modelgedrag dat overeenkomt met hun doelstellingen en om te controleren op modelinzichtigheid of ongelijke kenmerken.

+ Tijdens de deferencingsfase, omdat transparantie rond geïmplementeerde modellen leidinggevenden in staat stelt om 'wanneer geïmplementeerd' te begrijpen hoe het model werkt en hoe de beslissingen van het model mensen in het echt behandelen en beïnvloeden. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpreteerbaarheid met Azure Machine Learning

De model interpreteerbaarheidsklassen worden beschikbaar gesteld via het volgende SDK-pakket: (Meer informatie over het installeren van [SDK-pakketten voor Azure Machine Learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`, bevat functies die worden ondersteund door Microsoft.

Gebruiken `pip install azureml-interpret` voor algemeen gebruik.

## <a name="how-to-interpret-your-model"></a>Uw model interpreteren

Met behulp van de klassen en methoden in de SDK kunt u het volgende doen:
+ Leg modelvoorspelling uit door belangwaarden voor functies te genereren voor het hele model en/of afzonderlijke gegevenspunten. 
+ Interpreteerbaarheid van modellen realiseren voor gegevenssets in de echte wereld op schaal, tijdens training en de deferie.
+ Een dashboard voor interactieve visualisatie gebruiken om patronen in gegevens en uitleg te ontdekken tijdens het trainen

In machine learning zijn **functies de** gegevensvelden die worden gebruikt om een doelgegevenspunt te voorspellen. Als u bijvoorbeeld kredietrisico wilt voorspellen, kunnen gegevensvelden voor leeftijd, accountgrootte en leeftijd van het account worden gebruikt. In dit geval zijn leeftijd, accountgrootte en accountleeftijd **functies**. Functie-belang vertelt u hoe elk gegevensveld van invloed was op de voorspellingen van het model. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voorspelling, terwijl de accountgrootte en -leeftijd de voorspellingswaarden niet aanzienlijk beïnvloeden. Met dit proces kunnen gegevenswetenschappers de resulterende voorspellingen uitleggen, zodat belanghebbenden inzicht hebben in welke functies het belangrijkst zijn in het model.

## <a name="supported-interpretability-techniques"></a>Ondersteunde interpreteerbaarheidstechnieken

 `azureml-interpret` maakt gebruik van de interpreteerbaarheidstechnieken die zijn ontwikkeld in [Interpret-Community,](https://github.com/interpretml/interpret-community/)een open source Python-pakket voor het trainen van interpreteerbare modellen en het verklaren van BLACKBox AI-systemen. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungeert als host voor de ondersteunde uitleg over deze SDK en ondersteunt momenteel de volgende interpreteerbaarheidstechnieken:

|Interpreteerbaarheidstechniek|Beschrijving|Type|
|--|--|--------------------|
|SHAP Tree Explainer| [Shap](https://github.com/slundberg/shap)'s tree explainer, die zich richt op polynomiale tijd snelle SHAP-waardeschattingsalgoritme specifiek voor boomen en **ensembles van bomen**.|Modelsyte specifiek|
|SHAP Deep Explainer| Op basis van de uitleg van SHAP is Deep Explainer een high-speed benaderingsalgoritme voor SHAP-waarden in deep learning-modellen dat is gebaseerd op een verbinding met Deep LIFT die wordt beschreven in het [shap NIPS-document](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow-modellen** en **Keras-modellen** die gebruikmaken van de TensorFlow-back-end worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch)".|Modelsyte specifiek|
|SHAP Linear Explainer| Linear explainer van SHAP berekent SHAP-waarden voor een lineair **model,** optioneel waarmee correlaties tussen functies worden verwerkt.|Modelsyte specifiek|
|SHAP Kernel Explainer| Kernel-uitleg van SHAP maakt gebruik van een speciaal gewogen lokale lineaire regressie om SHAP-waarden voor elk **model te schatten.**|Modelagnostisch|
|Mimic Explainer (globale surrogaat)| Mimic explainer is gebaseerd op het idee van het trainen van [globale surrogaatmodellen om](https://christophm.github.io/interpretable-ml-book/global.html) blackbox-modellen na te bootsen. Een globaal surrogaatmodel is een intrinsiek interpreteerbaar model dat is getraind om de voorspellingen van een **black box-model** zo nauwkeurig mogelijk te benaderen. Gegevenswetenschappers kunnen het surrogaatmodel interpreteren om conclusies te trekken over het black box-model. U kunt een van de volgende interpreteerbare modellen gebruiken als uw surrogaatmodel: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) en Decision Tree (DecisionTreeExplainableModel).|Modelagnostisch|
|PFI (Permutation Feature Importance Explainer)| Het belang van permutatiefunctie is een techniek die wordt gebruikt om classificatie- en regressiemodellen uit te leggen die is geïnspireerd op het artikel Random [Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (zie sectie 10). Op hoog niveau is de manier waarop het werkt, het willekeurig in willekeurige volgorde insuffren van gegevens per functie voor de hele gegevensset en het berekenen van de mate van metrische prestatiewijzigingen. Hoe groter de wijziging, hoe belangrijker de functie is. PFI kan het algemene gedrag van elk **onderliggend model** verklaren, maar verklaart geen afzonderlijke voorspellingen. |Modelagnostisch|

Naast de technieken voor interpreteerbaarheid die hierboven worden beschreven, ondersteunen we nog een uitleg op basis van SHAP, genaamd `TabularExplainer` . Afhankelijk van het model gebruikt `TabularExplainer` een van de ondersteunde SHAP-uitlegs:

* TreeExplainer voor alle modellen op basis van een boomstructuur
* DeepExplainer voor DNN-modellen
* LinearExplainer voor lineaire modellen
* KernelExplainer voor alle andere modellen

`TabularExplainer` heeft ook belangrijke verbeteringen aangebracht in de functies en prestaties van de directe SHAP-uitlegders:

* **Samenvatting van de initialisatie-gegevensset**. In gevallen waarin de snelheid van de uitleg het belangrijkst is, geven we een overzicht van de initialisatie-gegevensset en genereren we een kleine set representatieve steekproeven, waardoor het genereren van algemene en afzonderlijke functie-belangwaarden wordt versnellen.
* **Steekproeven nemen van de evaluatiegegevensset**. Als de gebruiker een grote set evaluatievoorbeelden doorgeeft, maar ze niet allemaal hoeft te worden geëvalueerd, kan de samplingparameter worden ingesteld op true om de berekening van de algehele modelverklaringen te versnellen.

In het volgende diagram ziet u de huidige structuur van ondersteunde uitlegders.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Ondersteunde machine learning modellen

Het `azureml.interpret` pakket van de SDK ondersteunt modellen die zijn getraind met de volgende gegevenssetindelingen:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

De uitlegfuncties accepteren modellen en pijplijnen als invoer. Als er een model wordt opgegeven, moet het model de voorspellingsfunctie implementeren of voldoen `predict` `predict_proba` aan de Scikit-conventie. Als uw model dit niet ondersteunt, kunt u uw model verpakken in een functie die hetzelfde resultaat genereert als of in Scikit en die wrapper-functie gebruiken met de geselecteerde `predict` `predict_proba` uitlegfunctie. Als er een pijplijn wordt opgegeven, wordt met de uitlegfunctie ervan uitgenomen dat het script voor de lopende pijplijn een voorspelling retourneert. Met behulp van deze wrapping-techniek kunnen modellen worden ondersteund die zijn getraind `azureml.interpret` via PyTorch, TensorFlow en Keras deep learning-frameworks, evenals klassieke machine learning modellen.

## <a name="local-and-remote-compute-target"></a>Lokaal en extern rekendoel

Het `azureml.interpret` pakket is ontworpen om te werken met lokale en externe rekendoelen. Als de SDK-functies lokaal worden uitgevoerd, nemen ze geen contact op met Azure-services. 

U kunt uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleggegevens in het logboek Azure Machine Learning Run History Service. Zodra deze informatie is vastgelegd, zijn rapporten en visualisaties uit de uitleg direct beschikbaar op Azure Machine Learning-studio voor gebruikersanalyse.


## <a name="next-steps"></a>Volgende stappen

- Zie de [uitleg voor het inschakelen](how-to-machine-learning-interpretability-aml.md) van interpreteerbaarheid voor modellen die zowel lokaal als op Azure Machine Learning externe rekenbronnen. 
- Meer informatie over het [inschakelen van interpreteerbaarheid voor geautomatiseerde machine learning modellen.](how-to-machine-learning-interpretability-automl.md)
- Zie de [voorbeeldnote notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) voor aanvullende scenario's. 
- Als u geïnteresseerd bent in interpreteerbaarheid voor tekstscenario's, zie [Interpret-text](https://github.com/interpretml/interpret-text), een gerelateerde open source-repo voor [Interpret-Community,](https://github.com/interpretml/interpret-community/)voor interpreteerbaarheidstechnieken voor NLP. `azureml.interpret`het pakket biedt momenteel geen ondersteuning voor deze technieken, maar u kunt aan de slag met een [voorbeeldnotenote over tekstclassificatie.](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)