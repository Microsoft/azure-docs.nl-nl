---
title: Azure-schijven toewijzen aan Windows VM-gast schijven
description: De Azure-schijven bepalen die de gast schijven van een Windows-VM aan.
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 373fd26c36bf2f77de6a376f738bd3caaf735f00
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881869"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Azure-schijven toewijzen aan Windows VM-gast schijven

Mogelijk moet u de Azure-schijven bepalen die de gast schijven van een virtuele machine herstellen. In sommige scenario's kunt u de schijf-of volume grootte vergelijken met de grootte van de gekoppelde Azure-schijven. Als er meerdere Azure-schijven met dezelfde grootte zijn gekoppeld aan de VM, moet u het nummer van de logische eenheid (LUN) van de gegevens schijven gebruiken. 

## <a name="what-is-a-lun"></a>Wat is een LUN?

Een LUN (Logical Unit Number) is een nummer dat wordt gebruikt om een specifiek opslag apparaat te identificeren. Elk opslag apparaat krijgt een unieke numerieke id, beginnend bij nul. Het volledige pad naar een apparaat wordt vertegenwoordigd door het busnummer, het doel-ID-nummer en het LUN (Logical Unit Number). 

Bijvoorbeeld: ***bus nummer 0, doel-id 0, LUN 3***

Voor onze oefening hoeft u alleen het LUN te gebruiken.

## <a name="finding-the-lun"></a>Het LUN zoeken

Er zijn twee methoden om het LUN te vinden dat door u wordt gekozen, afhankelijk van of u [opslag ruimten](/windows-server/storage/storage-spaces/overview) gebruikt of niet.

### <a name="disk-management"></a>Schijfbeheer

Als u geen opslag groepen gebruikt, kunt u [schijf beheer](/windows-server/storage/disk-management/overview-of-disk-management) gebruiken om het LUN te vinden.

1. Maak verbinding met de virtuele machine en open schijf beheer a. Klik met de rechter muisknop op de knop Start en kies schijf beheer a. U kunt ook typen `diskmgmt.msc` in het vak Zoek opdracht starten
1. Klik in het onderste deel venster met de rechter muisknop op een van de schijven en kies Eigenschappen.
1. De LUN wordt weer gegeven in de eigenschap ' locatie ' op het tabblad Algemeen

### <a name="storage-pools"></a>Opslag groepen

1. Verbinding maken met de virtuele machine en Serverbeheer openen
1. Selecteer Bestands-en opslag Services, volumes, opslag groepen
1. In de rechter benedenhoek van Serverbeheer ziet u een sectie ' fysieke schijven '. De schijven die samen de opslag groep vormen, worden hier weer gegeven, evenals de LUN voor elke schijf.

## <a name="finding-the-lun-for-the-azure-disks"></a>Het LUN voor de Azure-schijven zoeken

U kunt het LUN voor een Azure-schijf vinden met behulp van de Azure Portal, Azure CLI of Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Het LUN van een Azure-schijf zoeken in het Azure Portal

1. Selecteer in de Azure Portal Virtual Machines om een lijst met uw Virtual Machines weer te geven
1. De virtuele machine selecteren
1. Selecteer schijven
1. Selecteer een gegevens schijf in de lijst met gekoppelde schijven.
1. Het LUN van de schijf wordt weer gegeven in het detail venster van de schijf. Het LUN dat hier wordt weer gegeven, is een relatie met de Lun's die zijn opgezocht in de gast met behulp van Apparaatbeheer of Serverbeheer.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Een Azure Disk LUN zoeken met behulp van Azure CLI of Azure PowerShell

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---