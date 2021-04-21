---
title: Notebook-code converteren naar Python-scripts
titleSuffix: Azure Machine Learning
description: Maak van uw experimentele notebooks met machine learning code die gereed is voor productie met behulp van de MLOpsPython-codesjabloon. Vervolgens kunt u deze code testen, implementeren en automatiseren.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817482"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Zelfstudie: ML-experimenten converteren naar de Python-code voor productie

In deze zelfstudie leert u hoe u Jupyter-notebooks kunt converteren naar Python-scripts om ze test- en automatiseringsvriendelijk te maken met behulp van de MLOpsPython-codesjabloon en Azure Machine Learning. Dit proces wordt doorgaans gebruikt om experimenterings-/trainingscode uit te voeren vanaf een Jupyter-notebook en deze te converteren naar Python-scripts. Deze scripts kunnen vervolgens worden gebruikt voor testen en CI/CD-automatisering in uw productieomgeving. 

Een machine learning-project vereist experimenten waarbij hypothesen worden getest met agile hulpprogram ma's zoals Jupyter Notebook met echte gegevenssets. Zodra het model gereed is voor productie, moet de modelcode in een opslagplaats voor productiecode worden geplaatst. In sommige gevallen moet de modelcode worden geconverteerd naar Python-scripts die in de opslagplaats voor productiecode moeten worden geplaatst. In deze zelfstudie wordt een aanbevolen methode beschreven voor het exporteren van experimenteringscode naar Python-scripts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Niet-essentiële code opschonen
> * Jupyter Notebook-code herstructureren in functies
> * Python-scripts maken voor verwante taken
> * Moduletests maken

## <a name="prerequisites"></a>Vereisten

- Genereer de [MLOpsPython-sjabloon](https://github.com/microsoft/MLOpsPython/generate) en gebruik de `experimentation/Diabetes Ridge Regression Training.ipynb`- en `experimentation/Diabetes Ridge Regression Scoring.ipynb`-notebooks. Deze notebooks worden gebruikt als voorbeeld van het converteren van experimenten naar productie. U kunt deze notebooks vinden op [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installeer `nbconvert`. Volg alleen de installatie-instructies onder sectie __nbconvert installeren__ op de pagina [Installatie](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Alle niet-essentiële code verwijderen

Sommige code die is geschreven tijdens het experiment, is alleen bedoeld voor experimentele doeleinden. Daarom is de eerste stap voor het converteren van experimentele code naar productiecode, deze niet-essentiële code te verwijderen. Als u niet-essentiële code verwijdert, wordt de code ook eenvoudiger te onderhouden. In deze sectie verwijdert u code uit het `experimentation/Diabetes Ridge Regression Training.ipynb`-notebook. De instructies voor het afdrukken van de vorm van `X` en `y` en de cel waarmee `features.describe` worden aangeroepen, zijn alleen bedoeld voor het verkennen van gegevens en kunnen worden verwijderd. Na het verwijderen van de niet-essentiële code moet `experimentation/Diabetes Ridge Regression Training.ipynb` er als de volgende code uitzien, zonder markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Code herstructureren in functies

Ten tweede moet de Jupyter-code worden geherstructureerd in functies. Het herstructureren van code in functies maakt het gemakkelijker om moduletests uit te voeren en maakt het eenvoudiger om de code te onderhouden. In deze sectie herstructureert u het volgende:

- De Diabetes Ridge Regression Training-notebook(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- De Diabetes Ridge Regression Scoring-notebook(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge Regression Training-notebook herstructureren in functies

Voltooi in `experimentation/Diabetes Ridge Regression Training.ipynb` de volgende stappen:

1. Maak een functie met de naam `split_data` om het gegevensframe te splitsen in test- en trainingsgegevens. De functie moet het gegevensframe `df` als parameter gebruiken en een woordenboek met de sleutels `train` en `test` retourneren.

    Verplaats de code onder de kop *Gegevens splitsen in trainings- en validatiesets* naar de functie `split_data` en wijzig deze om het `data`-object te retourneren.

1. Maak een functie met de naam `train_model`, waarbij de parameters `data` en `args` worden gebruikt en een getraind model wordt geretourneerd.

    Verplaats de code onder de kop *Trainingsmodel op trainingsset* naar de functie `train_model` en wijzig deze om het `reg_model`-object te retourneren. Verwijder het `args`-woordenboek, de waarden komen uit de parameter `args`.

1. Maak een functie met de naam `get_model_metrics`, die parameters `reg_model` en `data` gebruikt, het model evalueert en vervolgens een woordenboek van metrische gegevens retourneert voor het getrainde model.

    Verplaats de code onder de kop *Model valideren op validatieset* naar de functie `get_model_metrics` en wijzig deze om het `metrics`-object te retourneren.

De drie functies moeten er als volgt uitzien:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Voltooi in `experimentation/Diabetes Ridge Regression Training.ipynb` de volgende stappen:

1. Maak een nieuwe functie met de naam `main`, waarvoor geen parameters worden gebruikt en er niets wordt geretourneerd.
1. Verplaats de code onder de kop 'Gegevens laden' naar de functie `main`.
1. Voeg aanroepen voor de zojuist geschreven functies toe aan de functie `main`:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Verplaats de code onder de kop 'Model opslaan' naar de functie `main`.

De functie `main` moet eruitzien als de volgende code:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

In deze fase mag er geen code meer in de notebook staan die niet in een functie voorkomt, behalve de instructies voor importeren in de eerste cel.

Voeg een instructie toe die de functie `main` aanroept.

```python
main()
```

Na het herstructureren moet `experimentation/Diabetes Ridge Regression Training.ipynb` eruitzien als de volgende code maar zonder de markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge Regression Scoring-notebook herstructureren in functies

Voltooi in `experimentation/Diabetes Ridge Regression Scoring.ipynb` de volgende stappen:

1. Maak een nieuwe functie met de naam `init`, waarvoor geen parameters worden gebruikt en er niets wordt geretourneerd.
1. Kopieer de code onder de kop 'Model opslaan' naar de functie `init`.

De functie `init` moet eruitzien als de volgende code:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Zodra de functie `init` is gemaakt, vervangt u als volgt alle code onder de kop 'Model laden' door één aanroep naar `init`:

```python
init()
```

Voltooi in `experimentation/Diabetes Ridge Regression Scoring.ipynb` de volgende stappen:

1. Maak een nieuwe functie met de naam `run`, die `raw_data` en `request_headers` als parameters gebruikt en als volgt een woordenlijst met resultaten retourneert:

    ```python
    {"result": result.tolist()}
    ```

1. Kopieer de code onder de koppen 'Gegevens voorbereiden' en 'Scoregegevens' naar de functie `run`.

    De functie `run` moet eruitzien als de volgende code (Vergeet niet om de instructies te verwijderen waarmee de variabelen `raw_data` en `request_headers`worden ingesteld, die later worden gebruikt wanneer de functie `run` wordt aangeroepen):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Zodra de functie `run` is gemaakt, vervangt u alle code onder de koppen 'Gegevens voorbereiden' en 'Scoregegevens' door de volgende code:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Met de vorige code worden variabelen `raw_data` en `request_header` ingesteld, wordt de functie `run` aangeroepen met `raw_data` en `request_header` en worden de voorspellingen afgedrukt.

Na het herstructureren moet `experimentation/Diabetes Ridge Regression Scoring.ipynb` eruitzien als de volgende code maar zonder de markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Gerelateerde functies in Python-bestanden combineren

Ten derde moeten gerelateerde functies worden samengevoegd in Python-bestanden om de code beter te kunnen hergebruiken. In deze sectie maakt u Python-bestanden voor de volgende notebooks:

- De Diabetes Ridge Regression Training-notebook(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- De Diabetes Ridge Regression Scoring-notebook(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-bestand maken voor de Diabetes Ridge Regression Training-notebook

Converteer uw notebook naar een uitvoerbaar script door de volgende instructie uit te voeren in een opdrachtprompt, die gebruikmaakt van het `nbconvert`-pakket en het pad van `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Nadat de notebook is geconverteerd naar `train.py`, verwijdert u eventuele ongewenste opmerkingen. Vervang de aanroep naar `main()` aan het einde van het bestand met een voorwaardelijke aanroep zoals de volgende code:

```python
if __name__ == '__main__':
    main()
```

Uw `train.py`-bestand moet eruitzien als de volgende code:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` kan nu worden aangeroepen vanaf een terminal door `python train.py` uit te voeren.
De functies van `train.py` kunnen ook worden aangeroepen vanuit andere bestanden.

Het `train_aml.py`-bestand in de `diabetes_regression/training`-map in de MLOpsPython-opslagplaats roept de functies aan die in `train.py` zijn gedefinieerd in de context van een Azure Machine Learning-experimentuitvoering. De functies kunnen ook worden aangeroepen in moduletests, die verderop in deze handleiding worden besproken.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-bestand maken voor de Diabetes Ridge Regression Scoring-notebook

Converteer uw notebook naar een uitvoerbaar script door de volgende instructie uit te voeren in een opdrachtprompt, die gebruikmaakt van het `nbconvert`-pakket en het pad van `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Nadat de notebook is geconverteerd naar `score.py`, verwijdert u eventuele ongewenste opmerkingen. Uw `score.py`-bestand moet eruitzien als de volgende code:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

De variabele `model` moet globaal zijn zodat deze overal in het script zichtbaar is. Voeg de volgende instructie toe aan het begin van de functie `init`:

```python
global model
```

Nadat u de vorige instructie hebt toegevoegd, moet de functie `init` eruitzien als de volgende code:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Moduletests maken voor elk Python-bestand

Ten vierde maakt u moduletests voor uw Python-functies. Moduletests beveiligen code tegen functionele regressies en maken het gemakkelijker om deze te onderhouden. In deze sectie maakt u moduletests voor de functies in `train.py`.

`train.py` bevat meerdere functies, maar we maken slechts één moduletest voor de functie `train_model` met behulp van het Pytest-framework in deze zelfstudie. Pytest is niet het enige Python-framework voor moduletests, maar het is een van de meest gebruikte frameworks. Ga voor meer informatie naar [Pytest](https://pytest.org).

Een moduletest bevat normaal gesproken drie hoofdacties:

- Object ordenen: benodigde objecten maken en instellen
- Reageren op een object
- Bevestigen wat er wordt verwacht

De moduletest roept `train_model` aan met bepaalde in code vastgelegde gegevens en argumenten, en valideert dat `train_model` op de verwachte manier heeft gehandeld met behulp van het resulterende getrainde model om een voorspelling te doen en die voorspelling te vergelijken met een verwachte waarde.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u kunt converteren van een experiment naar productiecode, raadpleegt u de volgende links voor meer informatie en volgende stappen:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Bouw een CI/CD-pijplijn om uw eigen model te trainen, evalueren en implementeren met behulp van Azure-pijplijnen en Azure Machine Learning
+ [Uitvoeringen en metrische gegevens van Azure ML-experimenten bewaken](./how-to-log-view-metrics.md)
+ [Gegevens van ML-webservice-eindpunten bewaken en verzamelen](./how-to-enable-app-insights.md)