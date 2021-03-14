---
title: Concepten-netwerk-interconnectiviteit
description: Meer informatie over belang rijke aspecten en gebruiks voorbeelden van netwerken en interconnectiviteit in azure VMware-oplossing.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: cd62949c13b1f12e635d8d7bf07518a94c4e8d4b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462577"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution-netwerken en interconnectiviteit-concepten

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Er zijn twee manieren om interconnectiviteit te maken in de privécloud van Azure VMware-oplossing:

1. Met [**eenvoudige Azure-interconnectiviteit**](#azure-virtual-network-interconnectivity) kunt u uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. Deze implementatie is het meest geschikt voor evaluaties of implementaties van Azure VMware-oplossingen waarvoor geen toegang nodig is vanuit on-premises omgevingen.

1. [**Volledig on-premises naar privécloud interconnectiviteit**](#on-premises-interconnectivity) breidt de basis implementatie van alleen Azure uit met interconnectiviteit tussen on-premises en Azure VMware-oplossingen voor persoonlijke Clouds.
 
In dit artikel worden de belangrijkste concepten besproken waarmee netwerken en interconnectiviteit worden gemaakt, met inbegrip van vereisten en beperkingen. In dit artikel vindt u de informatie die u nodig hebt om uw netwerk te configureren voor gebruik met de Azure VMware-oplossing.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware-oplossing Private Cloud Use cases

De use cases voor persoonlijke Clouds van Azure VMware-oplossingen zijn:
- Nieuwe workloads voor virtuele VMware-machines in de Cloud
- VM-werk belasting bursting naar de Cloud (alleen on-premises naar Azure VMware-oplossing)
- Migratie van VM-workloads naar de Cloud (alleen on-premises naar Azure VMware-oplossing)
- Herstel na nood geval (Azure VMware-oplossing voor Azure VMware-oplossing of on-premises naar Azure VMware-oplossing)
- Verbruik van Azure-Services

> [!TIP]
> Alle use cases voor de Azure VMware Solution service zijn ingeschakeld met on-premises-verbinding met de privécloud.

## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network interconnectiviteit

U kunt uw virtuele Azure-netwerk verbinden met de Azure VMware-oplossing voor de privécloud-implementatie. U kunt uw persoonlijke cloud van Azure VMware-oplossingen beheren, werk belastingen gebruiken in uw privécloud en toegang krijgen tot andere Azure-Services.

In het onderstaande diagram ziet u de basis netwerk interconnectiviteit die zijn ingesteld op het moment van een implementatie van een privécloud. De logische netwerken tussen een virtueel netwerk in Azure en een privécloud worden weer gegeven. Deze verbinding wordt tot stand gebracht via een back-ExpressRoute die deel uitmaakt van de Azure VMware-oplossings service. De interconnectiviteit voldoet aan de volgende primaire use-cases:

- Inkomende toegang tot de vCenter-Server en NSX-T-beheer die toegankelijk is vanaf virtuele machines in uw Azure-abonnement.
- Uitgaande toegang van Vm's op de privécloud naar Azure-Services.
- Binnenkomende toegang van workloads die worden uitgevoerd in de privécloud.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basis-virtueel netwerk naar connectiviteit in de privécloud" border="false":::

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

In het volledig interconnected scenario hebt u toegang tot de Azure VMware-oplossing vanuit uw Azure Virtual Network (s) en on-premises. Deze implementatie is een uitbrei ding van de basis implementatie die wordt beschreven in de vorige sectie. Een ExpressRoute-circuit is vereist om vanuit on-premises verbinding te maken met uw persoonlijke cloud van Azure VMware-oplossing in Azure.

In het onderstaande diagram ziet u de interconnectiviteit on-premises naar de privécloud, waarmee de volgende gebruiks voorbeelden worden ingeschakeld:

- Hot/koude vCenter vMotion tussen on-premises en Azure VMware-oplossing.
- On-premises naar Azure VMware-oplossing persoonlijke Cloud beheer toegang.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuele netwerken en on-premises volledige particuliere cloud connectiviteit" border="false":::

Voor een volledige interconnectiviteit voor uw privécloud moet u ExpressRoute Global Reach inschakelen en vervolgens een autorisatie sleutel en een privé-peering-ID aanvragen voor Global Reach in de Azure Portal. De autorisatie sleutel en de peering-ID worden gebruikt om Global Reach te maken tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw privécloud. Zodra de twee ExpressRoute-circuits zijn gekoppeld, worden netwerk verkeer tussen uw on-premises omgevingen gerouteerd naar uw privécloud. Voor meer informatie over de procedures raadpleegt u de [zelf studie over het maken van een ExpressRoute Global Reach peering naar een privécloud](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Beperkingen
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Volgende stappen 

Nu u de Azure VMware-oplossingen voor netwerk-en interconnectiviteit-concepten hebt behandeld, wilt u mogelijk meer informatie over:

- [Concepten van Azure VMware-oplossingen opslag](concepts-storage.md).
- [Concepten van Azure VMware-oplossings identiteiten](concepts-identity.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
