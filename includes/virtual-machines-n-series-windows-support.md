---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b3e097f1c41f3047dc4e9d6cae2a05a6b19dea9d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554219"
---
## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-Stuur programma's (CUDA)

NVIDIA Tesla (CUDA)-Stuur Programma's voor NC-, NCv2-, NCv3-, NCasT4_v3-, ND-en NDv2-serie-Vm's (optioneel voor NV-serie) worden alleen ondersteund op de besturings systemen die in de volgende tabel worden vermeld. Koppelingen voor het downloaden van Stuur Programma's zijn actueel op het moment van publicatie. Ga naar de [NVIDIA](https://www.nvidia.com/)-website voor de nieuwste stuurprogramma's.

> [!TIP]
> Als alternatief voor het hand matig installeren van CUDA-Stuur Programma's op een virtuele machine met Windows Server kunt u een Azure [Data Science virtual machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -installatie kopie implementeren. Met de DSVM-edities voor Windows Server 2016 worden NVIDIA CUDA-Stuur Programma's, de CUDA diep Neural-netwerk bibliotheek en andere hulpprogram ma's vooraf geïnstalleerd.


| Besturingssysteem | Stuurprogramma |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA-raster Stuur Programma's

Micro soft distribueert installatie Programma's voor NVIDIA-raster stations voor NV-en NVv3-Vm's die worden gebruikt als virtuele werk stations of voor virtuele toepassingen. Installeer alleen deze raster Stuur Programma's op virtuele machines uit de Azure NV-serie, alleen voor de besturings systemen die in de volgende tabel worden vermeld. Deze Stuur Programma's omvatten licenties voor raster-Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-software licentie server in te stellen.

De raster Stuur Programma's die opnieuw worden gedistribueerd door Azure, werken niet op Vm's uit de niet-NV-serie, zoals NCv2-, NCv3-, ND-en NDv2-serie-vm's. De enige uitzonde ring hierop is de NCas_T4_V3 VM-serie waar de raster Stuur Programma's de grafische functionaliteiten kunnen inschakelen die vergelijkbaar zijn met de NV-serie.

De NC-Series met NVIDIA K80 Gpu's bieden geen ondersteuning voor GRID/grafische toepassingen.  

Houd er rekening mee dat de NVIDIA-extensie altijd het meest recente stuur programma installeert. We bieden hier koppelingen naar de vorige versie voor klanten, die afhankelijk zijn van een oudere versie.

Voor Windows Server 2019, Windows Server 2016 1607, 1709 en Windows 10 (Maxi maal build 20H2):
- [Raster 12,1 (461,33)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Raster 12,0 (461,09)](https://download.microsoft.com/download/4/8/d/48d2d45b-bebc-44ad-9c58-e0b79a9d4ff2/461.09_grid_win10_server2016_server2019_64bit_azure_swl.exe) (. exe) 

Voor Windows Server 2012 R2: 
- [Raster 12,1 (461,33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (. exe)
- [Raster 12,0 (461,09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (. exe) 


Ga naar [github](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) voor de volledige lijst met alle vorige NVIDIA grid-stuur programma koppelingen
