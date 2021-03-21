---
title: MLflow-modellen implementeren als webservices
titleSuffix: Azure Machine Learning
description: Stel MLflow in met Azure Machine Learning om uw ML-modellen te implementeren als een Azure-webservice.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c45b819f9fc02fae40c2bf7fc5c2247c8c0a6147
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517477"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>MLflow-modellen implementeren als Azure-webservices (preview)

In dit artikel vindt u informatie over het implementeren van uw [MLflow](https://www.mlflow.org) -model als een Azure-webservice, zodat u de model beheer-en gegevensopslag mogelijkheden van Azure machine learning kunt gebruiken en Toep assen op uw productie modellen.

Azure Machine Learning biedt implementatie configuraties voor:
* Azure container instance (ACI) is een geschikte keuze voor een snelle ontwikkel-en test implementatie.
* Azure Kubernetes service (AKS), die wordt aanbevolen voor schaal bare productie-implementaties.
> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die hun MLflow-model willen implementeren op een Azure Machine Learning-webservice-eind punt. Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide implementaties van modellen.
## <a name="mlflow-with-azure-machine-learning-deployment"></a>MLflow met Azure Machine Learning-implementatie

MLflow is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. Dankzij de integratie met Azure Machine Learning kunt u dit beheer uitbreiden buiten de model training naar de implementatie fase van uw productie model.

In het volgende diagram ziet u dat u met de MLflow implementation API en Azure Machine Learning, modellen kunt implementeren die zijn gemaakt met populaire frameworks, zoals PyTorch, tensor flow, scikit-learn, enzovoort, als Azure-webservices en deze beheren in uw werk ruimte. 

![ mlflow-modellen implementeren met Azure machine learning](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> MLflow is een open-source bibliotheek die regel matig wordt gewijzigd. Als zodanig moeten de functies die beschikbaar zijn via de Azure Machine Learning-en MLflow-integratie, worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.

## <a name="prerequisites"></a>Vereisten

* Een machine learning model. Als u geen getraind model hebt, kunt u het voor beeld van een notitie blok vinden dat het beste past bij uw reken scenario in [deze opslag plaats](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) en de instructies volgen. 
* [Stel de tracerings-URI voor MLflow in om verbinding te maken Azure machine learning](how-to-use-mlflow.md#track-local-runs).
* Installeer het `azureml-mlflow`-pakket. 
    * Dit pakket maakt automatisch deel uit `azureml-core` van de [Azure machine learning PYTHON-SDK](/python/api/overview/azure/ml/install), waarmee de connectiviteit voor MLflow wordt geboden om toegang te krijgen tot uw werk ruimte.
* Bekijk welke [toegangs machtigingen u nodig hebt om uw MLflow-bewerkingen uit te voeren met uw werk ruimte](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-azure-container-instance-aci"></a>Implementeren naar Azure container instance (ACI)

Als u uw MLflow-model wilt implementeren in een Azure Machine Learning-webservice, moet uw model zijn ingesteld met de [tracerings-URI van MLflow om verbinding te maken met Azure machine learning](how-to-use-mlflow.md). 

Stel uw implementatie configuratie in met de methode [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . U kunt ook Tags en beschrijvingen toevoegen om uw web-service bij te houden.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Registreer en implementeer vervolgens het model in één stap met de [implementatie](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) methode van MLflow voor Azure machine learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>Implementeren naar Azure Kubernetes service (AKS)

Als u uw MLflow-model wilt implementeren in een Azure Machine Learning-webservice, moet uw model zijn ingesteld met de [tracerings-URI van MLflow om verbinding te maken met Azure machine learning](how-to-use-mlflow.md). 

Als u wilt implementeren op AKS, moet u eerst een AKS-cluster maken. Maak een AKS-cluster met behulp van de methode [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-) . Het kan 20-25 minuten duren om een nieuw cluster te maken.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Stel uw implementatie configuratie in met de methode [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . U kunt ook Tags en beschrijvingen toevoegen om uw web-service bij te houden.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Registreer en implementeer vervolgens het model in één stap met de [implementatie](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) methode van MLflow voor Azure machine learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

De implementatie van de service kan enkele minuten duren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om uw geïmplementeerde webservice te gebruiken, kunt u `service.delete()` deze gebruiken om de service te verwijderen uit uw notitie blok.  Zie de documentatie voor [webservice. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--)voor meer informatie.

## <a name="example-notebooks"></a>Voorbeeldnotebooks

De [MLflow met Azure machine learning-notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) tonen en uitvouwen op concepten die in dit artikel worden gepresenteerd.

> [!NOTE]
> Een door de Community aangedreven opslag plaats van voor beelden met behulp van mlflow vindt u op https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Volgende stappen

* [Uw modellen beheren](concept-model-management-and-deployment.md).
* Bewaak uw productie modellen voor [gegevens drift](./how-to-enable-data-collection.md).
* [Volg Azure Databricks uitvoeringen met MLflow](how-to-use-mlflow-azure-databricks.md).

