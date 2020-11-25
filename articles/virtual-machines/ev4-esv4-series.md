---
title: Ev4 en Esv4-serie-Azure Virtual Machines
description: Specificaties voor de Ev4-en Esv4-serie-Vm's.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 01e9787d9b812334d959ce6ae9b929ec8734ffb5
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030472"
---
# <a name="ev4-and-esv4-series"></a>Ev4- en Esv4-serie

De Ev4-en Esv4-serie worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake) in een configuratie met Hyper-Threading, ideaal voor diverse geheugenintensieve bedrijfs toepassingen en-functies tot 504GIB aan RAM-geheugen. Het bevat een kern klok snelheid van 3,4 GHz.

> [!NOTE]
> Voor veelgestelde vragen raadpleegt u  [Azure VM-grootten zonder lokale tijdelijke schijf](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-serie

Ev4-serie-grootten worden uitgevoerd op Intel Xeon &reg; Platinum 8272CL (Cascade Lake). De instanties van de Ev4-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen. Virtuele machines uit de Ev4-serie bieden een Intel &reg; Hyper-Threading technologie.

Opslag van externe gegevens schijven wordt afzonderlijk van virtuele machines gefactureerd. Als u Premium Storage-schijven wilt gebruiken, gebruikt u de Esv4-grootten. De prijs-en facturerings meters voor Esv4-grootten zijn gelijk aan die van de Ev4-serie.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Alleen externe opslag | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Alleen externe opslag | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Alleen externe opslag | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Alleen externe opslag | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Alleen externe opslag | 32 | 8|10.000 |
| Standard_E32_v4 | 32 | 256 | Alleen externe opslag | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Alleen externe opslag | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Alleen externe opslag | 32| 8|30.000 |


## <a name="esv4-series"></a>Esv4-serie

Esv4-serie-grootten worden uitgevoerd op Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). De instanties van de Esv4-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen. Virtuele machines uit de Evs4-serie bieden een Intel &reg; Hyper-Threading technologie. Opslag van externe gegevens schijven wordt afzonderlijk van virtuele machines gefactureerd.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Alleen externe opslag | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Alleen externe opslag | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Alleen externe opslag | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Alleen externe opslag | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Alleen externe opslag | 32 | 32000/480  | 8|10.000 |
| Standard_E32s_v4 | 32 | 256 | Alleen externe opslag | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Alleen externe opslag | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Alleen externe opslag | 32 | 80000/1200 | 8|30.000 |

Er zijn <sup>1</sup> [beperkte core-grootten beschikbaar](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
