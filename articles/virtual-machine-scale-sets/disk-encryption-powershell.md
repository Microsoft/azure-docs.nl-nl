---
title: Schijven voor Azure-schaal sets versleutelen met Azure PowerShell
description: Meer informatie over het gebruik van Azure PowerShell voor het versleutelen van VM-exemplaren en gekoppelde schijven in een Windows-schaalset voor virtuele machines
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519568"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Besturings systeem en gekoppelde gegevens schijven versleutelen in een schaalset voor virtuele machines met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts.  In dit artikel leest u hoe u Azure PowerShell kunt gebruiken om een schaalset voor virtuele machines te maken en te versleutelen. Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md)voor meer informatie over het Toep assen van Azure Disk Encryption op een schaalset met virtuele machines.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Een Azure Key Vault maken die is ingeschakeld voor schijf versleuteling

Azure Key Vault kan sleutels, geheimen of wachtwoorden opslaan waarmee u deze veilig kunt implementeren in uw toepassingen en services. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u kunt uw sleutels in Hardware Security Modules (HMS's), die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden, importeren of genereren. Deze cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U behoudt de controle over deze cryptografische sleutels en kunt het gebruik controleren.

Maak een Key Vault met [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Stel de para meter *EnabledForDiskEncryption* in om de Key Vault te kunnen gebruiken voor schijf versleuteling. In het volgende voor beeld worden ook variabelen gedefinieerd voor de naam van de resource groep, Key Vault naam en locatie. Geef uw eigen unieke Key Vault naam op:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Een bestaande sleutelkluis gebruiken

Deze stap is alleen vereist als u een bestaande sleutelkluis hebt die u wilt gebruiken met schijfversleuteling. Sla deze stap over als u in de vorige sectie een sleutelkluis hebt gemaakt.

U kunt een bestaand Key Vault in hetzelfde abonnement en dezelfde regio inschakelen als de schaalset voor schijf versleuteling met [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definieer de naam van uw bestaande Key Vault in de *$vaultName* variabele als volgt:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Een schaalset maken

Stel eerst een beheerdersnaam en -wachtwoord in voor de VM-exemplaren met behulp van [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van extern bureaubladverkeer op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Versleuteling inschakelen

Als u VM-exemplaren in een schaalset wilt versleutelen, moet u eerst informatie over de Key Vault URI en de resource-ID ophalen met [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Deze variabelen worden gebruikt om het versleutelings proces vervolgens te starten met [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Wanneer u hierom wordt gevraagd, typt u *y* om door te gaan met het schijf versleutelings proces voor de VM-exemplaren van de schaalset

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Versleuteling inschakelen met KEK om de sleutel in te pakken

U kunt ook een sleutelversleutelingssleutel voor extra beveiliging gebruiken bij het versleutelen van de schaalset voor virtuele machines.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  De syntaxis voor de waarde van de parameter voor disk-encryption-keyvault is de volledige id-tekenreeks:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> De syntaxis voor de waarde van de parameter sleutel-versleuteling-sleutel is de volledige URI naar de KEK, zoals in:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Voortgang van de versleuteling controleren

Gebruik [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)om de status van schijf versleuteling te controleren:


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Wanneer VM-exemplaren worden versleuteld, worden de *EncryptionSummary* -code rapporten *ProvisioningState/geslaagd* , zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen

Als u geen schijven met versleutelde VM-exemplaren meer wilt gebruiken, kunt u versleuteling als volgt uitschakelen met [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) :


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Volgende stappen

- In dit artikel hebt u Azure PowerShell gebruikt voor het versleutelen van een schaalset met virtuele machines. U kunt ook de sjablonen van [Azure cli](disk-encryption-cli.md) of [Azure Resource Manager](disk-encryption-azure-resource-manager.md)gebruiken.
- Als u Azure Disk Encryption wilt toepassen nadat een andere extensie is ingericht, kunt u [extensiereeksen](virtual-machine-scale-sets-extension-sequencing.md) gebruiken.
