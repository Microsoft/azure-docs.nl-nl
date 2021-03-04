---
title: Msv2-serie (preview)-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Msv2-serie.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 986b02ee1127bc929ce34518226424ba06d24b89
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101542"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 en Mdsv2-Series gemiddeld geheugen (preview-versie)


> [!IMPORTANT]
> Voeg het formulier toe aan de preview-versie **https://aka.ms/Mv2MedMemoryPreview** .  

De VM-reeks voor het Msv2-en Mdsv2-midden-geheugen bevat Intel® Xeon® Platinum 8280 (Cascade Lake)-processor met een basis frequentie van 2,7 GHz en 4,0 GHz single core Turbo frequentie. Met deze virtuele machines hebben klanten meer flexibiliteit met de opties voor lokale schijven en vrije schijven. Klanten hebben ook toegang tot een aantal van de nieuwe geïsoleerde VM-grootten met meer CPU en geheugen die tot 192 vCPU met 4 TiB aan geheugen gaan. 


Virtuele machines uit de Msv2-en Mdsv2-serie zijn alleen generatie 2 en ondersteunen een subset van de door generatie ondersteunde installatie kopieën. Zie hieronder voor de volledige lijst met ondersteunde installatie kopieën voor de Msv2-en Mdsv2-serie.  

- Windows Server 2019 of hoger
- SUSE Linux Enterprise Server 12 SP4 en hoger of SUSE Linux Enterprise Server 15 SP1 en hoger
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 of hoger 
- Oracle Enter prise Linux 7,7 of hoger

Voor meer informatie over virtuele machines van de tweede generatie raadpleegt u [ondersteuning voor vm's van generatie 2 op Azure](./generation-2.md).



[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 2<br>
[Write Accelerator](./how-to-enable-write-accelerator.md): ondersteund<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 gemiddeld geheugen schijfloos 

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30.000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 gemiddeld geheugen met schijf  

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale gegevens schijf | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30.000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30.000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30.000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30.000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
