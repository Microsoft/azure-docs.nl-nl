---
title: 'Zelfstudie: Het voorspellende model maken met een notebook (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Ontdek hoe u een machine learning-model bouwt en implementeert met behulp van code in een Jupyter Notebook. Maak ook een scorescript waarmee invoer en uitvoer wordt gedefinieerd, voor eenvoudige integratie in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 00d5fa43245fb25b8ee99a0523d680bef891b71e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386999"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Zelfstudie: Power BI-integratie: het voorspellende model maken met een Jupyter-notebook (deel 1 van 2)

In deel 1 van deze zelfstudie traint en implementeert u een voorspellend machine learning-model, met behulp van code in een Jupyter Notebook. U maakt ook een scorescript om het invoer- en uitvoerschema van het model te definiëren voor integratie in Power BI.  In deel 2 gebruikt u het model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Jupyter-notebook maken.
> * Maak een Azure Machine Learning-rekenproces.
> * Train een regressiemodel met behulp van scikit-learn.
> * Schrijf een scorescript waarmee de invoer en uitvoer wordt gedefinieerd, voor eenvoudige integratie in Microsoft Power BI.
> * Implementeer het model in een eindpunt voor scoren in realtime.

Er zijn drie verschillende manieren om het model, dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op 'Optie A: Train en implementeer modellen met behulp van notebooks."  Deze optie is een op code gerichte creatie-ervaring. Het gebruikt Jupyter Notebooks die worden gehost in Azure Machine Learning Studio. 

Maar u kunt in plaats daarvan één van de andere opties gebruiken:

* [Optie B: Train en implementeer modellen met Azure Machine Learning-designer](tutorial-power-bi-designer-model.md). Deze creatie-ervaring met weinig code gebruikt een slepen en neerzetten-gebruikersinterface.
* [Optie C: Train en implementeer modellen met geautomatiseerde machine learning](tutorial-power-bi-automated-model.md). Deze creatie-ervaring zonder code automatiseert de gegevensvoorbereiding en modeltraining volledig.


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u een [gratis proefaccount](https://aka.ms/AMLFree) gebruiken. 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, zie [Azure Machine Learning-werkruimten maken en beheren](./how-to-manage-workspace.md#create-a-workspace).
- Introductiekennis van de Python-taal en machine learning-werkstromen.

## <a name="create-a-notebook-and-compute"></a>Een notebook en berekening maken

Selecteer op de startpagina van [**Azure Machine Learning Studio**](https://ml.azure.com) de optie **Nieuwe maken** > **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Schermopname toont hoe een notebook kan worden gemaakt.":::
 
Op de pagina **Maak een nieuw bestand**:

1. Geef uw notebook een naam (bijvoorbeeld *mijn_modelnotebook*).
1. Wijzig het **Bestandstype** in **Notebook**.
1. Selecteer **Maken**. 
 
Maak vervolgens een rekenproces en koppel dit aan uw notebook om codecellen uit te voeren. Start door het pluspictogram bovenaan het notebook te selecteren:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Schermopname toont hoe een rekenproces kan worden gemaakt.":::

Op de pagina **Rekenproces maken**:

1. Kies de grootte van de CPU van de virtuele machine. Voor deze zelfstudie kunt u een **Standard_D11_v2** kiezen, met 2 kernen en 14 GB RAM-geheugen.
1. Selecteer **Next**. 
1. Geef op de pagina **Instellingen configureren** een geldige **naam voor de berekening** op. Geldige tekens zijn hoofdletters en kleine letters, cijfers en afbreekstreepjes (-).
1. Selecteer **Maken**.

In het notebook ziet u mogelijk dat de cirkel naast **Berekening** lichtblauw geworden is. Deze kleurwijziging geeft aan dat het rekenproces wordt gemaakt:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Schermopname van de berekening die wordt gemaakt.":::

> [!NOTE]
> Het inrichten van het rekenproces kan 2 tot 4 minuten duren.

Zodra de berekening is ingericht, kunt u de notebook gebruiken om codecellen uit te voeren. In de cel kunt u bijvoorbeeld de volgende code typen:

```python
import numpy as np

np.sin(3)
```

Selecteer vervolgens Shift-Enter (of Control-Enter, of selecteer de knop **Afspelen** naast de cel). In dat geval moet de volgende uitvoer worden weergegeven:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Schermopname van de uitvoer van een cel.":::

U kunt nu een machine learning-model bouwen.

## <a name="build-a-model-by-using-scikit-learn"></a>Een model bouwen met behulp van scikit-learn

In deze zelfstudie gebruikt u de [gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Deze gegevensset is beschikbaar in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Gegevens importeren

Als u uw gegevens wilt importeren, kopieert en plakt u de onderstaande code in een nieuwe *codecel* in uw notebook.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Het gegevensframe `X_df` van de pandas bevat 10 invoervariabelen voor de basislijn. Deze variabelen bevatten leeftijd, geslacht, body mass index, gemiddelde bloeddruk en zes metingen voor bloedserum. Het gegevensframe `y_df` pandas is de doelvariabele. Het bevat een kwantitatieve maat voor de ziekteprogressie één jaar na de basislijn. Het gegevensframe bevat 442 records.

### <a name="train-the-model"></a>Het model trainen

Maak een nieuwe *codecel* in uw notebook. Kopieer vervolgens de volgende code en plak die in de cel. Met dit codefragment maakt u een Ridge Regression-model en serialiseert u het model met behulp van de pickle-indeling van Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Het model registreren

Naast de inhoud van het modelbestand zelf, worden metagegevens opgeslagen in uw geregistreerde model. De metagegevens bevatten de beschrijving, tags en frameworkgegevens van het model. 

Metagegevens zijn handig wanneer u modellen beheert en implementeert in uw werkruimte. Met behulp van tags kunt u uw modellen bijvoorbeeld categoriseren, en filters toepassen wanneer u modellen vermeldt in uw werkruimte. Bovendien wordt het eenvoudiger om dit model te implementeren als een webservice als u het markeert met het scikit-learn-framework.

Kopieer en plak de volgende code in een nieuwe *codecel* in uw notebook.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

U kunt ook het model in Azure Machine Learning Studio weergeven. Selecteer **Modellen** in het menu aan de linkerkant:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Schermopname toont hoe een model kan worden bekeken.":::

## <a name="define-the-scoring-script"></a>Het scorescript definiëren

Wanneer u een model implementeert dat wordt geïntegreerd in Power BI, moet u een Python-*scorescript* en een aangepaste omgeving definiëren. Het scorescript bevat twee functies:

- De functie `init()` wordt uitgevoerd wanneer de service wordt gestart. Hiermee wordt het model geladen (het model wordt automatisch gedownload uit het modelregister) en gedeserialiseerd.
- De functie `run(data)` wordt uitgevoerd wanneer een aanroep van de service invoergegevens bevat die moeten worden gescoord. 

>[!NOTE]
> De Python-decorators in de code hieronder definiëren het schema van de invoer- en uitvoergegevens. Dit is belangrijk voor integratie in Power BI.

Kopieer en plak de volgende code in een nieuwe *codecel* in uw notebook. Het volgende codefragment heeft cel-magic waarmee de code naar een bestand met de naam *score.py* wordt geschreven.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>De aangepaste omgeving definiëren

Definieer vervolgens de omgeving om het model te scoren. In de omgeving definieert u de Python-pakketten, zoals pandas en scikit-learn, die het scorescript (*score.py*) vereist.

Om de omgeving te definiëren kopieert en plakt u de onderstaande code in een nieuwe *codecel* in uw notebook.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Het model implementeren

Om het model te implementeren kopieert en plakt u de onderstaande code in een nieuwe *codecel* in uw notebook:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Het implementeren van de service kan 2 tot 4 minuten duren.

Als het implementeren van de service is geslaagd, zou u de volgende uitvoer moeten zien:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

U kunt de service ook in Azure Machine Learning Studio bekijken. Selecteer **Eindpunten** in het menu aan de linkerkant:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Schermopname toont hoe een service kan worden weergegeven.":::

Het wordt aanbevolen om de webservice te testen om te controleren of deze werkt zoals verwacht. Om naar uw notebook terug te gaan, gaat u naar Azure Machine Learning Studio. In het menu aan de linkerkant selecteert u vervolgens **Notebooks**. Kopieer en plak daarna de volgende code in een nieuwe *codecel* in uw notebook om de service te testen.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

Uw uitvoer moet eruitzien zoals in deze JSON-structuur: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een model kunt bouwen en implementeren dat kan worden gebruikt in Microsoft Power BI. In het volgende deel leert u hoe u dit model gebruikt vanuit een Power BI-rapport.

> [!div class="nextstepaction"]
> [Zelfstudie: Een model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
