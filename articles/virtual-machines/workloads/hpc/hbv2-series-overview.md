---
title: Overzicht van de VM van de HBv2-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de VM-grootte van de HBv2-serie in Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6648f77c5eacf40f848bc9b24aa6e257d8adf626
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674644"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Overzicht van de virtuele machine van de HBv2-serie 

 
Voor het maximaliseren van de prestaties van HPC-toepassingen (High Performance Compute) op AMD EPYC is een gerichte benadering van geheugen en proces plaatsing vereist. Hieronder wordt een overzicht gegeven van de AMD EPYC-architectuur en onze implementatie van IT op Azure voor HPC-toepassingen. De term **pNUMA** wordt gebruikt om te verwijzen naar een fysiek Numa-domein en **vNUMA** om te verwijzen naar een gevirtualiseerde Numa-domein. 

Een [HBv2-](../../hbv2-series.md) server is fysiek 2 * 64-core EPYC 7742 cpu's voor een totaal van 128 fysieke kernen. Deze 128 kernen zijn onderverdeeld in 32 pNUMA-domeinen (16 per socket), die elk 4 kernen zijn en worden genoemd door AMD als een **kern** (of **CCX**). Elk CCX heeft zijn eigen L3-cache, waarmee wordt aangegeven dat een besturings systeem een pNUMA/vNUMA-grens ziet. Vier aangrenzende CCXs delen toegang tot twee kanalen van fysieke DRAM. 

Om ruimte te maken voor de werking van de Azure-Hyper Visor zonder dat de virtuele machine wordt verstoord, reserveert u fysieke pNUMA-domeinen 0 en 16 (dat wil zeggen het eerste CCX van elke CPU-socket). Alle resterende 30 pNUMA-domeinen worden toegewezen aan de virtuele machine op welk punt ze worden vNUMA. Daarom ziet de VM er als volgt uit:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` kernen per VM 

De VM zelf is niet bekend dat pNUMA 0 en 16 zijn gereserveerd. De vNUMA ziet er als 0-29, met 15 vNUMA per socket symmetrisch, vNUMA 0-14 op vSocket 0 en vNUMA 15-29 op vSocket 1. In de volgende sectie vindt u instructies voor het uitvoeren van MPI-toepassingen op deze asymmetrische NUMA-indeling. 

Het vastmaken van processen werkt op Vm's uit de HBv2-serie, omdat we het onderliggende silicium beschikbaar maken voor de gast-VM. Het wordt ten zeerste aanbevolen om proces vastmaken voor optimale prestaties en consistentie. 


## <a name="hardware-specifications"></a>Hardwarespecificaties 

| Hardwarespecificaties          | VM van de HBv2-serie                   | 
|----------------------------------|----------------------------------|
| Kernen                            | 120 (SMT uitgeschakeld)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU-frequentie (niet-AVX)          | ~ 3,1 GHz (enkele + alle kernen)    | 
| Geheugen                           | 4 GB/core (480 GB in totaal)         | 
| Lokale schijf                       | 960 GB NVMe (blok), 480 GB SSD (wissel bestand) | 
| InfiniBand                       | 200 GB/s EDR Mellanox Connectx-6 | 
| Netwerk                          | 50 GB/s Ethernet (40 GB/s bruikbaar) Azure Second gen SmartNIC | 


## <a name="software-specifications"></a>Software specificaties 

| Software specificaties     | VM van de HBv2-serie                                            | 
|-----------------------------|-----------------------------------------------------------|
| Maximale grootte van MPI-taak            | 36000 kernen (300 Vm's in één schaalset voor virtuele machines met singlePlacementGroup = True) |
| MPI-ondersteuning                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| Aanvullende Frameworks       | Unified Communication X, libfabric, PGAS                  |
| Ondersteuning voor Azure Storage       | Standard-en Premium-schijven (Maxi maal 8 schijven)              |
| Ondersteuning van het besturings systeem voor SRIOV RDMA   | CentOS/RHEL 7,6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Orchestrator-ondersteuning        | CycleCloud, batch                                         | 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [AMD EPYC-architectuur](https://bit.ly/2Epv3kC) en [multi-chip architecturen](https://bit.ly/2GpQIMb). Zie de [HPC-afstemmings handleiding voor AMD EPYC-processors](https://bit.ly/2T3AWZ9)voor meer gedetailleerde informatie.
- Meer informatie over de meest recente aankondigingen en enkele HPC-voor beelden vindt u in de blogs over de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.