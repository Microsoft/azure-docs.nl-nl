---
title: Groottes van Azure VM-opslag | Microsoft Docs
description: Geeft een lijst van de verschillende voor opslag geoptimaliseerde grootten die beschikbaar zijn voor virtuele machines in Azure. Hiermee wordt informatie weer gegeven over het aantal Vcpu's, gegevens schijven en Nic's, evenals opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d97fe6cda1134d45468e257965fd5e28fe170e6f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561021"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Grootte van virtuele machines die zijn geoptimaliseerd voor opslag

De grootten voor opslag geoptimaliseerde virtuele machines bieden een hoge door Voer van de schijf en IO, en zijn ideaal voor Big Data, SQL, NoSQL data bases, data warehousing en grote transactionele data bases.  Voor beelden zijn Cassandra, MongoDB, Cloudera en redis. Dit artikel bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's, evenals de lokale opslag doorvoer en netwerk bandbreedte voor elke geoptimaliseerde grootte.

De [Lsv2-serie](lsv2-series.md) biedt een hoge door Voer, lage latentie, rechtstreeks toegewezen lokale NVMe-opslag die wordt uitgevoerd op de [AMD EPYC<sup>TM</sup> 7551-processor](https://www.amd.com/en/products/epyc-7000-series) met een alle kern versterking van 2.55 GHz en een maximale boost van 3,0 GHz. De virtuele machines uit de Lsv2-serie zijn beschikbaar in groottes van 8 tot 80 vCPU in een gelijktijdige configuratie met meerdere threads.  Er is 8 GiB geheugen per vCPU en één 1.92 TB NVMe SSD M. 2-apparaat per 8 Vcpu's, met Maxi maal 19.2 TB (10x 1.92 TB) dat beschikbaar is op de L80s v2.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

Meer informatie over het optimaliseren van de prestaties van de virtuele machines uit de Lsv2-serie voor [Windows](windows/storage-performance.md) of [Linux](linux/storage-performance.md).

Zie [grootte conventies voor virtuele machines van Azure](./vm-naming-conventions.md)voor meer informatie over de manier waarop Azure namen van de virtuele machines is.