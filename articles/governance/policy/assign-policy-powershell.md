---
title: 'Quickstart: Nieuwe beleidstoewijzing met PowerShell'
description: In deze quickstart maakt u met Azure PowerShell een Azure Policy-toewijzing om niet-compatibele resources te identificeren.
ms.date: 03/31/2021
ms.topic: quickstart
ms.openlocfilehash: 1043bd53fa2c494d34b0007a851b95afd937d991
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091409"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Quickstart: Met behulp van Azure PowerShell een beleidstoewijzing maken om niet-conforme resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. In deze snelstart maakt u een beleidstoewijzing om te identificeren welke virtuele machines geen beheerde schijven gebruiken. Zodra de toewijzing is voltooid, kunt u de virtuele machines identificeren die _niet-compatibel_ zijn.

De Azure PowerShell-module wordt gebruikt om Azure-resources vanaf de opdrachtregel of in scripts te beheren.
In deze handleiding wordt uitgelegd hoe u de Az-module gebruikt om een beleidstoewijzing te maken.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voordat u begint, moet u ervoor zorgen dat de nieuwste versie van Azure PowerShell is geïnstalleerd. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie.

- Registreer de resourceprovider van Azure Policy Insights met behulp van Azure PowerShell. Als u de resourceprovider registreert, controleer dan of uw abonnement ermee werkt. Als u een resourceprovider wilt registreren, moet u toestemming hebben om de bewerking van de resourceprovider te registeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Zie [Resourceproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over het registreren en weergeven van resourceproviders.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze quickstart maakt u een beleidstoewijzing voor de definitie _Virtuele machines zonder beheerde schijven controleren_. Met deze beleidsdefinitie worden virtuele machines geïdentificeerd die geen beheerde schijven gebruiken.

Voer de volgende opdrachten uit om een nieuwe beleidstoewijzing te maken:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- **Naam**: de werkelijke naam van de toewijzing. Voor dit voorbeeld is _audit-vm-manageddisks_ gebruikt.
- **Weergavenaam**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing _Virtuele machines zonder beheerde schijven controleren_.
- **Definitie**: de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_.
- **Bereik**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Dit kan variëren van een abonnement tot resourcegroepen. Vergeet niet om &lt;scope&gt; te vervangen door de naam van uw resourcegroep.

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Gebruik de volgende informatie om te identificeren welke resources niet compatibel zijn met de beleidstoewijzing die u hebt gemaakt. Voer de volgende opdrachten uit:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Zie [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState) voor meer informatie over het ophalen van de beleidsstatus.

De resultaten zien er ongeveer als volgt uit:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

De resultaten komen overeen met wat u ziet op het tabblad **Resourcenaleving** van een beleidstoewijzing in de Azure Portal-weergave.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de beleidstoewijzing te verwijderen:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)