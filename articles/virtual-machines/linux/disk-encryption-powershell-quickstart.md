---
title: Een Linux-VM maken en versleutelen met Azure PowerShell
description: In deze quickstart leert u hoe u Azure PowerShell gebruikt om een virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1833b25554df53a480af9a8fc643755dbc2bbdb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564676"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Quickstart: Een Linux-VM maken en versleutelen in Azure met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. In deze quickstart ziet u hoe u de module Azure PowerShell kunt gebruiken om een virtuele Linux-machine (VM) te maken, een sleutelkluis te maken voor de opslag van versleutelingssleutels en de virtuele machine te versleutelen. In deze quickstart wordt de Marketplace-installatiekopie voor Ubuntu 16.04 LTS van Canonical en een VM Standard_D2S_V3 gebruikt. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele Azure-machine met [New-AzVM](/powershell/module/az.compute/new-azvm) en geef het VM-configuratieobject hieraan door dat u hierboven hebt gemaakt.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een sleutelkluis maken die is geconfigureerd voor versleutelingssleutels

De versleutelingssleutel van Azure Disk Encryption wordt opgeslagen in een Azure-sleutelkluis. Maak een sleutelkluis met [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Gebruik de parameter -EnabledForDiskEncryption om de sleutelkluis in te schakelen voor het opslaan van versleutelingssleutels.

> [!Important]
> Elke sleutelkluis moet een naam hebben die uniek is binnen Azure. Vervang in de onderstaande voorbeelden <uw-unieke-sleutelkluisnaam> door de naam die u kiest.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw VM met [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Voor Set-AzVmDiskEncryptionExtension zijn bepaalde waarden uit uw sleutelkluisobject nodig. U kunt deze waarden ophalen door de unieke naam van uw sleutelkluis door te geven aan [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Na een paar minuten wordt het volgende door het proces geretourneerd:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

U kunt het versleutelingsproces controleren door [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)uit te voeren.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Wanneer versleuteling is ingeschakeld, ziet u het volgende in de geretourneerde uitvoer:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele machine gemaakt, een sleutelkluis gemaakt die is ingeschakeld voor versleutelingssleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over Azure Disk Encryption voor Linux-VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
