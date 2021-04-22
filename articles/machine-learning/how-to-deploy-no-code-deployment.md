---
title: Geen code-implementatie (preview)
titleSuffix: Azure Machine Learning
description: Zonder code-implementatie kunt u een model implementeren als een webservice zonder dat u handmatig een invoerscript hoeft te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: how-to
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: d1d74369dde0118d055261b8c04df36d250221b4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889408"
---
# <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van model zonder code

Implementatie van model zonder code is momenteel in preview en ondersteunt de volgende machine learning frameworks:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel-indeling
TensorFlow-modellen moeten worden geregistreerd in **savedmodel-indeling** om te kunnen werken met modelimplementatie zonder code.

Zie [deze koppeling](https://www.tensorflow.org/guide/saved_model) voor informatie over het maken van een SavedModel.

We ondersteunen een TensorFlow-versie die wordt vermeld onder Tags op [TensorFlow Serving DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX-modellen

ONNX-modelregistratie en -implementatie wordt ondersteund voor elke ONNX-deferentiegrafiek. Voorverwerkings- en naverwerkingsstappen worden momenteel niet ondersteund.

Hier is een voorbeeld van hoe u een MNIST ONNX-model registreert en implementeert:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Zie Consume an [Azure Machine Learning model deployed as a web service (Een](./how-to-consume-web-service.md)model gebruiken dat is geïmplementeerd als een webservice) om een model te scoren. Veel ONNX-projecten gebruiken protobuf-bestanden om trainings- en validatiegegevens compact op te slaan, waardoor het lastig kan zijn om te weten wat de gegevensindeling van de service verwacht. Als modelontwikkelaar moet u het volgende documenteren voor uw ontwikkelaars:

* Invoerindeling (JSON of binair)
* Vorm en type invoergegevens (bijvoorbeeld een matrix met floats van vorm [100.100,3])
* Domeingegevens (bijvoorbeeld voor een afbeelding, de kleurruimte, onderdeelorder en of de waarden zijn genormaliseerd)

Als u Pytorch gebruikt, vindt u in Modellen exporteren van [PyTorch naar ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) de details over conversie en beperkingen. 

## <a name="scikit-learn-models"></a>Scikit-learn-modellen

Er wordt geen implementatie van codemodellen ondersteund voor alle ingebouwde scikit-learn-modeltypen.

Hier is een voorbeeld van het registreren en implementeren van een sklearn-model zonder extra code:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Modellen die ondersteuning bieden predict_proba gebruiken die methode standaard. Als u dit wilt overschrijven om Predict te gebruiken, kunt u de POST-body wijzigen zoals hieronder wordt weergegeven:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Deze afhankelijkheden zijn opgenomen in de vooraf gebouwde scikit-learn-deference-container:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Clienttoepassingen maken om webservices te gebruiken](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurteniswaarschuwingen en triggers maken voor modelimplementaties](how-to-use-event-grid.md)