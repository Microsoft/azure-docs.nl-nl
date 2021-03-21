---
title: Azure VMware Solution verbinden met uw on-premises omgeving
description: Leer Azure VMware Solution verbinden met uw on-premises omgeving.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491789"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware Solution verbinden met uw on-premises omgeving

In dit artikel gaat u door met het gebruik van de [informatie verzameld tijden de planning](production-ready-deployment-steps.md) om Azure VMware Solution te verbinden met uw on-premises omgeving.

Voordat u begint, zijn er twee vereisten om Azure VMware Solution te verbinden met uw on-premises omgeving:

- Een ExpressRoute-circuit van uw on-premises omgeving naar Azure.
- Een/29 niet-overlappend CIDR-netwerk adres blok voor de ExpressRoute-Global Reach peering, die u hebt gedefinieerd als onderdeel van de [plannings fase](production-ready-deployment-steps.md).

>[!NOTE]
> U kunt verbinding maken via VPN, maar dit wordt niet besproken in deze quickstart.

## <a name="establish-an-expressroute-global-reach-connection"></a>Een ExpressRoute Global Reach-verbinding maken

Als u een on-premises verbinding tot stand wilt brengen met uw VMware Solution-privécloud met behulp van ExpressRoute Global Reach, volg dan de zelfstudie [On-premises omgevingen peeren met een privécloud](tutorial-expressroute-global-reach-private-cloud.md).

Deze zelf studie resulteert in een verbinding zoals in het diagram wordt weer gegeven.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach on-premises netwerk verbindings diagram." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>On-premises netwerkverbinding controleren

U ziet nu in uw **on-premises edge-router** waar de ExpressRoute de NSX-T-netwerksegmenten en de Azure VMware Solution-beheersegmenten verbindt.

>[!IMPORTANT]
>Iedereen heeft een andere omgeving en sommigen moeten deze routes toestaan om door te geven in het on-premises netwerk.  

Sommige omgevingen hebben firewalls die het ExpressRoute-circuit beveiligen.  Als er geen firewalls of routeverwijdering worden uitgevoerd, kunt u uw Azure VMware Solution vCenter-server of een [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) op het NSX-T-segment pingen vanuit uw on-premises omgeving. Daarnaast kunt u vanuit de virtuele machine op het NSX-T-segment bronnen in uw on-premises omgeving bereiken.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende sectie om VMware HCX te implementeren en te configureren

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)