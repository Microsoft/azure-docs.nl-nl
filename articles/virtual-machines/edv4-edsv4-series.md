---
title: Edv4- en Edsv4-serie
description: Specificaties voor de virtuele machines uit de Ev4-, Edv4-, Esv4-en Edsv4-serie.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 407d208ef0385c795d086bb495ed51a020395cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443983"
---
# <a name="edv4-and-edsv4-series"></a>Edv4- en Edsv4-serie

De Edv4-en Edsv4-serie wordt uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake)-processors in een Hyper-Threaded configuratie en is ideaal voor diverse geheugenintensieve bedrijfs toepassingen en-functies tot 504 RAM-geheugen, [Intel &reg; Turbo Boost-technologie 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) en [Intel &reg; Advanced Vector-uitbrei dingen 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Ze ondersteunen ook [Intel &reg; diepe leer versterking](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Deze nieuwe VM-grootten hebben 50% grotere lokale opslag, evenals een betere lokale schijf-IOPS voor zowel lees-als schrijf bewerkingen in vergelijking met de [Ev3/Esv3-](./ev3-esv3-series.md) grootten met [Gen2-vm's](./generation-2.md). Het bevat een kern klok snelheid van 3,4 GHz. 

## <a name="edv4-series"></a>Edv4-serie

Edv4-serie-grootten worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake). De Edv4-grootten van virtuele machines bieden Maxi maal 504 GiB aan RAM-geheugen, naast snelle en grote lokale SSD-opslag (Maxi maal 2.400 GiB). Deze virtuele machines zijn ideaal voor geheugenintensieve bedrijfs toepassingen en-toepassingen die profiteren van lage latentie en hoge snelheid voor lokale opslag. U kunt standaard Ssd's en standaard Hdd's-schijf opslag koppelen aan de virtuele machines van Edv4. 

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (*Mini maal 4 vCPU vereist*)<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | <sup>**</sup> Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10.000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30.000 |


<sup>**</sup> Deze IOPs-waarden kunnen worden gegarandeerd met behulp van [Gen2-vm's](generation-2.md)

## <a name="edsv4-series"></a>Edsv4-serie

Edsv4-serie-grootten worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake). De Edsv4-grootten van virtuele machines bieden Maxi maal 504 GiB aan RAM-geheugen, naast snelle en grote lokale SSD-opslag (Maxi maal 2.400 GiB). Deze virtuele machines zijn ideaal voor geheugenintensieve bedrijfs toepassingen en-toepassingen die profiteren van lage latentie en hoge snelheid voor lokale opslag.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (*Mini maal 4 vCPU vereist*)<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | <sup>**</sup> Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10.000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1.600) | 80000/1200 | 8|30.000 |
| Standard_E80ids_v4 <sup>2</sup> | 80 | 504 | 2400 | 32 | 615000/3872 (1.600) | 80000/1500 | 8|30.000 |

Er zijn <sup>1</sup> [beperkte core-grootten beschikbaar](./constrained-vcpu.md).

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
