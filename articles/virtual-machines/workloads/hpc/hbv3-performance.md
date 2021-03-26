---
title: HBv3-serie VM-grootte prestaties en schaal baarheid
description: Meer informatie over de prestaties en schaal baarheid van VM-grootten van de HBv3-serie in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604767"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Prestaties van virtuele machines uit de HBv3-serie

Prestatie verwachtingen met behulp van gebruikelijke HPC micro-benchmarks zijn als volgt:

| Workload                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad STREAMen                                    | 330-350 GB/s (~ 82-86 GB/s per NUMA)                                     |
| High-Performance Linpackuitvoer (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) voor VM-grootte van 120-core               |
| Band breedte & RDMA-latentie                        | 1,2 micro seconden (1-byte), 192 GB/s (eenrichtings)                                        |
| FIO op lokale NVMe-Ssd's (RAID0)                  | 7 GB/s Lees bewerkingen, 3 GB/s schrijf bewerkingen; 186k IOPS-Lees bewerkingen, 201k IOPS-schrijf bewerkingen |

## <a name="process-pinning"></a>Proces vastmaken

Het vastmaken van [processen](compiling-scaling-applications.md#process-pinning) werkt goed op vm's uit de HBv3-serie, omdat we het onderliggende silicium beschikbaar maken voor de gast-VM. Het wordt ten zeerste aanbevolen om proces vastmaken voor optimale prestaties en consistentie.

## <a name="mpi-latency"></a>MPI-latentie

De MPI-latentie test van de OSU microbench Mark-Suite kan worden uitgevoerd per hieronder. Voorbeeld scripts zijn op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI-band breedte
De MPI-bandbreedte test van de OSU microbench Mark-Suite kan worden uitgevoerd per hieronder. Voorbeeld scripts zijn op [github](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox perftest
Het [Mellanox perftest-pakket](https://community.mellanox.com/s/article/perftest-package) heeft veel InfiniBand-tests zoals latentie (ib_send_lat) en band breedte (ib_send_bw). Hieronder vindt u een voor beeld van een opdracht.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [schalen van MPI-toepassingen](compiling-scaling-applications.md).
- Bekijk de prestaties en schaal baarheid van HPC-toepassingen op de HBv3-Vm's in het [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Meer informatie over de laatste aankondigingen, HPC-voor beelden en prestatie resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.
