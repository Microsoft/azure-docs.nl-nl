---
title: 'Zelfstudie: Het voorspellende model maken met een notebook (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Leer hoe u een machine learning-model bouwt en implementeert, met behulp van code in een Jupyter-notebook, zodat u het kunt gebruiken om resultaten te voorspellen in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370630"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Zelfstudie: Power BI-integratie: het voorspellende model maken met een notebook (deel 1 van 2)

In het eerste deel van deze zelfstudie traint en implementeert u een voorspellend machine learning-model, met behulp van code in een Jupyter-notebook. In deel 2 gebruikt u het model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een Jupyter Notebook maken
> * Een Azure Machine Learning-rekenproces maken
> * Een regressiemodel trainen met scikit-learn
> * Het model implementeren in een realtime-eindpunt voor scoren

Er zijn drie verschillende manieren om het model dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op Optie A: Modellen trainen en implementeren met behulp van notebooks.  Deze optie toont een op code gerichte ontwerpervaring met behulp van Jupyter-notebooks die worden gehost in Azure Machine Learning Studio. 

Gebruik in plaats hiervan:

* [Optie B: Modellen trainen en implementeren met behulp van de ontwerpfunctie](tutorial-power-bi-designer-model.md): een ontwerpervaring met weinig code, met behulp van de ontwerpfunctie (een slepen en neerzetten-gebruikersinterface).
* [Optie C: Modellen trainen en implementeren met behulp van ML](tutorial-power-bi-automated-model.md): een ontwerpervaring zonder code waarmee de gegevensvoorbereiding en modeltraining volledig is geautomatiseerd.


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement ([een gratis proefversie beschikbaar](https://aka.ms/AMLFree)). 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, volgt u [Een Azure Machine Learning-werkruimte maken](./how-to-manage-workspace.md#create-a-workspace).
- Introductiekennis van de Python-taal en machine learning-werkstromen.

## <a name="create-a-notebook-and-compute"></a>Een notebook en berekening maken

Selecteer op de startpagina [Azure Machine Learning Studio](https://ml.azure.com) de optie **Nieuwe maken**, gevolgd door **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Schermopname van het maken van een notebook":::
 
U ziet nu het dialoogvenster **Een nieuw bestand maken**. Voer het volgende in:

1. Een bestandsnaam voor de notebook (bijvoorbeeld `my_model_notebook`)
1. Wijzig het **Bestandstype** in **Notebook**

Selecteer **Maken**. Hierna moet u wat berekeningen maken, en deze toevoegen aan uw notebook om de codecellen uit te voeren. Selecteer hiervoor het pluspictogram bovenaan het notebook:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Schermopname van het maken van een rekenproces":::

Doe vervolgens op de pagina **Rekenproces maken** het volgende:

1. Kies een CPU-grootte voor de virtuele machine. Voor deze zelfstudie is een **Standard_D11_v2** (twee kerngeheugens, 14-GB RAM) goed.
1. Selecteer **Volgende**. 
1. Geef op de pagina **Instellingen configureren** een geldige **Berekeningsnaam** op (geldige tekens zijn hoofdletters, kleine letters, cijfers, en het teken -).
1. Selecteer **Maken**.

Misschien valt u in de notebook op dat het rondje naast **Berekening** een cyaan is geworden. Dit geeft aan dat het rekenproces wordt gemaakt:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Schermopname van de berekening die wordt gemaakt":::

> [!NOTE]
> Het kan 2 tot 4 minuten duren voordat de berekening is ingericht.

Zodra de berekening is ingericht, kunt u de notebook gebruiken om codecellen uit te voeren. Typ bijvoorbeeld in de cel:

```python
import numpy as np

np.sin(3)
```

Gevolgd door **Shift-Enter** (of **Control-Enter**, of selecteer de knop Afspelen naast de cel). U moet de volgende uitvoer zien:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Schermopname van de uitvoering van de cel":::

U kunt nu een Machine Learning-model bouwen.

## <a name="build-a-model-using-scikit-learn"></a>Een model bouwen met behulp van scikit-learn

In deze zelfstudie gebruikt u de [Gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), die beschikbaar is in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Gegevens importeren

Als u uw gegevens wilt importeren, kopieert en plakt u de onderstaande code in een nieuwe **codecel** in uw notebook:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Het gegevensframe pandas `X_df` bevat 10 basislijnvariabelen voor invoer (zoals leeftijd, geslacht, BMI (body mass index), gemiddelde bloeddruk, en zes metingen voor bloedserum). Het gegevensframe pandas `y_df` is de doelvariabele die een kwantitatieve meting bevat van de ziektevoortgang één jaar na de basislijn. Er zijn in totaal 442 records.

### <a name="train-model"></a>Model trainen

Maak een nieuwe **codecel** in uw notebook, en kopieer en plak het onderstaande codefragment. Hiermee wordt een Ridge-regressiemodel gemaakt, en het model geserialiseerd met behulp van de pickle-indeling van Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Het model registreren

Naast de inhoud van het modelbestand zelf, worden in uw geregistreerde model ook metagegevens van het model opgeslagen (modelbeschrijving, tags, en frameworkgegevens) die nuttig zijn bij het beheren en implementeren van modellen in uw werkruimte. Met behulp van tags kunt u uw modellen bijvoorbeeld categoriseren, en filters toepassen wanneer u modellen vermeldt in uw werkruimte. Bovendien wordt het eenvoudiger om dit model te implementeren als een webservice als u het markeert met het scikit-learn-framework, zoals we later zien.

Kopieer en plak u de onderstaande code in een nieuwe **codecel** in uw notebook:

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

U kunt het model ook weergeven in Azure Machine Learning Studio door naar **Eindpunten** te navigeren in het menu aan de linkerkant:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Schermopname van het model":::

### <a name="define-the-scoring-script"></a>Het scorescript definiëren

Wanneer u een model implementeert voor integratie in Microsoft Power BI, moet u een Python-*scorescript* en een aangepaste omgeving definiëren. Het scorescript bevat twee functies:

- `init()`: deze functie wordt uitgevoerd zodra de service is gestart. Met deze functie wordt het model geladen (het model wordt automatisch gedownload uit het modelregister) en geserialiseerd.
- `run(data)`: deze functie wordt uitgevoerd wanneer de service wordt aangeroepen met enkele invoergegevens die moeten worden gescoord. 

>[!NOTE]
> We gebruiken Python-decorators om het schema van de invoer- en uitvoergegevens te definiëren. Dit is belangrijk voor de werking van de Microsoft Power BI-integratie.

Kopieer en plak u de onderstaande code in een nieuwe **codecel** in uw notebook. Het onderstaande codefragment is heel bijzonder. De code wordt geschreven naar een bestand met de naam score.py.

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>De aangepaste omgeving definiëren

Vervolgens moeten we de omgeving definiëren om het model te scoren. We moeten deze omgeving definiëren in de Python-pakketten die zijn vereist voor de scorescripts (score.py) die hierboven zijn gedefinieerd, zoals pandas, scikit-learn, enzovoort.

Als u de omgeving wilt definiëren, kopieert en plakt u de onderstaande code in een nieuwe **codecel** in uw notebook:

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

Als u de omgeving wilt implementeren, kopieert en plakt u de onderstaande code in een nieuwe **codecel** in uw notebook:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Het kan 2-4 minuten duren voordat de service is geïmplementeerd.

Als het implementeren van de service is geslaagd, ziet u de volgende uitvoer:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

U kunt de service ook weergeven in Azure Machine Learning Studio door naar **Eindpunten** te navigeren in het menu aan de linkerkant:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Schermopname van het eindpunt":::

Het wordt aanbevolen om de webservice te testen om te controleren of deze werkt zoals verwacht. Ga terug naar de notebook door **Notebook** te selecteren in het linkermenu in Azure Machine Learning Studio. Kopieer en plak de onderstaande code in een nieuwe **codecel** in uw notebook om de service te testen:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Uw uitvoer moet eruitzien zoals in de volgende JSON-structuur: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een model kunt bouwen en implementeren op een manier dat het model kan worden verbruikt in Microsoft Power BI. In het volgende deel leert u hoe u dit model gebruikt vanuit een Power BI-rapport.

> [!div class="nextstepaction"]
> [Zelfstudie: Model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
