---
title: Wisselen tussen besturingssysteemschijven met behulp van de Azure CLI '
description: Wijzig de besturingssysteemschijf die wordt gebruikt door een virtuele Azure-machine met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c9b0c1948dc4ecef74cd78ec1736803a0c0b4bc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497367"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>De besturingssysteemschijf wijzigen die wordt gebruikt door een azure-VM met behulp van de Azure CLI


Als u een bestaande VM hebt, maar u de schijf wilt wisselen voor een back-upschijf of een andere besturingssysteemschijf, kunt u de Azure CLI gebruiken om de besturingssysteemschijven te wisselen. U hoeft de VM niet te verwijderen en opnieuw te maken. U kunt zelfs een beheerde schijf in een andere resourcegroep gebruiken, zolang deze nog niet in gebruik is.

De VM moet worden gestopt\de toewijzing moet worden verbreed. Vervolgens kan de resource-id van de beheerde schijf worden vervangen door de resource-id van een andere beheerde schijf. 

Zorg ervoor dat de VM-grootte en het opslagtype compatibel zijn met de schijf die u wilt koppelen. Als de schijf die u wilt gebruiken zich bijvoorbeeld in Premium Storage, moet de virtuele Premium Storage kunnen gebruiken (zoals een DS-serie).

Voor dit artikel is Azure CLI versie 2.0.25 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 


Gebruik [az disk list om](/cli/azure/disk) een lijst met schijven in uw resourcegroep op te halen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Gebruik [az vm stop om](/cli/azure/vm) de VM te stoppen\de toewijzing ervan te herstellen voordat u de schijven verwisselt.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Gebruik [az vm update met](/cli/azure/vm#az-vm-update) de volledige resource-id van de nieuwe schijf voor de `--osdisk` parameter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Start de VM opnieuw op [met az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Volgende stappen**

Zie Momentopname maken van een schijf voor het [maken van een kopie van een schijf.](snapshot-copy-managed-disk.md)
