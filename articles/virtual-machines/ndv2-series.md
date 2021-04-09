---
title: NDv2-serie
description: Specificaties voor de virtuele machines uit de NDv2-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2f062aa43132543a2c149039eb50c0a0ab22d83d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618556"
---
# <a name="updated-ndv2-series"></a>Bijgewerkte NDv2-serie

De virtuele machine uit de NDv2-serie is een nieuwe toevoeging aan de GPU-familie, ontworpen voor de behoeften van de meest veeleisende AI-, machine learning-, simulatie-en HPC-workloads van GPU.

NDv2 wordt aangedreven door 8 NVIDIA Tesla V100 NVLINK-verbonden Gpu's, elk met 32 GB aan GPU-geheugen. Elke NDv2-VM beschikt ook over 40 niet-HyperThreaded Intel Xeon Platinum 8168 (Skylake)-kernen en 672 GiB van het systeem geheugen.

NDv2-instanties bieden uitstekende prestaties voor HPC-en AI-workloads die gebruikmaken van CUDA GPU-geoptimaliseerde reken-kernels en de vele AI-, ML-en analyse hulpprogramma's die ondersteuning bieden voor GPU-versnelling, zoals tensor flow, Pytorch, Caffe, RAPIDS en andere frameworks.

De NDv2 is in de praktijk opgebouwd voor zowel reken kundige, intense schaling (bundeling van 8 Gpu's per VM) en uitschalen (waarbij meerdere Vm's samen werken). De NDv2-serie biedt nu ondersteuning voor 100-Gigabit InfiniBand EDR back-end-netwerken, vergelijkbaar met die van de HB-serie HPC-VM, om hoogwaardige clusters mogelijk te maken voor parallelle scenario's, waaronder gedistribueerde trainingen voor AI en ML. Dit back-upnetwerk biedt ondersteuning voor alle belang rijke InfiniBand-protocollen, met inbegrip van die van de NCCL2-bibliotheken van NVIDIA, waardoor het clusteren van Gpu's naadloos kan worden gegroepeerd.

> [!IMPORTANT]
> Als u InfiniBand op de ND40rs_v2 virtuele machine [inschakelt](./workloads/hpc/enable-infiniband.md) , gebruikt u het OFED-stuur programma 4.7-1.0.0.1 Mellanox.
>
> Vanwege het toegenomen GPU-geheugen vereist de nieuwe ND40rs_v2 VM het gebruik van [virtuele machines van de tweede generatie](./generation-2.md) en Marketplace-installatie kopieën. 
>
> Opmerking: de ND40s_v2 met 16 GB per GPU-geheugen is niet meer beschikbaar voor preview en is vervangen door de bijgewerkte ND40rs_v2.

<br>

[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>
InfiniBand: ondersteund<br>
NVIDIA NVLink Interconnect: ondersteund<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale netwerk bandbreedte | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd.

Met de [uitbrei ding NVIDIA GPU-stuur programma](./extensions/hpccompute-gpu-linux.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie [extensies en functies van virtuele Azure-machines](./extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, raadpleegt u [het stuur programma voor de installatie van de N-Series GPU](./linux/n-series-driver-setup.md)

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
