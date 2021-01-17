---
title: Prestaties van virtuele machine en schijf
description: Meer informatie over hoe virtuele machines en de bijbehorende schijven samen werken in combi natie met prestaties.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540638"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestaties van virtuele machine en schijf
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuele machine niet in cache opgeslagen limieten versus cache
Voor virtuele machines die zijn ingeschakeld voor Premium Storage en Premium Storage-caching, hebben twee verschillende bandbreedte limieten voor opslag. Laten we eens kijken naar de Standard_D8s_v3 virtuele machine als voor beeld. Hier vindt u de documentatie over de [Dsv3-serie](dv3-dsv3-series.md) en de Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Laten we een benchmark test uitvoeren op deze virtuele machine en schijf combinatie waarmee i/o-activiteiten worden gemaakt. Zie [Bench Mark your application on Azure Disk Storage](disks-benchmarks.md)voor meer informatie over de Bench Mark-opslag-io in Azure. Vanuit het hulp programma benchmarking kunt u zien dat de combi natie van de virtuele machine en schijf 22.800 IOPS kan behaalt:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
