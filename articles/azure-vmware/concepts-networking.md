---
title: Concepten-netwerk-interconnectiviteit
description: Meer informatie over belang rijke aspecten en gebruiks voorbeelden van netwerken en interconnectiviteit in azure VMware-oplossing.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: eb701a7fca2be08d70e603734b9c4d8e266959cd
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475417"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution-netwerken en interconnectiviteit-concepten

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Een handig perspectief op interconnectiviteit is het overwegen van de twee typen Azure VMware-oplossing voor privécloud-implementaties:

1. Met [**eenvoudige Azure-interconnectiviteit**](#azure-virtual-network-interconnectivity) kunt u uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. Deze implementatie is het meest geschikt voor evaluaties of implementaties van Azure VMware-oplossingen waarvoor geen toegang nodig is vanuit on-premises omgevingen.

1. [**Volledig on-premises naar privécloud interconnectiviteit**](#on-premises-interconnectivity) breidt de basis implementatie van alleen Azure uit met interconnectiviteit tussen on-premises en Azure VMware-oplossingen voor persoonlijke Clouds.
 
In dit artikel worden enkele belang rijke concepten besproken waarmee netwerken en interconnectiviteit worden gemaakt, met inbegrip van vereisten en beperkingen. We gaan ook meer informatie over de twee typen Azure VMware-oplossingen persoonlijke Cloud interconnectiviteit-implementaties. In dit artikel vindt u de informatie die u nodig hebt om uw netwerk te configureren om met de Azure VMware-oplossing goed te kunnen werken.

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

In het virtuele netwerk naar de privécloud-implementatie kunt u de privécloud van Azure VMware-oplossingen beheren, werk belastingen gebruiken in uw privécloud en toegang krijgen tot Azure-Services via de ExpressRoute-verbinding. 

In het onderstaande diagram ziet u de basis netwerk interconnectiviteit die zijn ingesteld op het moment van een implementatie van een privécloud. Hierin wordt het logische, op ExpressRoute gebaseerde netwerk weer gegeven tussen een virtueel netwerk in Azure en een privécloud. De interconnectiviteit voldoet aan drie van de primaire use cases:
* Inkomende toegang tot de vCenter-Server en NSX-T-beheer die toegankelijk is vanaf virtuele machines in uw Azure-abonnement en niet vanaf uw on-premises systemen. 
* Uitgaande toegang van Vm's naar Azure-Services. 
* Inkomende toegang en verbruik van werk belastingen met een privécloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basis-virtueel netwerk naar connectiviteit in de privécloud" border="false":::

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

In het virtuele netwerk en on-premises naar volledige privécloud-implementatie hebt u toegang tot uw persoonlijke Clouds van Azure VMware-oplossingen vanuit on-premises omgevingen. Deze implementatie is een uitbrei ding van de basis implementatie die wordt beschreven in de vorige sectie. Net als bij de basis implementatie is een ExpressRoute-circuit vereist, maar bij deze implementatie wordt het gebruikt om vanuit on-premises omgevingen verbinding te maken met uw privécloud in Azure. 

In het onderstaande diagram ziet u de interconnectiviteit on-premises naar de privécloud, waarmee de volgende gebruiks voorbeelden worden ingeschakeld:
* Warme/koude cross-vCenter vMotion
* On-premises naar Azure VMware-oplossing persoonlijke Cloud beheer toegang

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuele netwerken en on-premises volledige particuliere cloud connectiviteit" border="false":::

Voor een volledige interconnectiviteit in uw privécloud schakelt u ExpressRoute Global Reach in en vraagt u een autorisatie sleutel en een privé-peering-ID voor Global Reach op in de Azure Portal. De autorisatie sleutel en de peering-ID worden gebruikt om Global Reach te maken tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw nieuwe privécloud. Zodra de twee ExpressRoute-circuits zijn gekoppeld, worden netwerk verkeer tussen uw on-premises omgevingen gerouteerd naar uw privécloud.  Zie de [zelf studie voor het maken van een ExpressRoute Global Reach peering naar een privécloud](tutorial-expressroute-global-reach-private-cloud.md)voor meer informatie over de procedures om de autorisatie sleutel en de PEERING-id te vragen en te gebruiken.

## <a name="next-steps"></a>Volgende stappen 

Nu u deze netwerk-en interconnectiviteit-concepten hebt behandeld, wilt u mogelijk meer informatie over:

- [Concepten van Azure VMware-oplossingen opslag](concepts-storage.md).
- [Concepten van Azure VMware-oplossings identiteiten](concepts-identity.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

