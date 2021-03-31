---
title: Formaten van virtuele machines
description: Geeft een lijst van de verschillende beschik bare grootten voor virtuele machines in Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89420214"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Grootten voor virtuele machines in azure

In dit artikel worden de beschik bare grootten en opties voor de virtuele machines van Azure beschreven die u kunt gebruiken om uw apps en werk belastingen uit te voeren. Het biedt ook overwegingen bij de implementatie om te weten wanneer u van plan bent deze resources te gebruiken. 

| Type | Grootten | Beschrijving |
|------|-------|-------------|
| [Algemeen gebruik](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Evenwichtige CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](sizes-compute.md) | F, FS, Fsv2 | Hoge CPU-geheugenverhouding. Geschikt voor webservers met gemiddeld verkeer, netwerk apparaten, batch processen en toepassings servers. |
| [Geoptimaliseerd voor geheugen](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, dv2 | Hoge geheugen-CPU-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen.                 |
| [Geoptimaliseerd voor opslag](sizes-storage.md) | Lsv2 | Hoge schijf doorvoer en IO ideaal voor Big Data, SQL, NoSQL data bases, data warehousing en grote transactionele data bases.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3 (preview), ND, NDv2 (preview), NV, NVv3, NVv4 | Gespecialiseerde virtuele machines gericht op zware grafische rendering en video bewerking, en model training en demijnen (ND) met diep gaande lessen. Beschikbaar met één of meerdere GPU's. |
| [Krachtig rekenvermogen](sizes-hpc.md) | HB, HBv2, HC, H | Onze snelste en krach tigste virtuele CPU-machines met optionele netwerk interfaces (RDMA) met hoge door voer. |

- Zie de pagina met prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)voor meer informatie over de prijzen van de verschillende grootten.
- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor beschik BAARHEID van VM-grootten in azure-regio's.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor algemene limieten voor virtuele Azure-machines.
- Zie [grootte conventies voor virtuele machines van Azure](./vm-naming-conventions.md)voor meer informatie over de manier waarop Azure namen van de virtuele machines is.

## <a name="rest-api"></a>REST-API

Zie het volgende voor informatie over het gebruik van de REST API om te zoeken naar VM-grootten:

- [Beschik bare grootten van virtuele machines weer geven voor het wijzigen van de grootte](/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschik bare grootten van virtuele machines voor een abonnement weer geven](/rest/api/compute/resourceskus/list)
- [Beschik bare grootten van virtuele machines in een beschikbaarheidsset weer geven](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over reken prestaties voor Linux-Vm's met behulp van de [Coopmerking-benchmark scores](./linux/compute-benchmark-scores.md).

Meer informatie over reken prestaties voor Windows-Vm's met behulp van de [SPECInt-benchmark scores](./windows/compute-benchmark-scores.md).

## <a name="manage-costs"></a>Kosten beheren

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende beschik bare VM-grootten:

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- Controleer de [vorige generatie](sizes-previous-gen.md) pagina voor een standaard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie
