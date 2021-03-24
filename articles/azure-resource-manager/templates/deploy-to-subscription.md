---
title: Resources implementeren voor het abonnement
description: Hierin wordt beschreven hoe u een resource groep maakt in een Azure Resource Manager sjabloon. Ook wordt uitgelegd hoe u resources kunt implementeren in het bereik van Azure-abonnementen.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: f557a3a15da33b7394d22784bcd2c1c914ad6201
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889430"
---
# <a name="subscription-deployments-with-arm-templates"></a>Implementaties van abonnementen met ARM-sjablonen

Om het beheer van resources te vereenvoudigen, kunt u een Azure Resource Manager sjabloon (ARM-sjabloon) gebruiken om resources te implementeren op het niveau van uw Azure-abonnement. U kunt bijvoorbeeld [beleid](../../governance/policy/overview.md) en [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) implementeren voor uw abonnement. Dit geldt voor het hele abonnement. U kunt ook resource groepen maken binnen het abonnement en resources implementeren voor resource groepen in het abonnement.

> [!NOTE]
> U kunt implementeren op 800 verschillende resource groepen in een implementatie op abonnements niveau.

Als u sjablonen wilt implementeren op abonnements niveau, gebruikt u Azure CLI, Power shell, REST API of de portal.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resource typen kunnen worden geïmplementeerd op het abonnements niveau. In deze sectie wordt een overzicht gegeven van de resource typen die worden ondersteund.

Gebruik voor Azure-blauw drukken:

* [artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blauw drukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versies (blauw drukken)](/azure/templates/microsoft.blueprint/blueprints/versions)

Gebruik voor Azure-beleid:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd op resource groepen, gebruikt u:

* [implementaties](/azure/templates/microsoft.resources/deployments)

Gebruik voor het maken van nieuwe resource groepen:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Gebruik voor het beheren van uw abonnement:

* [Advisor-configuraties](/azure/templates/microsoft.advisor/configurations)
* [budgetten](/azure/templates/microsoft.consumption/budgets)
* [Analyse profiel wijzigen](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Koptags](/azure/templates/microsoft.resources/tags)

Andere ondersteunde typen zijn:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Het schema dat u voor implementaties op abonnements niveau gebruikt, wijkt af van het schema voor implementaties van resource groepen.

Voor sjablonen gebruikt u:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Gebruik de implementatie opdrachten op abonnements niveau om te implementeren in een abonnement.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create)voor Azure cli. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor de Power shell-implementatie opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) of de alias `New-AzSubscriptionDeployment` . In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
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

Voor implementaties op abonnements niveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen. Er is ook een locatie vereist voor [beheer groep](deploy-to-management-group.md) -en [Tenant](deploy-to-tenant.md) implementaties. Voor implementaties van [resource groepen](deploy-to-resource-group.md) wordt de locatie van de resource groep gebruikt om de implementatie gegevens op te slaan.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam _azuredeploy.jsop_ implementeert, maakt de standaard implementatie naam **azuredeploy**.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u bijvoorbeeld een implementatie van een abonnement met de naam **deployment1** in **centralus** maakt, kunt u later geen andere implementatie maken met de naam **deployment1** , maar een locatie van **westus**. Als u de fout code krijgt `InvalidDeploymentLocation` , moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatie bereiken

Wanneer u naar een abonnement implementeert, kunt u resources implementeren voor het volgende:

* het doel abonnement van de bewerking
* elk abonnement in de Tenant
* resource groepen binnen het abonnement of andere abonnementen
* de Tenant voor het abonnement

Een [extensie resource](scope-extension-resources.md) kan worden ingedeeld naar een doel dat verschilt van het implementatie doel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie wordt beschreven hoe u verschillende bereiken kunt opgeven. U kunt deze verschillende bereiken combi neren in één sjabloon.

### <a name="scope-to-target-subscription"></a>Bereik voor het doel abonnement

Als u resources wilt implementeren in het doel abonnement, voegt u deze resources toe aan de sectie resources van de sjabloon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Zie [resource groepen maken](#create-resource-groups) en [beleids definitie toewijzen](#assign-policy-definition)voor voor beelden van het implementeren van het abonnement.

### <a name="scope-to-other-subscription"></a>Bereik voor ander abonnement

Als u resources wilt implementeren voor een abonnement dat verschilt van het abonnement van de bewerking, voegt u een geneste implementatie toe. Stel de `subscriptionId` eigenschap in op de id van het abonnement dat u wilt implementeren. Stel de `location` eigenschap voor de geneste implementatie in.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Bereik voor resource groep

Als u resources wilt implementeren voor een resource groep binnen het abonnement, voegt u een geneste implementatie toe en neemt u de `resourceGroup` eigenschap op. In het volgende voor beeld streeft de geneste implementatie naar een resource groep met de naam `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Zie [resource groep en resources maken](#create-resource-group-and-resources)voor een voor beeld van de implementatie van een resource groep.

### <a name="scope-to-tenant"></a>Bereik naar Tenant

Stel de in op om resources te maken in de Tenant `scope` `/` . De gebruiker die de sjabloon implementeert, moet de [vereiste toegang hebben om te implementeren op de Tenant](deploy-to-tenant.md#required-access).

Als u een geneste implementatie wilt gebruiken, stelt u `scope` en in `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Of u kunt het bereik instellen op `/` voor sommige resource typen, zoals beheer groepen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Zie [beheer groep](deploy-to-management-group.md#management-group)voor meer informatie.

## <a name="resource-groups"></a>Resourcegroepen

### <a name="create-resource-groups"></a>Resource groepen maken

Als u een resource groep in een ARM-sjabloon wilt maken, definieert u een resource van het [micro soft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) met een naam en een locatie voor de resource groep.

Met de volgende sjabloon maakt u een lege resource groep.

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

Gebruik het [element Copy](copy-resources.md) met resource groepen om meer dan één resource groep te maken.

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

Zie [resource-iteratie in arm-sjablonen](./copy-resources.md)en [zelf studie: meerdere resource-instanties maken met arm-sjablonen](./template-tutorial-create-multiple-instances.md)voor meer informatie over resource iteratie.

### <a name="create-resource-group-and-resources"></a>Resource groep en-resources maken

Als u de resource groep wilt maken en resources hierop wilt implementeren, gebruikt u een geneste sjabloon. De geneste sjabloon definieert de resources die moeten worden geïmplementeerd in de resource groep. Stel de geneste sjabloon afhankelijk van de resource groep in om ervoor te zorgen dat de resource groep bestaat voordat u de resources implementeert. U kunt implementeren in Maxi maal 800 resource groepen.

In het volgende voor beeld wordt een resource groep gemaakt en wordt een opslag account geïmplementeerd in de resource groep.

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

### <a name="assign-policy-definition"></a>Beleids definitie toewijzen

In het volgende voor beeld wordt een bestaande beleids definitie toegewezen aan het abonnement. Als de beleids definitie para meters heeft, geeft u ze als een object op. Als de beleids definitie geen para meters heeft, gebruikt u het standaard lege object.

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

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

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

### <a name="create-and-assign-policy-definitions"></a>Beleids definities maken en toewijzen

U kunt een beleids definitie [definiëren](../../governance/policy/concepts/definition-structure.md) en toewijzen in dezelfde sjabloon.

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

Gebruik de volgende CLI-opdracht om de beleids definitie in uw abonnement te maken en toe te wijzen aan het abonnement:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Definitie van blauw druk maken

U kunt een blauw druk-definitie [maken](../../governance/blueprints/tutorials/create-from-sample.md) op basis van een sjabloon.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Als u de definitie van de blauw druk in uw abonnement wilt maken, gebruikt u de volgende CLI-opdracht:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Toegangsbeheer

Zie [Azure-roltoewijzingen toevoegen met Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.

In het volgende voor beeld wordt een resource groep gemaakt, wordt er een vergren deling op toegepast en wordt een rol aan een principal toegewezen.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

* Zie [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* Voorbeeld sjablonen vindt u op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* U kunt ook sjablonen implementeren op het niveau van de [beheer groep](deploy-to-management-group.md) en het [Tenant niveau](deploy-to-tenant.md).
