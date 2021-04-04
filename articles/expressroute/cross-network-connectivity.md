---
title: Connectiviteit tussen meerdere netwerken van Azure
description: Op deze pagina wordt een toepassings scenario beschreven voor cross-netwerk connectiviteit en oplossingen op basis van Azure-netwerk functies.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202494"
---
# <a name="cross-network-connectivity"></a>Onderlinge netwerkconnectiviteit

Fabrikam Inc. heeft een grote fysieke aanwezigheid en Azure-implementatie in VS-Oost. Fabrikam heeft back-end-connectiviteit tussen de on-premises en Azure-implementaties via ExpressRoute. Op dezelfde manier heeft Contoso Ltd. een aanwezigheids-en Azure-implementatie in VS West. Contoso heeft een back-end-verbinding tussen de on-premises en Azure-implementaties via ExpressRoute.  

Fabrikam Inc. verwerft Contoso Ltd. Na de fusie wil Fabrikam de netwerken verbinden. In de volgende afbeelding ziet u het scenario:

![Het toepassings scenario](./media/cross-network-connectivity/premergerscenario.png)

De onderbroken pijlen in het midden van de bovenstaande afbeelding geven de gewenste netwerk verbindingen aan. Met name zijn er drie typen cross-Connections gewenst: 1) Fabrikam en contoso VNets Cross Connect, 2) Cross-premises lokale en VNets Kruis Connects (dat wil zeggen fabrikam on-premises netwerk verbinden met Contoso VNet en Contoso on-premises netwerk verbinden met fabrikam VNet), en 3) Fabrikam en Contoso on-premises Network Cross Connect. 

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd. vóór de fusie.

![Contoso ExpressRoute-route tabel vóór fusie](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

De volgende tabel bevat de efficiënte routes van een virtuele machine in het contoso-abonnement, vóór de fusie. De virtuele machine op het VNet is in de tabel op de hoogte van de VNet-adres ruimte en het on-premises netwerk van contoso, onafhankelijk van de standaard waarden.

![Contoso VM-routes vóór fusie](./media/cross-network-connectivity/contosovm-routes-premerger.png)

De volgende tabel toont de route tabel van de privé-peering van de ExpressRoute van Fabrikam Inc. vóór de fusie.

![Fabrikam ExpressRoute-route tabel vóór fusie](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

De volgende tabel toont de efficiënte routes van een virtuele machine in het fabrikam-abonnement, vóór de fusie. De virtuele machine op het VNet is in de tabel op de hoogte van de VNet-adres ruimte en het on-premises netwerk van Fabrikam, onafhankelijk van de standaard waarden.

![Fabrikam VM-routes vóór fusie](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

In dit artikel gaan we stapsgewijs door en bespreken hoe u de gewenste cross-verbindingen kunt maken met behulp van de volgende Azure-netwerk functies:

* [Peering op virtueel netwerk][Virtual network peering] 
* [Verbinding met virtuele netwerk ExpressRoute][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNets Kruis verbinding maken

Virtual Network-peering (VNet-peering) biedt de meest optimale en beste netwerk prestaties wanneer u twee virtuele netwerken verbindt. VNet-peering ondersteunt peering van twee VNets in dezelfde Azure-regio (ook wel VNet-peering genoemd) en in twee verschillende Azure-regio's (ook wel wereld wijde VNet-peering genoemd). 

Laten we globaal VNet-peering configureren tussen de VNets in contoso en fabrikam Azure-abonnementen. Zie het artikel [peering van een virtueel netwerk maken][Configure VNet peering] voor informatie over het maken van de peering van het virtuele netwerk tussen twee virtuele netwerken.

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van globale VNet-peering.

![De architectuur na VNet-peering](./media/cross-network-connectivity/vnet-peering.png )

De volgende tabel bevat de routes die bekend zijn bij de VM van het contoso-abonnement. Let op de laatste vermelding van de tabel. Dit item is het resultaat van het cross verbinden van de virtuele netwerken.

![Contoso VM-routes na VNet-peering](./media/cross-network-connectivity/contosovm-routes-peering.png)

In de volgende tabel ziet u de routes die bekend zijn bij de werk-VM van Fabrikam-abonnement. Let op de laatste vermelding van de tabel. Dit item is het resultaat van het cross verbinden van de virtuele netwerken.

![Fabrikam VM-routes na VNet-peering](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet-peering koppelt rechtstreeks twee virtuele netwerken (zie er is geen volgende hop voor *VNetGlobalPeering* -vermelding in de bovenstaande twee tabellen)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Verbinding maken tussen VNets en de on-premises netwerken

We kunnen een ExpressRoute-circuit verbinden met meerdere virtuele netwerken. Zie de [abonnements-en service limieten][Subscription limits] voor het maximum aantal virtuele netwerken dat kan worden verbonden met een ExpressRoute-circuit. 

We gaan verbinding maken met het ExpressRoute-circuit van Fabrikam met het VNet-abonnement van Contoso en een soortgelijk ExpressRoute-circuit van Contoso naar het VNet-abonnement van Fabrikam om tussen virtuele netwerken en de on-premises netwerken over te scha kelen. Als u een virtueel netwerk wilt verbinden met een ExpressRoute-circuit in een ander abonnement, moeten we een autorisatie maken en gebruiken.  Zie het artikel: [een virtueel netwerk verbinden met een ExpressRoute-circuit][Connect-ER-VNet].

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van de ExpressRoute-Kruis verbinding met de virtuele netwerken.

![De architectuur na een Expressroutes waaraan-Kruis verbinding](./media/cross-network-connectivity/exr-x-connect.png)

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd., nadat u virtuele netwerken met elkaar hebt verbonden met de on-premises netwerken via ExpressRoute. Zie dat de route tabel routes bevat die deel uitmaken van de virtuele netwerken.

![Contoso ExpressRoute-route tabel na cross-verbinding maken met ExR en VNets](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Fabrikam Inc., nadat u virtuele netwerken met elkaar hebt verbonden met de on-premises netwerken via ExpressRoute. Zie dat de route tabel routes bevat die deel uitmaken van de virtuele netwerken.

![Routerings tabel van Fabrikam ExpressRoute na cross-verbinding maken met ExR en VNets](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

De volgende tabel bevat de routes die bekend zijn bij de VM van het contoso-abonnement. Let op de gateway vermeldingen van het *virtuele netwerk* van de tabel. De virtuele machine ziet routes voor de on-premises netwerken.

![Contoso VM-routes na Kruis verbinding maken met ExR en VNets](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

In de volgende tabel ziet u de routes die bekend zijn bij de werk-VM van Fabrikam-abonnement. Let op de gateway vermeldingen van het *virtuele netwerk* van de tabel. De virtuele machine ziet routes voor de on-premises netwerken.

![Fabrikam VM-routes na Kruis verbinding maken met ExR en VNets](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>In de abonnementen Fabrikam en/of Contoso kunt u ook spoke VNets naar het respectieve hub VNet (een hub-en spoke-ontwerp wordt niet geïllustreerd in de architectuur diagrammen in dit artikel). Door de Kruis verbindingen tussen de hub VNet-gateways naar ExpressRoute wordt ook communicatie tussen Oost-en West-hubs en spokes toegestaan.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross-premises verbinding maken met netwerk

ExpressRoute Global Reach biedt connectiviteit tussen on-premises netwerken die zijn verbonden met verschillende ExpressRoute-circuits. Laten we Global Reach configureren tussen Contoso en fabrikam ExpressRoute-circuits. Omdat de ExpressRoute-circuits zich in verschillende abonnementen bevinden, moeten we een autorisatie maken en gebruiken. Zie [Configure ExpressRoute Global Reach][Configure Global Reach] article (Engelstalig) voor stapsgewijze instructies.

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van Global Reach.

![De architectuur na het configureren van Global Reach](./media/cross-network-connectivity/globalreach.png)

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd., na het configureren van Global Reach. Zie dat de route tabel routes bevat die deel uitmaken van de on-premises netwerken. 

![Contoso ExpressRoute-route tabel na Global Reach](./media/cross-network-connectivity/contosoexr-rt-gr.png)

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Fabrikam Inc. na het configureren van Global Reach. Zie dat de route tabel routes bevat die deel uitmaken van de on-premises netwerken.

![ExpressRoute-route tabel van Fabrikam na Global Reach]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Volgende stappen

Zie [Veelgestelde vragen over virtuele netwerken][VNet-FAQ]voor verdere vragen over VNet en vnet-peering. Zie [Veelgestelde vragen over ExpressRoute][ER-FAQ] voor meer vragen over de connectiviteit van ExpressRoute en het virtuele netwerk.

Global Reach wordt op basis van land/regio in een land/regio uitgerold. Zie [ExpressRoute Global Reach][Global Reach]als u wilt weten of Global Reach beschikbaar is in de gewenste landen/regio's.

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq