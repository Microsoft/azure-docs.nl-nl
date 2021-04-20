---
title: Msv2/Mdsv2 Medium Memory Series - Azure Virtual Machines
description: Specificaties voor de VM's uit de Msv2-serie.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: d85623184ad52fb0d4acd4c49d08badfaf886b30
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728226"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Gemiddeld geheugen uit de Msv2- en Mdsv2-serie

De Msv2- en Mdsv2 Medium Memory VM-serie bevat intel® Xeon® Een Cascade Lake-processor (Cascade Lake) met een basisfrequentie van 2,7 GHz en 4,0 GHz turbofrequentie met één kern. Met deze VM's bieden klanten meer flexibiliteit met lokale schijf- en schijfloze opties. Klanten hebben ook toegang tot een set nieuwe geïsoleerde VM-grootten met meer CPU en geheugen die tot 192 vCPU met 4 TiB geheugen gaan. 


VM's uit de Msv2- en Mdsv2-serie zijn alleen van de tweede generatie en ondersteunen een subset van door de tweede generatie ondersteunde afbeeldingen. Zie hieronder voor de volledige lijst met ondersteunde afbeeldingen voor de Msv2- en Mdsv2-serie.  

- Windows Server 2019 of hoger
- SUSE Linux Enterprise Server 12 SP4 en hoger of SUSE Linux Enterprise Server 15 SP1 en hoger
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 of hoger 
- Oracle Enterprise Linux 7.7 of hoger

Zie Ondersteuning voor virtuele machines van de tweede generatie in Azure voor meer informatie over virtuele machines [van de tweede generatie.](./generation-2.md)



[Premium Storage:](premium-storage-performance.md)ondersteund<br>
[Premium Storage in deaching:](premium-storage-performance.md)ondersteund<br>
[Livemigratie:](maintenance-and-updates.md)niet ondersteund<br>
[Updates met geheugenbehoud:](maintenance-and-updates.md)niet ondersteund<br>
[VM-generatieondersteuning:](generation-2.md)generatie 2<br>
[Write Accelerator:](./how-to-enable-write-accelerator.md)ondersteund<br>
[Versneld netwerken:](../virtual-network/create-vm-accelerated-networking-cli.md)ondersteund<br>
[Kortstondige besturingssysteemschijven:](ephemeral-os-disks.md)niet ondersteund <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 Medium Memory Diskless 

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer zondercachede schijf: IOPS/MBps | Max. aantal NIC's | Verwachte netwerkbandbreedte (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30.000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30.000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 gemiddeld geheugen met schijf  

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale gegevensschijf | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps | Maximale doorvoer zondercachede schijf: IOPS/MBps | Max. aantal NIC's | Verwachte netwerkbandbreedte (Mbps) | 
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

Prijscalculator: [Prijscalculator](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute Units (ACU)](acu.md) u kan helpen bij het vergelijken van rekenprestaties tussen Azure-SKU's.
