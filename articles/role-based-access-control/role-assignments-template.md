---
title: Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager sjablonen-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure Resource Manager sjablonen en Azure RBAC (op rollen gebaseerd toegangs beheer).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc8a704fd864fbb0e11da6cd062e0c5325679d8b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964220"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager sjablonen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Naast het gebruik van Azure PowerShell of de Azure CLI kunt u rollen toewijzen met behulp van [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md). Sjablonen kunnen nuttig zijn als u resources consistent en herhaaldelijk wilt implementeren. In dit artikel wordt beschreven hoe u rollen toewijst met behulp van sjablonen.

## <a name="get-object-ids"></a>Object-Id's ophalen

Als u een rol wilt toewijzen, moet u de ID opgeven van de gebruiker, groep of toepassing waaraan u de rol wilt toewijzen. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111` . U kunt de ID ophalen met behulp van de Azure Portal, Azure PowerShell of Azure CLI.

### <a name="user"></a>Gebruiker

Als u de ID van een gebruiker wilt ophalen, kunt u de opdrachten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) of [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) gebruiken.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Groep

Als u de ID van een groep wilt ophalen, kunt u de opdrachten [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) of [AZ Ad Group show](/cli/azure/ad/group#az-ad-group-show) gebruiken.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Beheerde identiteiten

U kunt de ID van een beheerde identiteit ophalen met behulp van [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) of [AZ AD SP](/cli/azure/ad/sp) -opdrachten.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Toepassing

Als u de ID wilt ophalen van een service-principal (identiteit die wordt gebruikt door een toepassing), kunt u de opdrachten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) of [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) gebruiken. Gebruik voor een service-principal de object-ID en **niet** de toepassings-id.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen.

### <a name="resource-group-scope-without-parameters"></a>Bereik van de resource groep (zonder para meters)

De volgende sjabloon toont een eenvoudige manier om een roltoewijzing toe te voegen. Sommige waarden worden opgegeven in de sjabloon. In de volgende sjabloon ziet u:

-  De rol van [lezer](built-in-roles.md#reader) toewijzen aan een gebruiker, groep of toepassing in een bereik van een resource groep

Als u de sjabloon wilt gebruiken, moet u het volgende doen:

- Een nieuw JSON-bestand maken en de sjabloon kopiëren
- Vervang door `<your-principal-id>` de id van een gebruiker, groep, beheerde identiteit of toepassing om de rol toe te wijzen aan

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

Hier vindt u een voor beeld van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) opdrachten voor het starten van de implementatie in een resource groep met de naam ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Hieronder ziet u een voor beeld van de toewijzing van de rol van lezers aan een gebruiker voor een resource groep na het implementeren van de sjabloon.

![Roltoewijzing op het bereik van de resource groep](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Resource groep of abonnements bereik

De vorige sjabloon is niet zeer flexibel. De volgende sjabloon maakt gebruik van para meters en kan worden gebruikt in verschillende bereiken. In de volgende sjabloon ziet u:

- Een rol toewijzen aan een gebruiker, groep of toepassing in een resource groep of abonnements bereik
- De rol van eigenaar, bijdrager en lezer opgeven als een para meter

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De ID van een gebruiker, groep, beheerde identiteit of toepassing waaraan de rol moet worden toegewezen
- Een unieke ID die wordt gebruikt voor de roltoewijzing, of u kunt de standaard-ID gebruiken

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
> Deze sjabloon is niet idempotent tenzij dezelfde `roleNameGuid` waarde wordt gegeven als een para meter voor elke implementatie van de sjabloon. Als er geen `roleNameGuid` wordt geleverd, wordt er standaard een nieuwe GUID gegenereerd voor elke implementatie en mislukken volgende implementaties met een `Conflict: RoleAssignmentExists` fout.

Het bereik van de roltoewijzing wordt bepaald op basis van het niveau van de implementatie. Hier vindt u een voor beeld van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) opdrachten voor het starten van de implementatie in een bereik van een resource groep.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Hier vindt u een voor beeld van [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) en [AZ Deployment sub Create](/cli/azure/deployment/sub#az_deployment_sub_create) opdrachten voor het starten van de implementatie op een abonnements bereik en het opgeven van de locatie.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Resourcebereik

Als u een roltoewijzing moet toevoegen op het niveau van een resource, stelt u de eigenschap van de roltoewijzing in op de `scope` naam van de resource.

In de volgende sjabloon ziet u:

- Een nieuw opslagaccount maken
- Een rol toewijzen aan een gebruiker, groep of toepassing in het bereik van het opslag account
- De rol van eigenaar, bijdrager en lezer opgeven als een para meter

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De ID van een gebruiker, groep, beheerde identiteit of toepassing waaraan de rol moet worden toegewezen

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

Als u de vorige sjabloon wilt implementeren, gebruikt u de opdrachten van de resource groep. Hier vindt u een voor beeld van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) opdrachten voor het starten van de implementatie in een resource bereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Hieronder ziet u een voor beeld van de toewijzing van de rol Inzender aan een gebruiker van een opslag account na de implementatie van de sjabloon.

![Roltoewijzing bij resource bereik](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nieuwe Service-Principal

Als u een nieuwe Service-Principal maakt en een rol onmiddellijk probeert toe te wijzen aan die Service-Principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een nieuwe beheerde identiteit maakt en vervolgens probeert een rol toe te wijzen aan die Service-Principal in hetzelfde Azure Resource Manager sjabloon, kan de roltoewijzing mislukken. De oorzaak van deze fout is waarschijnlijk een replicatie vertraging. De service-principal wordt gemaakt in één regio. de roltoewijzing kan echter plaatsvinden in een andere regio waarvoor de Service-Principal nog niet is gerepliceerd.

Als u dit scenario wilt aanpakken, moet u de `principalType` eigenschap instellen op `ServicePrincipal` bij het maken van de roltoewijzing. U moet ook de `apiVersion` toewijzing van de roltoewijzing instellen op `2018-09-01-preview` of hoger.

In de volgende sjabloon ziet u:

- Een nieuwe beheerde ID voor de service-principal maken
- Het opgeven van de `principalType`
- De rol van Inzender toewijzen aan de Service-Principal in een bereik van een resource groep

Als u de sjabloon wilt gebruiken, moet u de volgende invoer opgeven:

- De basis naam van de beheerde identiteit, of u kunt de standaard teken reeks gebruiken

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
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Hier vindt u een voor beeld van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) opdrachten voor het starten van de implementatie in een bereik van een resource groep.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Hieronder ziet u een voor beeld van de toewijzing van de rol Inzender aan een nieuwe beheerde ID service-principal na de implementatie van de sjabloon.

![Roltoewijzing voor een nieuwe beheerde ID service-principal](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

In azure RBAC kunt u de functie toewijzing verwijderen om de toegang tot een Azure-resource te verwijderen. Er is geen manier om een roltoewijzing te verwijderen met behulp van een sjabloon. Als u een roltoewijzing wilt verwijderen, moet u andere hulpprogram ma's gebruiken, zoals:

- [Azure-portal](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Azure-CLI](role-assignments-cli.md#remove-a-role-assignment)
- [REST API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: ARM-sjablonen maken en implementeren met behulp van Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Informatie over de structuur en de syntaxis van ARM-sjablonen](../azure-resource-manager/templates/template-syntax.md)
- [Resource groepen en-resources op abonnements niveau maken](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?term=rbac)
