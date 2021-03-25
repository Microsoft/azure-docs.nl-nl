---
title: Resources implementeren in beheer groep
description: Hierin wordt beschreven hoe u resources kunt implementeren in het bereik van de beheer groep in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dc7418d9e93fb50590c5e2502b3a3ffb3847273f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043305"
---
# <a name="management-group-deployments-with-arm-templates"></a>Implementaties van beheer groepen met ARM-sjablonen

Als uw organisatie is gerijpt, kunt u een Azure Resource Manager sjabloon (ARM-sjabloon) implementeren om resources te maken op het niveau van de beheer groep. U moet bijvoorbeeld [beleids regels](../../governance/policy/overview.md) of [toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/overview.md) voor een beheer groep definiëren en toewijzen. Met beheer groeps niveau sjablonen kunt u declaratief beleid Toep assen en rollen toewijzen op het niveau van de beheer groep.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resource typen kunnen worden geïmplementeerd op het niveau van de beheer groep. In deze sectie wordt een overzicht gegeven van de resource typen die worden ondersteund.

Gebruik voor Azure-blauw drukken:

* [artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blauw drukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [lager](/azure/templates/microsoft.blueprint/blueprints/versions)

Voor Azure Policy gebruikt u:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd op abonnementen of resource groepen, gebruikt u:

* [implementaties](/azure/templates/microsoft.resources/deployments)

Gebruik voor het beheren van uw resources:

* [Koptags](/azure/templates/microsoft.resources/tags)

Beheer groepen zijn resources op Tenant niveau. U kunt echter beheer groepen maken in een implementatie van een beheer groep door het bereik van de nieuwe beheer groep in te stellen op de Tenant. Zie [beheer groep](#management-group).

## <a name="schema"></a>Schema

Het schema dat u voor de implementaties van beheer groepen gebruikt, verschilt van het schema voor de implementatie van de resource groep.

Voor sjablonen gebruikt u:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Het schema voor een parameter bestand is hetzelfde voor alle implementatie bereiken. Gebruik voor parameter bestanden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Implementatie opdrachten

Als u wilt implementeren in een beheer groep, gebruikt u de opdrachten voor de implementatie van beheer groepen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create)voor Azure cli:

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voor Azure PowerShell gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Zie voor meer gedetailleerde informatie over implementatie opdrachten en opties voor het implementeren van ARM-sjablonen:

* [Resources implementeren met ARM-sjablonen en Azure Portal](deploy-portal.md)
* [Resources implementeren met ARM-sjablonen en Azure CLI](deploy-cli.md)
* [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)
* [Resources implementeren met ARM-sjablonen en Azure Resource Manager REST API](deploy-rest.md)
* [Een implementatie knop gebruiken voor het implementeren van sjablonen uit de GitHub-opslag plaats](deploy-to-azure-button.md)
* [ARM-sjablonen implementeren vanuit Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op beheer groepniveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen. Voor [abonnementen](deploy-to-subscription.md) en [Tenant](deploy-to-tenant.md) -implementaties is ook een locatie vereist. Voor implementaties van [resource groepen](deploy-to-resource-group.md) wordt de locatie van de resource groep gebruikt om de implementatie gegevens op te slaan.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam _azuredeploy.jsop_ implementeert, maakt de standaard implementatie naam **azuredeploy**.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u bijvoorbeeld een implementatie van een beheer groep maakt met de naam **deployment1** in **centraalus**, kunt u later geen andere implementatie maken met de naam **deployment1** maar een locatie van **westus**. Als u de fout code krijgt `InvalidDeploymentLocation` , moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatie bereiken

Wanneer u implementeert in een beheer groep, kunt u resources implementeren voor het volgende:

* de doel beheer groep van de bewerking
* een andere beheer groep in de Tenant
* abonnementen in de beheer groep
* resource groepen in de beheer groep
* de Tenant voor de resource groep

Een [extensie resource](scope-extension-resources.md) kan worden ingedeeld naar een doel dat verschilt van het implementatie doel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie wordt beschreven hoe u verschillende bereiken kunt opgeven. U kunt deze verschillende bereiken combi neren in één sjabloon.

### <a name="scope-to-target-management-group"></a>Bereik van doel beheer groep

Resources die zijn gedefinieerd in de sectie resources van de sjabloon, worden toegepast op de beheer groep via de implementatie opdracht.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Bereik naar een andere beheer groep

Als u een andere beheer groep wilt instellen, voegt u een geneste implementatie toe en geeft u de `scope` eigenschap op. Stel de `scope` eigenschap in op een waarde in de notatie `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Bereik voor abonnement

U kunt ook abonnementen in een beheer groep richten. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Gebruik een geneste implementatie en de eigenschap om een abonnement in de beheer groep te richten `subscriptionId` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Bereik voor resource groep

U kunt ook doel groepen in de beheer groep richten. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Gebruik een geneste implementatie als u een resource groep in de beheer groep wilt richten. Stel de `subscriptionId` Eigenschappen en in `resourceGroup` . Stel geen locatie in voor de geneste implementatie, omdat deze is geïmplementeerd op de locatie van de resource groep.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Voor het gebruik van een implementatie van een beheer groep voor het maken van een resource groep in een abonnement en het implementeren van een opslag account voor die resource groep, Zie [implementeren naar abonnement en resource groep](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Bereik naar Tenant

Stel de in op om resources te maken in de Tenant `scope` `/` . De gebruiker die de sjabloon implementeert, moet de [vereiste toegang hebben om te implementeren op de Tenant](deploy-to-tenant.md#required-access).

Als u een geneste implementatie wilt gebruiken, stelt u `scope` en in `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Of u kunt het bereik instellen op `/` voor sommige resource typen, zoals beheer groepen. Het maken van een nieuwe beheer groep wordt in de volgende sectie beschreven.

## <a name="management-group"></a>Beheergroep

Als u een beheer groep in een implementatie van een beheer groep wilt maken, moet u het bereik instellen op `/` voor de beheer groep.

In het volgende voor beeld wordt een nieuwe beheer groep gemaakt in de hoofd beheer groep.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

In het volgende voor beeld wordt een nieuwe beheer groep gemaakt in de beheer groep die is opgegeven als het bovenliggende item. U ziet dat het bereik is ingesteld op `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Abonnementen

Zie voor het gebruik van een ARM-sjabloon voor het maken van een nieuw Azure-abonnement in een beheer groep:

* [Programmatisch Azure Enterprise Overeenkomst-abonnementen maken](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programmatisch Azure-abonnementen maken voor een micro soft-klant overeenkomst](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programmatisch Azure-abonnementen maken voor een micro soft-partner overeenkomst](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Zie [abonnementen verplaatsen in arm-sjabloon](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template) voor het implementeren van een sjabloon waarmee een bestaand Azure-abonnement naar een nieuwe beheer groep wordt verplaatst.

## <a name="azure-policy"></a>Azure Policy

Aangepaste beleids definities die worden geïmplementeerd in de beheer groep zijn uitbrei dingen van de beheer groep. Als u de ID van een aangepaste beleids definitie wilt ophalen, gebruikt u de functie [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Ingebouwde beleids definities zijn resources op Tenant niveau. Als u de ID van een ingebouwde beleids definitie wilt ophalen, gebruikt u de functie [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

In het volgende voor beeld ziet u hoe u een beleid [definieert](../../governance/policy/concepts/definition-structure.md) op het niveau van de beheer groep en dit toewijst.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Implementeren naar het abonnement en de resource groep

Op basis van de implementatie van een beheer groep kunt u een abonnement in de beheer groep richten. In het volgende voor beeld wordt een resource groep gemaakt binnen een abonnement en wordt een opslag account geïmplementeerd op die resource groep.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure-roltoewijzingen toevoegen met Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* U kunt ook sjablonen implementeren op [abonnements niveau](deploy-to-subscription.md) en [Tenant niveau](deploy-to-tenant.md).
