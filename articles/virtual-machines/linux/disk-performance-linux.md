---
title: Prestaties van virtuele machines en schijven-Linux
description: Meer informatie over hoe virtuele machines en de bijbehorende schijven samen werken in combi natie met prestaties op Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591851"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Prestaties van virtuele machines en schijven (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuele machine niet in cache opgeslagen limieten versus cache
Voor virtuele machines die zijn ingeschakeld voor Premium Storage en Premium Storage-caching, hebben twee verschillende bandbreedte limieten voor opslag. Laten we eens kijken naar de Standard_D8s_v3 virtuele machine als voor beeld. Hier vindt u de documentatie over de [Dsv3-serie](../dv3-dsv3-series.md) en de Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Laten we een benchmark test uitvoeren op deze virtuele machine en schijf combinatie waarmee i/o-activiteiten worden gemaakt. Zie [Bench Mark your application on Azure Disk Storage](disks-benchmarks.md)voor meer informatie over de Bench Mark-opslag-io in Azure. Vanuit het hulp programma benchmarking kunt u zien dat de combi natie van de virtuele machine en schijf 22.800 IOPS kan behaalt:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
