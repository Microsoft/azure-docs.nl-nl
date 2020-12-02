---
title: PowerShell-voorbeelden
description: Lijst met Power shell-voor beelden voor Vm's
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: cc11c21eda243df1298286c4745588bc492e955d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498486"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Voor beelden van Azure VM Power shell voor het maken en beheren van virtuele Linux-machines

De volgende tabel bevat koppelingen naar Power shell-voorbeeld scripts voor het maken en beheren van virtuele Linux-machines.

| Script | Beschrijving |
|---|---|
|**Virtuele machines maken**||
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een resource groep, een virtuele machine en alle gerelateerde resources.|
| [Een VM maken waarop Docker is ingeschakeld](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, configureert u deze VM als een docker-host en voert u een NGINX-container uit. |
| [Een VM maken en een configuratiescript uitvoeren](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de aangepaste script extensie van Azure om NGINX te installeren. |
| [Een VM maken waarop WordPress is geïnstalleerd](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de aangepaste script extensie van Azure om WordPress te installeren. |
| [Een VM maken op basis van een beheerde besturingssysteemschijf](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf als besturingssysteemschijf te koppelen. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een momentopname door eerst een beheerde schijf te maken op basis van de momentopname en vervolgens de nieuwe beheerde schijf als besturingssysteemschijf te koppelen. |
|**Opslag beheren**||
| [Een beheerde schijf maken op basis van een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een gespecialiseerde VHD als een besturingssysteemschijf of op basis van een gegevens-VHD als een gegevensschijf in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u beheerde schijf op basis van een momentopname. |
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als een VHD naar een opslagaccount in een andere regio. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee exporteert u de onderliggende VHD van een beheerde schijf naar een opslagaccount in dezelfde of een andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een momentopname van een VHD en gebruikt u die momentopname om snel meerdere identieke beheerde schijven te maken.  |
| [Een momentopname kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee kopieert u een momentopname naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende momentopname. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor-logboeken](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de Log Analytics-agent en registreert u de VM in een Log Analytics-werk ruimte.  |
| [Een beheerde schijf kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee kopieert u een beheerde schijf naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende beheerde schijf.
| [Details verzamelen over alle virtuele machines in een abonnement met PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een CSV met de naam van de virtuele machine, de naam van de resourcegroep, de regio, het virtuele netwerk, het subnet, het privé-IP-adres, het type besturingssysteem en het openbare IP-adres van de virtuele machines in het opgegeven abonnement.
| | |