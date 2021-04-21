---
title: Een aangepaste linux-VM uploaden of kopiëren met Azure CLI
description: Een aangepaste virtuele machine uploaden of kopiëren met behulp van het Resource Manager implementatiemodel en de Azure CLI
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
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759595"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Een linux-VM maken op een aangepaste schijf met de Azure CLI

<!-- rename to create-vm-specialized -->

In dit artikel wordt beschreven hoe u een aangepaste virtuele harde schijf (VHD) uploadt en hoe u een bestaande VHD in Azure kopieert. De zojuist gemaakte VHD wordt vervolgens gebruikt om nieuwe virtuele Linux-machines (VM's) te maken. U kunt een Linux-distributie installeren en configureren om aan uw vereisten te voldoen en vervolgens die VHD gebruiken om een nieuwe virtuele Azure-machine te maken.

Als u meerdere VM's wilt maken van uw aangepaste schijf, maakt u eerst een afbeelding van uw VM of VHD. Zie Create a custom image of an Azure VM by using the CLI (Een [aangepaste afbeelding van een Azure-VM maken met behulp van de CLI) voor meer informatie.](tutorial-custom-images.md)

U hebt twee opties om een aangepaste schijf te maken:
* Een VHD uploaden
* Een bestaande Azure-VM kopiëren


## <a name="requirements"></a>Vereisten
Als u de volgende stappen wilt voltooien, hebt u het volgende nodig:

- Een virtuele Linux-machine die is voorbereid voor gebruik in Azure. In de sectie [De VM](#prepare-the-vm) voorbereiden van dit artikel wordt beschreven hoe u distributiespecifieke informatie vindt over het installeren van de Azure Linux-agent (waagent), die u nodig hebt om via SSH verbinding te maken met een VM.
- Het VHD-bestand van een bestaande Door Azure goedgekeurde [Linux-distributie](endorsed-distros.md) (of zie informatie voor [niet-goedgekeurde distributies)](create-upload-generic.md)naar een virtuele schijf in de VHD-indeling. Er bestaan meerdere hulpprogramma's om een VM en VHD te maken:
  - Installeer en configureer [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) of [KVM,](https://www.linux-kvm.org/page/RunningKVM)en zorg dat u VHD gebruikt als uw installatierindeling. Indien nodig kunt u een [afbeelding converteren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) met `qemu-img convert` .
  - U kunt hyper-V ook gebruiken [op Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) of op Windows [Server 2012/2012 R2.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

> [!NOTE]
> De nieuwere VHDX-indeling wordt niet ondersteund in Azure. Wanneer u een VM maakt, geeft u VHD op als de indeling. Indien nodig kunt u VHDX-schijven converteren naar VHD met [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) of de [PowerShell-cmdlet Convert-VHD.](/powershell/module/hyper-v/convert-vhd) Azure biedt geen ondersteuning voor het uploaden van dynamische VHD's, dus u moet dergelijke schijven converteren naar statische VHD's voordat u uploadt. U kunt hulpprogramma's zoals [Azure VHD-hulpprogramma's](https://github.com/Microsoft/azure-vhd-utils-for-go) voor GO gebruiken om dynamische schijven te converteren tijdens het uploaden naar Azure.
> 
> 


- Zorg ervoor dat u de nieuwste [versie van Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account met az [login](/cli/azure/reference-index#az_login).

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden, `myResourceGroup` zoals `mystorageaccount` , en `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden

Azure ondersteunt verschillende Linux-distributies (zie [Goedgekeurde distributies).](endorsed-distros.md) In de volgende artikelen wordt beschreven hoe u de verschillende Linux-distributies voorbereidt die worden ondersteund in Azure:

* [CentOS-distributies](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES en OpenSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Overig: niet-goedgekeurde distributies](create-upload-generic.md)

Zie ook de [Opmerkingen bij de installatie van Linux](create-upload-generic.md#general-linux-installation-notes) voor meer algemene tips over het voorbereiden van Linux-installatie images voor Azure.

> [!NOTE]
> De SLA van het [Azure-platform](https://azure.microsoft.com/support/legal/sla/virtual-machines/) is alleen van toepassing op VM's met Linux wanneer een van de goedgekeurde distributies wordt gebruikt met de configuratiegegevens zoals opgegeven onder Ondersteunde versies in Linux op [Azure-Endorsed-distributies.](endorsed-distros.md)
> 
> 

## <a name="option-1-upload-a-vhd"></a>Optie 1: Een VHD uploaden

U kunt nu VHD rechtstreeks uploaden naar een beheerde schijf. Zie Upload [a VHD to Azure using Azure CLI (Een VHD uploaden naar Azure met behulp van Azure CLI) voor instructies.](disks-upload-vhd-to-managed-disk-cli.md)

## <a name="option-2-copy-an-existing-vm"></a>Optie 2: Een bestaande VM kopiëren

U kunt ook een aangepaste VM maken in Azure en vervolgens de besturingssysteemschijf kopiëren en deze koppelen aan een nieuwe VM om nog een kopie te maken. Dit is prima om te testen, maar als u een bestaande Azure-VM als model wilt gebruiken voor meerdere nieuwe VM's, maakt u in plaats daarvan *een* afbeelding. Zie Create a custom image of an Azure VM by using the CLI (Een aangepaste azure-VM maken met behulp van de CLI) voor meer informatie over het maken van een afbeelding op basis [van een bestaande Azure-VM.](tutorial-custom-images.md)

Als u een bestaande VM naar een andere regio wilt kopiëren, kunt u azcopy gebruiken om een kopie van een schijf in een [andere regio te maken.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Anders moet u een momentopname van de VM maken en vervolgens een nieuwe VHD voor het besturingssysteem maken van de momentopname.

### <a name="create-a-snapshot"></a>Een momentopname maken

In dit voorbeeld wordt een momentopname gemaakt van een VM met de naam *myVM* in resourcegroep *myResourceGroup* en wordt een momentopname gemaakt met de *naam osDiskSnapshot.*

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>De beheerde schijf maken

Maak een nieuwe beheerde schijf op de momentopname.

Haal de id van de momentopname op. In dit voorbeeld heeft de momentopname *de naam osDiskSnapshot* en staat deze in de resourcegroep *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Maak de beheerde schijf. In dit voorbeeld maken we een beheerde schijf met de naam *myManagedDisk* op basis van onze momentopname, waarbij de schijf in standaardopslag is en een grootte heeft van 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>De VM maken

Maak uw VM [met az vm create](/cli/azure/vm#az_vm_create) en koppel (--attach-os-disk) de beheerde schijf als de besturingssysteemschijf. In het volgende voorbeeld wordt een VM met de *naam myNewVM gemaakt* met behulp van de beheerde schijf die u hebt gemaakt op basis van uw geüploade VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

U moet met de referenties van de bron-VM SSH kunnen gebruiken voor de VM. 

## <a name="next-steps"></a>Volgende stappen
Nadat u uw aangepaste virtuele schijf hebt voorbereid en geüpload, kunt u meer lezen over het [gebruik Resource Manager en sjablonen.](../../azure-resource-manager/management/overview.md) U kunt ook een [gegevensschijf toevoegen aan](add-disk.md) uw nieuwe VM's. Als er toepassingen worden uitgevoerd op uw VM's die u wilt openen, moet u [poorten en eindpunten openen.](nsg-quickstart.md)
