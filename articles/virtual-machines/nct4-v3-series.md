---
title: NCas T4 v3-serie
description: Specificaties voor de virtuele machines uit de NCas T4 v3-serie.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: d73bd81f15263c79e16b574eb961d4ae0ac61175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417804"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3-series 

De virtuele machines uit de NCasT4_v3-serie worden aangedreven door [Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU'S en AMD EPYC 7V12 (Rome) cpu's. De virtuele machines beschikken over Maxi maal 4 NVIDIA T4-Gpu's met 16 GB geheugen per, Maxi maal 64 niet-multi threaded AMD EPYC 7V12 (Rome)-processor kernen en 440 GiB van het systeem geheugen. Deze virtuele machines zijn ideaal voor het implementeren van AI-Services, zoals realtime deprocessoren van door de gebruiker gegenereerde aanvragen of voor interactieve grafische en visualisatie werk belastingen met behulp van het GRID-stuur programma en virtuele GPU-technologie van NVIDIA. Standaard GPU Compute-workloads op basis van CUDA, TensorRT, Caffe, ONNX en andere frameworks of GPU-versnelde grafische toepassingen op basis van OpenGL en DirectX kunnen economisch worden geïmplementeerd, met dichtbij de buurt van gebruikers op de NCasT4_v3 serie.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
NVIDIA NVLink Interconnect: ondersteund<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Als u gebruik wilt maken van de GPU-mogelijkheden van Azure NCasT4_v3-serie Vm's waarop Windows of Linux wordt uitgevoerd, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd.

Als u de NVIDIA GPU-Stuur Programma's hand matig wilt installeren, raadpleegt u [N-Series GPU-stuur programma-installatie voor Windows](./windows/n-series-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

Met de uitbrei ding van het Azure NVIDIA GPU-stuur programma worden CUDA-Stuur Programma's geïmplementeerd op de Vm's van de NCasT4_v3 serie. Voor grafische en visualisatie werk belastingen installeert u hand matig de raster Stuur Programma's die door Azure worden ondersteund.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
