---
title: Modellen implementeren in Azure Container Instances
titleSuffix: Azure Machine Learning
description: Leer hoe u uw Azure Machine Learning als webservice implementeert met behulp van Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 1eaf58f4f951547e6e4e461803e79844f99e630a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501736"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Een model implementeren op Azure Container Instances

Meer informatie over het gebruik Azure Machine Learning om een model te implementeren als een webservice op Azure Container Instances (ACI). Gebruik Azure Container Instances als aan een van de volgende voorwaarden wordt voldaan:

- U moet uw model snel implementeren en valideren. U hoeft ACI-containers niet van tevoren te maken. Ze worden gemaakt als onderdeel van het implementatieproces.
- U test een model dat in ontwikkeling is. 

Zie het artikel Quota and region [availability](../container-instances/container-instances-quotas.md) for Azure Container Instances (Quota- en regiobeschikbaarheid voor ACI) voor meer informatie Azure Container Instances beschikbaarheid in regio's.

> [!IMPORTANT]
> Het wordt ten zeerste aangeraden om lokaal fouten op te sporen voordat u implementeert in de [webservice.](./how-to-troubleshoot-deployment-local.md) Zie Lokaal fouten opsporen voor meer informatie
>
> U kunt ook verwijzen naar Azure Machine Learning: [Deploy to Local Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) (Implementeren naar lokale notebook)

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

- Een machine learning dat is geregistreerd in uw werkruimte. Zie Modellen implementeren als u geen geregistreerd model [hebt.](how-to-deploy-and-where.md)

- De [Azure CLI-extensie voor Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)of de [Azure Machine Learning Visual Studio Code-extensie](tutorial-setup-vscode-extension.md).

- In de Python-codefragmenten in dit artikel wordt ervan uitgenomen dat de volgende variabelen zijn ingesteld: 

    * `ws` - Stel in op uw werkruimte.
    * `model` - Stel in op uw geregistreerde model.
    * `inference_config` - Stel in op de deferentieconfiguratie voor het model.

    Zie Modellen implementeren voor meer informatie over het instellen [van deze variabelen.](how-to-deploy-and-where.md)

- In __de CLI-fragmenten__ in dit artikel wordt ervan uitgenomen dat u een document hebt `inferenceconfig.json` gemaakt. Zie How and where to deploy models (Modellen implementeren) voor meer informatie [over het maken van dit document.](how-to-deploy-and-where.md)

## <a name="limitations"></a>Beperkingen

* Wanneer u Azure Container Instances in een virtueel netwerk gebruikt, moet het virtuele netwerk zich in dezelfde resourcegroep als uw Azure Machine Learning werkruimte.
* Wanneer u Azure Container Instances in het virtuele netwerk gebruikt, kan de Azure Container Registry (ACR) voor uw werkruimte zich niet ook in het virtuele netwerk.

Zie De [deferencing beveiligen](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)met virtuele netwerken voor meer informatie.

## <a name="deploy-to-aci"></a>Implementeren naar ACI

Als u een model wilt implementeren Azure Container Instances, maakt u een __implementatieconfiguratie__ die de benodigde rekenbronnen beschrijft. Bijvoorbeeld het aantal kernen en het geheugen. U hebt ook een __deferentieconfiguratie nodig,__ die de omgeving beschrijft die nodig is om het model en de webservice te hosten. Zie How and where to deploy models (Modellen implementeren) voor meer informatie over het maken van de [deference-configuratie.](how-to-deploy-and-where.md)

> [!NOTE]
> * ACI is alleen geschikt voor kleine modellen van minder dan 1 GB. 
> * We raden u aan AKS met één knooppunt te gebruiken om grotere modellen te ontwikkelen en te testen.
> * Het aantal modellen dat moet worden geïmplementeerd, is beperkt tot 1000 modellen per implementatie (per container). 

### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Gebruik de volgende opdracht om te implementeren met behulp van de CLI. Vervang `mymodel:1` door de naam en versie van het geregistreerde model. Vervang `myservice` door de naam om deze service te geven:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Zie de naslaginformatie over [az ml model deploy voor meer](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) informatie. 

## <a name="using-vs-code"></a>VS Code gebruiken

Zie [Uw modellen implementeren met VS Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> U hoeft geen ACI-container te maken om van tevoren te testen. ACI-containers worden naar behoefte gemaakt.

> [!IMPORTANT]
> We hebben de id van de gehashte werkruimte aan alle onderliggende ACI-resources die worden gemaakt, alle ACI-namen uit dezelfde werkruimte hebben hetzelfde achtervoegsel. De Azure Machine Learning Service is nog steeds dezelfde klant die 'service_name' heeft opgegeven en alle gebruikers met Azure Machine Learning SDK-API's hoeven niets te wijzigen. We geven geen garanties voor de namen van de onderliggende resources die worden gemaakt.

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met de implementatie oplossen](how-to-troubleshoot-deployment.md)
* [De webservice bijwerken](how-to-deploy-update-web-service.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)