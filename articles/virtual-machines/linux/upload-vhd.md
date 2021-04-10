---
title: Een aangepaste Linux-VM uploaden of kopiëren met Azure CLI
description: Een aangepaste virtuele machine uploaden of kopiëren met behulp van het Resource Manager-implementatie model en de Azure CLI
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564608"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Een virtuele Linux-machine maken op basis van een aangepaste schijf met de Azure CLI

<!-- rename to create-vm-specialized -->

In dit artikel wordt beschreven hoe u een aangepaste virtuele harde schijf (VHD) uploadt en hoe u een bestaande VHD in azure kopieert. De zojuist gemaakte VHD wordt vervolgens gebruikt voor het maken van nieuwe virtuele Linux-machines (Vm's). U kunt een Linux-distributie installeren en configureren op uw vereisten en vervolgens die VHD gebruiken om een nieuwe virtuele Azure-machine te maken.

Als u meerdere virtuele machines vanaf uw aangepaste schijf wilt maken, moet u eerst een installatie kopie maken op basis van uw virtuele machine of VHD. Zie [een aangepaste installatie kopie van een virtuele Azure-machine maken met behulp van de CLI](tutorial-custom-images.md)voor meer informatie.

U hebt twee opties voor het maken van een aangepaste schijf:
* Een VHD uploaden
* Een bestaande virtuele machine van Azure kopiëren


## <a name="requirements"></a>Vereisten
Als u de volgende stappen wilt uitvoeren, moet u:

- Een virtuele Linux-machine die is voor bereid voor gebruik in Azure. In het gedeelte [de VM voorbereiden](#prepare-the-vm) van dit artikel wordt beschreven hoe u distributie informatie kunt vinden over het installeren van de Azure Linux-agent (waagent), die nodig is om verbinding te maken met een virtuele machine via SSH.
- Het VHD-bestand van een bestaande door [Azure goedgekeurde Linux-distributie](endorsed-distros.md) (of Zie [informatie over niet-goedgekeurde distributies](create-upload-generic.md)) naar een virtuele schijf in de VHD-indeling. Er zijn meerdere hulpprogram ma's voor het maken van een virtuele machine en VHD:
  - Installeer en configureer [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) of [KVM](https://www.linux-kvm.org/page/RunningKVM), waarbij u gebruik maakt van VHD als uw installatie kopie-indeling. Als dat nodig is, kunt u [een installatie kopie converteren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) met `qemu-img convert` .
  - U kunt ook Hyper-V gebruiken [in Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) of [op Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> De nieuwere VHDX-indeling wordt niet ondersteund in Azure. Wanneer u een virtuele machine maakt, geeft u VHD op als de indeling. Indien nodig kunt u VHDX-schijven converteren naar VHD met [qemu-img Convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) of de [Convert-VHD](/powershell/module/hyper-v/convert-vhd) Power shell-cmdlet. Azure biedt geen ondersteuning voor het uploaden van dynamische Vhd's. Daarom moet u dergelijke schijven converteren naar statische Vhd's voordat u deze uploadt. U kunt hulpprogram ma's zoals [Azure VHD-Hulpprogram ma's gebruiken voor](https://github.com/Microsoft/azure-vhd-utils-for-go) het converteren van dynamische schijven tijdens het proces van het uploaden naar Azure.
> 
> 


- Zorg ervoor dat de nieuwste [Azure cli](/cli/azure/install-az-cli2) is geïnstalleerd en dat u bent aangemeld bij een Azure-account met [AZ login](/cli/azure/reference-index#az-login).

Vervang in de volgende voor beelden voorbeeld parameter namen met uw eigen waarden, zoals `myResourceGroup` , `mystorageaccount` en `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden

Azure ondersteunt diverse Linux-distributies (Zie [goedgekeurde distributies](endorsed-distros.md)). In de volgende artikelen wordt beschreven hoe u de verschillende Linux-distributies voorbereidt die worden ondersteund in Azure:

* [CentOS-distributies](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES en OpenSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Overige: niet-goedgekeurde distributies](create-upload-generic.md)

Zie ook de [installatie notities van Linux](create-upload-generic.md#general-linux-installation-notes) voor meer algemene tips over het voorbereiden van Linux-installatie kopieën voor Azure.

> [!NOTE]
> De [Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/) van het Azure-platform is alleen van toepassing op Vm's waarop Linux wordt uitgevoerd wanneer een van de getekende distributies wordt gebruikt met de configuratie details zoals opgegeven onder ondersteunde versies in [Linux op Azure-Endorsed distributies](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Optie 1: een VHD uploaden

U kunt nu VHD direct uploaden naar een beheerde schijf. Zie [een VHD uploaden naar Azure met behulp van Azure cli](disks-upload-vhd-to-managed-disk-cli.md)voor instructies.

## <a name="option-2-copy-an-existing-vm"></a>Optie 2: een bestaande VM kopiëren

U kunt ook een aangepaste virtuele machine in azure maken en vervolgens de besturingssysteem schijf kopiëren en deze koppelen aan een nieuwe virtuele machine om een andere kopie te maken. Dit is prima voor het testen, maar als u een bestaande Azure-VM als model voor meerdere nieuwe Vm's wilt gebruiken, maakt u in plaats daarvan een *installatie kopie* . Zie [een aangepaste installatie kopie van een virtuele Azure-machine maken met behulp van de CLI](tutorial-custom-images.md)voor meer informatie over het maken van een installatie kopie van een bestaande virtuele machine van Azure.

Als u een bestaande VM wilt kopiëren naar een andere regio, wilt u mogelijk azcopy gebruiken om [een kopie van een schijf in een andere regio](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)te bevinden. 

Anders moet u een moment opname van de virtuele machine maken en vervolgens een nieuwe VHD met een besturings systeem van de moment opname aanmaken.

### <a name="create-a-snapshot"></a>Een momentopname maken

In dit voor beeld maakt u een moment opname van een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup* en maakt u een moment opname met de naam *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>De beheerde schijf maken

Een nieuwe beheerde schijf maken op basis van de moment opname.

De ID van de moment opname ophalen. In dit voor beeld heeft de moment opname de naam *osDiskSnapshot* en bevindt deze zich in de resource groep *myResourceGroup* .

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Maak de beheerde schijf. In dit voor beeld maken we een beheerde schijf met de naam *myManagedDisk* in de moment opname, waar de schijf zich in de standaard opslag bevindt en de grootte van 128 GB heeft.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>De VM maken

Maak een virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create) en attach (--attach-OS-disk) de beheerde schijf als de besturingssysteem schijf. In het volgende voor beeld wordt een VM gemaakt met de naam *myNewVM* met behulp van de beheerde schijf die u hebt gemaakt op basis van de GEÜPLOADe VHD

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

U moet met de referenties van de bron-VM een SSH-verbinding met de virtuele machine kunnen maken. 

## <a name="next-steps"></a>Volgende stappen
Nadat u uw aangepaste virtuele schijf hebt voor bereid en geüpload, kunt u meer lezen over het [gebruik van Resource Manager en sjablonen](../../azure-resource-manager/management/overview.md). Het is ook mogelijk dat u [een gegevens schijf wilt toevoegen](add-disk.md) aan de nieuwe virtuele machines. Als u toepassingen hebt die worden uitgevoerd op uw Vm's die u wilt openen, moet u [poorten en eind punten openen](nsg-quickstart.md).
