---
title: Resources implementeren in resource groepen
description: Hierin wordt beschreven hoe u resources in een Azure Resource Manager sjabloon implementeert. U ziet hoe u meer dan één resource groep kunt bereiken.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 1d636be9ffab5a4398e3e12867e601ce6df382bf
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889787"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Implementaties van resource groepen met ARM-sjablonen

In dit artikel wordt beschreven hoe u uw implementatie kunt beperken tot een resource groep. U gebruikt een Azure Resource Manager sjabloon (ARM-sjabloon) voor de implementatie. In dit artikel ziet u ook hoe u het bereik uitbreidt buiten de resource groep in de implementatie bewerking.

## <a name="supported-resources"></a>Ondersteunde resources

De meeste resources kunnen worden geïmplementeerd in een resource groep. Zie [arm-sjabloon referentie](/azure/templates)voor een lijst met beschik bare resources.

## <a name="schema"></a>Schema

Gebruik voor sjablonen het volgende schema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Gebruik voor parameter bestanden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Implementatie opdrachten

Als u wilt implementeren in een resource groep, gebruikt u de implementatie opdrachten voor de resource groep.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create)voor Azure cli. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor de Power shell-implementatie opdracht [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Zie voor meer gedetailleerde informatie over implementatie opdrachten en opties voor het implementeren van ARM-sjablonen:

* [Resources implementeren met ARM-sjablonen en Azure Portal](deploy-portal.md)
* [Resources implementeren met ARM-sjablonen en Azure CLI](deploy-cli.md)
* [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)
* [Resources implementeren met ARM-sjablonen en Azure Resource Manager REST API](deploy-rest.md)
* [Een implementatie knop gebruiken voor het implementeren van sjablonen uit de GitHub-opslag plaats](deploy-to-azure-button.md)
* [ARM-sjablonen implementeren vanuit Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Implementatie bereiken

Wanneer u naar een resource groep implementeert, kunt u resources implementeren voor het volgende:

* de doel resource groep van de bewerking
* andere resource groepen in hetzelfde abonnement of andere abonnementen
* elk abonnement in de Tenant
* de Tenant voor de resource groep

Een [extensie resource](scope-extension-resources.md) kan worden ingedeeld naar een doel dat verschilt van het implementatie doel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie wordt beschreven hoe u verschillende bereiken kunt opgeven. U kunt deze verschillende bereiken combi neren in één sjabloon.

### <a name="scope-to-target-resource-group"></a>Bereik naar doel resource groep

Als u resources wilt implementeren voor de doel resource, voegt u deze resources toe aan de sectie resources van de sjabloon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Zie voor een voorbeeld sjabloon [implementeren naar doel resource groep](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Bereik tot resource groep in hetzelfde abonnement

Als u resources wilt implementeren in een andere resource groep in hetzelfde abonnement, voegt u een geneste implementatie toe en neemt u de `resourceGroup` eigenschap op. Als u de abonnements-ID of de resource groep niet opgeeft, worden het abonnement en de resource groep van de bovenliggende sjabloon gebruikt. Alle resource groepen moeten bestaan voordat u de implementatie uitvoert.

In het volgende voor beeld streeft de geneste implementatie naar een resource groep met de naam `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Zie voor een voorbeeld sjabloon [implementeren naar meerdere resource groepen](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Het bereik van de resource groep in een ander abonnement

Als u resources wilt implementeren voor een resource groep in een ander abonnement, voegt u een geneste implementatie toe en neemt u de `subscriptionId` Eigenschappen en op `resourceGroup` . In het volgende voor beeld streeft de geneste implementatie naar een resource groep met de naam `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Zie voor een voorbeeld sjabloon [implementeren naar meerdere resource groepen](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Bereik voor abonnement

Als u resources wilt implementeren in een abonnement, voegt u een geneste implementatie toe en neemt u de `subscriptionId` eigenschap op. Het abonnement kan het abonnement zijn voor de doel resource groep of een ander abonnement in de Tenant. Stel ook de `location` eigenschap voor de geneste implementatie in.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Zie [resource groep maken](#create-resource-group)voor een voorbeeld sjabloon.

### <a name="scope-to-tenant"></a>Bereik naar Tenant

Stel de in op om resources te maken in de Tenant `scope` `/` . De gebruiker die de sjabloon implementeert, moet de [vereiste toegang hebben om te implementeren op de Tenant](deploy-to-tenant.md#required-access).

Als u een geneste implementatie wilt gebruiken, stelt u `scope` en in `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Of u kunt het bereik instellen op `/` voor sommige resource typen, zoals beheer groepen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Zie [beheer groep](deploy-to-management-group.md#management-group)voor meer informatie.

## <a name="deploy-to-target-resource-group"></a>Implementeren naar doel resource groep

Als u resources wilt implementeren in de doel resource groep, definieert u deze resources in de `resources` sectie van de sjabloon. Met de volgende sjabloon maakt u een opslag account in de resource groep die is opgegeven in de implementatie bewerking.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Implementeren in meerdere resource groepen

U kunt in één ARM-sjabloon implementeren voor meer dan één resource groep. Gebruik een [geneste of gekoppelde sjabloon](linked-templates.md)om een resource groep te richten die afwijkt van die van de bovenliggende sjabloon. Geef binnen het type implementatie resource waarden op voor de abonnements-ID en de resource groep waarop u wilt dat de geneste sjabloon wordt geïmplementeerd. De resource groepen kunnen bestaan in verschillende abonnementen.

> [!NOTE]
> U kunt implementeren naar **800-resource groepen** in één implementatie. Deze beperking betekent meestal dat u kunt implementeren in één resource groep die is opgegeven voor de bovenliggende sjabloon en Maxi maal 799 resource groepen in geneste of gekoppelde implementaties. Als uw bovenliggende sjabloon alleen geneste of gekoppelde sjablonen bevat en zelf geen resources implementeert, kunt u Maxi maal 800 resource groepen toevoegen in geneste of gekoppelde implementaties.

In het volgende voor beeld worden twee opslag accounts geïmplementeerd. Het eerste opslag account wordt geïmplementeerd in de resource groep die is opgegeven in de implementatie bewerking. Het tweede opslag account wordt geïmplementeerd naar de resource groep die is opgegeven in de `secondResourceGroup` `secondSubscriptionID` para meters en:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Als u `resourceGroup` de naam instelt van een resource groep die niet bestaat, mislukt de implementatie.

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Een resourcegroep maken

Vanuit een implementatie van een resource groep kunt u overschakelen naar het niveau van een abonnement en een resource groep maken. Met de volgende sjabloon wordt een opslag account geïmplementeerd naar de doel resource groep en wordt een nieuwe resource groep gemaakt in het opgegeven abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
