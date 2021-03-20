---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80334976"
---
**Niet-beheerde VM-schijven van Premium: limieten per account

| Resource | Limiet |
| --- | --- |
| Totale schijfcapaciteit per account |35 TB |
| Totale momentopnamecapaciteit per account |10 TB |
| Maximale bandbreedte per account (inkomend en uitgaand<sup>1</sup>) |<=50 Gbps |

<sup>1</sup>*Inkomend* verwijst naar alle gegevens van aanvragen die worden verzonden naar een opslagaccount. *Uitgaand* verwijst naar alle gegevens van aanvragen die worden ontvangen van een opslagaccount.

**Niet-beheerde VM-schijven van Premium: limieten per schijf**

| Schijftype voor Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Schijfgrootte |128 GiB |512 GiB |1024 GiB (1 TB) |2048 GiB (2 TB)|4095 GiB (4 TB)|
| Maximum aantal IOPS per schijf |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Maximale doorvoer per schijf |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Maximaal aantal schijven per opslagaccount |280 |70 |35 | 17 | 8 |

**Niet-beheerde VM-schijven van Premium: limieten per VM**

| Resource | Limiet |
| --- | --- |
| Maximum IOPS per VM |80.000 IOPS met GS5 VM |
| Maximale doorvoer per VM |2000 MB/s met GS5 VM |

