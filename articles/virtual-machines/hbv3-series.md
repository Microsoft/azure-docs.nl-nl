---
title: HBv3-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de HBv3-serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800836"
---
# <a name="hbv3-series"></a>HBv3-serie

Vm's uit de HBv3-serie zijn geoptimaliseerd voor HPC-toepassingen, zoals vloei bare dynamiek, expliciete en impliciete, beperkte element analyse, weer modellen, seismische verwerking, reservoir simulatie en V.R.N.L.-simulatie. HBv3 Vm's beschikken over Maxi maal 120 AMD EPYC™ 7003-Series (Milaan) CPU-kernen, 448 GB RAM en geen hyperthreading. Vm's uit de HBv3-serie bieden ook 350 GB/sec. geheugen bandbreedte, Maxi maal 32 MB L3-cache per kern, Maxi maal 7 GB/s van de snelheid van de SSD-prestaties van het apparaat en klok frequenties tot 3,675 gigahertz. 

Alle virtuele machines uit de HBv3-serie van 200 GB/sec-HDR InfiniBand van NVIDIA-netwerken voor het inschakelen van MPI-workloads op supercomputer schaal. Deze Vm's zijn verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. De HDR InfiniBand-Fabric biedt ook ondersteuning voor adaptieve route ring en het dynamisch verbonden Trans Port (DCT, in aanvulling op de standaard-RC-en UD-transport gegevens). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): binnenkort beschikbaar<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

|Grootte |vCPU |Processor |Geheugen (GiB) |Geheugen bandbreedte GB/s |Basis-CPU-frequentie (GHz) |Frequentie van alle kernen (GHz, piek) |Frequentie met één kern geheugen (GHz, piek) |RDMA-prestaties (GB/s) |MPI-ondersteuning |Tijdelijke opslag (GiB) |Max. aantal gegevensschijven |Maximum aantal Ethernet-Vnic's |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alles |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alles |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alles |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alles |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alles |2 * 960 |32 |8 |

Meer informatie over:
- [Architectuur en VM-topologie](./workloads/hpc/hbv3-series-overview.md)
- Ondersteunde [software stack](./workloads/hpc/hbv3-series-overview.md#software-specifications) inclusief ondersteund besturings systeem
- Verwachte [prestaties](./workloads/hpc/hbv3-performance.md) van de virtuele machine uit de HBv3-serie

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
