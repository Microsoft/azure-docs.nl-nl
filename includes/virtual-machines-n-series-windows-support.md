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
ms.openlocfilehash: caad292f06710fca8d6f64476eead5dfcb164e9d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96537041"
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

De raster Stuur Programma's die opnieuw worden gedistribueerd door Azure, werken niet op Vm's uit de niet-NV-serie, zoals NC-, NCv2-, NCv3-, ND-en NDv2-serie-vm's. De enige uitzonde ring hierop is de NCas_T4_V3 VM-serie waar de raster Stuur Programma's de grafische functionaliteiten kunnen inschakelen die vergelijkbaar zijn met de NV-serie.

Houd er rekening mee dat de NVIDIA-extensie altijd het meest recente stuur programma installeert. We bieden hier koppelingen naar de vorige versie voor klanten, die afhankelijk zijn van een oudere versie.

Voor Windows Server 2019, Windows Server 2016 en Windows 10 (tot build 2004):
- [Raster 11,2 (452,57)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Raster 11,1 (452,39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (. exe) 

Voor Windows Server 2012 R2: 
- [Raster 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe) 
- [Raster 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  

Ga naar [github](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) voor de volledige lijst met alle vorige NVIDIA grid-stuur programma koppelingen
