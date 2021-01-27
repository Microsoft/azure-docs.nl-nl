---
title: Ev3-serie en Esv3-serie
description: Specificaties voor de virtuele machines uit de Ev3-en Esv3-serie.
author: DavidCBerry13
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: daberry
ms.openlocfilehash: 1a0943c9f87960f9fcec074941b6e8ead81d92af
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917906"
---
# <a name="ev3-and-esv3-series"></a>Ev3- en Esv3-serie

De Ev3-en Esv3-serie worden uitgevoerd op Intel® Xeon® Platinum 8272CL (Cascade Lake), of Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4-processor (Broadwell) in een configuratie met Hyper-Threading, met een betere toegevoegde waarde voor 2,3 de meeste werk belastingen voor algemeen gebruik, en de Ev3 in overeenstemming brengen met de virtuele machines voor algemeen gebruik van de meeste andere Clouds.  Het geheugen is uitgevouwen (van 7 GiB/vCPU tot 8 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de verplaatsing van hyperthreading. De Ev3 is de follow-up van de VM-grootten van het hoge geheugen van de D/dv2-families.

## <a name="ev3-series"></a>Ev3-serie

Instanties van de Ev3-serie worden uitgevoerd op Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon-® E5-2673 v4 2,3 GHz (Broadwell)-processors en bieden Intel Turbo Boost-technologie 2,0. Ev3-exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de ESv3-grootten. De prijs- en factureringsmeters voor de ESv3-grootten zijn gelijk aan die van de Ev3-serie.

Intel® Hyper-Threading-Ev3-serie onderdeel van de VM.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Max. aantal NIC's/netwerkbandbreedte |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

Er zijn <sup>1</sup> beperkte core-grootten beschikbaar.

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

## <a name="esv3-series"></a>Esv3-serie

Instanties van de Esv3-serie worden uitgevoerd op Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon-® E5-2673 v4 2,3 GHz (Broadwell)-processor, functie Intel Turbo Boost Technology 2,0 en Premium Storage gebruiken. Instanties van de Esv3-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen.

Intel® Hyper-Threading-Esv3-serie onderdeel van de VM.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Burst cache en tijdelijke opslag doorvoer: IOPS/MBps<sup>3</sup> | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps |  Burst uncacheed Disk doorvoer: IOPS/MBps<sup>3</sup>| Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

Er zijn <sup>1</sup> beperkte core-grootten beschikbaar.

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

Virtuele machines uit <sup>de Esv3</sup> - [serie kunnen de](./disk-bursting.md) prestaties van de schijf opruimen en tot Maxi maal 30 minuten per keer tot hun burste-limiet ophalen.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)
- [Prijscalculator](https://azure.microsoft.com/pricing/calculator/)
- Zie [welke schijf typen zijn beschikbaar in azure?](disks-types.md) voor meer informatie over schijf typen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.