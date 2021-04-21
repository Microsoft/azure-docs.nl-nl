---
title: Een momentopname van een VHD maken met behulp van de Azure CLI
description: Meer informatie over het maken van een kopie van een VHD in Azure als back-up of voor het oplossen van problemen.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ab19bb1c6cc43334a3d0d427b6aff6ced2d6cc69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789659"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Een momentopname maken met behulp van de portal of Azure CLI

Maak een momentopname van een besturingssysteem of gegevensschijf voor back-up of om VM-problemen op te lossen. Een momentopname is een volledige, alleen-lezen kopie van een VHD. 

## <a name="use-azure-cli"></a>Azure CLI gebruiken 

In het volgende voorbeeld moet u [een](https://shell.azure.com/bash) Cloud Shell of de Azure CLI hebben geïnstalleerd.

De volgende stappen laten zien hoe u een momentopname maakt met behulp van **de opdracht az snapshot create** met de parameter **--source-disk.** In het volgende voorbeeld wordt ervan uitgenomen dat de resourcegroep *myResourceGroup* een VM met de naam *myVM* heeft.

Haal de schijf-id op [met az vm show](/cli/azure/vm#az_vm_show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Maak een momentopname met de *naam osDisk-backup met* [az snapshot create.](/cli/azure/snapshot#az_snapshot_create)

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Als u uw momentopname wilt opslaan in zone-flexibele opslag, moet [](../../availability-zones/az-overview.md) u deze maken in een regio die beschikbaarheidszones ondersteunt en de parameter **--sku Standard_ZRS** opnemen.

U kunt een lijst met de momentopnamen bekijken met [az snapshot list](/cli/azure/snapshot#az_snapshot_list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure Portal gebruiken 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in de linkerbovenhoek op **Een resource maken** en zoek naar **momentopname**. Selecteer **Momentopname** in de zoekresultaten.
3. Klik op **de** blade Momentopname op **Maken.**
4. Voer een **Naam in** voor de momentopname.
5. Selecteer een bestaande resourcegroep of typ de naam voor een nieuwe. 
7. Bij **Bronschijf selecteert** u de beheerde schijf waarop u een momentopname wilt maken.
8. Selecteer het **accounttype dat u** wilt gebruiken om de momentopname op te slaan. Gebruik **Standard - HDD** tenzij u deze moet opslaan op een ssd met hoge prestaties.
9. Klik op **Create**.


## <a name="next-steps"></a>Volgende stappen

 Maak een virtuele machine van een momentopname door een beheerde schijf te maken op de momentopname en vervolgens de nieuwe beheerde schijf als de besturingssysteemschijf te koppelen. Zie Create a [VM from a snapshot script (Een VM maken van een momentopnamescript) voor meer](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json) informatie.