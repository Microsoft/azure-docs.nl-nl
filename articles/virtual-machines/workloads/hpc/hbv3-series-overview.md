---
title: Overzicht van de VM van de HBv3-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de VM-grootte van de HBv3-serie in Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d1abd03f517f9e0b13a2994418cbae5cfbe22454
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801860"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Overzicht van virtuele machines uit de HBv3-serie 

Een [HBv3-](../../hbv3-series.md) server bevat 2 * 64-core EPYC 7V13 cpu's voor een totaal van 128 fysieke ' Zen3-kernen. Simultaan multi threading (SMT) is uitgeschakeld op HBv3. Deze 128 kernen zijn onderverdeeld in 16 secties (8 per socket), elke sectie met 8 processor kernen met eenvormige toegang tot een 32 MB L3-cache. Azure HBv3-servers voeren ook de volgende AMD BIOS-instellingen uit:

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

Als gevolg hiervan wordt de server opgestart met 4 NUMA-domeinen (2 per socket) elk 32-kern geheugen. Elk NUMA heeft directe toegang tot 4 kanalen van fysieke DRAM-verbinding met 3200 MT/s.

Om ruimte te bieden voor de werking van de Azure-Hyper Visor zonder dat de virtuele machine wordt verstoord, worden er 8 fysieke kernen per server gereserveerd. 

Houd er rekening mee dat de VM-grootten beperkte kernen alleen het aantal fysieke kernen beperken dat aan de virtuele machine wordt blootgesteld. Alle globale gedeelde assets (RAM, geheugen bandbreedte, L3-cache, GMI-en xGMI-connectiviteit, InfiniBand, Azure Ethernet-netwerk, lokale SSD) blijven constant. Hiermee kan een klant een VM-grootte kiezen die het beste kan worden afgestemd op een bepaalde reeks werk belasting-of software licentie vereisten.

In het volgende diagram ziet u de schei ding van kern geheugens die zijn gereserveerd voor Azure-Hyper Visor (geel) en de HBv3-serie (groen).
![Schei ding van kern geheugens gereserveerd voor Azure Hyper Visor en HBv3-serie VM](./media/architecture/hbv3-segregation-cores.png)

## <a name="infiniband-networking"></a>InfiniBand-netwerken
HBv3 Vm's maken ook gebruik van NVIDIA Mellanox HDR InfiniBand-netwerk adapters (Connectx-6) die met Maxi maal 200 gigabits per seconde werken. De NIC wordt door gegeven aan de virtuele machine via SRIOV, waardoor netwerk verkeer wordt ingeschakeld om de Hyper Visor over te slaan. Als gevolg hiervan laden klanten standaard Mellanox OFED-Stuur Programma's op HBv3 Vm's als een bare-metal omgeving.

HBv3 Vm's bieden ondersteuning voor adaptieve route ring, het dynamisch verbonden Trans Port (DCT, in aanvulling op Standard RC en UD trans ports), en op hardware gebaseerde offload van MPI collectief naar de onboarde-processor van de adapter Connectx-6. Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

## <a name="temporary-storage"></a>Tijdelijke opslag
HBv3 Vm's functie 3 fysiek lokale SSD-apparaten. Eén apparaat is vooraf opgemaakt als een wissel bestand en wordt weer gegeven in uw VM als een Gene riek ' SSD '-apparaat.

Twee andere, grotere Ssd's worden gegeven als niet-geformatteerde NVMe-apparaten via NVMeDirect. Omdat het blok NVMe-apparaat de Hyper Visor omzeilt, heeft het een hogere band breedte, hogere IOPS en een lagere latentie per ONTDUBBELINGS IOP.

Bij een koppeling in een striped matrix biedt de NVMe SSD tot 7 GB/s Lees bewerkingen en 3 GB/s schrijf bewerkingen, en Maxi maal 186.000 IOPS (Lees bewerkingen) en 201.000 IOPS (schrijf bewerkingen) voor diepe wachtrij diepten.

## <a name="hardware-specifications"></a>Hardwarespecificaties 

| Hardwarespecificaties          | Vm's uit de HBv3-serie              |
|----------------------------------|----------------------------------|
| Kernen                            | 120, 96, 64, 32 of 16 (SMT uitgeschakeld)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU-frequentie (niet-AVX)          | 3,1 GHz (alle kernen), 3,675 GHz (Maxi maal 10 kernen)    | 
| Geheugen                           | 448 GB (RAM per kern is afhankelijk van de VM-grootte)         | 
| Lokale schijf                       | 2 * 960 GB NVMe (blok keren), 480 GB SSD (wissel bestand) | 
| InfiniBand                       | 200 GB/s Mellanox Connectx-6 HDR InfiniBand | 
| Netwerk                          | 50 GB/s Ethernet (40 GB/s bruikbaar) Azure Second gen SmartNIC | 

## <a name="software-specifications"></a>Software specificaties 

| Software specificaties        | Vm's uit de HBv3-serie                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maximale grootte van MPI-taak               | 36.000 kernen (300 Vm's in één schaalset voor virtuele machines met singlePlacementGroup = True) |
| MPI-ondersteuning                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Aanvullende Frameworks          | UCX, libfabric, PGAS                  |
| Ondersteuning voor Azure Storage          | Standard-en Premium-schijven (Maxi maal 32 schijven)              |
| Ondersteuning van het besturings systeem voor SRIOV RDMA      | CentOS/RHEL 7,6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Aanbevolen besturings systeem voor prestaties | CentOS 8,1, Windows Server 2019 +
| Orchestrator-ondersteuning           | Azure CycleCloud, Azure Batch, AKS; [Opties voor cluster configuratie](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 wordt niet ondersteund op HBv3 en andere Vm's met meer dan 64 (virtuele of fysieke) kernen. Klik [hier](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.