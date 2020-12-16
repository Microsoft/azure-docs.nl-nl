---
title: Prestaties van virtuele machines en schijven-Windows
description: Meer informatie over hoe virtuele machines en de bijbehorende schijven samen werken voor de prestaties van Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584116"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Prestaties van virtuele machines en schijven (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuele machine niet in cache opgeslagen limieten versus cache
 Virtuele machines waarvoor Premium-opslag is ingeschakeld en Premium Storage-caching hebben ingeschakeld, hebben twee verschillende limieten voor opslag bandbreedte. We gaan nu verder met het bekijken van de Standard_D8s_v3 virtuele machine als voor beeld. Hier vindt u de documentatie over de [Dsv3-serie](../dv3-dsv3-series.md) en de Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Laten we een benchmark test uitvoeren op deze VM en schijf combinatie waarmee i/o-activiteiten worden gemaakt. u kunt [hier](disks-benchmarks.md)meer informatie vinden over de Bench Mark-opslag-i/o in Azure. Vanuit het benchmarking-hulp programma kunt u zien dat de combi natie van de virtuele machine en schijf 22.800 IOPS kan bezorgen:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]