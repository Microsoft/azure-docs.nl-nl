---
title: Migreren van schattingen naar ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Migratie handleiding voor de migratie van schattingen naar ScriptRunConfig voor het configureren van trainings taken.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 64c03b1c9fc18a4e78af9914b893599683069ced
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632776"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migreren van schattingen naar ScriptRunConfig

Tot nu toe zijn er meerdere methoden voor het configureren van een trainings taak in Azure Machine Learning via de SDK, met inbegrip van schattingen, ScriptRunConfig en de RunConfiguration op lagere niveaus.   Om deze dubbel zinnigheid en inconsistentie op te lossen, vereenvoudigen we het taak configuratie proces in azure ML.  U moet nu ScriptRunConfig gebruiken als de aanbevolen optie voor het configureren van trainings taken. 

Schattingen zijn afgeschaft met de 1.19.0-versie van de python-SDK. U moet ook in het algemeen voor komen dat u zelf een RunConfiguration-object kunt instantiëren en in plaats daarvan uw taak configureren met behulp van de klasse ScriptRunConfig.

Dit artikel heeft betrekking op algemene overwegingen bij de migratie van schattingen naar ScriptRunConfig.

> [!IMPORTANT]
> Als u wilt migreren naar ScriptRunConfig van schattingen, moet u >= 1.15.0 van de python-SDK gebruiken.

## <a name="scriptrunconfig-documentation-and-samples"></a>Documentatie en voor beelden ScriptRunConfig
Azure Machine Learning documentatie en voor beelden zijn bijgewerkt om [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) te gebruiken voor taak configuratie en-verzen ding.

Raadpleeg de volgende documentatie voor meer informatie over het gebruik van ScriptRunConfig:
* [Trainingsuitvoering configureren en verzenden](how-to-set-up-training-targets.md)
* [PyTorch training-uitvoeringen configureren](how-to-train-pytorch.md)
* [Tensor flow training-uitvoeringen configureren](how-to-train-tensorflow.md)
* [Scikit configureren-trainings uitvoeringen leren](how-to-train-scikit-learn.md)

Raadpleeg bovendien de volgende voor beelden & zelf studies:
* [Azure-MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>De trainings omgeving definiëren
Hoewel de verschillende Framework-schattingen vooraf geconfigureerde omgevingen hebben die worden ondersteund door docker-installatie kopieën, zijn de Dockerfiles voor deze installatie kopieën persoonlijk.  Daarom hebt u geen veel transparantie in wat deze omgevingen bevatten. Daarnaast nemen de schattingen in de omgeving-gerelateerde configuraties als afzonderlijke para meters (zoals `pip_packages` , `custom_docker_image` ) op hun respectieve constructors.

Wanneer u ScriptRunConfig gebruikt, worden alle omgevings configuraties ingekapseld in het `Environment` object dat wordt door gegeven aan de `environment` para meter van de ScriptRunConfig-constructor. Als u een trainings taak wilt configureren, geeft u een omgeving met alle afhankelijkheden die nodig zijn voor uw trainings script. Als er geen omgeving wordt opgegeven, gebruikt Azure ML een van de basis installatie kopieën van Azure ML, met name de naam die wordt gedefinieerd door `azureml.core.environment.DEFAULT_CPU_IMAGE` , als de standaard omgeving. Er zijn een aantal manieren om een omgeving te bieden:

* Het gebruik van een gehoste [omgeving met een bedrijfs netwerk](how-to-use-environments.md#use-a-curated-environment) is standaard vooraf gedefinieerde omgevingen die beschikbaar zijn in uw werk ruimte. Er is een overeenkomende toegewezen omgeving voor elk van de vooraf geconfigureerde docker-installatie kopieën van Framework/Version die een back-up van elk Framework-Estimator.
* [Uw eigen aangepaste omgeving definiëren](how-to-use-environments.md)

Hier volgt een voor beeld van het gebruik van de PyTorch 1,6-omgeving met curator voor training:

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

Als u **omgevings variabelen** wilt opgeven die worden ingesteld voor het proces waarin het trainings script wordt uitgevoerd, gebruikt u het omgevings object:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Zie voor meer informatie over het configureren en beheren van Azure ML-omgevingen:
* [Omgevingen gebruiken](how-to-use-environments.md)
* [Gecureerde omgevingen](resource-curated-environments.md)
* [Train met een aangepaste docker-installatie kopie](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Gegevens gebruiken voor training
### <a name="datasets"></a>Gegevenssets
Als u een Azure ML-gegevensset gebruikt voor training, geeft u de gegevensset door als een argument voor uw script met behulp van de `arguments` para meter. Als u dit doet, wordt het gegevenspad (koppelings punt of download traject) in uw trainings script via argumenten weer gegeven.

In het volgende voor beeld wordt een trainings taak geconfigureerd waarbij de FileDataset, `mnist_ds` wordt gekoppeld aan de externe compute.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (oud)
We raden u aan om Azure ML-gegevens sets te gebruiken op de oude DataReferencee manier als u DataReferences nog steeds gebruikt om een wille keurige reden, moet u uw taak als volgt configureren:
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
* [Train met gegevens sets in azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)

## <a name="distributed-training"></a>Gedistribueerde training
Als u een gedistribueerde taak voor training moet configureren, moet u de `distributed_job_config` para meter in de ScriptRunConfig-constructor opgeven. Geef een [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)of [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) door voor gedistribueerde taken van de respectieve typen.

In het volgende voor beeld wordt een PyTorch-trainings taak geconfigureerd voor het gebruik van gedistribueerde training met MPI/Horovod:
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
* [Gedistribueerde training met tensor flow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Diversen
Als u om een of andere reden toegang moet krijgen tot het onderliggende RunConfiguration-object voor een ScriptRunConfig, kunt u dit als volgt doen:
```
src.run_config
```

## <a name="next-steps"></a>Volgende stappen

* [Trainingsuitvoering configureren en verzenden](how-to-set-up-training-targets.md)
