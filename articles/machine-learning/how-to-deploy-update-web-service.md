---
title: Webservices bijwerken
titleSuffix: Azure Machine Learning
description: Meer informatie over het vernieuwen van een webservice die al is geïmplementeerd in Azure Machine Learning. U kunt instellingen zoals model, omgeving en invoerscript bijwerken.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 5a586d29fd25ee7332f11737345aef8209de8824
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889336"
---
# <a name="update-a-deployed-web-service"></a>Een geïmplementeerde webservice bijwerken

In dit artikel leert u hoe u een webservice bij kunt werken die is geïmplementeerd met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgenomen dat u al een webservice hebt geïmplementeerd met Azure Machine Learning. Als u wilt leren hoe u een webservice implementeert, [volgt u deze stappen.](how-to-deploy-and-where.md)

## <a name="update-web-service"></a>Webservice bijwerken

Als u een webservice wilt bijwerken, gebruikt u de `update` methode . U kunt de webservice bijwerken voor het gebruik van een nieuw model, een nieuw invoerscript of nieuwe afhankelijkheden die kunnen worden opgegeven in een deference-configuratie. Zie de documentatie voor [Webservice.update voor meer informatie.](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-)

Zie [Updatemethode voor AKS-service.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Zie [Updatemethode voor ACI-service.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Wanneer u een nieuwe versie van een model maakt, moet u elke service die u wilt gebruiken handmatig bijwerken.
>
> U kunt de SDK niet gebruiken om een webservice bij te werken die is gepubliceerd vanuit de Azure Machine Learning designer.

**De SDK gebruiken**

De volgende code laat zien hoe u de SDK gebruikt om het model, de omgeving en het invoerscript voor een webservice bij te werken:

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

U kunt een webservice ook bijwerken met behulp van de ML CLI. In het volgende voorbeeld wordt gedemonstreerd hoe u een nieuw model registreert en vervolgens een webservice bij te werken om het nieuwe model te gebruiken:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In dit voorbeeld wordt een JSON-document gebruikt om de modelgegevens van de registratieopdracht door te geven aan de updateopdracht.
>
> Als u de service wilt bijwerken voor het gebruik van een nieuw invoerscript of een nieuwe omgeving, maakt u een [deferentieconfiguratiebestand](./reference-azure-machine-learning-cli.md#inference-configuration-schema) en geeft u dit op met de `ic` parameter .

Zie de documentatie over [az ml service update voor meer](/cli/azure/ml/service#az_ml_service_update) informatie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Clienttoepassingen maken om webservices te gebruiken](how-to-consume-web-service.md)
* [Een model implementeren met behulp van een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurteniswaarschuwingen en triggers maken voor modelimplementaties](how-to-use-event-grid.md)
