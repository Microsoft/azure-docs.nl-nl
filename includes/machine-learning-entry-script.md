---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325299"
---
Het invoerscript ontvangt de gegevens die bij een geïmplementeerde webservice zijn ingediend en stuurt ze door naar het model. Vervolgens wordt de reactie die door het model is geretourneerd naar de client geretourneerd. *Het script is specifiek voor uw model*. Het moet inzicht hebben in de gegevens die het model verwacht en retourneert.

De twee dingen die u nodig hebt om uw invoer script uit te voeren, zijn:

1. Uw model laden (met behulp van een functie genaamd `init()` )
1. Uw model uitvoeren op invoer gegevens (met behulp van een functie genaamd `run()` )

We gaan deze stappen uitgebreid door nemen.

### <a name="writing-init"></a>Init schrijven () 

#### <a name="loading-a-registered-model"></a>Een geregistreerd model laden

Uw geregistreerde modellen worden opgeslagen in een pad naar een omgevings variabele met de naam `AZUREML_MODEL_DIR` . Zie voor meer informatie over de exacte directory structuur [model bestanden zoeken in uw invoer script](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Een lokaal model laden

Als u hebt gekozen voor het registreren van uw model en uw model hebt door gegeven als onderdeel van de bron directory, kunt u het op dezelfde manier lezen als u lokaal zou doen door het pad naar het model te geven ten opzichte van uw score script. Als u bijvoorbeeld een directory hebt gestructureerd als:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

u kunt uw modellen laden met de volgende python-code:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Schrijf bewerking ()

`run()` wordt uitgevoerd telkens wanneer uw model een score aanvraag ontvangt, en verwacht de hoofd tekst van de aanvraag een JSON-document met de volgende structuur:

```json
{
    "data": <model-specific-data-structure>
}

```

De invoer naar `run()` is een python-teken reeks die wille keurig de gegevens sleutel volgt.

In het volgende voor beeld wordt gedemonstreerd hoe u een geregistreerd scikit-model kunt laden en een score hebt met numpy-gegevens:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Voor meer geavanceerde voor beelden, waaronder automatische Swagger-schema's genereren en binaire bestanden (d.w.z. afbeeldings gegevens), leest u [het artikel over geavanceerde invoer scripts ontwerpen](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)