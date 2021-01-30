---
title: Dv4 en Dsv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Dv4-en Dsv4-serie.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: aea0a79d1ae27d987a6a30cfefe91e875f9a8382
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097503"
---
# <a name="dv4-and-dsv4-series"></a>Dv4- en Dsv4-serie

De Dv4-en Dsv4-serie worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake) in een configuratie met Hyper-Threading, met een betere toegevoegde waarde voor de meeste werk belastingen voor algemeen gebruik. Het bevat een kern klok snelheid van 3,4 GHz. 

> [!NOTE]
> Voor veelgestelde vragen raadpleegt u  [Azure VM-grootten zonder lokale tijdelijke schijf](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Dv4-serie

Dv4-serie-grootten worden uitgevoerd op Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). De grootte van de Dv4-serie biedt een combi natie van opties voor vCPU, geheugen en externe opslag voor de meeste productiewerk belastingen. Virtuele machines uit de Dv4-serie bieden een [Intel &reg; Hyper-Threading technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Opslag van externe gegevens schijven wordt afzonderlijk van virtuele machines gefactureerd. Als u Premium Storage-schijven wilt gebruiken, gebruikt u de Dsv4-grootten. De prijs-en facturerings meters voor Dsv4-grootten zijn gelijk aan die van de Dv4-serie.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Alleen externe opslag | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Alleen externe opslag | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Alleen externe opslag | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Alleen externe opslag | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Alleen externe opslag | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Alleen externe opslag | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Alleen externe opslag | 32 | 8|30.000 |

## <a name="dsv4-series"></a>Dsv4-serie

Dsv4-serie-grootten worden uitgevoerd op Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). De grootte van de Dv4-serie biedt een combi natie van opties voor vCPU, geheugen en externe opslag voor de meeste productiewerk belastingen. Virtuele machines uit de Dsv4-serie bieden een [Intel &reg; Hyper-Threading technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Opslag van externe gegevens schijven wordt afzonderlijk van virtuele machines gefactureerd.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Alleen externe opslag | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Alleen externe opslag | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Alleen externe opslag | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Alleen externe opslag | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Alleen externe opslag | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Alleen externe opslag | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Alleen externe opslag | 32 | 80000/1200 | 8|30.000 |
