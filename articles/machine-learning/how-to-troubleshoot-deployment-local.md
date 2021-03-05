---
title: Problemen met de implementatie van het lokale model oplossen
titleSuffix: Azure Machine Learning
description: Voer een implementatie van het lokale model uit als eerste stap in het oplossen van fouten in model implementaties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: ebd984ad6fd91aa29af9766042a03bc56efe17eb
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215745"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Problemen oplossen met een lokale model implementatie

Probeer de implementatie van een lokaal model als eerste stap in het oplossen van problemen met de implementatie van Azure Container Instances (ACI) of Azure Kubernetes service (AKS).  Het gebruik van een lokale webservice maakt het gemakkelijker om veelvoorkomende Azure Machine Learning implementatie fouten van de docker-webservice te herkennen en op te lossen.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* Optie A (**Aanbevolen**): lokaal fouten opsporen in azure machine learning reken exemplaar
   * Een Azure Machine Learning-werkruimte met een [reken instantie](how-to-deploy-local-container-notebook-vm.md) die wordt uitgevoerd
* Optie B: lokaal op uw computer fouten opsporen
   * De [Azure machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
   * De [Azure CLI](/cli/azure/install-azure-cli).
   * De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
   * Een werkende docker-installatie op uw lokale systeem hebben. 
   * Als u de docker-installatie wilt controleren, gebruikt u de opdracht `docker run hello-world` vanaf een Terminal of opdracht prompt. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het installeren van docker of het oplossen van problemen met docker-fouten.

## <a name="debug-locally"></a>Lokaal fouten opsporen

U vindt een voor beeld van een [lokale implementatie notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) in de  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -opslag plaats om een uitvoer bare-voorbeeld te verkennen.

> [!WARNING]
> Lokale web service-implementaties worden niet ondersteund voor productie scenario's.

Als u lokaal wilt implementeren, wijzigt u de code die u wilt gebruiken `LocalWebservice.deploy_configuration()` om een implementatie configuratie te maken. Vervolgens gebruiken `Model.deploy()` om de service te implementeren. In het volgende voor beeld wordt een model (opgenomen in de variabele model) geïmplementeerd als lokale webservice:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Als u uw eigen Conda-specificatie YAML definieert, vermeldt u de versie van azureml-defaults >= 1.0.45 als een PIP-afhankelijkheid. Dit pakket is nodig om het model als een webservice te hosten.

Op dit moment kunt u de service als normaal gebruiken. De volgende code toont het verzenden van gegevens naar de service:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

### <a name="update-the-service"></a>De service bijwerken

Tijdens lokale tests moet u het bestand mogelijk bijwerken `score.py` om logboek registratie toe te voegen of om problemen op te lossen die u hebt gedetecteerd. Als u wijzigingen in het `score.py` bestand wilt laden, gebruikt u `reload()` . Met de volgende code wordt bijvoorbeeld het script voor de service opnieuw geladen en vervolgens worden er gegevens naar verzonden. De gegevens worden beoordeeld aan de hand van het bijgewerkte `score.py` bestand:

> [!IMPORTANT]
> De `reload` methode is alleen beschikbaar voor lokale implementaties. Zie [How to update your webservice](how-to-deploy-update-web-service.md)(Engelstalig) voor meer informatie over het bijwerken van een implementatie naar een ander Compute-doel.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Het script wordt opnieuw geladen vanaf de locatie die is opgegeven door het `InferenceConfig` object dat door de service wordt gebruikt.

Als u het model, Conda afhankelijkheden of implementatie configuratie wilt wijzigen, gebruikt u [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). In het volgende voor beeld wordt het model bijgewerkt dat door de service wordt gebruikt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>De service verwijderen

Als u de service wilt verwijderen, gebruikt u [Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Het docker-logboek controleren

U kunt de gedetailleerde docker-engine logboek berichten vanuit het Service object afdrukken. U kunt het logboek weer geven voor ACI-, AKS-en lokale implementaties. In het volgende voor beeld ziet u hoe u de logboeken afdrukt.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Als de regel `Booting worker with pid: <pid>` meerdere keren in de logboeken wordt weer gegeven, betekent dit dat er onvoldoende geheugen is om de werk nemer te starten.
U kunt de fout aanpakken door de waarde van in te verhogen `memory_gb``deployment_config`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over implementatie:

* [Problemen met externe implementaties oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren en waar](how-to-deploy-and-where.md)
* [Zelf studie: modellen trainen & implementeren](tutorial-train-models-with-aml.md)
* [Experimenten lokaal uitvoeren en fouten opsporen](./how-to-debug-visual-studio-code.md)