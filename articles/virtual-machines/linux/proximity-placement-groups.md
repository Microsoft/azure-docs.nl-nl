---
title: Een nabijheidsplaatsingsgroep maken met behulp van de Azure CLI
description: Meer informatie over het maken en gebruiken van nabijheidsplaatsingsgroepen voor virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771439"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>VM's implementeren op nabijheidsplaatsingsgroepen met behulp van Azure CLI

Als u VM's zo dicht mogelijk bij elkaar wilt krijgen en zo de laagst mogelijke latentie bereikt, moet u deze implementeren binnen een [nabijheidsplaatsingsgroep.](../co-location.md#proximity-placement-groups)

Een nabijheidsplaatsingsgroep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen zich fysiek dicht bij elkaar bevinden. Nabijheidsplaatsingsgroepen zijn handig voor workloads waarbij lage latentie een vereiste is.


## <a name="create-the-proximity-placement-group"></a>De nabijheidsplaatsingsgroep maken
Maak een nabijheidsplaatsingsgroep met [`az ppg create`](/cli/azure/ppg#az_ppg_create) behulp van . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen op een lijst plaatsen

U kunt al uw nabijheidsplaatsingsgroepen in een lijst plaatsen [met az ppg list](/cli/azure/ppg#az_ppg_list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM binnen de nabijheidsplaatsingsgroep met behulp [van de nieuwe az vm](/cli/azure/vm#az_vm_create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

U kunt de VM in de nabijheidsplaatsingsgroep zien met [az ppg show](/cli/azure/ppg#az_ppg_show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Beschikbaarheidssets
U kunt ook een beschikbaarheidsset maken in uw nabijheidsplaatsingsgroep. Gebruik dezelfde `--ppg` parameter met az [vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) om een beschikbaarheidsset te maken en alle VM's in de beschikbaarheidsset worden ook in dezelfde nabijheidsplaatsingsgroep gemaakt.

## <a name="scale-sets"></a>Schaalsets

U kunt ook een schaalset maken in uw nabijheidsplaatsingsgroep. Gebruik dezelfde parameter met az vmss create om een schaalset te maken. Alle exemplaren worden in dezelfde `--ppg` nabijheidsplaatsingsgroep gemaakt. [](/cli/azure/vmss#az_vmss_create)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure CLI-opdrachten](/cli/azure/ppg) voor nabijheidsplaatsingsgroepen.