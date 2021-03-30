---
title: Azure-netwerk bronnen verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om virtuele netwerken en andere netwerk bronnen te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485232"
---
# <a name="move-guidance-for-networking-resources"></a>Richt lijnen voor netwerk bronnen verplaatsen

In dit artikel wordt beschreven hoe u virtuele netwerken en andere netwerk bronnen verplaatst voor specifieke scenario's.

## <a name="dependent-resources"></a>Afhankelijke resources

Wanneer u een virtueel netwerk verplaatst, moet u ook de afhankelijke resources verplaatsen. Voor VPN-gateways moet u IP-adressen, virtuele netwerk gateways en alle gekoppelde verbindings bronnen verplaatsen. Lokale netwerk gateways kunnen zich in een andere resource groep bevindt.

Als u een virtuele machine met een netwerk interface kaart wilt verplaatsen naar een nieuw abonnement, moet u alle afhankelijke resources verplaatsen. Verplaats het virtuele netwerk voor de netwerk interface kaart, alle andere netwerk interface kaarten voor het virtuele netwerk en de VPN-gateways.

Zie [scenario voor verplaatsen tussen abonnementen](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)voor meer informatie.

## <a name="peered-virtual-network"></a>Gekoppeld virtueel netwerk

Als u een gekoppeld virtueel netwerk wilt verplaatsen, moet u eerst de peering van het virtuele netwerk uitschakelen. Als deze is uitgeschakeld, kunt u het virtuele netwerk verplaatsen. Schakel na het verplaatsen de peering van het virtuele netwerk opnieuw in.

## <a name="subnet-links"></a>Subnet koppelingen

U kunt een virtueel netwerk niet verplaatsen naar een ander abonnement als het virtuele netwerk een subnet met resource navigatie koppelingen bevat. Als er bijvoorbeeld een Azure cache voor de redis-resource wordt geïmplementeerd in een subnet, heeft dat subnet een koppeling naar een resource navigatie.

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.
