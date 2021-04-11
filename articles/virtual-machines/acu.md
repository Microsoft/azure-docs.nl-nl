---
title: Overzicht van de Azure Compute-eenheid
description: Overzicht van het concept van de Azure-reken eenheden. De ACU biedt een manier om de CPU-prestaties in azure Sku's te vergelijken.
author: mimckitt
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: 3233695f8a8cc14d70f0c482458e77094dde7622
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969067"
---
# <a name="azure-compute-unit-acu"></a>Azure Compute-eenheid (ACU)

Het concept van de Azure Compute Unit (ACU) biedt een manier om Compute-prestaties (CPU) te vergelijken tussen Azure-Sku's. Op die manier kunt u eenvoudig bepalen welke SKU hoogstwaarschijnlijk aan uw prestatiebehoeften zal voldoen. ACU is momenteel gestandaardiseerd op een kleine (Standard_A1) virtuele machine die wordt 100 en alle andere Sku's vertegenwoordigen vervolgens ongeveer de hoeveelheid sneller die SKU een standaard referentie kan uitvoeren

* ACUs gebruik Intel® Turbo-technologie om de CPU-frequentie te verhogen en een hogere prestatie te bieden.  De omvang van de prestatie verhoging kan variëren op basis van de VM-grootte, werk belasting en andere workloads die op dezelfde host worden uitgevoerd.

* * ACUs gebruik AMD® Boost-technologie om de CPU-frequentie te verhogen en een betere prestaties te leveren.  De omvang van de prestatie verhoging kan variëren op basis van de VM-grootte, werk belasting en andere workloads die op dezelfde host worden uitgevoerd.

Hyper-Threaded en geschikt voor het uitvoeren van geneste virtualisatie

AMD simultaan multi threading-technologie

> [!IMPORTANT]
> De ACU is slechts een richtlijn. De resultaten voor uw workload kunnen verschillen.
<br>

| SKU-familie | ACU \ vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1-A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5-A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2-A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2-A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8-A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |Varieert | 1:1 |
| [D1-D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2-D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1-DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2-DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160-190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160-190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Dasv4](dav4-dasv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Dv4](dv4-dsv4-series.md) | 195-210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195-210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160-190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160-190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Easv4](eav4-easv4-series.md) | 230-260 * * | 2:1\*\*\*\* |
| [Ev4](ev4-esv4-series.md) | 195-210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195-210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [F2s_v2-F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1-F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [D11-F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1-GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [HxBxD](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [KM](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2-L80s_v2](lsv2-series.md) |150-175 * * | 2:1\*\*\*\* |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230-260 * * | 2:1\*\*\*\* |

Hier vindt u koppelingen naar meer informatie over de verschillende grootten:

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
