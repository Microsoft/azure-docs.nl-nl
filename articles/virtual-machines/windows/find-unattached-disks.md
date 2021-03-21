---
title: Niet-aangesloten beheerde en niet-beheerde Azure-schijven zoeken en verwijderen
description: Het zoeken en verwijderen van niet-gekoppelde, door Azure beheerde en onbeheerde en niet-beheerde schijven (Vhd's/pagina-blobs) met behulp van Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554375"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Niet-aangesloten beheerde en niet-beheerde Azure-schijven zoeken en verwijderen

Wanneer u een virtuele machine (VM) in azure verwijdert, worden schijven die zijn gekoppeld aan de VM standaard niet verwijderd. Deze functie helpt gegevens verlies te voor komen vanwege de onbedoelde verwijdering van Vm's. Nadat een virtuele machine is verwijderd, blijft u betalen voor niet-gekoppelde schijven. In dit artikel leest u hoe u niet-gekoppelde schijven kunt zoeken en verwijderen en overbodige kosten kunt verlagen.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Managed disks: niet-gekoppelde schijven zoeken en verwijderen

Het volgende script zoekt naar niet-gekoppelde [beheerde schijven](../managed-disks-overview.md) door de waarde van de eigenschap **ManagedBy** te controleren. Wanneer een beheerde schijf is gekoppeld aan een virtuele machine, bevat de eigenschap **ManagedBy** de resource-id van de virtuele machine. Wanneer een beheerde schijf niet is gekoppeld, is de eigenschap **ManagedBy** null. Het script onderzoekt alle beheerde schijven in een Azure-abonnement. Wanneer het script een beheerde schijf zoekt waarvan de eigenschap **ManagedBy** is ingesteld op NULL, wordt door het script bepaald dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedDisks** in te stellen op 0. Met deze actie kunt u alle niet-gekoppelde beheerde schijven zoeken en weer geven.
>
>Nadat u alle niet-gekoppelde schijven hebt gecontroleerd, voert u het script opnieuw uit en stelt u de **deleteUnattachedDisks** -variabele in op 1. Met deze actie kunt u alle niet-gekoppelde beheerde schijven verwijderen.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niet-beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als [pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure-opslag accounts](../../storage/common/storage-account-overview.md). Het volgende script zoekt naar niet-gekoppelde niet-beheerde schijven (pagina-blobs) door de waarde van de eigenschap **LeaseStatus** te controleren. Wanneer een niet-beheerde schijf is gekoppeld aan een virtuele machine, wordt de eigenschap **LeaseStatus** ingesteld op **vergrendeld**. Wanneer een onbeheerde schijf niet is gekoppeld, wordt de eigenschap **LeaseStatus** ingesteld op **ontgrendeld**. Het script onderzoekt alle niet-beheerde schijven in alle Azure Storage-accounts in een Azure-abonnement. Wanneer het script een onbeheerde schijf zoekt waarvan de eigenschap **LeaseStatus** is ingesteld op **ontgrendeld**, wordt door het script bepaald dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedVHDs** in te stellen op `$false` . Met deze actie kunt u alle niet-gekoppelde onbeheerde Vhd's vinden en weer geven.
>
>Nadat u alle niet-gekoppelde schijven hebt gecontroleerd, voert u het script opnieuw uit en stelt u de **deleteUnattachedVHDs** -variabele in op `$true` . Met deze actie kunt u alle niet-gekoppelde onbeheerde Vhd's verwijderen.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [een opslag account verwijderen](../../storage/common/storage-account-create.md#delete-a-storage-account) en [zwevende schijven identificeren met Power shell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) voor meer informatie.