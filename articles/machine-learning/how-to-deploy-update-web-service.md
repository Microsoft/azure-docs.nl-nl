---
title: Webservices bijwerken
titleSuffix: Azure Machine Learning
description: Meer informatie over het vernieuwen van een webservice die al is geïmplementeerd in Azure Machine Learning. U kunt instellingen bijwerken, zoals model, omgeving en invoer script.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: cd8bef53bb8f7c42aa6dbd0bc1e1c67400b8f2a0
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219077"
---
# <a name="update-a-deployed-web-service"></a>Een geïmplementeerde webservice bijwerken

In dit artikel leert u hoe u een webservice bijwerkt die is geïmplementeerd met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u al een webservice met Azure Machine Learning hebt geïmplementeerd. Als u wilt weten hoe u een webservice implementeert, [voert u de volgende stappen uit](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Webservice bijwerken

Als u een webservice wilt bijwerken, gebruikt u de- `update` methode. U kunt de webservice bijwerken voor het gebruik van een nieuw model, een nieuw invoer script of nieuwe afhankelijkheden die kunnen worden opgegeven in een Afleidings configuratie. Zie de documentatie voor [webservice-update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)voor meer informatie.

Zie de [AKS-service-update methode.](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Zie de [service-update methode ACI.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Wanneer u een nieuwe versie van een model maakt, moet u elke service die u wilt gebruiken hand matig bijwerken.
>
> U kunt de SDK niet gebruiken om een webservice bij te werken die is gepubliceerd vanuit de Azure Machine Learning Designer.

**De SDK gebruiken**

De volgende code laat zien hoe u met behulp van de SDK het model, de omgeving en het invoer script voor een webservice bijwerkt:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**De CLI gebruiken**

U kunt ook een webservice bijwerken met behulp van de versie van ML CLI. In het volgende voor beeld ziet u hoe u een nieuw model registreert en een webservice bijwerkt om het nieuwe model te gebruiken:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In dit voor beeld wordt een JSON-document gebruikt om de model gegevens van de registratie opdracht door te geven aan de opdracht update.
>
> Als u de service wilt bijwerken om een nieuw invoer script of-omgeving te gebruiken, maakt u een Afleidings [configuratie bestand](./reference-azure-machine-learning-cli.md#inference-configuration-schema) en geeft u het op met de `ic` para meter.

Zie de documentatie van [AZ ml service update](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-update) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)
