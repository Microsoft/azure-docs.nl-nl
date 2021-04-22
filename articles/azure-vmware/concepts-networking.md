---
title: Concepten - Interconnectiviteit tussen netwerken
description: Meer informatie over belangrijke aspecten en gebruiksgevallen van netwerken en interconnectiviteit in Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871683"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution en interconnectiviteitsconcepten

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Er zijn twee manieren om interconnectiviteit in de Azure VMware Solution privécloud:

- [**Met interconnectiviteit van**](#azure-virtual-network-interconnectivity) alleen Azure kunt u uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. Deze implementatie is het meest geschikt voor Azure VMware Solution evaluaties of implementaties waarvoor geen toegang vanuit on-premises omgevingen is vereist.

- [**Volledige interconnectiviteit van on-premises**](#on-premises-interconnectivity) naar privécloud breidt de basis-azure-implementatie uit met interconnectiviteit tussen on-premises en Azure VMware Solution privéclouds.
 
In dit artikel worden de belangrijkste concepten beschreven die netwerken en interconnectiviteit tot stand brengen, inclusief vereisten en beperkingen. In dit artikel vindt u de informatie die u moet weten om uw netwerk te configureren voor gebruik met Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution van privéclouds

De gebruiksgevallen voor Azure VMware Solution privé clouds zijn onder andere:
- Nieuwe VMware-VM-workloads in de cloud
- Bursting van VM-workloads naar de cloud (on-premises Azure VMware Solution alleen)
- Migratie van VM-workloads naar de cloud (on-premises Azure VMware Solution alleen)
- Herstel na noodherstel (Azure VMware Solution naar Azure VMware Solution of on-premises naar Azure VMware Solution)
- Verbruik van Azure-services

> [!TIP]
> Alle use cases voor de Azure VMware Solution-service zijn ingeschakeld met on-premises naar privécloudconnectiviteit.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnectiviteit van virtueel Azure-netwerk

U kunt uw virtuele Azure-netwerk verbinden met Azure VMware Solution implementatie van de privécloud. U kunt uw privécloud Azure VMware Solution, workloads in uw privécloud gebruiken en toegang krijgen tot andere Azure-services.

In het onderstaande diagram ziet u de basisnetwerk interconnectiviteit die tot stand is gebracht op het moment van de implementatie van een privécloud. Het toont de logische netwerken tussen een virtueel netwerk in Azure en een privécloud. Deze connectiviteit wordt tot stand gebracht via een back-Azure VMware Solution ExpressRoute. De interconnectiviteit voldoet aan de volgende primaire gebruiksgevallen:

- Inkomende toegang tot de vCenter-server en NSX-T-manager die toegankelijk is vanaf VM's in uw Azure-abonnement.
- Uitgaande toegang van VM's in de privécloud naar Azure-services.
- Inkomende toegang van workloads die worden uitgevoerd in de privécloud.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basisverbinding van virtueel netwerk naar privécloud" border="false":::

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

In het volledig onderling verbonden scenario hebt u toegang tot de Azure VMware Solution uw virtuele Azure-netwerk(s) en on-premises. Deze implementatie is een uitbreiding van de basis implementatie die in de vorige sectie is beschreven. Een ExpressRoute-circuit is vereist om on-premises verbinding te maken met Azure VMware Solution privécloud in Azure.

In het onderstaande diagram ziet u de interconnectiviteit tussen on-premises en privéclouds, waardoor de volgende gebruiksgevallen mogelijk zijn:

- Hot/cold vCenter vMotion tussen on-premises en Azure VMware Solution.
- On-premises om Azure VMware Solution toegang tot privécloudbeheer te bieden.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Connectiviteit van virtuele netwerken en on-premises volledige privéclouds" border="false":::

Voor volledige interconnectiviteit met uw privécloud moet u ExpressRoute Global Reach inschakelen en vervolgens een autorisatiesleutel en persoonlijke peering-id voor Global Reach in de Azure Portal. De autorisatiesleutel en peering-id worden gebruikt om een Global Reach tot stand te brengen tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw privécloud. Zodra de twee ExpressRoute-circuits zijn gekoppeld, wordt netwerkverkeer tussen uw on-premises omgevingen naar uw privécloud gerouterd. Zie de zelfstudie voor het maken van een [ExpressRoute-Global Reach peering met een privécloud](tutorial-expressroute-global-reach-private-cloud.md)voor meer informatie over de procedures.

## <a name="limitations"></a>Beperkingen
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Volgende stappen 

Nu u de verschillende netwerk- Azure VMware Solution en interconnectiviteitsconcepten hebt behandeld, kunt u het volgende leren:

- [Azure VMware Solution opslagconcepten](concepts-storage.md)
- [Azure VMware Solution identiteitsconcepten](concepts-identity.md)
- [Een resource Azure VMware Solution inschakelen](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
