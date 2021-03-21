---
title: Dav4- en Dasv4-serie
description: Specificaties voor de virtuele machines uit de Dav4-en Dasv4-serie.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d68e5b7406b2cfa32b06f4731180684bae0c4334
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554102"
---
# <a name="dav4-and-dasv4-series"></a>Dav4- en Dasv4-serie

De Dav4-serie en de Dasv4-serie zijn nieuwe grootten die gebruikmaken van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere threads, met maxi maal 256 MB L3-cache van 8 MB aan de L3-cache tot elke 8 kernen die de klant opties verhogen voor het uitvoeren van hun werk belastingen voor algemeen gebruik. De Dav4-serie en de Dasv4-serie hebben dezelfde geheugen-en schijf configuraties als de D & Dsv3-serie.

## <a name="dav4-series"></a>Dav4-serie

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (*Mini maal 4 vCPU vereist*)<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>
<br>

De grootte van de Dav4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een maximale maximum frequentie van 3.35 GHz kan bereiken. De grootte van de Dav4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium SSD wilt gebruiken, gebruikt u de Dasv4-grootten. De prijs-en facturerings meters voor Dasv4-grootten zijn gelijk aan die van de Dav4-serie.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 800 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 1600 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12.000 / 187 / 93 | 4 | 3200 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24.000 / 375 / 187 |8 | 6400 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48.000 / 750 / 375 |8 | 12800 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 19200 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 25600 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 32000 |

## <a name="dasv4-series"></a>Dasv4-serie

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (*Mini maal 4 vCPU vereist*)<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>
<br>

De grootte van de Dasv4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een hogere maximum frequentie van 3.35 GHz kan bereiken en Premium SSD kan gebruiken. De grootte van de Dasv4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200|8 | 25600 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200|8 | 32000 |

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
