---
title: Bursting van beheerde schijven
description: Meer informatie over schijf bursting voor Azure-schijven en Azure virtual machines.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585048"
---
# <a name="managed-disk-bursting"></a>Bursting van beheerde schijven
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burstisatie op het niveau van de virtuele machine
Burstisatie op VM-niveau is ingeschakeld voor de volgende VM-reeks in alle regio's waarvoor deze worden ondersteund:
- [Lsv2-serie](lsv2-series.md)
- [Dsv3-serie](dv3-dsv3-series.md)
- [Esv3-serie](ev3-esv3-series.md)

Bursting is standaard ingeschakeld voor virtuele machines die dit ondersteunen.

## <a name="disk-level-bursting"></a>Burstisatie op schijf niveau
Bursting is ook beschikbaar op onze [Premium-ssd's](disks-types.md#premium-ssd) voor de schijf grootten P20 en kleiner in alle regio's in azure Public-, Government-en China-Clouds. Schijf bursting is standaard ingeschakeld voor alle nieuwe en bestaande implementaties van de schijf grootten die dit ondersteunen. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [metrische gegevens over schijf bursting](disks-metrics.md)voor meer informatie over het verkrijgen van inzicht in uw burst-resources.
