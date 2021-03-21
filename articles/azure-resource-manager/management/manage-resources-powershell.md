---
title: Resources beheren-Azure PowerShell
description: Gebruik Azure PowerShell en Azure Resource Manager om uw resources te beheren. Laat zien hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185739"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure-resources beheren met behulp van Azure PowerShell

Meer informatie over het gebruik van Azure PowerShell met [Azure Resource Manager](overview.md) voor het beheren van uw Azure-resources. Zie [Azure-resource groepen beheren met behulp van Azure PowerShell](manage-resource-groups-powershell.md)voor het beheren van resource groepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met behulp van de Azure Portal](manage-resources-portal.md)
- [Azure-resources beheren met Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resource groep

U kunt Azure-resources rechtstreeks implementeren met behulp van Azure PowerShell of een resource manager-sjabloon implementeren om Azure-resources te maken.

### <a name="deploy-a-resource"></a>Een resource implementeren

Met het volgende script maakt u een opslag account.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Een sjabloon implementeren

Met het volgende script maakt u een Quick Start-sjabloon voor het maken van een opslag account. Zie Quick Start (Engelstalig) voor meer informatie over het [maken van Azure Resource Manager sjablonen met Visual Studio code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Raadpleeg voor meer informatie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Een resource groep en-resources implementeren

U kunt een resource groep maken en resources implementeren voor de groep. Zie [resource groep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-groups)voor meer informatie.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Resources implementeren voor meerdere abonnementen of resource groepen

Doorgaans implementeert u alle resources in uw sjabloon tot één resource groep. Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze wilt plaatsen in verschillende resource groepen of-abonnementen. Zie [Azure-resources implementeren voor meerdere abonnementen of resource groepen](../templates/deploy-to-resource-group.md)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

Het volgende script toont hoe u een opslag account verwijdert.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Zie [Azure Resource Manager resource groep verwijderen](delete-resource-group.md)voor meer informatie over de manier waarop Azure Resource Manager het verwijderen van resources ordent.

## <a name="move-resources"></a>Resources verplaatsen

Het volgende script toont hoe u een opslag account van een resource groep kunt verwijderen naar een andere resource groep.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergren delen voor komt dat andere gebruikers in uw organisatie per ongeluk essentiële resources verwijderen of wijzigen, zoals een Azure-abonnement, resource groep of resource. 

Met het volgende script wordt een opslag account vergrendeld zodat het account niet kan worden verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Met het volgende script worden alle vergren delingen voor een opslag account opgehaald:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Met het volgende script wordt een vergren deling van een opslag account verwijderd:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Door labels kunt u de resource groep en resources logisch ordenen. Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md#powershell)voor meer informatie.

## <a name="manage-access-to-resources"></a>Toegang tot resources beheren

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot resources in azure beheert. Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Resource Manager](overview.md)voor meer informatie Azure Resource Manager.
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](../templates/template-syntax.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](../index.yml)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.