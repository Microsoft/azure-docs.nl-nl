---
title: 'Azure CLI: niet-gekoppelde beheerde en onbeheerde schijven zoeken en verwijderen'
description: Het zoeken en verwijderen van niet-gekoppelde, door Azure beheerde en onbeheerde (Vhd's/pagina-blobs) schijven met behulp van Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016213"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Niet-gekoppelde door Azure beheerde en onbeheerde schijven zoeken en verwijderen met de Azure CLI
Wanneer u een virtuele machine (VM) in azure verwijdert, worden schijven die zijn gekoppeld aan de VM standaard niet verwijderd. Deze functie helpt gegevens verlies te voor komen vanwege de onbedoelde verwijdering van Vm's. Nadat een virtuele machine is verwijderd, blijft u betalen voor niet-gekoppelde schijven. In dit artikel leest u hoe u niet-gekoppelde schijven kunt zoeken en verwijderen en overbodige kosten kunt verlagen. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Managed disks: niet-gekoppelde schijven zoeken en verwijderen 

Het volgende script zoekt naar niet-gekoppelde [beheerde schijven](../managed-disks-overview.md) door de waarde van de eigenschap **ManagedBy** te controleren. Wanneer een beheerde schijf is gekoppeld aan een virtuele machine, bevat de eigenschap **ManagedBy** de resource-id van de virtuele machine. Wanneer een beheerde schijf niet is gekoppeld, is de eigenschap **ManagedBy** null. Het script onderzoekt alle beheerde schijven in een Azure-abonnement. Wanneer het script een beheerde schijf zoekt waarvan de eigenschap **ManagedBy** is ingesteld op NULL, wordt door het script bepaald dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedDisks** in te stellen op 0. Met deze actie kunt u alle niet-gekoppelde beheerde schijven zoeken en weer geven.
>
>Nadat u alle niet-gekoppelde schijven hebt gecontroleerd, voert u het script opnieuw uit en stelt u de **deleteUnattachedDisks** -variabele in op 1. Met deze actie kunt u alle niet-gekoppelde beheerde schijven verwijderen.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niet-beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen 

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als [pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure-opslag accounts](../../storage/common/storage-account-overview.md). Het volgende script zoekt naar niet-gekoppelde niet-beheerde schijven (pagina-blobs) door de waarde van de eigenschap **LeaseStatus** te controleren. Wanneer een niet-beheerde schijf is gekoppeld aan een virtuele machine, wordt de eigenschap **LeaseStatus** ingesteld op **vergrendeld**. Wanneer een onbeheerde schijf niet is gekoppeld, wordt de eigenschap **LeaseStatus** ingesteld op **ontgrendeld**. Het script onderzoekt alle niet-beheerde schijven in alle Azure Storage-accounts in een Azure-abonnement. Wanneer het script een onbeheerde schijf zoekt waarvan de eigenschap **LeaseStatus** is ingesteld op **ontgrendeld**, wordt door het script bepaald dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedVHDs** in te stellen op 0. Met deze actie kunt u alle niet-gekoppelde onbeheerde Vhd's vinden en weer geven.
>
>Nadat u alle niet-gekoppelde schijven hebt gecontroleerd, voert u het script opnieuw uit en stelt u de **deleteUnattachedVHDs** -variabele in op 1. Met deze actie kunt u alle niet-gekoppelde onbeheerde Vhd's verwijderen.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Volgende stappen

Zie [een opslag account verwijderen](../../storage/common/storage-account-create.md#delete-a-storage-account)voor meer informatie.