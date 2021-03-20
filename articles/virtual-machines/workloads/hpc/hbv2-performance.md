---
title: Prestaties van VM-grootte van HBv2-serie
description: Meer informatie over prestatie test resultaten voor VM-grootten van de HBv2-serie in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670060"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Grootte van virtuele machines uit de HBv2-serie

Er zijn verschillende prestatie tests uitgevoerd op Vm's [met HBv2-serie](../../hbv2-series.md) grootte. Hier volgen enkele van de resultaten van deze prestatie tests.


| Workload                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad STREAMen                                    | 350 GB/s (21-23 GB/s per CCX)                                     |
| High-Performance Linpackuitvoer (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (rmax, FP32)               |
| Band breedte & RDMA-latentie                        | 1,2 micro seconden, 190 GB/s                                        |
| FIO op lokale NVMe-SSD                           | 2,7 GB/s Lees bewerkingen, 1,1 GB/sec. 102k IOPS-Lees bewerkingen, 115 IOPS-schrijf bewerkingen |
| IOR op 8 * Azure Premium-SSD (P40 Managed Disks, RAID0) * *  | 1,3 GB/s Lees bewerkingen, 2,5 GB/schrijf bewerkingen; 101k IOPS-Lees bewerkingen, 105k IOPS-schrijf bewerkingen |


## <a name="mpi-latency"></a>MPI-latentie

MPI-latentie test van de OSU microbench Mark-suite wordt uitgevoerd. Voorbeeld scripts zijn op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI-latentie op Azure HB.":::


## <a name="mpi-bandwidth"></a>MPI-band breedte

De MPI-bandbreedte test van de OSU microbench Mark-suite wordt uitgevoerd. Voorbeeld scripts zijn op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI-band breedte op Azure HB.":::


## <a name="mellanox-perftest"></a>Mellanox perftest

Het [Mellanox perftest-pakket](https://community.mellanox.com/s/article/perftest-package) heeft veel InfiniBand-tests zoals latentie (ib_send_lat) en band breedte (ib_send_bw). Hieronder vindt u een voor beeld van een opdracht. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de meest recente aankondigingen en enkele HPC-voor beelden (High Performance Computing) en resultaten van de [Azure Compute tech-community blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.