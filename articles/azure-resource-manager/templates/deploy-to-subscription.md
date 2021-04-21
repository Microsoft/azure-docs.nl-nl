---
title: Resources implementeren in een abonnement
description: Beschrijft hoe u een resourcegroep maakt in een Azure Resource Manager sjabloon. U ziet ook hoe u resources implementeert in het bereik van het Azure-abonnement.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 3598fe290fd993cbbc662ba9d3a3c5ba8c207bc0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781915"
---
# <a name="subscription-deployments-with-arm-templates"></a>Abonnementsimplementaties met ARM-sjablonen

Om het beheer van resources te vereenvoudigen, kunt u een Azure Resource Manager-sjabloon (ARM-sjabloon) gebruiken om resources te implementeren op het niveau van uw Azure-abonnement. U kunt bijvoorbeeld [beleidsregels](../../governance/policy/overview.md) en op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) implementeren in uw abonnement, wat van toepassing is op uw abonnement. U kunt ook resourcegroepen binnen het abonnement maken en resources implementeren in resourcegroepen in het abonnement.

> [!NOTE]
> U kunt implementeren naar 800 verschillende resourcegroepen in een implementatie op abonnementsniveau.

Als u sjablonen op abonnementsniveau wilt implementeren, gebruikt u Azure CLI, PowerShell, REST API of de portal.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resourcetypen kunnen worden geïmplementeerd op abonnementsniveau. In deze sectie wordt vermeld welke resourcetypen worden ondersteund.

Gebruik Azure Blueprints voor meer informatie:

* [Artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Blauwdrukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versies (blauwdrukken)](/azure/templates/microsoft.blueprint/blueprints/versions)

Gebruik voor Azure-beleid het volgende:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) het volgende:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd in resourcegroepen, gebruikt u:

* [Implementaties](/azure/templates/microsoft.resources/deployments)

Voor het maken van nieuwe resourcegroepen gebruikt u:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Gebruik het volgende voor het beheren van uw abonnement:

* [Advisor-configuraties](/azure/templates/microsoft.advisor/configurations)
* [Begrotingen](/azure/templates/microsoft.consumption/budgets)
* [wijzigingsanalyse profiel](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tags](/azure/templates/microsoft.resources/tags)

Andere ondersteunde typen zijn:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Het schema dat u gebruikt voor implementaties op abonnementsniveau verschilt van het schema voor resourcegroepimplementaties.

Gebruik voor sjablonen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Als u wilt implementeren naar een abonnement, gebruikt u de implementatieopdrachten op abonnementsniveau.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik az [deployment sub create voor](/cli/azure/deployment/sub#az_deployment_sub_create)Azure CLI. In het volgende voorbeeld wordt een sjabloon geïmplementeerd om een resourcegroep te maken:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor de PowerShell-implementatieopdracht [New-AzDeployment of](/powershell/module/az.resources/new-azdeployment) de alias `New-AzSubscriptionDeployment` . In het volgende voorbeeld wordt een sjabloon geïmplementeerd om een resourcegroep te maken:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Zie voor meer informatie over implementatieopdrachten en -opties voor het implementeren van ARM-sjablonen:

* [Resources implementeren met ARM-sjablonen en Azure Portal](deploy-portal.md)
* [Resources implementeren met ARM-sjablonen en Azure CLI](deploy-cli.md)
* [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)
* [Resources implementeren met ARM-sjablonen en Azure Resource Manager REST API](deploy-rest.md)
* [Een implementatieknop gebruiken om sjablonen te implementeren vanuit een GitHub-opslagplaats](deploy-to-azure-button.md)
* [ARM-sjablonen implementeren vanuit Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Implementatielocatie en -naam

Voor implementaties op abonnementsniveau moet u een locatie voor de implementatie verstrekken. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen. [Voor beheergroep-](deploy-to-management-group.md) en tenantimplementaties is ook een locatie vereist. [](deploy-to-tenant.md) Voor [](deploy-to-resource-group.md) resourcegroepimplementaties wordt de locatie van de resourcegroep gebruikt om de implementatiegegevens op te slaan.

U kunt een naam voor de implementatie of de standaard implementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de _azuredeploy.jsop,_ wordt de standaardimplementatienaam **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u bijvoorbeeld een abonnementsimplementatie maakt met de naam **deployment1** in **centralus**, kunt u later geen andere implementatie maken met de naam **deployment1,** maar met de locatie **westus**. Als u de foutcode krijgt, gebruikt u een andere naam of dezelfde locatie `InvalidDeploymentLocation` als de vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatiebereiken

Wanneer u implementeert in een abonnement, kunt u resources implementeren voor:

* het doelabonnement van de bewerking
* elk abonnement in de tenant
* resourcegroepen binnen het abonnement of andere abonnementen
* de tenant voor het abonnement

Een [extensieresource](scope-extension-resources.md) kan worden gericht op een doel dat verschilt van het implementatiedoel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie ziet u hoe u verschillende scopes opgeeft. U kunt deze verschillende scopes combineren in één sjabloon.

### <a name="scope-to-target-subscription"></a>Bereik naar doelabonnement

Als u resources wilt implementeren in het doelabonnement, voegt u deze resources toe aan de sectie resources van de sjabloon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Zie Resourcegroepen maken en Beleidsdefinitie toewijzen voor voorbeelden van implementatie [in het abonnement.](#assign-policy-definition) [](#create-resource-groups)

### <a name="scope-to-other-subscription"></a>Bereik naar ander abonnement

Als u resources wilt implementeren naar een ander abonnement dan het abonnement, voegt u een geneste implementatie toe. Stel de `subscriptionId` eigenschap in op de id van het abonnement dat u wilt implementeren. Stel de `location` eigenschap in voor de geneste implementatie.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Bereik naar resourcegroep

Als u resources wilt implementeren in een resourcegroep binnen het abonnement, voegt u een geneste implementatie toe en voegt u de eigenschap `resourceGroup` toe. In het volgende voorbeeld is de geneste implementatie gericht op een resourcegroep met de naam `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Zie Resourcegroep en resources maken voor een voorbeeld van een implementatie [in een resourcegroep.](#create-resource-group-and-resources)

### <a name="scope-to-tenant"></a>Bereik naar tenant

Als u resources wilt maken in de tenant, stelt u de `scope` in op `/` . De gebruiker die de sjabloon implementeert, moet de [vereiste toegang hebben om te implementeren in de tenant](deploy-to-tenant.md#required-access).

Als u een geneste implementatie wilt gebruiken, stelt u `scope` en `location` in.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

U kunt het bereik ook instellen op `/` voor bepaalde resourcetypen, zoals beheergroepen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Zie Beheergroep [voor meer informatie.](deploy-to-management-group.md#management-group)

## <a name="resource-groups"></a>Resourcegroepen

### <a name="create-resource-groups"></a>Resourcegroepen maken

Als u een resourcegroep wilt maken in een ARM-sjabloon, definieert u een [resource Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) met een naam en locatie voor de resourcegroep.

Met de volgende sjabloon maakt u een lege resourcegroep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Gebruik het [kopieerelement](copy-resources.md) met resourcegroepen om meer dan één resourcegroep te maken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Zie Resource-iteratie in ARM-sjablonen en Zelfstudie: Meerdere resource-exemplaren maken met ARM-sjablonen voor meer [informatie over resource-iteratie.](./template-tutorial-create-multiple-instances.md) [](./copy-resources.md)

### <a name="create-resource-group-and-resources"></a>Resourcegroep en resources maken

Gebruik een geneste sjabloon om de resourcegroep te maken en er resources in te implementeren. De geneste sjabloon definieert de resources die in de resourcegroep moeten worden geïmplementeerd. Stel de geneste sjabloon in als afhankelijk van de resourcegroep om ervoor te zorgen dat de resourcegroep bestaat voordat u de resources implementeert. U kunt implementeren naar maximaal 800 resourcegroepen.

In het volgende voorbeeld wordt een resourcegroep gemaakt en wordt een opslagaccount geïmplementeerd in de resourcegroep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Beleidsdefinitie toewijzen

In het volgende voorbeeld wordt een bestaande beleidsdefinitie toegewezen aan het abonnement. Als de beleidsdefinitie parameters gebruikt, geeft u deze op als een -object. Als de beleidsdefinitie geen parameters gebruikt, gebruikt u het standaard lege object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Als u deze sjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Als u deze sjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Beleidsdefinities maken en toewijzen

U kunt [een](../../governance/policy/concepts/definition-structure.md) beleidsdefinitie definiëren en toewijzen in dezelfde sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Gebruik de volgende CLI-opdracht om de beleidsdefinitie in uw abonnement te maken en deze toe te wijzen aan het abonnement:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Als u deze sjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Blauwdrukdefinitie maken

U kunt [een blauwdrukdefinitie](../../governance/blueprints/tutorials/create-from-sample.md) maken op basis van een sjabloon.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Gebruik de volgende CLI-opdracht om de blauwdrukdefinitie in uw abonnement te maken:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Als u deze sjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Toegangsbeheer

Zie Add Azure role assignments using Azure Resource Manager templates (Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager [sjablonen) voor meer informatie over het toewijzen Azure Resource Manager rollen.](../../role-based-access-control/role-assignments-template.md)

In het volgende voorbeeld wordt een resourcegroep gemaakt, een vergrendeling toegepast en een rol toegewezen aan een principal.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

* Voor een voorbeeld van het implementeren van werkruimte-instellingen voor Azure Security Center, zie [deployASCwithWorkspaceSettings.jsop](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Voorbeeldsjablonen vindt u op [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)
* U kunt ook sjablonen implementeren op [beheergroep- en](deploy-to-management-group.md) [tenantniveau.](deploy-to-tenant.md)
