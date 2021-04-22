---
title: Geautomatiseerde ML-experimenten maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het definiëren van gegevensbronnen, berekeningen en configuratie-instellingen voor uw geautomatiseerde machine learning experimenten.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: d0a15b16c04a28bcc67caeeceedfcbad485b7157
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861459"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Geautomatiseerde ML-experimenten configureren in Python


In deze handleiding leert u hoe u verschillende configuratie-instellingen van uw geautomatiseerde machine learning kunt definiëren met [de Azure Machine Learning SDK.](/python/api/overview/azure/ml/intro) Geautomatiseerde machine learning kiest een algoritme en hyperparameters voor u en genereert een model dat gereed is voor implementatie. Er zijn verschillende opties die u kunt gebruiken om geautomatiseerde machine learning configureren.

Zie Zelfstudie: Een classificatiemodel trainen met geautomatiseerde machine learning voor een end-to-end-voorbeeld [van een machine learning.](tutorial-auto-train-models.md)

Configuratieopties die beschikbaar zijn in geautomatiseerde machine learning:

* Selecteer uw experimenttype: Classificatie, Regressie of Time Series-prognose
* Gegevensbron, formatteert en haalt gegevens op
* Kies uw rekendoel: lokaal of extern
* Instellingen voor machine learning-experiment
* Een geautomatiseerd machine learning-experiment uitvoeren
* Metrische modelgegevens verkennen
* Model registreren en implementeren

Als u liever geen code gebruikt, kunt u ook Uw geautomatiseerde [machine learning-experimenten maken in Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u het volgende nodig: 
* Een Azure Machine Learning-werkruimte. Zie Een werkruimte maken om de [Azure Machine Learning maken.](how-to-manage-workspace.md)

* De Azure Machine Learning Python SDK geïnstalleerd.
    Als u de SDK wilt installeren, kunt u: 
    * Maak een rekenproces dat automatisch de SDK installeert en vooraf is geconfigureerd voor ML-werkstromen. Zie Create and manage an Azure Machine Learning compute instance (Een [Azure Machine Learning compute-exemplaar maken](how-to-create-manage-compute-instance.md) en beheren) voor meer informatie. 

    * [Installeer het `automl` pakket zelf,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)met de [standaardinstallatie](/python/api/overview/azure/ml/install#default-install) van de SDK.
    
    > [!WARNING]
    > Python 3.8 is niet compatibel met `automl` . 

## <a name="select-your-experiment-type"></a>Het type experimenten selecteren

Voordat u met uw experiment begint, moet u bepalen wat voor soort machine learning u wilt oplossen. Geautomatiseerde machine learning ondersteunt taaktypen `classification` van , en `regression` `forecasting` . Meer informatie over [taaktypen](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

De volgende code gebruikt de `task` parameter in de `AutoMLConfig` constructor om het experimenttype op te geven als `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Gegevensbron en -indeling

Geautomatiseerde machine learning biedt ondersteuning voor gegevens die zich bevinden op de lokale desktop of in de cloud, zoals Azure Blob Storage. De gegevens kunnen worden gelezen in een **Pandas-dataframe** of een **Azure Machine Learning TabularDataset.** [Meer informatie over gegevenssets](how-to-create-register-datasets.md).

Vereisten voor trainingsgegevens in machine learning:
- Gegevens moeten in tabelvorm zijn.
- De waarde die moet worden voorspeld, de doelkolom, moet in de gegevens zijn.

**Voor externe experimenten moeten** trainingsgegevens toegankelijk zijn vanaf de externe berekening. AutoML accepteert alleen [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset) wanneer ze werken op een externe compute. 

Azure Machine Learning-gegevenssets bieden functionaliteit voor:

* U kunt eenvoudig gegevens overdragen van statische bestanden of URL-bronnen naar uw werkruimte.
* Het beschikbaar maken van uw gegevens voor trainingsscripts bij uitvoering in computeresources in de cloud. Zie [Trainen met gegevenssets voor](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) een voorbeeld van het gebruik van de klasse om gegevens te `Dataset` mounten aan uw externe rekendoel.

Met de volgende code maakt u een TabularDataset op basis van een web-URL. Zie [Een TabularDatasets maken](how-to-create-register-datasets.md#create-a-tabulardataset) voor codevoorbeelden over het maken van gegevenssets van andere bronnen, zoals lokale bestanden en gegevensstores.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Voor lokale rekenexperimenten** raden we pandas-gegevensframes aan voor snellere verwerkingstijden.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Trainings-, validatie- en testgegevens

U kunt afzonderlijke **trainingsgegevens en validatiegegevenssets** rechtstreeks in de `AutoMLConfig` constructor opgeven. Meer informatie over [het configureren van gegevenssplitsingen en kruisvalidatie](how-to-configure-cross-validation-data-splits.md) voor uw AutoML-experimenten. 

Als u niet expliciet een parameter of opgeeft, past geautomatiseerde ML standaardtechnieken toe `validation_data` om te bepalen hoe `n_cross_validation` validatie wordt uitgevoerd. Deze beslissing is afhankelijk van het aantal rijen in de gegevensset die is toegewezen aan uw `training_data` parameter. 

|Grootte &nbsp; van &nbsp; trainingsgegevens| Validatietechniek |
|---|-----|
|**Groter &nbsp; dan &nbsp; 20.000 &nbsp; rijen**| Gegevenssplitsing voor trainen/valideren wordt toegepast. De standaardwaarde is om 10% van de initiële trainingsgegevensset als validatieset te gebruiken. Die validatieset wordt op zijn beurt gebruikt voor het berekenen van metrische gegevens.
|**Kleiner &nbsp; dan &nbsp; 20.000 &nbsp; rijen**| Er wordt een benadering voor kruisvalidatie toegepast. Het standaard aantal vouwen is afhankelijk van het aantal rijen. <br> **Als de gegevensset minder dan 1000** rijen bevat, worden er 10 vouwen gebruikt. <br> **Als de rijen tussen 1000 en 20.000** liggen, worden er drie vouwen gebruikt.

Op dit moment moet u uw eigen testgegevens voor **modelevaluatie** verstrekken. Zie de sectie Testen van dit [Jupyter-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)voor een codevoorbeeld van het meenemen van uw eigen testgegevens voor modelevaluatie. 

## <a name="compute-to-run-experiment"></a>Compute en uitvoering van het experiment instellen

Bepaal vervolgens waar het model wordt getraind. Een experiment met geautomatiseerd machine learning-training kan worden uitgevoerd op de volgende compute-opties. Meer informatie over de [voordelen en nadelen van lokale en externe compute-opties](concept-automated-ml.md#local-remote). 

* Uw **lokale** computer, zoals een lokaal bureaublad of laptop: doorgaans wanneer u een kleine gegevensset hebt en u nog in de verkenningsfase bent. Bekijk [dit notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) voor een lokaal compute-voorbeeld. 
 
* Een **externe** machine in de [cloud: Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) is een beheerde service waarmee u modellen kunt machine learning trainen op clusters van virtuele Azure-machines. 

    Raadpleeg [dit notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) voor een extern voorbeeld met behulp van beheerde compute van Azure Machine Learning. 

* Een **Azure Databricks cluster** in uw Azure-abonnement. Meer informatie vindt u in Set up an Azure Databricks cluster for automated ML (Een cluster voor geautomatiseerde [ML instellen).](how-to-configure-databricks-automl-environment.md) Zie deze [GitHub-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) voor voorbeelden van notebooks met Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Uw experimentinstellingen configureren

Er zijn verschillende opties die u kunt gebruiken om uw geautomatiseerde machine learning configureren. Deze parameters worden ingesteld door een -object te `AutoMLConfig` instantiëren. Zie de [klasse AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor een volledige lijst met parameters.

Voorbeelden zijn:

1. Classificatie-experiment met AUC gewogen als de primaire metrische gegevens met time-outminuten voor experimenten ingesteld op 30 minuten en 2 kruisvalidatie-vouwen.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. In het volgende voorbeeld wordt een regressieexperiment ingesteld om na 60 minuten te eindigen met vijf kruisvgevouwen validaties.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Voor prognosetaken is extra installatie vereist. Zie het artikel [Autotrain a time-series forecast model (Een](how-to-auto-train-forecast.md) prognosemodel voor tijdreeksen automatisch trainen) voor meer informatie. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Ondersteunde modellen

Geautomatiseerde machine learning verschillende modellen en algoritmen tijdens het automatiserings- en afstemmingsproces. Als gebruiker hoeft u het algoritme niet op te geven. 

De drie verschillende `task` parameterwaarden bepalen de lijst met algoritmen of modellen die moeten worden toegepast. Gebruik de `allowed_models` parameters of `blocked_models` om iteraties verder te wijzigen met de beschikbare modellen die moeten worden opgesloten of uitgesloten. 

De volgende tabel bevat een overzicht van de ondersteunde modellen op taaktype. 

> [!NOTE]
> Als u van plan bent om uw automatisch gemaakte ML-modellen te exporteren naar een [ONNX-model,](concept-onnx.md)kunnen alleen de algoritmen die worden aangegeven met een * worden geconverteerd naar de ONNX-indeling. Meer informatie over [het converteren van modellen naar ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Houd er ook rekening mee dat ONNX op dit moment alleen classificatie- en regressietaken ondersteunt. 

Classificatie | Regressie | Tijdreeksvoorspelling
|-- |-- |--
[Logistieke regressie](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastisch net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[JET Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastische gradiëntafdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastische gradiëntafdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Willekeurig forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Willekeurig forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Willekeurig forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer willekeurige bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Zeer willekeurige bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Zeer willekeurige bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Gemiddelde Perceptron-classificatie](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Profeet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastische gradiëntafdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Lineaire SVM-classificatie](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Primaire metrische gegevens
De `primary metric` parameter bepaalt de metrische waarde die moet worden gebruikt tijdens het trainen van het model voor optimalisatie. De beschikbare metrische gegevens die u kunt selecteren, worden bepaald door het taaktype dat u kiest en de volgende tabel bevat geldige primaire metrische gegevens voor elk taaktype.

Het kiezen van een primaire metrische gegevens voor geautomatiseerde machine learning optimaliseren is afhankelijk van veel factoren. We raden u aan om een metrische gegevens te kiezen die het beste bij uw bedrijfsbehoeften past. Overweeg vervolgens of de metrische gegevens geschikt zijn voor uw gegevenssetprofiel (gegevensgrootte, bereik, klassendistributie, enzovoort).

Meer informatie over de specifieke definities van deze metrische gegevens in Inzicht in [geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).

|Classificatie | Regressie | Tijdreeksvoorspelling
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Primaire metrische gegevens voor classificatiescenario's 

Metrische gegevens na een drempelwaarde, zoals , , en zijn mogelijk niet zo goed geoptimaliseerd voor gegevenssets die klein zijn, een zeer grote klassenverschil hebben (ongelijke klasse) of wanneer de verwachte metrische waarde zeer dicht bij `accuracy` `average_precision_score_weighted` `norm_macro_recall` `precision_score_weighted` 0,0 of 1,0 ligt. In dergelijke gevallen `AUC_weighted` kan een betere keuze zijn voor de primaire metrische gegevens. Nadat geautomatiseerde machine learning voltooid, kunt u het prijsmodel kiezen op basis van de metrische gegevens die het meest geschikt zijn voor uw bedrijfsbehoeften.

| Metrisch | Voorbeeld van use-case(s) |
| ------ | ------- |
| `accuracy` | Afbeeldingsclassificatie, sentimentanalyse, verloopvoorspelling |
| `AUC_weighted` | Fraudedetectie, afbeeldingsclassificatie, anomaliedetectie/spamdetectie |
| `average_precision_score_weighted` | Sentimentanalyse |
| `norm_macro_recall` | Verloopvoorspelling |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Primaire metrische gegevens voor regressiescenario's

Metrische gegevens zoals en kunnen de kwaliteit van het model beter vertegenwoordigen wanneer de schaal van de te voorspellen waarde vele `r2_score` `spearman_correlation` ordes van grootte dekt. Bijvoorbeeld salarisschatting, waarbij veel mensen een salaris van $ 20.000 tot $ 100.000 hebben, maar de schaal zeer hoog gaat met enkele salarissen in het bereik van $ 100 miljoen. 

`normalized_mean_absolute_error` en behandelt in dit geval een voorspellingsfout van $ 20.000 op dezelfde manier voor een werker met een salaris van $ 30.000 als een werker die `normalized_root_mean_squared_error` $ 20 miljoen maakt. In werkelijkheid is het voorspellen van slechts $ 20.000 van een salaris van $ 20 miljoen heel dichtbij (een klein relatief verschil van 0,1%), terwijl $ 20.000 van $ 30.000 niet dicht bij elkaar ligt (een relatief groot verschil van 67%). `normalized_mean_absolute_error` en `normalized_root_mean_squared_error` zijn handig wanneer de te voorspellen waarden zich in een vergelijkbare schaal hebben.

| Metrisch | Voorbeeld van use-case(s) |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prijsvoorspelling (huis/product/tip), Beoordelingsscorevoorspelling |
| `r2_score` | Vertraging luchtvaartmaatschappij, salarisschatting, oplossingstijd van fouten |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Primaire metrische gegevens voor scenario's voor tijdreeksprognoses

Zie regressienotities hierboven.

| Metrisch | Voorbeeld van use-case(s) |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prijsvoorspelling (prognose), Inventarisoptimalisatie, Vraagprognose |
| `r2_score` | Prijsvoorspelling (prognose), inventarisoptimalisatie, Vraagprognose |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Kenmerken van gegevens

In elk geautomatiseerd machine learning experiment worden uw gegevens automatisch geschaald en genormaliseerd om bepaalde algoritmen te helpen die gevoelig zijn voor functies op verschillende schaal.  Deze schaalbaarheid en normalisatie wordt ook wel featurization genoemd. Zie [Featurization in AutoML (Featurization in AutoML)](how-to-configure-auto-features.md#) voor meer informatie en codevoorbeelden. 

Wanneer u uw experimenten in uw `AutoMLConfig` -object configureert, kunt u de instelling in- of `featurization` uitschakelen. In de volgende tabel ziet u de geaccepteerde instellingen voor featurization in het [AutoMLConfig-object](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Featurization-configuratie | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Geeft aan dat gegevensbescherming en [featurization-stappen](how-to-configure-auto-features.md#featurization) automatisch worden uitgevoerd als onderdeel van voorverwerking. **Standaardinstelling**.|
|`"featurization": 'off'`| Geeft aan dat de featurization-stap niet automatisch moet worden uitgevoerd.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Geeft aan dat de aangepaste featurization-stap moet worden gebruikt. [Meer informatie over het aanpassen van featurization](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Geautomatiseerde machine learning functienormalisatie (functienormalisatie, verwerking van ontbrekende gegevens, het converteren van tekst naar numerieke gegevens, enzovoort) maken deel uit van het onderliggende model. Wanneer u het model voor voorspellingen gebruikt, worden dezelfde featurization-stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoergegevens.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Ensembleconfiguratie

Ensemblemodellen zijn standaard ingeschakeld en worden weergegeven als de laatste uitvoerings iteraties in een AutoML-uitvoering. Momenteel **worden VotingEnsemble** **en StackEnsemble** ondersteund. 

Met stemmen wordt een zachte stem geïmplementeerd, waarbij gebruik wordt gemaakt van gewogen gemiddelden. De stacking-implementatie maakt gebruik van een implementatie met twee lagen, waarbij de eerste laag dezelfde modellen heeft als het stemmende ensemble, en het tweede laagmodel wordt gebruikt om de optimale combinatie van de modellen van de eerste laag te vinden. 

Als u ONNX-modellen gebruikt **of** als u model verklaarbaarheid hebt ingeschakeld, wordt stapelen uitgeschakeld en wordt alleen stemmen gebruikt.

Ensembletraining kan worden uitgeschakeld met behulp van de `enable_voting_ensemble` `enable_stack_ensemble` booleaanse parameters en .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Als u het standaardgedrag van het ensemble wilt wijzigen, zijn er meerdere standaardargumenten die kunnen worden opgegeven `kwargs` als in een `AutoMLConfig` -object.

> [!IMPORTANT]
>  De volgende parameters zijn geen expliciete parameters van de klasse AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Tijdens **het genereren van VotingEnsemble-** en **StackEnsemble-modellen** worden meerdere passende modellen van de vorige onderliggende uitvoeringen gedownload. Als u deze fout tegenkomt: , moet u mogelijk meer tijd geven om de `AutoMLEnsembleException: Could not find any models for running ensembling` modellen te downloaden. De standaardwaarde is 300 seconden voor het parallel downloaden van deze modellen en er is geen maximale time-outlimiet. Configureer deze parameter met een hogere waarde dan 300 sec als er meer tijd nodig is. 

  > [!NOTE]
  >  Als de time-out is bereikt en er modellen zijn gedownload, gaat het ensembling verder met het aantal modellen dat is gedownload. Het is niet vereist dat alle modellen moeten worden gedownload om deze time-out te voltooien.

De volgende parameters zijn alleen van toepassing **op StackEnsemble-modellen:** 

* `stack_meta_learner_type`: de meta-learner is een model dat is getraind op de uitvoer van de afzonderlijke heterogene modellen. Standaard meta-learners zijn voor classificatietaken (of als kruisvalidatie is ingeschakeld) en voor `LogisticRegression` `LogisticRegressionCV` `ElasticNet` regressie-/prognosetaken (of als kruisvalidatie `ElasticNetCV` is ingeschakeld). Deze parameter kan een van de volgende tekenreeksen zijn: `LogisticRegression` , , , , , of `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` `LinearRegression` .

* `stack_meta_learner_train_percentage`: hiermee geeft u het aandeel aan van de trainingsset (wanneer u train- en validatietype training kiest) dat moet worden gereserveerd voor het trainen van de meta-learner. De standaardwaarde is `0.2`. 

* `stack_meta_learner_kwargs`: optionele parameters om door te geven aan de initialisatie van de meta-learner. Deze parameters en parametertypen spiegelen de parameters en parametertypen van de bijbehorende model constructor en worden doorgestuurd naar de model constructor.

De volgende code toont een voorbeeld van het opgeven van aangepast ensemblegedrag in een `AutoMLConfig` object.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Criteria voor afsluiten

Er zijn enkele opties die u kunt definiëren in uw AutoMLConfig om uw experiment te beëindigen.

|Criteria| beschrijving
|----|----
Geen &nbsp; criteria | Als u geen afsluitende parameters definieert, wordt het experiment voortgezet totdat er geen verdere voortgang is gemaakt met uw primaire metrische gegevens.
Na &nbsp; &nbsp; een &nbsp; periode &nbsp;| Gebruik `experiment_timeout_minutes` in uw instellingen om te definiëren hoe lang, in minuten, uw experiment moet worden uitgevoerd. <br><br> Om time-outfouten bij het experiment te voorkomen, is er minimaal 15 minuten of 60 minuten als uw rij per kolomgrootte groter is dan 10 miljoen.
Er &nbsp; &nbsp; is een score &nbsp; &nbsp; bereikt| Met `experiment_exit_score` Gebruik wordt het experiment voltooid nadat een opgegeven primaire metrische score is bereikt.

## <a name="run-experiment"></a>Experiment uitvoeren

Voor geautomatiseerde ML maakt u een -object, een benoemd `Experiment` object in een dat wordt gebruikt om experimenten uit te `Workspace` voeren.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Dien het experiment in om een model uit te voeren en te genereren. Geef de `AutoMLConfig` door aan de methode om het model te `submit` genereren.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Afhankelijkheden worden eerst geïnstalleerd op een nieuwe computer.  Het kan tot 10 minuten duren voordat de uitvoer wordt weergegeven.
>Instelling `show_output` op resulteert in uitvoer die wordt weergegeven op de `True` console.

### <a name="multiple-child-runs-on-clusters"></a>Meerdere onderliggende runs op clusters

Onderliggende runs van geautomatiseerde ML-experimenten kunnen worden uitgevoerd op een cluster waar al een ander experiment wordt uitgevoerd. De timing is echter afhankelijk van het aantal knooppunten dat het cluster heeft en of deze knooppunten beschikbaar zijn om een ander experiment uit te voeren.

Elk knooppunt in het cluster fungeert als een afzonderlijke virtuele machine (VM) die één trainingsrun kan uitvoeren; voor geautomatiseerde ML betekent dit een onderliggende run. Als alle knooppunten bezet zijn, wordt het nieuwe experiment in de wachtrij geplaatst. Maar als er gratis knooppunten zijn, voert het nieuwe experiment geautomatiseerde onderliggende ML-runs parallel uit in de beschikbare knooppunten/VM's.

Om onderliggende runs te helpen beheren en wanneer ze kunnen worden uitgevoerd, raden we u aan per experiment een toegewezen cluster te maken en het aantal van uw experiment te laten overeenkomen met het aantal knooppunten `max_concurrent_iterations` in het cluster. Op deze manier gebruikt u alle knooppunten van het cluster tegelijkertijd met het aantal gelijktijdige onderliggende runs/iteraties dat u wilt.

Configureer  `max_concurrent_iterations` in uw `AutoMLConfig` -object. Als deze niet is geconfigureerd, is standaard slechts één gelijktijdige onderliggende run/iteratie per experiment toegestaan.  

## <a name="explore-models-and-metrics"></a>Modellen en metrische gegevens verkennen

Geautomatiseerde ML biedt opties voor het bewaken en evalueren van uw trainingsresultaten. 

* U kunt uw trainingsresultaten weergeven in een widget of inline als u zich in een notebook. Zie [Geautomatiseerde machine learning controleren](#monitor) voor meer informatie.

* Zie Evaluate automated machine learning experiment results voor definities en voorbeelden van prestatiegrafieken [en metrische gegevens voor elke uitvoering.](how-to-understand-automated-ml.md) 

* Zie Featurization transparency (Transparantie van [featurization)](how-to-configure-auto-features.md#featurization-transparency)voor een samenvatting van featurization en inzicht te krijgen in welke functies zijn toegevoegd aan een bepaald model. 

U kunt de hyperparameters, de schalings- en normalisatietechnieken en het algoritme die zijn toegepast op een specifieke geautomatiseerde ML-run weergeven met de volgende aangepaste codeoplossing. 

Het volgende definieert de aangepaste methode, , waarmee de hyperparameters van elke stap van de geautomatiseerde `print_model()` ML-trainingspijplijn worden afgedrukt.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Voor een lokale of externe uitvoering die zojuist is verzonden en getraind vanuit hetzelfde experimentnoteboek, kunt u het beste model doorgeven met behulp van de `get_output()` methode . 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

De volgende uitvoer geeft aan dat:
 
* De standardScalerWrapper-techniek is gebruikt om de gegevens vóór de training te schalen en te normaliseren.

* Het XGBoostClassifier-algoritme is geïdentificeerd als de beste run en toont ook de hyperparameterwaarden. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

Voor een bestaande run vanuit een ander experiment in uw werkruimte haalt u de specifieke run-id op die u wilt verkennen en geef u deze door aan de `print_model()` methode . 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> De algoritmen die door geautomatiseerde ML worden gebruikt, hebben inherente willekeurigheid die een kleine variatie kan veroorzaken in de uiteindelijke score voor metrische gegevens van een aanbevolen model, zoals nauwkeurigheid. Geautomatiseerde ML voert indien nodig ook bewerkingen uit op gegevens zoals trainen/testen splitsen, splitsing voor trainen/validatie of kruisvalidatie. Dus als u meerdere keren een experiment met dezelfde configuratie-instellingen en primaire metrische gegevens hebt uitgevoerd, zult u waarschijnlijk variatie zien in elke metrische score van elke experimenten als gevolg van deze factoren. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Geautomatiseerde machine learning bewaken

Als u automatische machine learning wilt uitvoeren, vervangt u voor toegang tot de grafieken van een vorige run `<<experiment_name>>` door de juiste experimentnaam:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-widget voor Geautomatiseerde Machine Learning](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Modellen registreren en implementeren

U kunt een model registreren, zodat u er later naar kunt teruggaan. 

Als u een model wilt registreren vanuit een geautomatiseerde ML-uitvoering, gebruikt u de [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) methode . 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Zie hoe en waar u een model implementeert voor meer informatie over het maken van een implementatieconfiguratie en het implementeren van een geregistreerd [model in een webservice.](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)

> [!TIP]
> Voor geregistreerde modellen is implementatie met één klik beschikbaar via [de Azure Machine Learning-studio](https://ml.azure.com). Zie [hoe u geregistreerde modellen implementeert vanuit de studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
<a name="explain"></a>

## <a name="model-interpretability"></a>Interpreteerbaarheid van modellen

Met de interpreteerbaarheid van modellen kunt u begrijpen waarom uw modellen voorspellingen hebben gedaan en de onderliggende waarden voor het belang van functies. De SDK bevat verschillende pakketten voor het inschakelen van model interpreteerbaarheidsfuncties, zowel tijdens de training als de deference time, voor lokale en geïmplementeerde modellen.

Zie de [uitleg voor codevoorbeelden](how-to-machine-learning-interpretability-automl.md) over het inschakelen van interpreteerbaarheidsfuncties specifiek binnen geautomatiseerde machine learning experimenten.

Zie het [conceptartikel](how-to-machine-learning-interpretability.md) over interpreteerbaarheid voor algemene informatie over hoe modelverklaringen en het belang van functies buiten geautomatiseerde machine learning kunnen worden ingeschakeld in andere gebieden van de SDK.

> [!NOTE]
> Het ForecastTCN-model wordt momenteel niet ondersteund door de Explanation Client. Dit model retourneerde geen uitlegdashboard als het wordt geretourneerd als het beste model en biedt geen ondersteuning voor uitvoeringen van uitleg op aanvraag.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [hoe en waar u een model implementeert.](how-to-deploy-and-where.md)

+ Meer informatie over [het trainen van een regressiemodel met Automated machine learning](tutorial-auto-train-models.md).

+ Meer informatie over het trainen van meerdere modellen met AutoML in [de oplossingsversneller Veel modellen.](https://aka.ms/many-models)

+ [Problemen met geautomatiseerde ML-experimenten oplossen.](how-to-troubleshoot-auto-ml.md) 
