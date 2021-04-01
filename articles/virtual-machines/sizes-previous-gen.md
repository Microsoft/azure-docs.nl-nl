---
title: Azure VM-grootten-vorige generaties | Microsoft Docs
description: Een lijst met de vorige generaties beschik bare grootten voor virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/01/2020
ms.author: mimckitt
ms.openlocfilehash: 90bc98d63b45e43c9325eed4fe019b18f52d0de8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500288"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Vorige generaties virtuele machine grootten

Deze sectie bevat informatie over vorige generaties virtuele machine grootten. Deze grootten kunnen nog steeds worden gebruikt, maar er zijn nieuwere generaties beschikbaar.

## <a name="f-series"></a>F-serie

De F-serie is gebaseerd op de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor, die met Intel Turbo Boost Technology 2.0 een kloksnelheid kan halen van 3,1 GHz. Dit zijn dezelfde CPU-prestaties als de virtuele machines uit de Dv2-serie.  

Virtuele machines uit de F-serie vormen een uitstekende keuze voor workloads die snellere CPU's nodig hebben, maar niet zo veel geheugen of tijdelijke opslag per vCPU.  Voor workloads voor analysen, gameservers, webservers en batchverwerking is de F-serie wellicht een betere keuze.

ACU: 210 - 250

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/MBps lezen/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4-4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8-8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16-16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32-32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS-serie <sup>1</sup>

De Fs-serie biedt alle voordelen van de F-serie, plus Premium-opslag.

ACU: 210 - 250

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bytes per seconde en GiB = 1024^3 bytes.

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een virtuele machine uit de FS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven.  Zie voor meer informatie [ontwerp voor hoge prestaties](premium-storage-performance.md).


## <a name="nvv2-series"></a>NVv2-serie

**Aanbeveling voor nieuwere grootte**: [NVv3-serie](nvv3-series.md)

De virtuele machines uit de NVv2-serie worden aangedreven door [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu's en de NVIDIA Grid-technologie met Intel Broadwell cpu's. Deze virtuele machines zijn gericht op GPU-versnelde grafische toepassingen en virtuele Bureau bladen waar klanten hun gegevens willen visualiseren, de resultaten kunnen simuleren om te bekijken, te werken met CAD of inhoud te genereren en te streamen. Daarnaast kunnen deze virtuele machines single Precision-werk belastingen uitvoeren, zoals code ring en rendering. Virtuele machines van NVv2 ondersteunen Premium Storage en komen twee maal zoveel systeem geheugen (RAM) te staan in vergelijking met de bijbehorende voorafgaande NV-serie.  

Elke GPU in NVv2-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werk station voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de virtuele machine voor een virtuele-toepassings scenario.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werk stations | Virtuele toepassingen |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

## <a name="older-generations-of-virtual-machine-sizes"></a>Oudere generaties virtuele-machine grootten

Deze sectie bevat informatie over oudere generaties virtuele machine grootten. Deze grootten worden nog steeds ondersteund, maar er is geen aanvullende capaciteit. Er zijn nieuwe nieuwere of alternatieve grootten die algemeen beschikbaar zijn. Raadpleeg de [grootten van virtuele machines in azure](./sizes.md) om te kiezen welke VM-grootten het meest geschikt is voor uw behoeften.  

Zie [het formaat van een virtuele Linux-machine wijzigen](linux/change-vm-size.md)voor meer informatie over het wijzigen van het formaat van een virtuele Linux-machine.  

<br>

### <a name="basic-a"></a>Basic A  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](av2-series.md)

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

De basislaaggrootten zijn met name bedoeld voor ontwikkelworkloads en andere toepassingen waarvoor geen taakverdeling, automatische schaling of geheugenintensieve virtuele machines zijn vereist.

| Grootte - grootte\naam | vCPU | Geheugen|NIC's (max.)| Maximumgrootte van tijdelijke schijf | Met maximaal gegevens schijven (1023 GB elk)| Met maximaal IOPS (300 per schijf) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 met CLI en PowerShell

In het klassieke implementatiemodel verschillen sommige namen van VM-grootten enigszins in CLI en in PowerShell:

* Standard_A0 is ExtraSmall
* Standard_A1 is Small
* Standard_A2 is Medium
* Standard_A3 is Large
* Standard_A4 is ExtraLarge

### <a name="a-series"></a>A-serie  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](av2-series.md)

ACU: 50-100

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Max. aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2 x 500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> de a0-grootte is te veel geabonneerd op de fysieke hardware. Alleen bij deze specifieke grootte kunnen implementaties van andere klanten invloed hebben op de prestaties van uw uitgevoerde workload. De relatieve prestaties worden hieronder beschreven, zoals de verwachte basislijn, met een variabiliteit van ongeveer 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serie: rekenintensieve exemplaren  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](av2-series.md)

ACU: 225

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Grootten uit de A8-A11- en H-serie worden ook wel *rekenintensieve exemplaren* genoemd. De hardware waarop deze grootten worden uitgevoerd, is ontworpen en geoptimaliseerd voor rekenintensieve en netwerkintensieve toepassingen, waaronder HPC-clustertoepassingen (high-performance computing), modellerings- en simulatietoepassingen. De A8-A11-serie gebruikt Intel Xeon E5-2670 @ 2,6 GHZ en de H-serie gebruikt Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Max. aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Max. aantal NIC's|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> Voor MPI-toepassingen wordt een toegewijd RDMA-back-end-netwerk ingeschakeld door FDR InfiniBand-netwerk. Dit biedt een zeer lage latentie en hoge band breedte.  

> [!NOTE]
> De [A8-A11-vm's worden gepland voor buiten gebruiks telling op 3/2021](https://azure.microsoft.com/updates/a8-a11-azure-virtual-machine-sizes-will-be-retired-on-march-1-2021/). We raden u ten zeerste aan geen nieuwe A8-A11-Vm's te maken. Migreer alle bestaande A8-A11-Vm's naar nieuwere en krachtige computing VM-grootten zoals H, HB, HC, HBv2 en de grootte van Compute-VM'S voor algemeen gebruik, zoals D, E en F voor een betere prijs-prestatie verhouding. Zie voor meer informatie [HPC-migratie handleiding](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-serie  

**Nieuwere grootte aanbeveling**: [Dav4-Series](dav4-dasv4-series.md), [Dv4-serie](dv4-dsv4-series.md) en [Ddv4-serie](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/MBps lezen/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4-4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8-8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16-16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32-32x500 | 8/4000 |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-Series-geoptimaliseerd voor geheugen  

**Nieuwere grootte aanbeveling**: [Dav4-Series](dav4-dasv4-series.md), [Dv4-serie](dv4-dsv4-series.md) en [Ddv4-serie](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/MBps lezen/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8-8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16-16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32-32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="preview-dc-series"></a>Voor beeld: DC-serie

**Aanbeveling voor nieuwere grootte**: [DCsv2-serie](dcv2-series.md)

Premium Storage: ondersteund

Premium Storage caching: ondersteund

De DC-serie gebruikt de nieuwste generatie van 3,7 GHz Intel XEON E-2176G-processor met SGX-technologie, en met de Intel Turbo Boost-technologie kunt u tot 4,7 GHz gaan. 

| Grootte          | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Vm's uit de DC-serie zijn [twee vm's van de tweede generatie](./generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen `Gen2` installatie kopieën.


### <a name="ds-series"></a>DS-serie  

**Nieuwere grootte aanbeveling**: [Dasv4-Series](dav4-dasv4-series.md), [Dsv4-serie](dv4-dsv4-series.md) en [Ddsv4-serie](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-Series-geoptimaliseerd voor geheugen  

**Nieuwere grootte aanbeveling**: [Dasv4-Series](dav4-dasv4-series.md), [Dsv4-serie](dv4-dsv4-series.md) en [Ddsv4-serie](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een VM van de DS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven.  Zie voor meer informatie [ontwerp voor hoge prestaties](premium-storage-performance.md).
<sup>2</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-serie

**Aanbeveling voor nieuwere grootte**: [Lsv2-serie](lsv2-series.md)

De Ls-serie biedt maximaal 32 vCPU's en maakt gebruik van een [Intel Xeon®-processor uit de E5 v3-familie](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). De Ls-serie heeft dezelfde CPU-prestaties als de G/GS-serie en wordt geleverd met 8 GiB geheugen per vCPU.

De LS-serie biedt geen ondersteuning voor het maken van een lokale cache om het aantal IOPS dat door duurzame gegevens schijven kan worden behaald, te verg Roten. Dankzij de hoge door Voer en IOPS van de lokale schijf zijn de Vm's uit de LS-serie ideaal voor NoSQL-archieven zoals Apache Cassandra en MongoDB die gegevens repliceren op meerdere Vm's om persistentie te krijgen in het geval van een storing in één virtuele machine.

ACU: 180-240

Premium Storage: ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen (GiB) | Tijdelijke opslag (GiB) | Max. aantal gegevensschijven | Maximale tijdelijke opslag doorvoer (IOPS/MBps) | Maxi maal aantal niet-opgeslagen schijf doorvoer (IOPS/MBps) | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

De maximale schijf doorvoer die mogelijk is met Vm's uit de LS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven. Zie voor meer informatie [ontwerp voor hoge prestaties](premium-storage-performance.md).

<sup>1</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

### <a name="gs-series"></a>GS-serie

**Nieuwere grootte aanbeveling**: [Easv4-serie](eav4-easv4-series.md), [Esv4-Serie](ev4-esv4-series.md), [Edsv4-serie](edv4-edsv4-series.md) en [M-serie](m-series.md)

ACU: 180-240 <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4 &nbsp; <sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5 &nbsp; <sup>2, &nbsp; 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een GS-serie VM kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven. Zie voor meer informatie [ontwerp voor hoge prestaties](premium-storage-performance.md).

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

<sup>3</sup> beperkte core-grootten beschikbaar.

<br>

### <a name="g-series"></a>G-serie

**Nieuwere grootte aanbeveling**: [Eav4-Series](eav4-easv4-series.md), [Ev4-Serie](ev4-esv4-series.md) en [Edv4-](edv4-edsv4-series.md) Series en [M-serie](m-series.md)

ACU: 180 - 240

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/MBps lezen/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8-8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16-16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32-32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5 &nbsp; <sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.
<br>

### <a name="nv-series"></a>NV-serie
**Nieuwe aanbeveling**: [NVv3-Series](nvv3-series.md) en [NVv4-serie](nvv4-series.md)

De virtuele machines van de NV-serie worden aangedreven door [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu's en de NVIDIA Grid-technologie voor desktop-versnelde toepassingen en virtuele Bureau bladen waar klanten hun gegevens of simulaties kunnen visualiseren. Gebruikers kunnen hun grafische intensieve werk stromen op de NV-exemplaren visualiseren om de superieure grafische mogelijkheden te verkrijgen en daarnaast ook werk belastingen met één precisie, zoals code ring en rendering, uit te voeren. Virtuele machines van de NV-serie worden ook aangedreven door de Intel Xeon E5-2690 v3-Cpu's (Haswell).

Elke GPU in NV-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werk station voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de virtuele machine voor een virtuele-toepassings scenario.

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werk stations | Virtuele toepassingen |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = halve M60-kaart.
<br>

### <a name="nc-series"></a>NC-serie
**Nieuwere grootte aanbeveling**: [NC T4 v3-serie](nct4-v3-series.md)

Virtuele machines in de NC-serie worden aangedreven door de [Nvidia Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) -kaart en de Intel Xeon E5-2690 v3-processor (Haswell). Gebruikers kunnen sneller gegevens belichten door gebruik te maken van CUDA voor energie onderzoek toepassingen, crash simulaties, Ray traceed rendering, dieper leren en meer. De NC24r-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen op parallelle computers.

[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = halve K80-kaart.

*RDMA-compatibel


<br>


### <a name="ncv2-series"></a>NCv2-serie
**Nieuwere grootte aanbeveling**: [NC T4 v3-Series](nct4-v3-series.md) en [NC V100 v3-serie](ncv3-series.md)

Virtuele machines uit de NCv2-serie worden aangedreven door NVIDIA Tesla P100 Gpu's. Deze Gpu's kunnen meer dan twee maal de reken prestaties van de NC-serie bieden. Klanten kunnen profiteren van deze bijgewerkte Gpu's voor traditionele HPC-workloads, zoals het maken van tank modellen, DNA-sequentiëren, eiwithoudende analyse, Monte Carlo-simulaties en andere. Naast de Gpu's worden de virtuele machines uit de NCv2-serie ook aangedreven door de Intel Xeon E5-2690 v4-Cpu's (Broadwell).

De NC24rs v2-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen voor parallelle berekeningen.

[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>

> Voor deze VM-serie wordt het vCPU-quotum (core) in uw abonnement in eerste instantie ingesteld op 0 in elke regio. [Vraag een toename van een vCPU-quotum](../azure-portal/supportability/resource-manager-core-quotas-request.md) aan voor deze reeks in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>
| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = One P100-kaart.

*RDMA-compatibel

<br>

### <a name="nd-series"></a>ND-serie
**Nieuwere grootte aanbeveling**: [NDv2-Series](ndv2-series.md) en [NC V100 v3-serie](ncv3-series.md)

De virtuele machines uit de ND-serie vormen een nieuwe toevoeging aan de GPU-familie die is ontworpen voor AI en diep gaande werk belastingen. Ze bieden uitstekende prestaties voor training en demijnen. ND-instanties worden aangedreven door [Nvidia Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) -Gpu's en Intel Xeon E5-2690 v4-Cpu's (Broadwell). Deze instanties bieden uitstekende prestaties voor drijvende-komma bewerkingen met één precisie, voor AI-workloads die gebruikmaken van Microsoft Cognitive Toolkit, tensor flow, Caffe en andere frameworks. De ND-serie biedt ook een veel grotere GPU-geheugen grootte (24 GB), waardoor u veel grotere Neural net-modellen kunt aanpassen. Net als de NC-serie biedt de ND-serie een configuratie met een secundaire netwerk met lage latentie en een hoge door Voer via RDMA en InfiniBand-connectiviteit, zodat u grootschalige trainings taken kunt uitvoeren die veel Gpu's bespannen.

[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>

> Voor deze VM-serie wordt het vCPU (kern quotum) per regio in uw abonnement in eerste instantie ingesteld op 0. [Vraag een toename van een vCPU-quotum](../azure-portal/supportability/resource-manager-core-quotas-request.md) aan voor deze reeks in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>
| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = One P40-kaart.

*RDMA-compatibel

<br>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.