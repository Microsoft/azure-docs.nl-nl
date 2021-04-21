---
title: Resources implementeren in tenant
description: Beschrijft hoe u resources implementeert op tenantbereik in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781897"
---
# <a name="tenant-deployments-with-arm-templates"></a>Tenantimplementaties met ARM-sjablonen

Naarmate uw organisatie zich verder ontwikkelen, [](../../governance/policy/overview.md) moet u mogelijk beleidsregels of op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) definiëren en toewijzen in uw Azure AD-tenant. Met sjablonen op tenantniveau kunt u beleidsregels declaratief toepassen en rollen toewijzen op globaal niveau.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resourcetypen kunnen worden geïmplementeerd op tenantniveau. In deze sectie wordt vermeld welke resourcetypen worden ondersteund.

Gebruik voor Azure-beleid het volgende:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Gebruik voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) het volgende:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Voor geneste sjablonen die worden geïmplementeerd in beheergroepen, abonnementen of resourcegroepen, gebruikt u:

* [Implementaties](/azure/templates/microsoft.resources/deployments)

Voor het maken van beheergroepen gebruikt u:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Voor het maken van abonnementen gebruikt u:

* [Aliassen](/azure/templates/microsoft.subscription/aliases)

Voor het beheren van kosten gebruikt u:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [Instructies](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Voor het configureren van de portal gebruikt u:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schema

Het schema dat u gebruikt voor tenantimplementaties is anders dan het schema voor resourcegroepimplementaties.

Gebruik voor sjablonen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
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

## <a name="required-access"></a>Vereiste toegang

De principal die de sjabloon implementeert, moet machtigingen hebben om resources te maken in het tenantbereik. De principal moet machtigingen hebben om de implementatieacties ( ) uit te voeren en om de resources te maken `Microsoft.Resources/deployments/*` die in de sjabloon zijn gedefinieerd. Als u bijvoorbeeld een beheergroep wilt maken, moet de principal de machtiging Inzender hebben voor het tenantbereik. Als u roltoewijzingen wilt maken, moet de principal de machtiging Eigenaar hebben.

De globale beheerder voor de Azure Active Directory is niet automatisch machtigingen om rollen toe te wijzen. Als u sjabloonimplementaties in het tenantbereik wilt inschakelen, moet de globale beheerder de volgende stappen uitvoeren:

1. Verhoog accounttoegang zodat de globale beheerder rollen kan toewijzen. Zie voor meer informatie [Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren](../../role-based-access-control/elevate-access-global-admin.md).

1. Wijs Eigenaar of Inzender toe aan de principal die de sjablonen moet implementeren.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

De principal beschikt nu over de vereiste machtigingen om de sjabloon te implementeren.

## <a name="deployment-commands"></a>Implementatieopdrachten

De opdrachten voor tenantimplementaties zijn anders dan de opdrachten voor resourcegroepimplementaties.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik voor Azure CLI [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik Azure PowerShell [New-AzTenantDeployment voor meer informatie.](/powershell/module/az.resources/new-aztenantdeployment)

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
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

Voor implementaties op tenantniveau moet u een locatie voor de implementatie verstrekken. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen. [Voor](deploy-to-subscription.md) [implementaties van abonnementen en](deploy-to-management-group.md) beheergroepen is ook een locatie vereist. Voor [](deploy-to-resource-group.md) resourcegroepimplementaties wordt de locatie van de resourcegroep gebruikt om de implementatiegegevens op te slaan.

U kunt een naam voor de implementatie of de standaard implementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de _azuredeploy.jsop,_ wordt de standaardimplementatienaam **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u bijvoorbeeld een tenantimplementatie maakt met de naam **deployment1** in **centralus**, kunt u later geen andere implementatie maken met de naam **deployment1,** maar met de locatie **westus**. Als u de foutcode krijgt, gebruikt u een andere naam of dezelfde locatie als de `InvalidDeploymentLocation` vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatiebereiken

Wanneer u implementeert in een tenant, kunt u resources implementeren op:

* de tenant
* beheergroepen binnen de tenant
* Abonnementen
* Resourcegroepen

Een [extensieresource](scope-extension-resources.md) kan worden gericht op een ander doel dan het implementatiedoel.

De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

In deze sectie ziet u hoe u verschillende scopes opgeeft. U kunt deze verschillende scopes combineren in één sjabloon.

### <a name="scope-to-tenant"></a>Bereik naar tenant

Resources die zijn gedefinieerd in de sectie Resources van de sjabloon worden toegepast op de tenant.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Bereik tot beheergroep

Als u zich wilt richten op een beheergroep binnen de tenant, voegt u een geneste implementatie toe en geeft u de eigenschap `scope` op.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Bereik naar abonnement

U kunt zich ook richten op abonnementen binnen de tenant. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Als u zich wilt richten op een abonnement binnen de tenant, gebruikt u een geneste implementatie en de `subscriptionId` eigenschap .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Bereik naar resourcegroep

U kunt zich ook richten op resourcegroepen binnen de tenant. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Als u zich wilt richten op een resourcegroep binnen de tenant, gebruikt u een geneste implementatie. Stel de eigenschappen `subscriptionId` en `resourceGroup` in. Stel geen locatie in voor de geneste implementatie omdat deze wordt geïmplementeerd op de locatie van de resourcegroep.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Beheergroep maken

Met de volgende sjabloon maakt u een beheergroep.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Als uw account geen machtiging heeft om te implementeren in de tenant, kunt u nog steeds beheergroepen maken door te implementeren in een ander bereik. Zie [Beheergroep voor meer informatie.](deploy-to-management-group.md#management-group)

## <a name="assign-role"></a>Rol toewijzen

Met de volgende sjabloon wordt een rol toegewezen in het tenantbereik.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

* Zie Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager [sjablonen voor meer informatie Azure Resource Manager toewijzen.](../../role-based-access-control/role-assignments-template.md)
* U kunt ook sjablonen implementeren op [abonnementsniveau](deploy-to-subscription.md) of [beheergroepniveau.](deploy-to-management-group.md)
