---
title: Azure DDoS Protection standaard bedrijfscontinuïteit | Microsoft Docs
description: Meer informatie over wat u moet doen in het geval van een onderbreking van de Azure-service die van invloed is op Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730453"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard : bedrijfscontinuïteit

Bedrijfscontinuïteit en herstel na nood Azure DDoS Protection Standard stelt uw bedrijf in staat om te blijven werken bij een onderbreking. In dit artikel wordt de beschikbaarheid (binnen regio) en herstel na noodherstel besproken.

## <a name="overview"></a>Overzicht
Azure DDoS Protection Standard beveiligt openbare IP-adressen in virtuele netwerken. Beveiliging is eenvoudig in te stellen op een nieuw of bestaand virtueel netwerk en vereist geen wijzigingen in de toepassing of resource.

Een Virtual Network (VNet) is een logische weergave van uw netwerk in de cloud. VNets fungeren als een vertrouwensgrens voor het hosten van uw resources, zoals Azure Application Gateway, Azure Firewall en Azure Virtual Machines. Deze wordt gemaakt binnen het bereik van een regio. U *kunt* VNets met dezelfde adresruimte maken in twee verschillende regio's (bijvoorbeeld US - oost en US - west), maar omdat ze dezelfde adresruimte hebben, kunt u ze niet met elkaar verbinden. 

## <a name="business-continuity"></a>Bedrijfscontinuïteit

Er kunnen verschillende manieren zijn waarop uw toepassing kan worden onderbroken. Een regio kan volledig worden afgesloten vanwege een natuurramp of een gedeeltelijke ramp, als gevolg van een storing van meerdere apparaten of services. De impact op uw beveiligde VNet's verschilt in elk van deze situaties.

**V: Wat moet ik doen als er een storing optreedt voor een hele regio? Als een regio bijvoorbeeld volledig is afgesloten vanwege een natuurramp? Wat gebeurt er met de virtuele netwerken die in de regio worden gehost?**

A: Het virtuele netwerk en de resources in de getroffen regio blijven ontoegankelijk tijdens de onderbreking van de service.

![Eenvoudig Virtual Network diagram.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**V: Wat kan ik doen om hetzelfde virtuele netwerk opnieuw te maken in een andere regio?**

A: Virtuele netwerken zijn redelijk lichte resources. U kunt Azure-API's aanroepen om een VNet met dezelfde adresruimte in een andere regio te maken. Als u dezelfde omgeving wilt maken die aanwezig was in de getroffen regio, maakt u API-aanroepen om de resources in de VNets die u had opnieuw teploeeren. Als u on-premises connectiviteit hebt, zoals in een hybride implementatie, moet u een nieuwe VPN Gateway implementeren en verbinding maken met uw on-premises netwerk.

Zie Een virtueel netwerk maken voor het [maken van een virtueel netwerk.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

**V: Kan een replica van een VNet in een bepaalde regio van tevoren opnieuw worden gemaakt in een andere regio?**

A: Ja, u kunt van tevoren twee VNets maken met dezelfde privé-IP-adresruimte en resources in twee verschillende regio's. Als u internetservices host in het VNet, hebt u mogelijk Traffic Manager georouteerverkeer naar de regio die actief is. U kunt echter geen twee VNets met dezelfde adresruimte verbinden met uw on-premises netwerk, omdat dit routeringsproblemen zou veroorzaken. Op het moment van nood en verlies van een VNet in één regio kunt u het andere VNet in de beschikbare regio verbinden met de overeenkomende adresruimte voor uw on-premises netwerk.

Zie Een virtueel netwerk maken voor het [maken van een virtueel netwerk.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beveiligingsplan.](manage-ddos-protection.md)