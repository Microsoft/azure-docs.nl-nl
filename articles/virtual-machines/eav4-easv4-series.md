---
title: Eav4-serie en Easv4-serie
description: Specificaties voor de virtuele machines uit de Eav4-en Easv4-serie.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f94e7860bff67218c9629e76b06b7293974e491d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097520"
---
# <a name="eav4-and-easv4-series"></a>Eav4- en Easv4-serie

De Eav4-serie en de Easv4-serie maken gebruik van AMD 2.35 GHz EPYC<sup>TM</sup> 7452-processor in een configuratie met meerdere threads met Maxi maal 256 MB L3-cache, waarmee u de meeste opties voor het uitvoeren van de meeste voor geheugen geoptimaliseerde werk belastingen kunt verhogen. De Eav4-serie en de Easv4-serie hebben dezelfde geheugen-en schijf configuraties als de Ev3 & Esv3-serie.

## <a name="eav4-series"></a>Eav4-serie

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generaties 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

De grootte van de Eav4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een maximale maximum frequentie van 3.35 GHz kan bereiken. De grootten van de Eav4-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium SSD wilt gebruiken, gebruikt u de grootte van de Easv4-serie. De prijs-en facturerings meters voor Easv4-grootten zijn gelijk aan die van de Eav3-serie.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard \_ E2a \_ v4|2|16|50|4|3000 / 46 / 23|2 | 800 |
| Standard \_ E4a \_ v4|4|32|100|8|6000 / 93 / 46|2 | 1600 |
| Standard \_ E8a \_ v4|8|64|200|16|12.000 / 187 / 93|4 | 3200 |
| Standard \_ E16a \_ v4|16|128|400|32|24.000 / 375 / 187|8 | 6400 |
| Standard \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 8000 |
| Standard \_ E32a \_ v4|32|256|800|32|48.000 / 750 / 375|8 | 12800 |
| Standard \_ E48a \_ v4|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| Standard \_ E64a \_ v4|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| Standard \_ E96a \_ v4|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Easv4-serie

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generaties 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

De grootte van de Easv4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een hogere maximum frequentie van 3.35 GHz kan bereiken en Premium SSD kan gebruiken. De grootten van de Easv4-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

Er zijn <sup>1</sup> [beperkte core-grootten beschikbaar](./constrained-vcpu.md).

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
