---
title: GitHub-acties voor CI/CD
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een GitHub Actions voor het trainen van een model op Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780367"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Gebruik GitHub Actions met Azure Machine Learning

Ga aan de [slag GitHub Actions](https://docs.github.com/en/actions) een model te trainen op Azure Machine Learning. 

> [!NOTE]
> GitHub Actions voor Azure Machine Learning worden geleverd zoals ze zijn en worden niet volledig ondersteund door Microsoft. Als u problemen ondervindt met een specifieke actie, opent u een probleem in de opslagplaats voor de actie. Als u bijvoorbeeld een probleem ondervindt met de actie aml-deploy, meldt u het probleem in [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) de repo.

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft vier secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Verbinding maken** | 1. Maak verbinding met machine learning werkruimte. <br /> 2. Verbinding maken met een rekendoel. |
|**Uitvoeren** | 1. Dien een trainingsrun in. |
|**Implementeren** | 1. Het model registreren in Azure Machine Learning register. 1. Het model implementeren. |

## <a name="create-repository"></a>Opslagplaats maken

Maak een nieuwe opslagplaats op basis van [ml ops met GitHub Actions en Azure Machine Learning sjabloon](https://github.com/machine-learning-apps/ml-template-azure). 

1. Open de [sjabloon](https://github.com/machine-learning-apps/ml-template-azure) op GitHub. 
2. Selecteer **Deze sjabloon gebruiken.** 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selecteer Deze sjabloon gebruiken":::
3. Maak een nieuwe opslagplaats op basis van de sjabloon. Stel de naam van de opslagplaats `ml-learning` in op of een naam naar keuze. 


## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Vervang in het bovenstaande voorbeeld de tijdelijke aanduidingen door uw abonnements-id, resourcegroepnaam en app-naam. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik.

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

1. Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

2. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Verbinding maken met de werkruimte

Gebruik de [Azure Machine Learning-werkruimte om verbinding](https://github.com/marketplace/actions/azure-machine-learning-workspace) te maken met uw Azure Machine Learning werkruimte. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

De actie verwacht standaard een `workspace.json` bestand. Als uw JSON-bestand een andere naam heeft, kunt u dit opgeven met de `parameters_file` invoerparameter. Als er geen bestand is, wordt er een nieuw bestand gemaakt met de naam van de opslagplaats.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Met de actie worden de eigenschappen van Azure Resource Manager werkruimten (ARM) naar een configuratiebestand weggeslagen, dat door alle toekomstige Azure Machine Learning GitHub Actions. Het bestand wordt opgeslagen in `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Verbinding maken met een rekendoel in Azure Machine Learning

Gebruik de [Azure Machine Learning Compute-actie om](https://github.com/Azure/aml-compute) verbinding te maken met een rekendoel in Azure Machine Learning.  Als het rekendoel bestaat, maakt de actie er verbinding mee. Anders maakt de actie een nieuw rekendoel. De [AML Compute-actie](https://github.com/Azure/aml-compute) ondersteunt alleen het Azure ML-rekencluster en Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Trainingsuitvoering verzenden

Gebruik de [Azure Machine Learning Training-actie om](https://github.com/Azure/aml-run) een ScriptRun, een estimator of een pijplijn te verzenden om een Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Model registreren in register

Gebruik de [actie Azure Machine Learning Model registreren om](https://github.com/Azure/aml-registermodel) een model te registreren voor Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Model implementeren om Azure Machine Learning ACI te implementeren

Gebruik de [actie Azure Machine Learning implementeren om](https://github.com/Azure/aml-deploy) een model te implementeren en een eindpunt voor het model te maken. U kunt ook de implementatie Azure Machine Learning implementeren naar Azure Kubernetes Service. Zie [deze voorbeeldwerkstroom](https://github.com/Azure-Samples/mlops-enterprise-template) voor een model dat wordt geïmplementeerd in Azure Kubernetes Service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Volledig voorbeeld

Uw model trainen en implementeren naar Azure Machine Learning. 

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

Wanneer uw resourcegroep en opslagplaats niet meer nodig zijn, schoont u de resources op die u hebt geïmplementeerd door de resourcegroep en uw GitHub-opslagplaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Pijplijnen maken machine learning uitvoeren met Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)