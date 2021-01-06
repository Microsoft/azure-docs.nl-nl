---
title: End-to-end-versleuteling inschakelen met behulp van versleuteling op host-Azure CLI-Managed disks
description: Gebruik versleuteling op host om end-to-end versleuteling in te scha kelen op uw Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 6b53f9b9599a899a23c5d243c345f48d7f17748c
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916385"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>De Azure CLI gebruiken om end-to-end versleuteling in te scha kelen met behulp van versleuteling op de host

Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Zie [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)voor conceptuele informatie over versleuteling op de host, evenals andere beheerde schijf versleutelings typen.

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Mogelijk vindt u ook de VM-grootten via een programma. Zie de sectie [ondersteunde VM-grootten zoeken](#finding-supported-vm-sizes) voor meer informatie over het ophalen van deze Program ma's.

## <a name="prerequisites"></a>Vereisten

Als u versleuteling wilt gebruiken op de host voor uw Vm's of virtuele-machine schaal sets, moet u de functie inschakelen voor uw abonnement. Stuur een e-mail naar encryptionAtHost@microsoft.com met uw abonnement-id's om de functie in te scha kelen voor uw abonnementen.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Een Azure Key Vault en DiskEncryptionSet maken

Zodra de functie is ingeschakeld, moet u een Azure Key Vault en een DiskEncryptionSet instellen, als u dat nog niet hebt gedaan.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Voorbeelden

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Maak een virtuele machine met versleuteling op host die is ingeschakeld met door de klant beheerde sleutels. 

Maak een virtuele machine met Managed disks met behulp van de resource-URI van de DiskEncryptionSet die eerder is gemaakt voor het versleutelen van de cache van besturings systemen en gegevens schijven met door de klant beheerde sleutels De tijdelijke schijven worden versleuteld met door het platform beheerde sleutels. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Maak een virtuele machine met versleuteling op host die is ingeschakeld met door het platform beheerde sleutels. 

Maak een virtuele machine met versleuteling op de host en schakel deze in voor het versleutelen van cache van besturings systeem/gegevens schijven en tijdelijke schijven met door het platform beheerde sleutels. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Werk een virtuele machine bij om versleuteling op de host in te scha kelen. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Controleer de status van versleuteling op de host voor een VM

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Een schaalset voor virtuele machines maken met versleuteling op host die is ingeschakeld met door de klant beheerde sleutels. 

Maak een schaalset voor virtuele machines met Managed disks met behulp van de resource-URI van de DiskEncryptionSet die eerder is gemaakt voor het versleutelen van de cache van besturings systemen en gegevens schijven met door de klant beheerde sleutels. De tijdelijke schijven worden versleuteld met door het platform beheerde sleutels. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Een schaalset voor virtuele machines maken met versleuteling op host die is ingeschakeld met door het platform beheerde sleutels. 

Een schaalset voor virtuele machines maken met versleuteling op de host ingeschakeld voor het versleutelen van cache van OS/gegevens schijven en tijdelijke schijven met door het platform beheerde sleutels. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Een schaalset voor virtuele machines bijwerken om versleuteling op de host in te scha kelen. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Controleer de status van versleuteling op de host voor een schaalset voor virtuele machines

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Ondersteunde VM-grootten zoeken

Verouderde VM-grootten worden niet ondersteund. U kunt de lijst met ondersteunde VM-grootten vinden door een van de volgende opties:

De [resource-sku's-API](/rest/api/compute/resourceskus/list) wordt aangeroepen en er wordt gecontroleerd of de `EncryptionAtHostSupported` mogelijkheid is ingesteld op **waar**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

U kunt ook de Power shell [-cmdlet Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0&preserve-view=true) aanroepen.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u deze resources hebt gemaakt en geconfigureerd, kunt u deze gebruiken om uw beheerde schijven te beveiligen. De volgende koppeling bevat voorbeeld scripts, elk met een eigen scenario, dat u kunt gebruiken om uw beheerde schijven te beveiligen.

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
