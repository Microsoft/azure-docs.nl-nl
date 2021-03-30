---
title: Geen code-implementatie (preview-versie)
titleSuffix: Azure Machine Learning
description: Als u geen code implementeert, kunt u een model implementeren als een webservice zonder dat u hand matig een invoer script hoeft te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324926"
---
# <a name="preview-no-code-model-deployment"></a>Evaluatie Implementatie van geen code model

Implementatie zonder code model is momenteel als preview-versie beschikbaar en ondersteunt de volgende machine learning frameworks:

## <a name="tensorflow-savedmodel-format"></a>Tensor flow SavedModel-indeling
Tensor flow-modellen moeten worden geregistreerd in **SavedModel-indeling** om te kunnen werken met implementatie zonder code modellen.

Zie [deze koppeling](https://www.tensorflow.org/guide/saved_model) voor meer informatie over het maken van een SavedModel.

We ondersteunen een tensor flow-versie die wordt vermeld onder ' Tags ' op het [tensor flow voor DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

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

Registratie en implementatie van het ONNX-model worden ondersteund voor elk ONNX-Afleidings diagram. De stappen voor preprocess en postprocess worden momenteel niet ondersteund.

Hier volgt een voor beeld van hoe u een MNIST ONNX-model registreert en implementeert:

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

Zie [een Azure machine learning model gebruiken dat is geïmplementeerd als een webservice](./how-to-consume-web-service.md)om een model te scoren. Veel ONNX-projecten maken gebruik van protobuf-bestanden om trainings-en validatie gegevens op te slaan, waardoor het moeilijk is om te weten wat de door de service verwachte gegevens indeling is. Als model ontwikkelaar kunt u het beste documenten voor uw ontwikkel aars documenteren:

* Invoer indeling (JSON of binair)
* De vorm van invoer gegevens en het type (bijvoorbeeld een matrix met zwevende vormen [100100, 3])
* Domein informatie (bijvoorbeeld voor een afbeelding, de kleur ruimte, de volg orde van onderdelen en of de waarden genormaliseerd zijn)

Als u Pytorch gebruikt, bevat het [exporteren van modellen van Pytorch naar ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) de details over conversie en beperkingen. 

## <a name="scikit-learn-models"></a>Scikit-modellen leren

Er wordt geen code model implementatie ondersteund voor alle ingebouwde scikit-informatie over model typen.

Hier volgt een voor beeld van het registreren en implementeren van een sklearn-model zonder extra code:

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
> Modellen die ondersteuning bieden voor predict_proba, gebruiken deze methode standaard. Als u dit wilt overschrijven om te voors pellen, kunt u de bericht tekst als volgt wijzigen:

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
> Deze afhankelijkheden zijn opgenomen in de vooraf gemaakte scikit-informatie over de de:

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
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)