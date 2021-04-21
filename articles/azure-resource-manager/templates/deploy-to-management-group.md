---
title: Resources implementeren in beheergroep
description: Beschrijft hoe u resources implementeert in het bereik van de beheergroep in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781933"
---
# <a name="management-group-deployments-with-arm-templates"></a>Implementaties van beheergroep met ARM-sjablonen

Naarmate uw organisatie zich verder ontwikkelde, kunt u een Azure Resource Manager (ARM-sjabloon) implementeren om resources te maken op beheergroepsniveau. U moet bijvoorbeeld beleidsregels of [](../../governance/policy/overview.md) op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) definiëren en toewijzen voor een beheergroep. Met beheergroepssjablonen kunt u beleidsregels declaratief toepassen en rollen toewijzen op beheergroepsniveau.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resourcetypen kunnen worden geïmplementeerd op het niveau van de beheergroep. In deze sectie wordt vermeld welke resourcetypen worden ondersteund.

Gebruik Azure Blueprints voor meer informatie:

* [Artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Blauwdrukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Versies](/azure/templates/microsoft.blueprint/blueprints/versions)

Gebruik Azure Policy voor meer informatie:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) het volgende:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd in abonnementen of resourcegroepen, gebruikt u:

* [Implementaties](/azure/templates/microsoft.resources/deployments)

Gebruik het volgende voor het beheren van uw resources:

* [Tags](/azure/templates/microsoft.resources/tags)

Beheergroepen zijn resources op tenantniveau. U kunt echter beheergroepen maken in een beheergroepimplementatie door het bereik van de nieuwe beheergroep in te stellen op de tenant. Zie [Beheergroep](#management-group).

## <a name="schema"></a>Schema

Het schema dat u gebruikt voor implementaties van beheergroep verschilt van het schema voor resourcegroepimplementaties.

Gebruik voor sjablonen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Het schema voor een parameterbestand is hetzelfde voor alle implementatiebereiken. Gebruik voor parameterbestanden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Implementatieopdrachten

Als u wilt implementeren in een beheergroep, gebruikt u de implementatieopdrachten voor beheergroep.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik voor Azure CLI [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor Azure PowerShell [New-AzManagementGroupDeployment.](/powershell/module/az.resources/new-azmanagementgroupdeployment)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Zie voor meer informatie over implementatieopdrachten en -opties voor het implementeren van ARM-sjablonen:

* [Resources implementeren met ARM-sjablonen en Azure Portal](deploy-portal.md)
* [Resources implementeren met ARM-sjablonen en Azure CLI](deploy-cli.md)
* [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)
* [Resources implementeren met ARM-sjablonen en Azure Resource Manager REST API](deploy-rest.md)
* [Een implementatieknop gebruiken om sjablonen te implementeren vanuit een GitHub-opslagplaats](deploy-to-azure-button.md)
* [ARM-sjablonen implementeren vanuit Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Locatie en naam van implementatie

Voor implementaties op beheergroepniveau moet u een locatie voor de implementatie verstrekken. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen. [Voor](deploy-to-subscription.md) abonnements- en tenantimplementaties is ook een locatie vereist. [](deploy-to-tenant.md) Voor [](deploy-to-resource-group.md) resourcegroepimplementaties wordt de locatie van de resourcegroep gebruikt om de implementatiegegevens op te slaan.

U kunt een naam voor de implementatie of de standaard implementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de _azuredeploy.jsop implementeert,_ wordt de standaardimplementatienaam **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u bijvoorbeeld een beheergroepimplementatie met de naam **deployment1** in **centralus** maakt, kunt u later geen andere implementatie maken met de naam **deployment1,** maar met de locatie **westus**. Als u de foutcode krijgt, gebruikt u een andere naam of dezelfde locatie als de `InvalidDeploymentLocation` vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatiebereiken

Wanneer u implementeert in een beheergroep, kunt u resources implementeren op:

* de doelbeheergroep van de bewerking
* een andere beheergroep in de tenant
* abonnementen in de beheergroep
* resourcegroepen in de beheergroep
* de tenant voor de resourcegroep

Een [extensieresource](scope-extension-resources.md) kan worden gericht op een doel dat verschilt van het implementatiedoel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie ziet u hoe u verschillende scopes opgeeft. U kunt deze verschillende scopes combineren in één sjabloon.

### <a name="scope-to-target-management-group"></a>Bereik naar doelbeheergroep

Resources die zijn gedefinieerd in de sectie resources van de sjabloon worden toegepast op de beheergroep met de implementatieopdracht .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Bereik tot een andere beheergroep

Als u zich wilt richten op een andere beheergroep, voegt u een geneste implementatie toe en geeft u de eigenschap `scope` op. Stel de `scope` eigenschap in op een waarde in de notatie `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Bereik tot abonnement

U kunt zich ook richten op abonnementen binnen een beheergroep. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Als u zich wilt richten op een abonnement in de beheergroep, gebruikt u een geneste implementatie en de `subscriptionId` eigenschap .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Bereik naar resourcegroep

U kunt zich ook richten op resourcegroepen binnen de beheergroep. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Als u zich wilt richten op een resourcegroep in de beheergroep, gebruikt u een geneste implementatie. Stel de eigenschappen `subscriptionId` en `resourceGroup` in. Stel geen locatie in voor de geneste implementatie omdat deze wordt geïmplementeerd op de locatie van de resourcegroep.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Zie Implementeren naar abonnement en resourcegroep als u een implementatie van een beheergroep wilt gebruiken voor het maken van een resourcegroep binnen een abonnement en het implementeren van een opslagaccount voor die [resourcegroep.](#deploy-to-subscription-and-resource-group)

### <a name="scope-to-tenant"></a>Bereik naar tenant

Als u resources in de tenant wilt maken, stelt u de `scope` in op `/` . De gebruiker die de sjabloon implementeert, moet de [vereiste toegang hebben om te implementeren op de tenant](deploy-to-tenant.md#required-access).

Als u een geneste implementatie wilt gebruiken, stelt u `scope` en `location` in.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

U kunt het bereik ook instellen op `/` voor bepaalde resourcetypen, zoals beheergroepen. Het maken van een nieuwe beheergroep wordt beschreven in de volgende sectie.

## <a name="management-group"></a>Beheergroep

Als u een beheergroep wilt maken in een beheergroepimplementatie, moet u het bereik instellen `/` op voor de beheergroep.

In het volgende voorbeeld wordt een nieuwe beheergroep gemaakt in de hoofdbeheergroep.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

In het volgende voorbeeld wordt een nieuwe beheergroep gemaakt in de beheergroep die is opgegeven als bovenliggend. U ziet dat het bereik is ingesteld op `/` .

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

Als u een ARM-sjabloon wilt gebruiken om een nieuw Azure-abonnement te maken in een beheergroep, gaat u naar:

* [Programmatisch Azure Enterprise Agreement maken](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programmatisch Azure-abonnementen maken voor een Microsoft-klantovereenkomst](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programmatisch Azure-abonnementen maken voor een Microsoft Partner-overeenkomst](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Zie Abonnementen verplaatsen in ARM-sjabloon voor het implementeren van een sjabloon die een bestaand Azure-abonnement verplaatst naar een nieuwe [beheergroep](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Aangepaste beleidsdefinities die in de beheergroep worden geïmplementeerd, zijn extensies van de beheergroep. Gebruik de functie [extensionResourceId()](template-functions-resource.md#extensionresourceid) om de id van een aangepaste beleidsdefinitie op te halen. Ingebouwde beleidsdefinities zijn resources op tenantniveau. Gebruik de functie [tenantResourceId()](template-functions-resource.md#tenantresourceid) om de id van een ingebouwde beleidsdefinitie op te halen.

In het volgende voorbeeld ziet u hoe [u een](../../governance/policy/concepts/definition-structure.md) beleid definieert op het niveau van de beheergroep en dit toewijst.

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

## <a name="deploy-to-subscription-and-resource-group"></a>Implementeren naar abonnement en resourcegroep

Vanuit een implementatie op beheergroepniveau kunt u zich richten op een abonnement binnen de beheergroep. In het volgende voorbeeld wordt een resourcegroep binnen een abonnement gemaakt en wordt een opslagaccount geïmplementeerd in die resourcegroep.

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

* Zie Add Azure role assignments using Azure Resource Manager templates (Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager [sjablonen) voor meer informatie over het toewijzen Azure Resource Manager rollen.](../../role-based-access-control/role-assignments-template.md)
* Voor een voorbeeld van het implementeren van werkruimte-instellingen voor Azure Security Center, zie [deployASCwithWorkspaceSettings.jsop](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* U kunt ook sjablonen implementeren op [abonnementsniveau en](deploy-to-subscription.md) [tenantniveau.](deploy-to-tenant.md)
