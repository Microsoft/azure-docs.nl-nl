---
title: Linux-Vm's samen zoeken
description: Meer informatie over het co-locatie van Azure VM-resources voor Linux kan de latentie verbeteren.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 304623ca50fd030ab6e016b940f8be52819c161a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500628"
---
# <a name="co-locate-resources-for-improved-latency"></a>Resources gecombineerd zoeken voor verbeterde latentie

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt, wat van invloed kan zijn op de algehele prestaties van uw toepassing. 

## <a name="proximity-placement-groups"></a>Nabijheidsplaatsingsgroepen

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine op een [proximity-plaatsings groep](proximity-placement-groups.md) met behulp van de Azure cli.

Meer informatie over het testen van de [netwerk latentie](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Meer informatie over het optimaliseren van de [netwerk doorvoer](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Meer informatie over het [gebruik van proximity-plaatsings groepen met SAP-toepassingen](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).