---
title: Uitleg in geautomatiseerde ML (preview)
titleSuffix: Azure Machine Learning
description: Ontdek hoe u uitleg krijgt over hoe uw geautomatiseerde ML-model het belang van functies bepaalt en voorspellingen doet wanneer u de Azure Machine Learning SDK gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576462"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpreteerbaarheid: modeluitleg in geautomatiseerde machine learning (preview)


In dit artikel leert u hoe u uitleg krijgt over geautomatiseerde machine learning (geautomatiseerde ML) in Azure Machine Learning met behulp van de Python SDK. Geautomatiseerde ML helpt u inzicht te krijgen in het belang van functies van de modellen die worden gegenereerd. 

Alle SDK-versies na 1.0.85 zijn `model_explainability=True` standaard ingesteld. In SDK-versie 1.0.85 en eerdere versies moeten gebruikers instellen in het -object om de interpreteerbaarheid van `model_explainability=True` het model te kunnen `AutoMLConfig` gebruiken. 


In dit artikel leert u het volgende:

- Interpreteerbaarheid uitvoeren tijdens de training voor het beste model of een model.
- Schakel visualisaties in om patronen in gegevens en uitleg te bekijken.
- Implementeer interpreteerbaarheid tijdens de deference of scoring.

## <a name="prerequisites"></a>Vereisten

- Interpreteerbaarheidsfuncties. Voer uit `pip install azureml-interpret` om het benodigde pakket op te halen.
- Kennis van het bouwen van geautomatiseerde ML-experimenten. Voor meer informatie over het gebruik van de Azure Machine Learning SDK, voltooit u deze zelfstudie over [het regressiemodel](tutorial-auto-train-models.md) of bekijkt u hoe u [geautomatiseerde ML-experimenten configureert.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Interpreteerbaarheid tijdens training voor het beste model

Haal de uitleg op uit de , die uitleg bevat over `best_run` zowel onbewerkte als ontworpen functies.

> [!NOTE]
> Interpretability, best model explanation, is not available for Auto ML forecasting experiments that recommend the following algorithms as the best model: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Profeet
> * Gemiddeld 
> * Naive
> * Seizoensgebonden gemiddelde 
> * Seizoensgebonden naive

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Download de belangrijke functies van de ontworpen functie uit de beste run

U kunt gebruiken `ExplanationClient` om de uitleg over de ontworpen functie te downloaden uit het artefactopslag van de `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>De belangrijkste functies van onbewerkte functies downloaden bij de beste run

U kunt gebruiken om `ExplanationClient` de uitleg over de onbewerkte functie te downloaden uit het artefactopslag van de `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpreteerbaarheid tijdens de training voor elk model 

Wanneer u de uitleg van het model berekent en ze visualiseert, bent u niet beperkt tot een bestaande modelverklaring voor een AutoML-model. U kunt ook een uitleg krijgen voor uw model met verschillende testgegevens. De stappen in deze sectie laten zien hoe u het belang van een ontworpen functie kunt berekenen en visualiseren op basis van uw testgegevens.

### <a name="retrieve-any-other-automl-model-from-training"></a>Elk ander AutoML-model ophalen uit de training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Uitleg over het model instellen

Gebruik `automl_setup_model_explanations` om de gemanipuleerde en onbewerkte uitleg te krijgen. De `fitted_model` kan de volgende items genereren:

- Aanbevolen gegevens van getrainde of testvoorbeelden
- Lijsten met naam van engineered functies
- Vindbare klassen in uw gelabelde kolom in classificatiescenario's

De `automl_explainer_setup_obj` bevat alle structuren uit de bovenstaande lijst.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>De Functie-uitleg initialiseren voor functie-belang

Als u een uitleg wilt genereren voor geautomatiseerde ML-modellen, gebruikt u de `MimicWrapper` klasse . U kunt de MimicWrapper initialiseren met de volgende parameters:

- Het installatieobject van de uitleg
- Uw werkruimte
- Een surrogaatmodel om het geautomatiseerde `fitted_model` ML-model uit te leggen

De MimicWrapper neemt ook het `automl_run` object waar de ontworpen uitleg wordt geüpload.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Gebruik Mimic Explainer voor het berekenen en visualiseren van het belang van ontworpen functies

U kunt de methode aanroepen in MimicWrapper met de getransformeerde testvoorbeelden om de functie-belang voor de `explain()` gegenereerde, ontworpen functies op te halen. U kunt zich ook aanmelden bij [Azure Machine Learning-studio](https://ml.azure.com/) dashboardvisualisatie van de functie-belangwaarden van de gegenereerde ontworpen functies door geautomatiseerde ML-featurizers weer te geven.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Voor modellen die zijn getraind met geautomatiseerde ML, kunt u lokaal het beste model krijgen met behulp van de `get_output()` methode en reken uitleg.  U kunt de resultaten van de uitleg visualiseren met `ExplanationDashboard` uit `interpret-community` het pakket.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Gebruik Mimic Explainer voor het berekenen en visualiseren van onbewerkte functie-belang

U kunt de methode aanroepen in MimicWrapper met de getransformeerde testvoorbeelden om het belang van de functie voor `explain()` de onbewerkte functies te krijgen. In de [Machine Learning Studio kunt](https://ml.azure.com/)u de dashboardvisualisatie bekijken van de functie-belangwaarden van de onbewerkte functies.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpreteerbaarheid tijdens de deferentie

In deze sectie leert u hoe u een geautomatiseerd ML-model operationeel kunt maken met de uitleg die is gebruikt voor het berekenen van de uitleg in de vorige sectie.

### <a name="register-the-model-and-the-scoring-explainer"></a>Het model en de score-uitleg registreren

Gebruik de om de score-uitleg te maken waarmee de belangwaarden van de ontworpen functie worden berekend `TreeScoringExplainer` tijdens de deferentie. U initialiseert de score-uitleg met `feature_map` de die eerder is berekend. 

Sla de scoring-uitleg op en registreer vervolgens het model en de score-uitleg bij Modelbeheer Service. Voer de volgende code uit:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>De Conda-afhankelijkheden maken voor het instellen van de service

Maak vervolgens de benodigde omgevingsafhankelijkheden in de container voor het geïmplementeerde model. Houd er rekening mee dat azureml-defaults met versie >= 1.0.45 moet worden vermeld als pip-afhankelijkheid, omdat deze de functionaliteit bevat die nodig is om het model als een webservice te hosten.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Het scoring-script maken

Schrijf een script dat uw model laadt en voorspellingen en uitleg produceert op basis van een nieuwe batch met gegevens.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>De service implementeren

Implementeer de service met behulp van het conda-bestand en het scoring-bestand uit de vorige stappen.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>De deferentie met testgegevens

De deferentie met enkele testgegevens om de voorspelde waarde van het AutoML-model te zien, die momenteel alleen wordt ondersteund in Azure Machine Learning SDK. Bekijk de functie-belang die bijdragen aan een voorspelde waarde. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualiseren om patronen in gegevens en uitleg te ontdekken tijdens het trainen

U kunt de grafiek voor functie-belang in uw werkruimte visualiseren in [Azure Machine Learning-studio](https://ml.azure.com). Nadat de AutoML-uitvoering is voltooid, selecteert u **Modeldetails weergeven** om een specifieke uitvoering weer te geven. Selecteer het **tabblad Uitleg om** de visualisaties in het uitlegdashboard weer te geven.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Raadpleeg voor meer informatie over de uitleg van dashboardvisualisaties en specifieke plots het document uitleg [over de interpreteerbaarheid.](how-to-machine-learning-interpretability-aml.md)

## <a name="next-steps"></a>Volgende stappen

Zie het [conceptartikel](how-to-machine-learning-interpretability.md)over interpreteerbaarheid voor meer informatie over het inschakelen van modelverklaringen en het belang van functies op andere gebieden van de Azure Machine Learning-SDK dan geautomatiseerde machine learning.
