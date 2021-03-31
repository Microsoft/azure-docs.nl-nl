---
title: Experimenteren en implementeren van modellen
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het bijhouden en registreren van experimenten van de Data Science Virtual Machine met Azure Machine Learning en/of MLFlow.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518535"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Experimenten bijhouden en modellen implementeren in Azure Machine Learning

Verbeter het proces voor het maken van het model door de metrische gegevens voor experimenten en bewakings uitvoeringen bij te houden. In dit artikel leert u hoe u logboek registratie code kunt toevoegen aan uw trainings script met behulp van de [MLflow](https://mlflow.org/) -API en hoe u het experiment bijhoudt in azure machine learning.

In het volgende diagram ziet u hoe u met MLflow tracking de metrische gegevens van een experiment kunt bijhouden en model artefacten kunt opslaan in uw Azure Machine Learning-werk ruimte.

![experimenten bijhouden](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Vereisten

* U moet [een Azure machine learning-werkruimte inrichten](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Een nieuwe notebook maken

De SDK voor Azure Machine Learning en MLFlow zijn vooraf geïnstalleerd op de Data Science VM en kunnen worden geopend in de **azureml_py36_ \*** Conda-omgeving. Klik in Jjupyterlab op het start programma en selecteer de volgende kernel:

![kernel selecteren](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>De werk ruimte instellen

Ga naar de [Azure Portal](https://portal.azure.com) en selecteer de werk ruimte die u als onderdeel van de vereisten hebt ingericht. U ziet het __download config.jsop__ (zie hieronder). down load de configuratie en controleer of deze is opgeslagen in uw werkmap op het DSVM.

![Configuratie bestand ophalen](./media/how-to-track-experiments/experiment-tracking-2.png)

De configuratie bevat informatie zoals de naam van de werk ruimte, het abonnement enzovoort. Dit betekent dat u deze para meters niet hoeft vast te coderen.

## <a name="track-dsvm-runs"></a>DSVM-uitvoeringen bijhouden

Voeg de volgende code toe aan uw notitie blok (of script) om het object AzureML-werk ruimte in te stellen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
De tracerings-URI is Maxi maal een uur of minder geldig. Als u het script opnieuw opstart na enige inactieve tijd, gebruikt u de get_mlflow_tracking_uri-API om een nieuwe URI op te halen.

### <a name="load-the-data"></a>De gegevens laden

In dit voor beeld wordt de diabetes-gegevensset gebruikt, een bekende kleine gegevensset die wordt geleverd met scikit-learn. In deze cel wordt de gegevensset geladen en gesplitst in wille keurige trainings-en test sets.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Tracking toevoegen

Voeg experiment tracking toe met behulp van de Azure Machine Learning SDK en upload een persistent model in de record experiment run. De volgende code voegt Logboeken toe en uploadt een model bestand naar de uitvoering van het experiment. Het model wordt ook geregistreerd in het Azure Machine Learning model register.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Uitvoeringen weer geven in Azure Machine Learning

U kunt de uitvoering van het experiment bekijken in [Azure machine learning Studio](https://ml.azure.com). Klik op __experimenten__ in het menu aan de linkerkant en selecteer de experiment_with_mlflow (of als u hebt besloten om uw experiment een andere naam te gegeven in het bovenstaande fragment, klikt u op de naam die wordt gebruikt):

![experiment selecteren](./media/how-to-track-experiments/mlflow-experiments.png)

Als het goed is, wordt de gekwadrateerde fout (MSE) weer geven:

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Als u op uitvoeren klikt, ziet u andere details en ook het selectie model in de __uitvoer en logboeken__ .

## <a name="deploy-model-in-azure-machine-learning"></a>Model implementeren in Azure Machine Learning

In deze sectie wordt uitgelegd hoe u modellen implementeert die zijn getraind op een DSVM om Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Stap 1: een berekenings berekening maken

Klik in het menu aan de linkerkant in [AzureML Studio](https://ml.azure.com) op __Compute__ en vervolgens op het tabblad Afleidings __clusters__ . Klik vervolgens op __+ Nieuw__ , zoals hieronder wordt beschreven:

![Reken kracht maken](./media/how-to-track-experiments/mlflow-experiments-6.png)

In de __nieuwe__ Details van het deel venster voor het delegeren van het cluster paneel voor:

* Compute name
* Kubernetes-service-Selecteer nieuwe maken
* De regio selecteren
* Selecteer de VM-grootte (in het kader van deze zelf studie is de standaard waarde van Standard_D3_v2 voldoende)
* Cluster doel-Selecteer __dev-test__
* Aantal knoop punten moet gelijk zijn aan __1__
* Netwerk configuratie-basis

Klik vervolgens op __maken__.

![Reken Details](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Stap 2: implementeer de service voor het afwijzen van geen code

Wanneer we het model in onze code hebben geregistreerd met `register_model` , hebben we het Framework opgegeven als sklearn. Azure Machine Learning ondersteunt geen code-implementaties voor de volgende frameworks:

* scikit-learn
* Tensor flow SaveModel-indeling
* Model indeling ONNX

Implementatie zonder code betekent dat u direct vanuit het model artefact kunt implementeren zonder dat u een specifiek Score script hoeft op te geven.

Als u het diabetes-model wilt implementeren, gaat u naar het menu links in het [Azure machine learning Studio](https://ml.azure.com) en selecteert u __modellen__. Klik vervolgens op de diabetes_model geregistreerd:

![Model selecteren](./media/how-to-track-experiments/mlflow-experiments-3.png)

Klik vervolgens op de knop __implementeren__ in het deel venster model Details:

![Implementeren](./media/how-to-track-experiments/mlflow-experiments-4.png)

We implementeren het model in het cluster voor afnemen (Azure Kubernetes service) dat we hebben gemaakt in stap 1. Vul de onderstaande gegevens in door een naam op te geven voor de service en de naam van het AKS Compute-Cluster (gemaakt in stap 1). We raden u ook aan om de __capaciteit van de CPU-reserve ring__ te verhogen naar 1 (van 0,1) en de capaciteit van het __geheugen reserve ring__ op 1 (van 0,5). u kunt deze toename doen door te klikken op __Geavanceerd__ en de details op te vullen. Klik vervolgens op __implementeren__.

![Details implementeren](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Stap 3: gebruiken

Wanneer het model is geïmplementeerd, ziet u het volgende (als u deze pagina wilt openen, klikt u op eind punten in het linkermenu > klikt u op de naam van de geïmplementeerde service):

![Model verbruiken](./media/how-to-track-experiments/mlflow-experiments-8.png)

U ziet dat de implementatie status van __overgang__ naar __in orde__ gaat. Daarnaast bevat deze sectie Details de Url's REST-eind punt en Swagger die een toepassings ontwikkelaar kan gebruiken om uw ML-model in hun apps te integreren.

U kunt het eind punt testen met behulp van [postman](https://www.postman.com/), of u kunt de AZUREML-SDK gebruiken:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Stap 4: opschonen

Verwijder de reken kracht die u in stap 1 hebt gemaakt, zodat u geen lopende reken kosten hebt. Klik in het menu aan de linkerkant in het Azure Machine Learning Studio op Compute-> clusters > Selecteer de reken > verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van modellen in AzureML](../how-to-deploy-and-where.md)
