---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: 9ea5fb26a52c967c5296f1a83976e748c86c9e18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763803"
---
De grootte van een VM bepaalt de hoeveelheid rekenresources, zoals CPU, GPU en geheugen, die voor de VM beschikbaar zijn gesteld. Virtuele machines moeten worden gemaakt met een grootte die geschikt is voor de werkbelasting. Hoewel alle computers worden uitgevoerd op dezelfde hardware, hebben computer grootten verschillende limieten voor schijf toegang, die u kan helpen bij het beheren van de algemene schijf toegang op uw Vm's. Als een werkbelasting toeneemt, kan de grootte van een bestaande virtuele machine ook worden gewijzigd.

De volgende virtuele machines worden ondersteund voor maken op Azure Stack edge-apparaat.

### <a name="dv2-series"></a>Dv2-serie
|Grootte     |vCPU     |Geheugen (GiB) | Bron schijf grootte (GiB)  | Grootte van de besturingssysteem schijf (GiB) | Max. aantal gegevensschijven | Max. aantal NIC's |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3,5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2-serie
|Grootte     |vCPU     |Geheugen (GiB) |  Bron schijf grootte (GiB)  | Grootte van de besturingssysteem schijf (GiB) | Max. aantal gegevensschijven| Max. aantal NIC's |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3,5 |7  |4000  |1000 |4  |2 |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |4 |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |4 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |8 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |8 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |2 |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |4 |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |8 |


Ga voor meer informatie naar [dv2 Series op algemeen VM-grootten](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3-serie (preview-versie)

Deze grootten worden ondersteund voor GPU-Vm's op uw apparaat en zijn geoptimaliseerd voor computerintensieve toepassingen met GPU-snelheid. Deze serie is gericht op het afleiden van workloads met Nvidia Tesla T4 GPU. 

|Grootte     |vCPU     |Geheugen (GiB) | Bron schijf grootte (GiB)  |Grootte van de besturingssysteem schijf (GiB)| GPU | GPU-geheugen (GiB) | Max. aantal NIC's |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

Ga voor meer informatie naar [NCasT4_v3-Series op GPU geoptimaliseerde VM-grootten](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F-serie

Deze reeksen zijn geoptimaliseerd voor reken werkbelastingen en worden uitgevoerd op Intel Xeon-processors. 

| Grootte | van vCPU | Geheugen: GiB |Bron schijf grootte (GiB) |Grootte van de besturingssysteem schijf (GiB)|  Max. aantal gegevensschijven | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

Ga voor meer informatie naar [Fsv2-series over geoptimaliseerde virtuele machines met reken capaciteit](../articles/virtual-machines/fsv2-series.md).

