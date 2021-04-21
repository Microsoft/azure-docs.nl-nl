---
title: Azure-rollen toewijzen met behulp Azure Resource Manager-sjablonen - Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure Resource Manager-sjablonen en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771871"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure-rollen toewijzen met behulp Azure Resource Manager sjablonen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]Naast het gebruik van Azure PowerShell of de Azure CLI kunt u rollen toewijzen met behulp [van Azure Resource Manager sjablonen.](../azure-resource-manager/templates/template-syntax.md) Sjablonen kunnen handig zijn als u resources consistent en herhaaldelijk wilt implementeren. In dit artikel wordt beschreven hoe u rollen toewijst met behulp van sjablonen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Object-ID's op halen

Als u een rol wilt toewijzen, moet u de id opgeven van de gebruiker, groep of toepassing aan wie u de rol wilt toewijzen. De id heeft de volgende indeling: `11111111-1111-1111-1111-111111111111` . U kunt de id op de Azure Portal, Azure PowerShell of Azure CLI.

### <a name="user"></a>Gebruiker

U kunt de opdrachten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) of az [ad user show](/cli/azure/ad/user#az_ad_user_show) gebruiken om de id van een gebruiker op te halen.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Groep

U kunt de opdrachten [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) of az [ad group show](/cli/azure/ad/group#az_ad_group_show) gebruiken om de id van een groep op te halen.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Beheerde identiteiten

Als u de id van een beheerde identiteit wilt weten, kunt u de opdrachten [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) of [az ad sp](/cli/azure/ad/sp) gebruiken.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Toepassing

U kunt de opdrachten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) of az ad sp list gebruiken om de id van een service-principal (identiteit die wordt gebruikt door een [toepassing) op te](/cli/azure/ad/sp#az_ad_sp_list) halen. Gebruik voor een service-principal de object-id en **niet de** toepassings-id.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Een Azure-rol toewijzen

In Azure RBAC wijst u een rol toe om toegang te verlenen.

### <a name="resource-group-scope-without-parameters"></a>Bereik van resourcegroep (zonder parameters)

De volgende sjabloon toont een eenvoudige manier om een rol toe te wijzen. Sommige waarden worden opgegeven in de sjabloon. In de volgende sjabloon wordt het volgende gedemonstreerd:

-  De rol Lezer [toewijzen aan](built-in-roles.md#reader) een gebruiker, groep of toepassing in een resourcegroepbereik

Als u de sjabloon wilt gebruiken, moet u het volgende doen:

- Een nieuw JSON-bestand maken en de sjabloon kopiëren
- Vervang `<your-principal-id>` door de id van een gebruiker, groep, beheerde identiteit of toepassing om de rol aan toe te wijzen

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Hier volgen een voorbeeld [van opdrachten voor New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) voor het starten van de implementatie in een resourcegroep met de naam ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Hieronder ziet u een voorbeeld van de roltoewijzing Lezer aan een gebruiker voor een resourcegroep na de implementatie van de sjabloon.

![Roltoewijzing bij het bereik van de resourcegroep](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Resourcegroep of abonnementsbereik

De vorige sjabloon is niet erg flexibel. De volgende sjabloon maakt gebruik van parameters en kan worden gebruikt op verschillende scopes. In de volgende sjabloon wordt het volgende gedemonstreerd:

- Een rol toewijzen aan een gebruiker, groep of toepassing in een resourcegroep of abonnementsbereik
- De rollen Eigenaar, Inzender en Lezer opgeven als parameter

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De id van een gebruiker, groep, beheerde identiteit of toepassing waar de rol aan moet worden toegewezen
- Een unieke id die wordt gebruikt voor de roltoewijzing of u kunt de standaard-id gebruiken

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Deze sjabloon is niet idempotent, tenzij dezelfde waarde wordt opgegeven als `roleNameGuid` een parameter voor elke implementatie van de sjabloon. Als er geen is opgegeven, wordt standaard een nieuwe GUID gegenereerd voor elke implementatie en mislukken volgende `roleNameGuid` implementaties met een `Conflict: RoleAssignmentExists` fout.

Het bereik van de roltoewijzing wordt bepaald op basis van het niveau van de implementatie. Hier volgen een voorbeeld van de opdrachten [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) voor het starten van de implementatie in het bereik van een resourcegroep.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Hier volgen een voorbeeld van de opdrachten [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) en [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) voor het starten van de implementatie in een abonnementsbereik en het opgeven van de locatie.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Resourcebereik

Als u een rol moet toewijzen op het niveau van een resource, stelt u de eigenschap voor de roltoewijzing in `scope` op de naam van de resource.

In de volgende sjabloon wordt het volgende gedemonstreerd:

- Een nieuw opslagaccount maken
- Een rol toewijzen aan een gebruiker, groep of toepassing in het bereik van het opslagaccount
- De rollen Eigenaar, Inzender en Lezer opgeven als parameter

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De id van een gebruiker, groep, beheerde identiteit of toepassing om de rol aan toe te wijzen

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Als u de vorige sjabloon wilt implementeren, gebruikt u de resourcegroepopdrachten. Hier volgen een voorbeeld van de opdrachten [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) voor het starten van de implementatie in een resourcebereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Hieronder ziet u een voorbeeld van de roltoewijzing Inzender aan een gebruiker voor een opslagaccount nadat de sjabloon is geïmplementeerd.

![Roltoewijzing op resourcebereik](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nieuwe service-principal

Als u een nieuwe service-principal maakt en onmiddellijk een rol probeert toe te wijzen aan die service-principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een nieuwe beheerde identiteit maakt en vervolgens probeert een rol toe te wijzen aan die service-principal in dezelfde Azure Resource Manager-sjabloon, kan de roltoewijzing mislukken. De reden voor deze fout is waarschijnlijk een replicatievertraging. De service-principal wordt in één regio gemaakt; De roltoewijzing kan echter plaatsvinden in een andere regio die de service-principal nog niet heeft gerepliceerd.

Als u dit scenario wilt aanpakken, moet u de eigenschap `principalType` instellen op bij het maken van de `ServicePrincipal` roltoewijzing. U moet ook de `apiVersion` van de roltoewijzing instellen op `2018-09-01-preview` of hoger.

In de volgende sjabloon wordt het volgende gedemonstreerd:

- Een nieuwe service-principal voor beheerde identiteiten maken
- Het opgeven van de `principalType`
- De rol Inzender toewijzen aan die service-principal in het bereik van een resourcegroep

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De basisnaam van de beheerde identiteit of u kunt de standaardreeks gebruiken

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Hier volgen een voorbeeld van de opdrachten [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) voor het starten van de implementatie op een resourcegroepbereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Hieronder ziet u een voorbeeld van de roltoewijzing Inzender voor een nieuwe service-principal voor beheerde identiteit na de implementatie van de sjabloon.

![Roltoewijzing voor een nieuwe service-principal voor beheerde identiteiten](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: ARM-sjablonen maken en implementeren met behulp van Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Informatie over de structuur en de syntaxis van ARM-sjablonen](../azure-resource-manager/templates/template-syntax.md)
- [Resourcegroepen en resources maken op abonnementsniveau](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?term=rbac)
