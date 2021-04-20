---
title: Azure VMware Solution verbinden met uw on-premises omgeving
description: Leer Azure VMware Solution verbinden met uw on-premises omgeving.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725607"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware Solution verbinden met uw on-premises omgeving

In dit artikel gaat u door met het gebruik van de [informatie verzameld tijden de planning](production-ready-deployment-steps.md) om Azure VMware Solution te verbinden met uw on-premises omgeving.

Voordat u begint, moet u een ExpressRoute-circuit van uw on-premises omgeving naar Azure hebben.


>[!NOTE]
> U kunt verbinding maken via VPN, maar dit wordt niet besproken in deze quickstart.

## <a name="establish-an-expressroute-global-reach-connection"></a>Een ExpressRoute Global Reach-verbinding maken

Als u een on-premises verbinding tot stand wilt brengen met uw VMware Solution-privécloud met behulp van ExpressRoute Global Reach, volg dan de zelfstudie [On-premises omgevingen peeren met een privécloud](tutorial-expressroute-global-reach-private-cloud.md).

Deze zelfstudie resulteert in een verbinding zoals weergegeven in het diagram.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach on-premises netwerkverbindingsdiagram." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>On-premises netwerkverbinding controleren

U ziet nu in uw **on-premises edge-router** waar de ExpressRoute de NSX-T-netwerksegmenten en de Azure VMware Solution-beheersegmenten verbindt.

>[!IMPORTANT]
>Iedereen heeft een andere omgeving en sommigen moeten deze routes toestaan om door te geven in het on-premises netwerk.  

Sommige omgevingen hebben firewalls die het ExpressRoute-circuit beveiligen.  Als er geen firewalls of routeverwijdering worden uitgevoerd, kunt u uw Azure VMware Solution vCenter-server of een [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) op het NSX-T-segment pingen vanuit uw on-premises omgeving. Daarnaast kunt u vanuit de virtuele machine op het NSX-T-segment bronnen in uw on-premises omgeving bereiken.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende sectie om VMware HCX te implementeren en te configureren

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)