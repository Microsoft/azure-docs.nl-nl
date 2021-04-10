---
title: Het formaat van een virtuele Linux-machine wijzigen met de Azure CLI
description: U kunt een virtuele Linux-machine omhoog of omlaag schalen door de VM-grootte te wijzigen.
author: DavidCBerry13
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.collection: linux
ms.openlocfilehash: 290c42cadd840e5a292201247b1555059b5b4381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556635"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Het formaat van een virtuele Linux-machine wijzigen met behulp van Azure CLI 

Nadat u een virtuele machine (VM) hebt ingericht, kunt u de VM omhoog of omlaag schalen door de [VM-grootte][vm-sizes]te wijzigen. In sommige gevallen moet u eerst de toewijzing van de VM ongedaan maken. U moet de toewijzing van de virtuele machine ongedaan maken als de gewenste grootte niet beschikbaar is op het hardwareplatform dat als host fungeert voor de virtuele machine. In dit artikel wordt beschreven hoe u het formaat van een virtuele Linux-machine kunt wijzigen met de Azure CLI. 

## <a name="resize-a-vm"></a>De grootte van een virtuele machine wijzigen
Als u de grootte van een virtuele machine wilt wijzigen, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

1. Bekijk de lijst met beschik bare VM-grootten op het hardware-cluster waarop de virtuele machine wordt gehost met [AZ VM List-VM-resize-Options](/cli/azure/vm). Het volgende voor beeld bevat een lijst met VM-grootten voor de virtuele machine `myVM` met de naam in de regio van de resource groep `myResourceGroup` :
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Als de gewenste VM-grootte wordt weer gegeven, wijzigt u het formaat van de virtuele machine met [AZ VM resize](/cli/azure/vm). In het volgende voor beeld wordt de grootte van de virtuele machine `myVM` met de naam `Standard_DS3_v2` gewijzigd in:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    De virtuele machine wordt opnieuw opgestart tijdens dit proces. Na het opnieuw opstarten worden uw bestaande besturings systeem en de gegevens schijven opnieuw toegewezen. Alles op de tijdelijke schijf gaat verloren.

3. Als de gewenste VM-grootte niet wordt weer gegeven, moet u eerst de toewijzing van de virtuele machine met [AZ VM disallocate](/cli/azure/vm)ongedaan maken. Met dit proces kan de VM vervolgens worden verg root of verkleind tot elke beschik bare grootte die beschikbaar is voor de regio en vervolgens wordt gestart. Met de volgende stappen wordt de toewijzing van de virtuele machine `myVM` in de resource groep met de naam gewijzigd, wordt het formaat ervan gewijzigd en wordt de VM opnieuw gestart `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Als u de toewijzing van de virtuele machine ongedaan hebt, worden er ook dynamische IP-adressen vrijgegeven die aan de virtuele machine Het besturings systeem en de gegevens schijven worden niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen
Voor extra schaal baarheid voert u meerdere VM-exemplaren uit en uitschalen. Zie [Linux-machines automatisch schalen in een schaalset voor virtuele machines][scale-set]voor meer informatie. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
