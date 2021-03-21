---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4bfac9be5041fdf4ebfe7ea56f064b8b85806703
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98859741"
---
## <a name="supported-distributions-and-drivers"></a>Ondersteunde distributies en stuurprogramma 's

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-Stuur Programma's

NVIDIA CUDA-Stuur Programma's voor NC-, NCv2-, NCv3-, ND-en NDv2-serie-Vm's (optioneel voor NV-serie) worden alleen ondersteund in de Linux-distributies die in de volgende tabel worden vermeld. De gegevens van het CUDA-stuur programma zijn actueel op het moment van publicatie. Ga naar de [NVIDIA](https://developer.nvidia.com/cuda-zone) -website voor de meest recente CUDA-Stuur Programma's en ondersteunde besturings systemen. Zorg ervoor dat u de meest recente CUDA-Stuur Programma's voor uw distributie installeert of bijwerkt. 

> [!TIP]
> Als alternatief voor het hand matig installeren van CUDA-Stuur Programma's op een virtuele Linux-machine, kunt u een Azure [Data Science virtual machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -installatie kopie implementeren. De DSVM-edities voor Ubuntu 16,04 LTS of CentOS 7,4 vooraf: Installeer NVIDIA CUDA-Stuur Programma's, de CUDA diepe Neural-netwerk bibliotheek en andere hulpprogram ma's.


### <a name="nvidia-grid-drivers"></a>NVIDIA-raster Stuur Programma's

Micro soft distribueert installatie Programma's voor NVIDIA-raster stations voor NV-en NVv3-Vm's die worden gebruikt als virtuele werk stations of voor virtuele toepassingen. Installeer alleen deze raster Stuur Programma's op Azure NV-Vm's, alleen voor de besturings systemen die in de volgende tabel worden vermeld. Deze Stuur Programma's omvatten licenties voor raster-Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-software licentie server in te stellen.

De raster Stuur Programma's die opnieuw worden gedistribueerd door Azure, werken niet op Vm's uit de niet-NV-serie, zoals NC-, NCv2-, NCv3-, ND-en NDv2-serie-vm's.

|Distributie|Stuurprogramma|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,7 tot 7,9, 8,0, 8,1<br/><br/>SUSE Linux Enterprise Server 12 SP2 <br/><br/>SUSE Linux Enterprise Server 15 SP2 | NVIDIA-raster 12,0, [R460](https://go.microsoft.com/fwlink/?linkid=874272)(. exe)|

Ga naar [github](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) voor de volledige lijst met alle vorige NVIDIA grid-stuur programma-koppelingen.

> [!WARNING] 
> Installatie van software van derden in Red Hat-producten kan invloed hebben op de ondersteuningsvoorwaarden van Red Hat. Zie het [Knowledge Base-artikel over Red Hat](https://access.redhat.com/articles/1067).
>
