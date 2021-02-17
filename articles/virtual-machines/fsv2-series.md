---
title: Fsv2-serie
description: Specificaties voor de virtuele machines uit de Fsv2-serie.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e738a3e18d4ee16b2bbfd7edb6cc32152bb82b48
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557804"
---
# <a name="fsv2-series"></a>Fsv2-serie

De Fsv2-serie wordt uitgevoerd op de Intel® Xeon® Platinum 8272CL (Cascade Lake)-processors en Intel® Xeon® Platinum 8168-processors (Skylake). De IT-service heeft een zeer hoge Turbo klok snelheid van 3,4 GHz en een maximale Turbo frequentie van 3,7 GHz met één kern. Intel® AVX-512-instructies zijn nieuw op schaal bare Intel-processors. Deze instructies bieden een 2X hoge prestatie verbetering van de werk belasting voor vector verwerking op bewerkingen met een drijvende komma van zowel één als dubbele precisie. Met andere woorden, ze zijn heel snel voor elke reken werk belasting.

Virtuele machines uit de Fsv2-serie bieden een Intel® Hyper-Threading technologie.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (*Mini maal 4 vCPU vereist*)<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>
<br>

| Grootte | van vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30.000 |

<sup>1</sup> voor het gebruik van meer dan 64 vCPU is een van de volgende ondersteunde gast besturingssystemen vereist:

- Windows Server 2016 of hoger
- Ubuntu 16,04 LTS of hoger, met door Azure afgestemde kernel (4,15 kernel of hoger)
- SLES 12 SP2 of hoger
- RHEL of CentOS versie 6,7 tot en met 6,10, met het door micro soft meegeleverde LIS-pakket 4.3.1 (of hoger)
- RHEL of CentOS versie 7,3, met het door micro soft meegeleverde LIS-pakket 4.2.1 (of hoger) geïnstalleerd
- RHEL of CentOS versie 7,6 of hoger
- Oracle Linux met UEK4 of hoger
- Debian 9 met de backports-kernel, Debian 10 of hoger
- CoreOS met een 4,14-kernel of hoger

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.