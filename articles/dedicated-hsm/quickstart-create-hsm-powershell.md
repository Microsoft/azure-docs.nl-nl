---
title: 'Quickstart: Een Azure Dedicated HSM maken met Azure PowerShell'
description: Een Azure Dedicated HSM maken met Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: d5b6d0399ceb98caf9bdd7bbd725e6d92af0eaa3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537793"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Quickstart: Een Azure Dedicated HSM maken met Azure PowerShell

In dit artikel wordt beschreven hoe u een Azure Dedicated HSM kunt maken met behulp van de PowerShell-module [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm).

## <a name="requirements"></a>Vereisten

* Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Zolang de PowerShell-module **Az.DedicatedHsm** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt met de opgegeven naam en op de opgegeven locatie.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Een toegewezen HSM maken

Als u een toegewezen HSM wilt maken, gebruikt u de cmdlet [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm). In het volgende voorbeeld wordt een toegewezen HSM gemaakt in het opgegeven abonnement.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Verkrijg een toegewezen HSM

Als u informatie wilt ophalen over een bestaande toegewezen HSM, gebruikt u de cmdlet [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm). In het volgende voorbeeld wordt de opgegeven toegewezen HSM opgehaald.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Een toegewezen HSM bijwerken

Als u een toegewezen HSM wilt bijwerken, gebruikt u de cmdlet [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm). In het volgende voorbeeld wordt een toegewezen HSM bijgewerkt in het opgegeven abonnement.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die in dit artikel zijn gemaakt, niet meer nodig zijn, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

### <a name="remove-a-dedicated-hsm"></a>Een toegewezen HSM verwijderen

Als u een toegewezen HSM wilt verwijderen, gebruikt u de cmdlet [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm). In het volgende voorbeeld wordt de opgegeven toegewezen HSM verwijderd.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Dedicated HSM](overview.md).
