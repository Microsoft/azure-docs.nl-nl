---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078792"
---
De VM-grootte bepaalt de hoeveelheid reken resources (zoals CPU, GPU en geheugen) die beschikbaar worden gesteld voor de virtuele machine. U moet virtuele machines maken met behulp van een VM-grootte die geschikt is voor de werk belasting. Hoewel alle computers worden uitgevoerd op dezelfde hardware, hebben computer grootten verschillende limieten voor schijf toegang. Dit kan u helpen bij het beheren van de algemene schijf toegang op uw Vm's. Als een werk belasting toeneemt, kunt u ook de grootte van een bestaande virtuele machine wijzigen.

De volgende virtuele machines worden ondersteund voor maken op uw Azure Stack edge-apparaat.

### <a name="dv2-series"></a>Dv2-serie
|Grootte     |vCPU     |Geheugen (GiB) | Tijdelijke opslag (GiB)   | Max. aantal gegevensschijven | Max. aantal NIC's |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3,5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>DSv2-serie
|Grootte     |vCPU     |Geheugen (GiB) | Tijdelijke opslag (GiB)  | Max. aantal gegevensschijven| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3,5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


Zie [dv2 and DSv2-Series](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)voor meer informatie.

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3-serie (preview-versie)

Deze grootten worden ondersteund voor GPU-Vm's op uw apparaat en zijn geoptimaliseerd voor computerintensieve toepassingen met GPU-snelheid. Deze serie is gericht op het afleiden van workloads met Nvidia Tesla T4 GPU. 

|Grootte     |vCPU     |Geheugen (GiB) | Tijdelijke opslag (GiB)  | GPU | GPU-geheugen (GiB) | Max. aantal NIC's |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

Zie [NCasT4_v3-serie](../articles/virtual-machines/nct4-v3-series.md)voor meer informatie.

### <a name="f-series"></a>F-serie

Deze reeksen zijn geoptimaliseerd voor reken werkbelastingen en worden uitgevoerd op Intel Xeon-processors. 

| Grootte | van vCPU | Geheugen: GiB | Tijdelijke opslag (GiB) |  Max. aantal gegevensschijven | Max. aantal NIC's |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

Zie [Fsv2-Series](../articles/virtual-machines/fsv2-series.md)voor meer informatie.

