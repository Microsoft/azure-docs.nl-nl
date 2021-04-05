---
title: GitHub-acties voor CI/CD
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een GitHub-actie werk stroom voor het trainen van een model op Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102218278"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>GitHub-acties gebruiken met Azure Machine Learning

Aan de slag met [github-acties](https://docs.github.com/en/actions) voor het trainen van een model op Azure machine learning. 

> [!NOTE]
> GitHub-acties voor Azure Machine Learning worden meegeleverd als-is en worden niet volledig ondersteund door micro soft. Als u problemen ondervindt met een specifieke actie, opent u een probleem in de opslag plaats voor de actie. Als u bijvoorbeeld een probleem ondervindt met de actie AML-Deploy, meldt u het probleem in de [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) opslag plaats.

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft vier secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Verbinding maken** | 1. Maak verbinding met de machine learning-werk ruimte. <br /> 2. Maak verbinding met een compute-doel. |
|**Uitvoeren** | 1. Voer een training uit. |
|**Implementeren** | 1. Registreer model in Azure Machine Learning REGI ster. 1. Het model implementeren. |

## <a name="create-repository"></a>Opslag plaats maken

Maak een nieuwe opslag plaats [met behulp van github-acties en Azure machine learning sjabloon](https://github.com/machine-learning-apps/ml-template-azure). 

1. Open de [sjabloon](https://github.com/machine-learning-apps/ml-template-azure) op github. 
2. Selecteer **deze sjabloon gebruiken**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selecteer deze sjabloon gebruiken":::
3. Maak een nieuwe opslag plaats op basis van de sjabloon. Stel de naam van de opslag plaats in op `ml-learning` of een naam van uw keuze. 


## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

In het bovenstaande voor beeld vervangt u de tijdelijke aanduidingen door de abonnements-ID, naam van de resource groep en de naam van de app. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

1. In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

2. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Verbinding maken met de werk ruimte

Gebruik de [actie Azure machine learning-werkruimte](https://github.com/marketplace/actions/azure-machine-learning-workspace) om verbinding te maken met uw Azure machine learning-werk ruimte. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

De actie verwacht een `workspace.json` bestand standaard. Als het JSON-bestand een andere naam heeft, kunt u dit opgeven met de `parameters_file` invoer parameter. Als er geen bestand is, wordt er een nieuwe gemaakt met de naam van de opslag plaats.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Met deze actie worden de eigenschappen van de werkruimte Azure Resource Manager (ARM) geschreven naar een configuratie bestand, dat door alle toekomstige Azure Machine Learning GitHub-acties wordt opgenomen. Het bestand wordt opgeslagen in `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Verbinding maken met een compute-doel in Azure Machine Learning

Gebruik de [Azure machine learning reken actie](https://github.com/Azure/aml-compute) om verbinding te maken met een compute-doel in azure machine learning.  Als het berekenings doel bestaat, wordt er met de actie verbinding mee gemaakt. Anders wordt er een nieuw Compute-doel gemaakt. De [AML-reken actie](https://github.com/Azure/aml-compute) ondersteunt alleen het Azure ml Compute-Cluster en de Azure Kubernetes-service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Trainingsuitvoering verzenden

Gebruik de [Azure machine learning trainings actie](https://github.com/Azure/aml-run) om een ScriptRun, een Estimator of een pijp lijn, naar Azure machine learning te verzenden. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Model registreren in het REGI ster

Gebruik de [Azure machine learning model actie registreren](https://github.com/Azure/aml-registermodel) om een model te registreren bij Azure machine learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Model implementeren naar Azure Machine Learning ACI

Gebruik de [actie Azure machine learning implementeren](https://github.com/Azure/aml-deploy) om een model te implementeren en een eind punt voor het model te maken. U kunt ook de Azure Machine Learning implementeren gebruiken om te implementeren naar Azure Kubernetes service. Bekijk [deze voorbeeld werk stroom](https://github.com/Azure-Samples/mlops-enterprise-template) voor een model dat wordt geïmplementeerd in azure Kubernetes service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Voor beeld volt ooien

Train uw model en implementeer het op Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Resources opschonen

Als uw resource groep en opslag plaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resource groep en de GitHub-opslag plaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)