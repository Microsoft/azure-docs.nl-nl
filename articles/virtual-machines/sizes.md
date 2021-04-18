---
title: Formaten van virtuele machines
description: Geeft de verschillende grootten weer die beschikbaar zijn voor virtuele machines in Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: c3eecb0b9628ae889ac22bfe6d621266f06abc83
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598921"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Grootten voor virtuele machines in Azure

In dit artikel worden de beschikbare grootten en opties beschreven voor de virtuele Azure-machines die u kunt gebruiken om uw apps en workloads uit te voeren. Het biedt ook overwegingen bij de implementatie waar u rekening mee moet houden wanneer u van plan bent om deze resources te gebruiken. 

| Type | Grootten | Beschrijving |
|------|-------|-------------|
| [Algemeen gebruik](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Evenwichtige CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](sizes-compute.md) | F, Fs, Fsv2 | Hoge CPU-geheugenverhouding. Goed voor webservers met gemiddeld verkeer, netwerkapparaten, batchprocessen en toepassingsservers. |
| [Geoptimaliseerd voor geheugen](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Hoge geheugen-CPU-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen.                 |
| [Geoptimaliseerd voor opslag](sizes-storage.md) | Lsv2 | Hoge schijfdoorvoer en I/O ideaal voor Big Data-, SQL-, NoSQL-databases, datawarehousing- en grote transactionele databases.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | Gespecialiseerde virtuele machines die zijn gericht op zware grafische rendering en videobewerking, evenals modeltraining en deferencing (ND) met deep learning. Beschikbaar met één of meerdere GPU's. |
| [Krachtig rekenvermogen](sizes-hpc.md) | HB, HBv2, HC, H | Onze snelste en krachtigste CPU-VM's met optionele netwerkinterfaces met hoge doorvoer (RDMA). |

- Zie de pagina's met prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) of Windows voor informatie over de prijzen van de verschillende [grootten.](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)
- Zie Beschikbare producten per regio voor beschikbaarheid van VM-grootten in [Azure-regio's.](https://azure.microsoft.com/regions/services/)
- Zie Limieten, quota's en beperkingen voor Azure-abonnementen en -service voor algemene limieten [voor Azure-VM's.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Zie Naamconventnamen voor virtuele [Azure-machines](./vm-naming-conventions.md)voor meer informatie over hoe Azure de VM's een naam gegeven.

## <a name="rest-api"></a>REST-API

Zie voor meer informatie over het gebruik REST API om een query uit te voeren voor VM-grootten:

- [Beschikbare grootten van virtuele machines voor het formaat van de virtuele machine](/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschikbare grootten van virtuele machines voor een abonnement opsningen](/rest/api/compute/resourceskus/list)
- [Beschikbare grootten voor virtuele machines in een beschikbaarheidsset](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure Compute Units (ACU)](acu.md) u kan helpen bij het vergelijken van rekenprestaties in Azure-SKU's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over rekenprestaties voor Virtuele Linux-VM's met behulp van de [CoreMark-benchmarkscores.](./linux/compute-benchmark-scores.md)

Meer informatie over rekenprestaties voor Windows-VM's met behulp van de [SPECInt-benchmarkscores.](./windows/compute-benchmark-scores.md)

## <a name="manage-costs"></a>Kosten beheren

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende VM-grootten die beschikbaar zijn:

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- Controleer de [pagina van de](sizes-previous-gen.md) vorige generatie voor A Standard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie
