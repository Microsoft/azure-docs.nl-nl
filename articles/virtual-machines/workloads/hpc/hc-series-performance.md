---
title: Prestaties van VM-grootte van HC-serie
description: Meer informatie over prestatie test resultaten voor VM-grootten van de HC-serie in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0d164c19da1ed2cbf6930a92686b35690fb0afb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666915"
---
# <a name="hc-series-virtual-machine-sizes"></a>Grootte van virtuele machines in HC-serie

Er zijn verschillende prestatie tests uitgevoerd op de grootte van de HC-serie. Hier volgen enkele van de resultaten van deze prestatie tests.

| Workload                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triad STREAMen                                    | 190 GB/s (Intel MLC AVX-512)  |
| High-Performance Linpackuitvoer (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| Band breedte & RDMA-latentie                        | 1,05 micro seconden, 96,8 GB/s   |
| FIO op lokale NVMe-SSD                           | 1,3 GB/s Lees bewerkingen, 900 MB/s |  
| IOR op 4 Azure Premium-SSD (P30 Managed Disks, RAID0) * *  | 780 MB/s Lees bewerkingen, 780 MB/schrijf bewerkingen |

## <a name="mpi-latency"></a>MPI-latentie

MPI-latentie test van de OSU microbench Mark-suite wordt uitgevoerd. Voorbeeld scripts bevinden zich op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="MPI-latentie op Azure HC.":::

## <a name="mpi-bandwidth"></a>MPI-band breedte

De MPI-bandbreedte test van de OSU microbench Mark-suite wordt uitgevoerd. Voorbeeld scripts bevinden zich op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="MPI-band breedte op Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox perftest

Het [Mellanox perftest-pakket](https://community.mellanox.com/s/article/perftest-package) heeft veel InfiniBand-tests zoals latentie (ib_send_lat) en band breedte (ib_send_bw). Hieronder vindt u een voor beeld van een opdracht.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de meest recente aankondigingen en enkele HPC-voor beelden (High Performance Computing) en resultaten van de [Azure Compute tech-community blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.
