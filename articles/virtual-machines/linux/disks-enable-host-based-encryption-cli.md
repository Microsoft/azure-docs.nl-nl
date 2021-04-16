---
title: End-to-end-versleuteling inschakelen met behulp van versleuteling op de host - Azure CLI - beheerde schijven
description: Gebruik versleuteling op de host om end-to-end-versleuteling op uw beheerde Azure-schijven in teschakelen.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533609"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>De Azure CLI gebruiken om end-to-end-versleuteling in teschakelen met behulp van versleuteling op de host

Wanneer u versleuteling op de host inschakelen, worden gegevens die zijn opgeslagen op de VM-host versleuteld at rest en stromen versleuteld naar de Storage-service. Zie Encryption [at host - End-to-end encryption for your VM data](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)(Versleuteling op host - End-to-end-versleuteling voor uw VM-gegevens) voor conceptuele informatie over versleuteling op de host en andere typen versleuteling van beheerde schijven.

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

De volledige lijst met ondersteunde VM-grootten kan programmatisch worden binnengehaald. Raadpleeg de sectie Ondersteunde VM-grootten zoeken voor informatie over het programmatisch ophalen van [deze VM-grootten.](#finding-supported-vm-sizes)
Het upgraden van de VM-grootte resulteert in validatie om te controleren of de nieuwe VM-grootte de functie EncryptionAtHost ondersteunt.

## <a name="prerequisites"></a>Vereisten

U moet de functie voor uw abonnement inschakelen voordat u de eigenschap EncryptionAtHost voor uw VM/VMSS gebruikt. Volg de onderstaande stappen om de functie voor uw abonnement in teschakelen:

1.  Voer de volgende opdracht uit om de functie voor uw abonnement te registreren

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Controleer met behulp van de onderstaande opdracht of de registratietoestand Geregistreerd is (enkele minuten) voordat u de functie uitprompt.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Een Azure Key Vault en DiskEncryptionSet maken

Zodra de functie is ingeschakeld, moet u een Azure Key Vault en een DiskEncryptionSet instellen, als u dat nog niet hebt gedaan.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Voorbeelden

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Maak een VM met versleuteling op de host ingeschakeld met door de klant beheerde sleutels. 

Maak een VM met beheerde schijven met behulp van de resource-URI van de DiskEncryptionSet die u eerder hebt gemaakt om de cache van besturingssysteem- en gegevensschijven te versleutelen met door de klant beheerde sleutels. De tijdelijke schijven worden versleuteld met door het platform beheerde sleutels. 

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

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Maak een VM met versleuteling op de host ingeschakeld met door het platform beheerde sleutels. 

Maak een VM met versleuteling op de host ingeschakeld om de cache van OS-/gegevensschijven en tijdelijke schijven te versleutelen met door het platform beheerde sleutels. 

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

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Werk een VM bij om versleuteling op de host in teschakelen. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>De status van versleuteling op de host voor een VM controleren

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Maak een virtuele-machineschaalset met versleuteling op de host ingeschakeld met door de klant beheerde sleutels. 

Maak een virtuele-machineschaalset met beheerde schijven met behulp van de resource-URI van de DiskEncryptionSet die u eerder hebt gemaakt om de cache van besturingssysteem- en gegevensschijven te versleutelen met door de klant beheerde sleutels. De tijdelijke schijven worden versleuteld met door het platform beheerde sleutels. 

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

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Maak een virtuele-machineschaalset met versleuteling op de host ingeschakeld met door het platform beheerde sleutels. 

Maak een virtuele-machineschaalset met versleuteling op de host ingeschakeld voor het versleutelen van de cache van besturingssysteem-/gegevensschijven en tijdelijke schijven met door het platform beheerde sleutels. 

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

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Werk een virtuele-machineschaalset bij om versleuteling op de host in te stellen. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>De status van versleuteling op de host voor een virtuele-machineschaalset controleren

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Ondersteunde VM-grootten zoeken

Verouderde VM-grootten worden niet ondersteund. U vindt de lijst met ondersteunde VM-grootten op een van de volgende:

Het [aanroepen van de Resource Skus-API](/rest/api/compute/resourceskus/list) en controleren of `EncryptionAtHostSupported` de mogelijkheid is ingesteld op **Waar.**

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

Of roep de PowerShell-cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) aan.

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

Nu u deze resources hebt gemaakt en geconfigureerd, kunt u ze gebruiken om uw beheerde schijven te beveiligen. De volgende koppeling bevat voorbeeldscripts, elk met een respectieve scenario, die u kunt gebruiken om uw beheerde schijven te beveiligen.

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
