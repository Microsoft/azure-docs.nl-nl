---
title: Azure-schijven toewijzen aan gast schijven van Linux VM
description: De Azure-schijven bepalen die aan de gast schijven van een virtuele Linux-machine.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556720"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Azure-schijven toewijzen aan gast schijven van Linux VM

Mogelijk moet u de Azure-schijven bepalen die de gast schijven van een virtuele machine herstellen. In sommige scenario's kunt u de schijf-of volume grootte vergelijken met de grootte van de gekoppelde Azure-schijven. Als er meerdere Azure-schijven met dezelfde grootte zijn gekoppeld aan de VM, moet u het nummer van de logische eenheid (LUN) van de gegevens schijven gebruiken. 

## <a name="what-is-a-lun"></a>Wat is een LUN?

Een LUN (Logical Unit Number) is een nummer dat wordt gebruikt om een specifiek opslag apparaat te identificeren. Elk opslag apparaat krijgt een unieke numerieke id, beginnend bij nul. Het volledige pad naar een apparaat wordt vertegenwoordigd door het busnummer, het doel-ID-nummer en het LUN (Logical Unit Number). 

Bijvoorbeeld: ***bus nummer 0, doel-id 0, LUN 3***

Voor onze oefening hoeft u alleen het LUN te gebruiken.

## <a name="finding-the-lun"></a>Het LUN zoeken

Hieronder ziet u twee methoden voor het vinden van het LUN van een schijf in Linux.

### <a name="lsscsi"></a>lsscsi

1. Verbinding maken met de virtuele machine
1. `sudo lsscsi`

De eerste kolom die wordt vermeld, bevat het LUN, de indeling is [host: kanaal: doel:**LUN**].

### <a name="listing-block-devices"></a>Apparaten weer geven blok keren

1. Verbinding maken met de virtuele machine
1. `sudo ls -l /sys/block/*/device`

De laatste kolom die wordt weer gegeven, bevat het LUN, de indeling is [host: kanaal: doel:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Het LUN voor de Azure-schijven zoeken

U kunt het LUN voor een Azure-schijf vinden met behulp van de Azure Portal Azure CLI.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Het LUN van een Azure-schijf zoeken in het Azure Portal

1. Selecteer in de Azure Portal Virtual Machines om een lijst met uw Virtual Machines weer te geven
1. De virtuele machine selecteren
1. Selecteer schijven
1. Selecteer een gegevens schijf in de lijst met gekoppelde schijven.
1. Het LUN van de schijf wordt weer gegeven in het detail venster van de schijf. De LUN die hier wordt weer gegeven, correleren met de Lun's die u in de gast hebt gezocht met behulp van lsscsi of de blok apparaten weer geven.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Een Azure Disk LUN zoeken met behulp van Azure CLI

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
