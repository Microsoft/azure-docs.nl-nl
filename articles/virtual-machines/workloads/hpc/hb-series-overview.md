---
title: Overzicht van de VM van de HB-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de preview-ondersteuning voor de VM-grootte van de HB-serie in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b0e8e2e0ee7ce730f6bf00d7e5ef4bd4eae65ce7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666938"
---
# <a name="hb-series-virtual-machines-overview"></a>Overzicht van virtuele machines uit de HB-serie

Voor het maximaliseren van de prestaties van HPC-toepassingen (High Performance Compute) op AMD EPYC is een gerichte benadering van geheugen en proces plaatsing vereist. Hieronder wordt een overzicht gegeven van de AMD EPYC-architectuur en onze implementatie van IT op Azure voor HPC-toepassingen. We gebruiken de term ' pNUMA ' om te verwijzen naar een fysiek NUMA-domein en ' vNUMA ' om te verwijzen naar een gevirtualiseerde NUMA-domein.

Een [HB-](../../hb-series.md) server is fysiek 2 * 32-core EPYC 7551 cpu's voor een totaal van 64 fysieke kernen. Deze 64 kernen zijn onderverdeeld in 16 pNUMA-domeinen (8 per socket), die elk vier kernen zijn en ook wel een ' CPU complex ' (of ' CCX ' genoemd). Elk CCX heeft zijn eigen L3-cache, waarmee wordt aangegeven dat een besturings systeem een pNUMA/vNUMA-grens ziet. Een paar aaneengesloten CCXs-shares hebben toegang tot twee kanalen van fysieke DRAM (32 GB DRAM in servers van de HB-serie).

Om ruimte te maken voor de werking van de Azure-Hyper Visor zonder dat de virtuele machine wordt verstoord, reserveren we fysiek pNUMA domein 0 (de eerste CCX). Vervolgens wijst u pNUMA domeinen 1-15 (de resterende CCX-eenheden) toe voor de virtuele machine. De VM ziet er als volgt uit:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` kernen per VM

De virtuele machine is zelf niet bekend dat pNUMA 0 niet aan het apparaat is toegewezen. De VM begrijpt pNUMA 1-15 als vNUMA 0-14, met 7 vNUMA op vSocket 0 en 8 vNUMA op vSocket 1. Hoewel dit asymmetrisch is, moet het besturings systeem worden opgestart en normaal functioneren. Verderop in deze hand leiding wordt u aangeraden om MPI-toepassingen uit te voeren op deze asymmetrische NUMA-indeling.

Het vastmaken van processen werkt op Vm's uit de HB-serie, omdat we het onderliggende silicium beschikbaar maken voor de gast-VM. Het wordt ten zeerste aanbevolen om proces vastmaken voor optimale prestaties en consistentie.

In het volgende diagram ziet u de schei ding van kernen die zijn gereserveerd voor Azure Hyper Visor en de HB-serie.

![Schei ding van kern geheugens gereserveerd voor Azure Hyper Visor en HB-serie VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespecificaties

| Hardwarespecificaties                | VM van de HB-serie                     |
|----------------------------------|----------------------------------|
| Kernen                            | 60 (SMT uitgeschakeld)                |
| CPU                              | AMD EPYC 7551                    |
| CPU-frequentie (niet-AVX)          | ~ 2,55 GHz (enkele + alle kernen)   |
| Geheugen                           | 4 GB/core (240 GB in totaal)         |
| Lokale schijf                       | 700 GB SSD                       |
| InfiniBand                       | 100 GB EDR Mellanox Connectx-5 |
| Netwerk                          | 50 GB Ethernet (40 GB bruikbaar) Azure Second gen SmartNIC |

## <a name="software-specifications"></a>Software specificaties

| Software specificaties           |VM van de HB-serie           |
|-----------------------------|-----------------------|
| Maximale grootte van MPI-taak            | 18000 kernen (300 Vm's in één schaalset voor virtuele machines met singlePlacementGroup = True)  |
| MPI-ondersteuning                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| Aanvullende Frameworks       | Unified Communication X, libfabric, PGAS |
| Ondersteuning voor Azure Storage       | Standard-en Premium-schijven (Maxi maal 4 schijven) |
| Ondersteuning van het besturings systeem voor SRIOV RDMA   | CentOS/RHEL 7,6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Orchestrator-ondersteuning        | CycleCloud, batch  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [AMD EPYC-architectuur](https://bit.ly/2Epv3kC) en [multi-chip architecturen](https://bit.ly/2GpQIMb). Zie de [HPC-afstemmings handleiding voor AMD EPYC-processors](https://bit.ly/2T3AWZ9)voor meer gedetailleerde informatie.
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
