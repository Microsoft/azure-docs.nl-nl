---
title: Resources beheren-Azure CLI
description: Gebruik Azure CLI en Azure Resource Manager om uw resources te beheren. Laat zien hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6672fd41dbd199940d663c4f7e1cb7f3b1936ce9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185773"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure-resources beheren met Azure CLI

Meer informatie over het gebruik van Azure CLI met [Azure Resource Manager](overview.md) voor het beheren van uw Azure-resources. Zie [Azure-resource groepen beheren met Azure cli](manage-resource-groups-cli.md)voor meer informatie over het beheren van resource groepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met behulp van de Azure Portal](manage-resources-portal.md)
- [Azure-resources beheren met behulp van Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resource groep

U kunt Azure-resources rechtstreeks implementeren met behulp van Azure CLI of een resource manager-sjabloon implementeren om Azure-resources te maken.

### <a name="deploy-a-resource"></a>Een resource implementeren

Met het volgende script maakt u een opslag account.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Een sjabloon implementeren

Met het volgende script maakt u een Quick Start-sjabloon voor het maken van een opslag account. Zie Quick Start (Engelstalig) voor meer informatie over het [maken van Azure Resource Manager sjablonen met Visual Studio code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../templates/deploy-cli.md) voor meer informatie.

## <a name="deploy-a-resource-group-and-resources"></a>Een resource groep en-resources implementeren

U kunt een resource groep maken en resources implementeren voor de groep. Zie [resource groep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-groups)voor meer informatie.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Resources implementeren voor meerdere abonnementen of resource groepen

Doorgaans implementeert u alle resources in uw sjabloon tot één resource groep. Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze wilt plaatsen in verschillende resource groepen of-abonnementen. Zie [Azure-resources implementeren voor meerdere abonnementen of resource groepen](../templates/deploy-to-resource-group.md)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

Het volgende script toont hoe u een opslag account verwijdert.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Zie [Azure Resource Manager resource groep verwijderen](delete-resource-group.md)voor meer informatie over de manier waarop Azure Resource Manager het verwijderen van resources ordent.

## <a name="move-resources"></a>Resources verplaatsen

Het volgende script toont hoe u een opslag account van een resource groep kunt verwijderen naar een andere resource groep.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergren delen voor komt dat andere gebruikers in uw organisatie per ongeluk essentiële resources verwijderen of wijzigen, zoals een Azure-abonnement, resource groep of resource. 

Met het volgende script wordt een opslag account vergrendeld zodat het account niet kan worden verwijderd.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Met het volgende script worden alle vergren delingen voor een opslag account opgehaald:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Met het volgende script wordt een vergren deling van een opslag account verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Door labels kunt u de resource groep en resources logisch ordenen. Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md#azure-cli)voor meer informatie.

## <a name="manage-access-to-resources"></a>Toegang tot resources beheren

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot resources in azure beheert. Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure cli](../../role-based-access-control/role-assignments-cli.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Resource Manager](overview.md)voor meer informatie Azure Resource Manager.
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](../templates/template-syntax.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](../index.yml)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.