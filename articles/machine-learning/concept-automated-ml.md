---
title: Wat is geautomatiseerde ML? AutoML
titleSuffix: Azure Machine Learning
description: Ontdek hoe Azure Machine Learning automatisch een model kunt genereren met behulp van de parameters en criteria die u op geeft.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6f8f775e474b47cbfd5f3b4aca8987a009a7f6e1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874131"
---
# <a name="what-is-automated-machine-learning-automl"></a>Wat is geautomatiseerde machine learning (AutoML)?

Geautomatiseerde machine learning, ook wel geautomatiseerde ML of AutoML genoemd, is het proces van het automatiseren van de tijdrovende, iteratieve taken van machine learning modelontwikkeling. Hiermee kunnen gegevenswetenschappers, analisten en ontwikkelaars ML-modellen bouwen met een hoge schaal, efficiëntie en productiviteit en tegelijkertijd de kwaliteit van het model handhaven. Geautomatiseerde ML in Azure Machine Learning is gebaseerd op een doorbrak van onze [Microsoft Research-afdeling.](https://www.microsoft.com/research/project/automl/)

Traditionele machine learning modelontwikkeling is resource-intensief, wat veel domeinkennis en -tijd vereist om tientallen modellen te produceren en te vergelijken. Met geautomatiseerde machine learning versnelt u de tijd die nodig is om ml-modellen die gereed zijn voor productie met veel gemak en efficiëntie te krijgen.

## <a name="automl-in-azure-machine-learning"></a>AutoML in Azure Machine Learning

Azure Machine Learning biedt twee ervaringen voor het werken met geautomatiseerde ML:

* Voor klanten met code-ervaring gebruikt [Azure Machine Learning Python SDK.](/python/api/overview/azure/ml/intro)  Aan de slag met [Zelfstudie: Geautomatiseerde machine learning om taxiritten te voorspellen.](tutorial-auto-train-models.md)

* Voor klanten met beperkte/geen code-ervaring kunt Azure Machine Learning-studio op [https://ml.azure.com](https://ml.azure.com/) .  Ga aan de slag met deze zelfstudies:
    * [Zelfstudie: Een classificatiemodel maken met geautomatiseerde ML in Azure Machine Learning](tutorial-first-experiment-automated-ml.md).
    *  [Zelfstudie: Vraag voorspellen met automatische machine learning](tutorial-automated-ml-forecast.md)


## <a name="when-to-use-automl-classify-regression--forecast"></a>Wanneer u AutoML gebruikt: classificeren, regressie, & voorspellen

Pas geautomatiseerde ML toe wanneer u Azure Machine Learning model voor u wilt trainen en afstemmen met behulp van de doelmetrische gegevens die u opgeeft. Geautomatiseerde ML democratiseert het machine learning-modelontwikkelingsproces en stelt de gebruikers, ongeacht hun expertise op het gebied van data science, in staat om een end-to-end machine learning-pijplijn te identificeren voor elk probleem.

Gegevenswetenschappers, analisten en ontwikkelaars in verschillende branches kunnen geautomatiseerde ML gebruiken voor het volgende:
+ ML-oplossingen implementeren zonder uitgebreide programmeerkennis
+ Tijd en resources besparen
+ Best practices voor data science gebruiken
+ Flexibele probleemoplossing bieden

### <a name="classification"></a>Classificatie

Classificatie is een veelvoorkomende machine learning taak. Classificatie is een type leren onder supervisie waarin modellen leren met behulp van trainingsgegevens en deze kennis toepassen op nieuwe gegevens. Azure Machine Learning biedt featurizations specifiek voor deze taken, zoals deep neural network text featurizers voor classificatie. Meer informatie over [featurization-opties.](how-to-configure-auto-features.md#featurization) 

Het belangrijkste doel van classificatiemodellen is om te voorspellen in welke categorieën nieuwe gegevens vallen op basis van de trainingsgegevens. Veelvoorkomende classificatievoorbeelden zijn fraudedetectie, handschriftherkenning en objectdetectie. Zie Een classificatiemodel maken met geautomatiseerde ML voor meer informatie en een [voorbeeld.](tutorial-first-experiment-automated-ml.md)

Zie voorbeelden van classificatie en geautomatiseerde machine learning in deze Python-notebooks: [Fraudedetectie,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [Marketingvoorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)en [Newsgroup-gegevensclassificatie](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regressie

Net als bij classificatie zijn regressietaken ook een algemene leertaak onder supervisie. Azure Machine Learning biedt [specifieke featurizations voor deze taken.](how-to-configure-auto-features.md#featurization)

Anders dan bij classificatie waarbij voorspelde uitvoerwaarden categorisch zijn, voorspellen regressiemodellen numerieke uitvoerwaarden op basis van onafhankelijke voorspellingen. In regressie is het doel om te helpen de relatie tot stand te brengen tussen deze onafhankelijke voorspellingsvariabelen door te schatten hoe één variabele de andere beïnvloedt. Bijvoorbeeld de prijs van auto's op basis van functies zoals, kilometerstand bij gas, veiligheidsclassificatie, enzovoort. Meer informatie en een voorbeeld van [regressie met geautomatiseerde machine learning](tutorial-auto-train-models.md).

Zie voorbeelden van regressie en geautomatiseerde machine learning voor voorspellingen in deze Python-notebooks: [CPU-prestatievoorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognose voor tijdreeksen

Het bouwen van prognoses is een integraal onderdeel van elk bedrijf, of het nu gaat om omzet, inventaris, verkoop of vraag van klanten. U kunt geautomatiseerde ML gebruiken om technieken en benaderingen te combineren en een aanbevolen tijdreeksprognose van hoge kwaliteit te krijgen. Meer informatie met deze zelf-how-to: [geautomatiseerde machine learning voor tijdreeksprognoses.](how-to-auto-train-forecast.md) 

Een geautomatiseerd tijdreeksexperiment wordt behandeld als een multivariate regressieprobleem. Waarden uit eerdere tijdreeksen worden 'draaiend' om extra dimensies voor de regressor te worden, samen met andere voorspellingen. Deze benadering heeft, in tegenstelling tot klassieke tijdreeksmethoden, een voordeel van het op natuurlijke wijze opnemen van meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Geautomatiseerde ML leert één, maar vaak intern vertakt model voor alle items in de gegevensset en voorspellingsperioden. Er zijn dus meer gegevens beschikbaar voor het schatten van modelparameters en generalisatie voor niet-geziene reeksen.

Geavanceerde prognoseconfiguratie omvat:
* vakantiedetectie en -featurization
* tijdreeks- en DNN-studenten (Auto-ARIMA, Timeline, ForecastTCN)
* ondersteuning voor veel modellen via groepering
* kruisvalidatie rolling-origin
* configureerbare vertragingen
* functies voor aggregatie van rolling window


Zie voorbeelden van regressie en geautomatiseerde machine learning voor voorspellingen [](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)in deze Python-notebooks: Verkoopprognose, [](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)Vraagprognose en [Prognose voor productie.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="how-automated-ml-works"></a>Hoe geautomatiseerde ML werkt

Tijdens de training Azure Machine Learning een aantal pijplijnen parallel gemaakt waarmee verschillende algoritmen en parameters voor u worden geprobeerd. De service doormaakt ml-algoritmen die zijn gekoppeld aan functieselecties, waarbij elke iteratie een model met een trainingsscore produceert. Hoe hoger de score, hoe beter het model wordt beschouwd als 'passend' in uw gegevens.  Deze wordt gestopt zodra deze voldoet aan de exitcriteria die zijn gedefinieerd in het experiment. 

Met **Azure Machine Learning** kunt u uw geautomatiseerde ML-trainingsexperimenten ontwerpen en uitvoeren met de volgende stappen:

1. **Identificeer het ML-probleem** dat moet worden opgelost: classificatie, prognose of regressie

1. **Kies of u de Python SDK** of de studio-webervaring wilt gebruiken: meer informatie over de pariteit tussen de [Python SDK en de Studio-webervaring.](#parity)

   * Voor beperkte of geen code-ervaring kunt u de Azure Machine Learning-studio webervaring op [https://ml.azure.com](https://ml.azure.com/)  
   * Voor Python-ontwikkelaars raadpleegt u [de Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 
    
1. **Geef de bron en indeling van de gelabelde trainingsgegevens** op: Numpy-matrices of Pandas-gegevensframe

1. **Configureer het rekendoel voor modeltraining,** zoals uw lokale [computer, Azure Machine Learning Computes,](how-to-set-up-training-targets.md)externe VM's of Azure Databricks .

1. Configureer de geautomatiseerde **machine learning-parameters** die bepalen hoeveel iteraties er worden gebruikt voor verschillende modellen, hyperparameterinstellingen, geavanceerde voorverwerking/parametrisatie en welke metrische gegevens u moet bekijken bij het bepalen van het beste model.  
1. **Verzend de trainingsrun.**

1. **De resultaten bekijken** 

Het volgende diagram illustreert dit proces. 
![Geautomatiseerde machine learning](./media/concept-automated-ml/automl-concept-diagram2.png)


U kunt ook de vastgelegde gegevens van de run inspecteren, die [metrische gegevens bevat](how-to-understand-automated-ml.md) die tijdens de run zijn verzameld. De trainingsrun produceert een geseraliseerd Python-object (bestand) dat het `.pkl` model en de voorverwerking van gegevens bevat.

Hoewel het bouwen van modellen geautomatiseerd is, kunt u ook [leren hoe belangrijk of relevant de functies zijn](how-to-configure-auto-train.md#explain) voor de gegenereerde modellen.



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Functie-engineering

Functie-engineering is het proces waarbij domeinkennis van de gegevens wordt gebruikt om functies te maken waarmee ML-algoritmen beter kunnen leren. In Azure Machine Learning worden schaal- en normalisatietechnieken toegepast om de feature engineering. Gezamenlijk worden deze technieken en feature engineering aangeduid als featurization.

Voor geautomatiseerde machine learning wordt featurization automatisch toegepast, maar kan ook worden aangepast op basis van uw gegevens. [Meer informatie over wat featurization is opgenomen.](how-to-configure-auto-features.md#featurization)  

> [!NOTE]
> Geautomatiseerde machine learning functienormalisatie (functienormalisatie, verwerking van ontbrekende gegevens, het converteren van tekst naar numerieke gegevens, enzovoort) maken deel uit van het onderliggende model. Wanneer u het model voor voorspellingen gebruikt, worden dezelfde featurization-stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoergegevens.

### <a name="automatic-featurization-standard"></a>Automatische featurization (standaard)

In elk geautomatiseerd machine learning experiment worden uw gegevens automatisch geschaald of genormaliseerd om algoritmen goed te laten presteren. Tijdens het trainen van het model wordt een van de volgende schaal- of normalisatietechnieken toegepast op elk model. Meer informatie over hoe AutoML helpt te voorkomen dat gegevens in uw modellen [overfitting](concept-manage-ml-pitfalls.md) en onevenwichtigheid veroorzaken.

|Verwerking &nbsp; & &nbsp; schalen| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Functies standaardiseren door het gemiddelde te verwijderen en te schalen naar eenheidsvariantie  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformeert functies door elke functie te schalen op het minimum en maximum van die kolom  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Elke functie schalen met de maximale absolute waarde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Deze scaler is voorzien van hun kwantielbereik |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linear dimensionality reduction using Singular Value Decomposition of the data to project it to a lower dimensional space (Lineaire dimensionaliteitsvermindering met behulp van enkelvoudige waardeafleding van de gegevens om deze naar een lager dimensionale ruimte te projecteren) |
| [AfgekaptSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Met deze transformator wordt lineaire dimensionaliteitsvermindering uitgevoerd door middel van afgekapte enkelvoudige waardedecompositie (SVD). In tegenstelling tot PCA centreert deze estimator de gegevens niet voordat de enkelvoudige waarde wordt ontleding bewerkt, wat betekent dat deze efficiënt kan werken met scipy.sparse-matrices |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Elk voorbeeld (dat wil zeggen, elke rij van de gegevensmatrix) met ten minste één niet-nulonderdeel wordt onafhankelijk van andere steekproeven opnieuw geschaald, zodat de norm (l1 of l2) gelijk is aan één |

### <a name="customize-featurization"></a>Featurization aanpassen

Er feature engineering extra technieken beschikbaar, zoals codering en transformaties. 

Schakel deze instelling in met:

+ Azure Machine Learning-studio: Schakel **Automatische featurization** in de **sectie Aanvullende configuratie** weergeven in met deze [stappen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: geef `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` op in uw [AutoMLConfig-object.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) Meer informatie over het [inschakelen van featurization](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensemblemodellen

Geautomatiseerde machine learning ondersteuning voor ensemblemodellen, die standaard zijn ingeschakeld. Ensemble learning verbetert machine learning resultaten en voorspellende prestaties door meerdere modellen te combineren in plaats van afzonderlijke modellen te gebruiken. De ensemble-iteraties worden weergegeven als de laatste iteraties van uw run. Geautomatiseerde machine learning maakt gebruik van stem- en stacking-ensemblemethoden voor het combineren van modellen:

* **Stemmen:** voorspelt op basis van het gewogen gemiddelde van voorspelde klassekansen (voor classificatietaken) of voorspelde regressiedoelen (voor regressietaken).
* **Stapelen:** stapelen combineert heterogene modellen en traint een metamodel op basis van de uitvoer van de afzonderlijke modellen. De huidige standaard metamodellen zijn LogisticRegression voor classificatietaken en ElasticNet voor regressie-/prognosetaken.

Het [selectiealgoritme van het Caruana-ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) met gesorteerde ensemble-initialisatie wordt gebruikt om te bepalen welke modellen in het ensemble moeten worden gebruikt. Op hoog niveau initialiseert dit algoritme het ensemble met maximaal vijf modellen met de beste afzonderlijke scores en wordt gecontroleerd of deze modellen binnen de drempelwaarde van 5% van de beste score liggen om een slecht eerste ensemble te voorkomen. Vervolgens wordt voor elke ensemble-iteratie een nieuw model toegevoegd aan het bestaande ensemble en wordt de resulterende score berekend. Als een nieuw model de bestaande ensemblescore heeft verbeterd, wordt het ensemble bijgewerkt met het nieuwe model.

Zie de [how-to voor het](how-to-configure-auto-train.md#ensemble) wijzigen van standaardinstellingen voor ensembles in geautomatiseerde machine learning.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Richtlijnen voor lokale versus extern beheerde ML-rekendoelen

De webinterface voor geautomatiseerde ML maakt altijd gebruik van een extern [rekendoel.](concept-compute-target.md)  Maar wanneer u de Python-SDK gebruikt, kiest u een lokaal rekendoel of een extern rekendoel voor geautomatiseerde ML-training.

* **Lokale rekenkracht:** Training vindt plaats op uw lokale laptop of VM-rekenkracht. 
* **Extern berekenen:** training vindt plaats op Machine Learning rekenclusters.  

### <a name="choose-compute-target"></a>Rekendoel kiezen
Houd rekening met deze factoren bij het kiezen van uw rekendoel:

 * **Kies een lokale berekening:** als uw scenario gaat over initiële verkenningen of demo's met behulp van kleine gegevens en korte trainen (dat wil zeggen seconden of een paar minuten per onderliggende run), is training op uw lokale computer mogelijk een betere keuze.  Er is geen installatietijd, de infrastructuurbronnen (uw pc of VM) zijn rechtstreeks beschikbaar.
 * Een extern **ML-rekencluster** kiezen: als u met grotere gegevenssets traint, zoals bij het maken van modellen voor productietraining die langere training nodig hebben, biedt externe rekenkracht veel betere end-to-end-tijdprestaties, omdat de trainen op de knooppunten van het cluster parallel worden `AutoML` uitgevoerd. Op een externe berekening wordt de opstarttijd voor de interne infrastructuur ongeveer 1,5 minuten per onderliggende run, plus extra minuten voor de clusterinfrastructuur als de VM's nog niet actief zijn.

### <a name="pros-and-cons"></a>Voor- en nadelen
Houd rekening met deze voor- en nadelen bij het kiezen van lokale versus externe.

|  | Voordelen (voordelen)  |Nadelen (handicaps)  |
|---------|---------|---------|---------|
|**Lokaal rekendoel** |  <li> Geen opstarttijd omgeving   | <li>  Subset van functies<li>  Kan runs niet parallelliseren <li> Slechter voor grote gegevens. <li>Geen gegevensstreaming tijdens training <li>  Geen op DNN gebaseerde featurization <li> Alleen Python SDK |
|**Externe ML-rekenclusters**|  <li> Volledige set functies <li> Onderliggende runs parallelliseren <li>   Ondersteuning voor grote gegevens<li>  Op DNN gebaseerde featurization <li>  Dynamische schaalbaarheid van rekencluster op aanvraag <li> No-code experience (web-UI) also available (Geen code-ervaring (webinterface) ook beschikbaar  |  <li> Opstarttijd voor clusterknooppunten <li> Opstarttijd voor elke onderliggende run    |

### <a name="feature-availability"></a>Beschikbaarheid van functies 

 Er zijn meer functies beschikbaar wanneer u de externe berekening gebruikt, zoals wordt weergegeven in de onderstaande tabel. 

| Functie                                                    | Extern | Lokaal | 
|------------------------------------------------------------|--------|-------|
| Gegevensstreaming (ondersteuning voor grote gegevens, maximaal 100 GB)          | ✓      |       | 
| Op DNN gebaseerde tekst featurization en training op basis van DNN             | ✓      |       |
| Out-of-the-box GPU-ondersteuning (training en deference)        | ✓      |       |
| Ondersteuning voor afbeeldingsclassificatie en labels                  | ✓      |       |
| Auto-ARIMA- En PrognoseTCN-modellen voor prognoses | ✓      |       | 
| Meerdere runs/iteraties parallel                       | ✓      |       |
| Modellen met interpreteerbaarheid maken in de gebruikersinterface van AutoML Studio-webervaring      | ✓      |       |
| Aanpassing van functie-engineering in de gebruikersinterface van studio-webervaring| ✓      |       |
| Hyperparameter-afstemming van Azure ML                             | ✓      |       |
| Ondersteuning voor Azure ML Pipeline-werkstroom                         | ✓      |       |
| Doorgaan met uitvoeren                                             | ✓      |       |
| Prognose                                                | ✓      | ✓     |
| Experimenten maken en uitvoeren in notebooks                    | ✓      | ✓     |
| De gegevens en metrische gegevens van het experiment registreren en visualiseren in de gebruikersinterface | ✓      | ✓     |
| Gegevensbescherming                                            | ✓      | ✓     |

## <a name="many-models"></a>Veel modellen 

De [](https://aka.ms/many-models) oplossingsversneller Veel modellen (preview) bouwt voort op Azure Machine Learning en stelt u in staat om geautomatiseerde ML te gebruiken voor het trainen, gebruiken en beheren van honderden of zelfs duizenden machine learning modellen.

Het bouwen van een model __voor elke instantie of elk individu__ in de volgende scenario's kan bijvoorbeeld leiden tot verbeterde resultaten:

* Verkoop voor elke afzonderlijke winkel voorspellen
* Voorspellend onderhoud voor honderden olievelden
* Een ervaring aanpassen voor afzonderlijke gebruikers.

<a name="parity"></a>

### <a name="experiment-settings"></a>Experimentinstellingen 

Met de volgende instellingen kunt u uw geautomatiseerde ML-experiment configureren. 

| |De Python-SDK|De studio-webervaring|
----|:----:|:----:
|**Gegevens splitsen in sets voor trainen/valideren**| ✓|✓
|**Ondersteunt ML-taken: classificatie, regressie en prognose**| ✓| ✓
|**Optimaliseert op basis van primaire metrische gegevens**| ✓| ✓
|**Ondersteunt Azure ML Compute als rekendoel** | ✓|✓
|**Prognoseperiode configureren, doelvertraging & rolling window**|✓|✓
|**Exitcriteria instellen** |✓|✓ 
|**Gelijktijdige iteraties instellen**| ✓|✓
|**Kolommen verwijderen**| ✓|✓
|**Algoritmen blokkeren**|✓|✓
|**Kruisvalidatie** |✓|✓
|**Ondersteunt training op Azure Databricks clusters**| ✓|
|**Namen van ontworpen functies weergeven**|✓|
|**Samenvatting van featurization**| ✓|
|**Featurization voor feestdagen**|✓|
|**Verbosity-niveaus van logboekbestanden**| ✓|

### <a name="model-settings"></a>Modelinstellingen

Deze instellingen kunnen worden toegepast op het beste model als gevolg van uw geautomatiseerde ML-experiment.

| |De Python-SDK|De studio-webervaring|
|----|:----:|:----:|
|**Beste modelregistratie, implementatie, uitleg**| ✓|✓|
|**Stemmende ensemble-& stack-ensemblemodellen inschakelen**| ✓|✓|
|**Beste model tonen op basis van niet-primaire metrische gegevens**|✓||
|**Compatibiliteit met ONNX-modellen in-/uitschakelen**|✓||
|**Het model testen** | ✓| |

### <a name="run-control-settings"></a>Instellingen voor besturingselementen uitvoeren

Met deze instellingen kunt u uw experiment en de onderliggende runs controleren en beheren. 

| |De Python-SDK|De studio-webervaring|
|----|:----:|:----:|
|**Samenvattingstabel uitvoeren**| ✓|✓|
|**Runs annuleren & onderliggende runs**| ✓|✓|
|**Beveiligingsrails krijgen**| ✓|✓|
|**De & hervatten onderbreken**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Met Azure Machine Learning kunt u geautomatiseerde ML gebruiken om een Python-model te bouwen en dit te laten omzetten naar de ONNX-indeling. Zodra de modellen de ONNX-indeling hebben, kunnen ze worden uitgevoerd op verschillende platforms en apparaten. Meer informatie over [het versnellen van ML-modellen met ONNX](concept-onnx.md).

Zie hoe u kunt converteren naar ONNX-indeling [in dit Jupyter Notebook-voorbeeld.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Ontdek welke [algoritmen worden ondersteund in ONNX.](how-to-configure-auto-train.md#select-your-experiment-type)

De ONNX-runtime ondersteunt ook C#, zodat u het model dat automatisch is gebouwd in uw C#-apps kunt gebruiken zonder dat u hoeft te coderen of een van de netwerklatentie die REST-eindpunten introduceren. Meer informatie over het gebruik van een [AutoML ONNX-model in](./how-to-use-automl-onnx-model-dotnet.md) een .NET-toepassing met ML.NET en de deferencing van [ONNX-modellen met de ONNX runtime C#-API.](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md) 

## <a name="next-steps"></a>Volgende stappen

Er zijn meerdere resources om u op weg te helpen met AutoML. 

### <a name="tutorials-how-tos"></a>Zelfstudies/handleidingen
Zelfstudies zijn end-to-end inleidende voorbeelden van AutoML-scenario's.
+ **Voor een eerste code-ervaring** volgt u de [Zelfstudie: Automatisch een regressiemodel](tutorial-auto-train-models.md)trainen met Azure Machine Learning Python SDK.

 + **Zie voor een ervaring met** weinig of geen code de [Zelfstudie: Geautomatiseerde ML-classificatiemodellen maken met Azure Machine Learning-studio](tutorial-first-experiment-automated-ml.md).

Artikelen met meer informatie over de functionaliteit die AutoML biedt. Bijvoorbeeld: 

+ De instellingen voor automatische trainingsexperimenten configureren
    + Gebruik Azure Machine Learning-studio stappen in [de volgende stappen.](how-to-use-automated-ml-for-ml-models.md) 
    + Gebruik deze stappen met [](how-to-configure-auto-train.md)de Python-SDK.

+  Ontdek met deze stappen hoe u automatisch kunt trainen met behulp [van tijdreeksgegevens.](how-to-auto-train-forecast.md)

### <a name="jupyter-notebook-samples"></a>Voorbeelden van Jupyter-notebooks 

Bekijk gedetailleerde codevoorbeelden en gebruiksvoorbeelden in de [GitHub-notebookopslagplaats voor geautomatiseerde machine learning voorbeelden.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

### <a name="python-sdk-reference"></a>Naslaginformagtie over de Python-SDK

U kunt uw expertise op het gebied van SDK-ontwerppatronen en klassespecificaties verdiepen met de naslagdocumentatie voor [AutoML-klassen.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 

> [!Note]
> Automatische machine learning zijn ook beschikbaar in andere Microsoft-oplossingen, zoals [ML.NET,](/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](/power-bi/service-machine-learning-automated) en [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
