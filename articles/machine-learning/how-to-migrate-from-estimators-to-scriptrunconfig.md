---
title: Migreren van schatters naar ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Migratiehandleiding voor het migreren van estimators naar ScriptRunConfig voor het configureren van trainingstaken.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: daf142f6e49a09556d05faf4eea23b31a2cab995
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888832"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migreren van estimators naar ScriptRunConfig

Tot nu toe zijn er meerdere methoden voor het configureren van een trainings job in Azure Machine Learning via de SDK, waaronder estimators, ScriptRunConfig en RunConfiguration op lager niveau.   Om deze dubbelzinnigheid en inconsistentie aan te pakken, vereenvoudigen we het taakconfiguratieproces in Azure ML.  U moet nu ScriptRunConfig gebruiken als de aanbevolen optie voor het configureren van trainingstaken. 

Estimators zijn afgeschaft met de versie 1.19.0 van de Python SDK. Over het algemeen moet u ook voorkomen dat u zelf expliciet een RunConfiguration-object maakt en in plaats daarvan uw taak configureert met behulp van de klasse ScriptRunConfig.

In dit artikel worden veelvoorkomende overwegingen beschreven bij het migreren van estimators naar ScriptRunConfig.

> [!IMPORTANT]
> Als u wilt migreren naar ScriptRunConfig vanuit Estimators, moet u ervoor zorgen dat u >= 1.15.0 van de Python SDK gebruikt.

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig-documentatie en -voorbeelden
Azure Machine Learning en voorbeelden zijn bijgewerkt voor het gebruik [van ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) voor het configureren en indienen van de taak.

Raadpleeg de volgende documentatie voor informatie over het gebruik van ScriptRunConfig:
* [Trainingsuitvoering configureren en verzenden](how-to-set-up-training-targets.md)
* [PyTorch-trainings runs configureren](how-to-train-pytorch.md)
* [TensorFlow-trainingsuit runs configureren](how-to-train-tensorflow.md)
* [Scikit-learn-trainings runs configureren](how-to-train-scikit-learn.md)

Raadpleeg daarnaast de volgende voorbeelden & zelfstudies:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>De trainingsomgeving definiëren
Hoewel de verschillende frameworkschattingen vooraf geconfigureerde omgevingen hebben die worden gebruikt door Docker-afbeeldingen, zijn de Dockerfiles voor deze afbeeldingen privé.  Daarom hebt u niet veel transparantie in wat deze omgevingen bevatten. Bovendien nemen de estimators omgevingsgerelateerde configuraties op als afzonderlijke parameters (zoals `pip_packages` , ) op hun respectieve `custom_docker_image` constructors.

Wanneer u ScriptRunConfig gebruikt, worden alle omgevingsconfiguraties ingekapseld in het -object dat wordt doorgegeven aan de parameter van de `Environment` `environment` ScriptRunConfig-constructor. Als u een trainings job wilt configureren, geeft u een omgeving op met alle afhankelijkheden die vereist zijn voor uw trainingsscript. Als er geen omgeving is opgegeven, gebruikt Azure ML een van de Azure ML-basisafbeeldingen, met name de omgeving die is gedefinieerd door `azureml.core.environment.DEFAULT_CPU_IMAGE` , als de standaardomgeving. Er zijn een aantal manieren om een omgeving te bieden:

* [Een gecureerde omgeving gebruiken:](how-to-use-environments.md#use-a-curated-environment) gecureerde omgevingen zijn standaard vooraf gedefinieerde omgevingen die beschikbaar zijn in uw werkruimte. Er is een bijbehorende gecureerde omgeving voor elk van de vooraf geconfigureerde Docker-framework-/versie-afbeeldingen die een back-van-elke framework-estimator maakten.
* [Uw eigen aangepaste omgeving definiëren](how-to-use-environments.md)

Hier is een voorbeeld van het gebruik van de gecureerde PyTorch 1.6-omgeving voor training:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Als u **omgevingsvariabelen wilt opgeven** die worden ingesteld voor het proces waarin het trainingsscript wordt uitgevoerd, gebruikt u het object Environment:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Zie voor meer informatie over het configureren en beheren van Azure ML-omgevingen:
* [Omgevingen gebruiken](how-to-use-environments.md)
* [Gecureerde omgevingen](resource-curated-environments.md)
* [Trainen met een aangepaste Docker-afbeelding](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Gegevens gebruiken voor training
### <a name="datasets"></a>Gegevenssets
Als u een Azure ML-gegevensset gebruikt voor training, geeft u de gegevensset als argument aan uw script door met behulp van de `arguments` parameter . Als u dit doet, krijgt u het gegevenspad (het bevestigingspunt of downloadpad) via argumenten in uw trainingsscript op.

In het volgende voorbeeld wordt een trainings job geconfigureerd waarbij de FileDataset, `mnist_ds` , wordt toegevoegd aan de externe rekenkracht.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (oud)
Hoewel we het gebruik van Azure ML-gegevenssets op de oude DataReference-manier aanraden, moet u uw taak als volgt configureren als u DataReferences nog steeds gebruikt:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Zie voor meer informatie over het gebruik van gegevens voor training:
* [Trainen met gegevenssets in Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Gedistribueerde training
Als u een gedistribueerde taak wilt configureren voor training, doet u dit door de parameter op te geven `distributed_job_config` in de ScriptRunConfig-constructor. Geef een [MpiConfiguration,](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)of [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) door voor gedistribueerde taken van de respectieve typen.

In het volgende voorbeeld wordt een PyTorch-trainings job geconfigureerd voor het gebruik van gedistribueerde training met MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Zie voor meer informatie:
* [Gedistribueerde training met PyTorch](how-to-train-pytorch.md#distributed-training)
* [Gedistribueerde training met TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Diversen
Als u om welke reden dan ook toegang nodig hebt tot het onderliggende RunConfiguration-object voor een ScriptRunConfig, kunt u dit als volgt doen:
```
src.run_config
```

## <a name="next-steps"></a>Volgende stappen

* [Trainingsuitvoering configureren en verzenden](how-to-set-up-training-targets.md)