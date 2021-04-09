---
title: 'ML Studio (klassiek): werk ruimten implementeren met Azure Resource Manager-Azure'
description: Een werk ruimte voor Azure Machine Learning Studio (klassiek) implementeren met behulp van Azure Resource Manager sjabloon
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520031"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Machine Learning Studio-werk ruimte (klassieke) implementeren met behulp van Azure Resource Manager

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Met een Azure Resource Manager-implementatie sjabloon bespaart u tijd door u een schaal bare manier te bieden voor het implementeren van onderling verbonden onderdelen met een validatie en een mechanisme voor opnieuw proberen. Als u bijvoorbeeld Azure Machine Learning Studio (klassieke) werk ruimten wilt instellen, moet u eerst een Azure-opslag account configureren en vervolgens uw werk ruimte implementeren. Stel dit hand matig in voor honderden werk ruimten. Een eenvoudiger alternatief is het gebruik van een Azure Resource Manager sjabloon voor het implementeren van een studio-werk ruimte (klassiek) en alle bijbehorende afhankelijkheden. Dit artikel begeleidt u stapsgewijs door de stapsgewijze procedure. Zie [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)voor een geweldig overzicht van Azure Resource Manager.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Stap voor stap: een Machine Learning-werkruimte maken
We gaan een Azure-resource groep maken en vervolgens een nieuw Azure-opslag account en een nieuwe Azure Machine Learning Studio (klassieke) werk ruimte implementeren met behulp van een resource manager-sjabloon. Zodra de implementatie is voltooid, worden belang rijke gegevens afgedrukt over de werk ruimten die zijn gemaakt (de primaire sleutel, de workspaceID en de URL naar de werk ruimte).

### <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon maken

Een Machine Learning-werkruimte vereist een Azure Storage-account om de gekoppelde gegevensset op te slaan.
De volgende sjabloon maakt gebruik van de naam van de resource groep om de naam van het opslag account en de naam van de werk ruimte te genereren.  De naam van het opslag account wordt ook gebruikt als eigenschap bij het maken van de werk ruimte.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Sla deze sjabloon op als mlworkspace.jsvoor het bestand onder c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementeer de resource groep op basis van de sjabloon

* Open PowerShell.
* Modules voor Azure Resource Manager en Azure Service Management installeren

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Met deze stappen downloadt en installeert u de benodigde modules om de resterende stappen uit te voeren. Dit hoeft alleen te worden gedaan in de omgeving waarin u de Power shell-opdrachten uitvoert.

* Verifiëren bij Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Deze stap moet voor elke sessie worden herhaald. Nadat de gegevens zijn geverifieerd, moeten uw abonnements informatie worden weer gegeven.

![Azure-account](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu u toegang hebt tot Azure, kunnen we de resource groep maken.

* Een resourcegroep maken

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Controleer of de resource groep juist is ingericht. **ProvisioningState** moet "geslaagd" zijn.
De naam van de resource groep wordt door de sjabloon gebruikt voor het genereren van de naam van het opslag account. De naam van het opslag account moet tussen 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.

![Resourcegroep](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Implementeer een nieuwe Machine Learning-werkruimte met behulp van de implementatie van de resource groep.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Zodra de implementatie is voltooid, hebt u eenvoudig toegang tot de eigenschappen van de werk ruimte die u hebt geïmplementeerd. U kunt bijvoorbeeld toegang krijgen tot het primaire-sleutel token.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Een andere manier om tokens van een bestaande werk ruimte op te halen, is met behulp van de Invoke-AzResourceAction opdracht. U kunt bijvoorbeeld de primaire en secundaire tokens van alle werk ruimten weer geven.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Nadat de werk ruimte is ingericht, kunt u ook veel Azure Machine Learning Studio (klassieke) taken automatiseren met behulp [van de Power shell-module voor Azure machine learning Studio (klassiek)](https://aka.ms/amlps).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md).
* Bekijk de [opslag plaats voor Azure Quick](https://github.com/Azure/azure-quickstart-templates)start-sjablonen.
* Bekijk deze video over [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zie de [Naslag informatie voor de Resource Manager-sjabloon](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->