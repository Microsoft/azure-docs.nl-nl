---
title: High Performance Computing op VM's uit de voor InfiniBand geschikte H-serie en N-serie - Azure Virtual Machines
description: Meer informatie over de functies en mogelijkheden van VM's uit de voor InfiniBand geschikte H-serie en N-serie die zijn geoptimaliseerd voor HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720590"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>High Performance Computing op VM's uit de voor InfiniBand geschikte H-serie en N-serie

De VM's uit de voor InfiniBand geschikte H-serie en N-serie van Azure zijn ontworpen om eersteklas prestaties, een MPI-schaalbaarheid (MPI: Message Passing Interface) en kostenefficiëntie te bieden voor diverse reële HPC- en AI-workloads. Deze voor HPC-geoptimaliseerde VM's (HPC: High-Performance Computing) worden gebruikt om een aantal van de meest rekenintensieve problemen in de wetenschap en techniek op te lossen, zoals: vloeistofdynamica, geologische modellen, weersimulaties enzovoort.

In deze artikelen wordt, naast een optimale configuratie van de HPC- en AI-workloads op de VM's voor schaalbaarheid, ook beschreven hoe u aan de slag gaat met de VM's uit de voor InfiniBand geschikte H-serie en N-serie op Azure.

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

De VM's uit de voor InfiniBand geschikte H-serie en N-serie zijn ontworpen om de beste HPC-prestaties, MPI-schaalbaarheid (Message Passing Interface) en kostenefficiëntie te bieden voor HPC-workloads. Zie VM's uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) voor meer informatie over de functies en mogelijkheden van de VM's.

### <a name="rdma-and-infiniband"></a>RDMA en InfiniBand

[RDMA-compatibele](../../sizes-hpc.md#rdma-capable-instances) VM's uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) communiceren via het InfiniBand-netwerk met lage latentie en hoge bandbreedte. De RDMA-functionaliteit via een dergelijke verbinding is essentieel om de schaalbaarheid en prestaties van HPC- en AI-workloads op gedistribueerde knooppunten te vergroten. De VM's uit de voor InfiniBand geschikte H-serie en N-serie zijn verbonden binnen een niet-blokkerende FAT-structuur met een ontwerp met een lage diameter voor optimale en consistente RDMA-prestaties.
Zie [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie over het instellen van InfiniBand op de voor InfiniBand geschikte VM's.

### <a name="message-passing-interface"></a>Message Passing Interface

De voor SR-IOV geschikte H-serie en N-serie bieden ondersteuning voor bijna alle MPI-bibliotheken en -versies. Enkele van de meest gebruikte MPI-bibliotheken zijn: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, platform MPI. Alle RDMA-werk woorden (Remote Direct Memory Access) worden ondersteund.
Zie [MPI instellen](setup-mpi.md) voor meer informatie over het installeren van verschillende ondersteunde MPI-bibliotheken en hun optimale configuratie.

## <a name="get-started"></a>Aan de slag

De eerste stap bestaat uit het selecteren van het VM-type uit de [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) dat optimaal is voor de workload op basis van de specificaties van de VM en de [RDMA-functionaliteit](../../sizes-hpc.md#rdma-capable-instances).
Ten tweede moet u de VM configureren door InfiniBand in te schakelen. Er zijn verschillende manieren om dit te doen, waaronder het gebruik van geoptimaliseerde VM-installatiekopieën met geïntegreerde stuurprogramma's. Zie [Optimalisatie voor Linux](configure.md) en [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie.
Ten derde, voor de werk belasting van gedistribueerde knoop punten, is het kiezen en configureren van MPI op de juiste manier essentieel. Zie [MPI instellen](setup-mpi.md) voor meer informatie.
Ten vierde, voor prestaties en schaal baarheid, kunt u de werk belastingen optimaal configureren door de richt lijnen te volgen die specifiek zijn voor de VM-familie, zoals voor [HBv3-serie overzicht](hbv3-series-overview.md) en [HC-serie Overview](hc-series-overview.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren en optimaliseren van VM's uit ](configure.md)de voor InfiniBand geschikte [H-serie](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md).
- Bekijk het [overzicht van de HBv3-serie](hb-series-overview.md) en het [HC-serie-overzicht](hc-series-overview.md) voor meer informatie over het optimaal configureren van workloads voor prestaties en schaal baarheid.
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
