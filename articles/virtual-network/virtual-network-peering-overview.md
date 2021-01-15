---
title: Peering van virtuele netwerken in Azure
titlesuffix: Azure Virtual Network
description: Meer informatie over Virtual Network-peering in azure, met inbegrip van hoe u netwerken kunt verbinden in azure Virtual Network.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: feea2d54edd8a93e6e0effbef03389ef895d5ffb
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216747"
---
# <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Met Virtual Network-peering kunt u naadloos verbinding maken met twee of meer [virtuele netwerken](virtual-networks-overview.md) in Azure. De virtuele netwerken worden als een voor connectiviteits doeleinden weer gegeven. Het verkeer tussen virtuele machines in gekoppelde virtuele netwerken maakt gebruik van de micro soft backbone-infra structuur. Net als verkeer tussen virtuele machines in hetzelfde netwerk, wordt verkeer alleen gerouteerd via het *particuliere* netwerk van micro soft.

Azure ondersteunt de volgende typen peering:

* Peering op virtueel netwerk: virtuele netwerken in dezelfde Azure-regio verbinden.
* Globale virtuele netwerk peering: virtuele netwerken verbinden tussen Azure-regio's.

Enkele voordelen van het gebruik van peering van virtuele netwerken - lokaal of globaal - zijn:

* Een verbinding met lage latentie en hoge bandbreedte tussen resources in verschillende virtuele netwerken.
* De mogelijkheid voor bronnen in één virtueel netwerk om te communiceren met resources in een ander virtueel netwerk.
* De mogelijkheid om gegevens over te dragen tussen virtuele netwerken over Azure-abonnementen, Azure Active Directory-tenants, implementatie modellen en Azure-regio's.
* De mogelijkheid om virtuele netwerken die zijn gemaakt via de Azure Resource Manager te koppelen.
* De mogelijkheid om een virtueel netwerk dat is gemaakt via Resource Manager te koppelen aan één dat is gemaakt via het klassieke implementatie model. Zie [Over Azure-implementatiemodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over Azure-implementatiemodellen.
* Geen downtime tot bronnen in een virtueel netwerk bij het maken van de peering of nadat de peering is gemaakt.

Netwerkverkeer tussen gekoppelde virtuele netwerken is privé. Verkeer tussen de virtuele netwerken wordt opgeslagen in het Microsoft-backbone-netwerk. Er zijn geen openbare internetverbinding, gateways of versleuteling vereist in de communicatie tussen de virtuele netwerken.

## <a name="connectivity"></a>Connectiviteit

Voor gekoppelde virtuele netwerken kunnen resources in een virtueel netwerk rechtstreeks verbinding maken met resources in het peered virtuele netwerk.

De netwerklatentie tussen virtuele machines in gekoppelde virtuele netwerken in dezelfde regio is gelijk aan de latentie binnen één virtueel netwerk. De netwerkdoorvoer is gebaseerd op de bandbreedte die is toegestaan voor de virtuele machine, evenredig aan de grootte. Er gelden verder geen extra beperkingen voor bandbreedte binnen de peering.

Het verkeer tussen virtuele machines in gekoppelde virtuele netwerken wordt rechtstreeks doorgestuurd via de Microsoft-backbone-infrastructuur, niet via een gateway of via het openbare internet.

U kunt netwerk beveiligings groepen in beide virtuele netwerken Toep assen om de toegang tot andere virtuele netwerken of subnetten te blok keren.
Wanneer u peering voor het virtuele netwerk configureert, opent of sluit u de regels voor de netwerk beveiligings groep tussen de virtuele netwerken. Als u de volledige connectiviteit tussen gekoppelde virtuele netwerken opent, kunt u netwerk beveiligings groepen Toep assen voor het blok keren of weigeren van specifieke toegang. Volledige connectiviteit is de standaard optie. Zie [beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.

## <a name="service-chaining"></a>Servicechaining

Met Service ketens kunt u verkeer van het ene virtuele netwerk naar een virtueel apparaat of gateway in een gekoppeld netwerk omleiden via door de gebruiker gedefinieerde routes.

Als u service ketening wilt inschakelen, configureert u door de gebruiker gedefinieerde routes die naar virtuele machines in gekoppelde virtuele netwerken verwijzen als het *volgende hop* -IP-adres. Door de gebruiker gedefinieerde routes kunnen ook verwijzen naar virtuele netwerk gateways om service ketens in te scha kelen.

U kunt hub-en-spoke netwerken implementeren, waarbij het virtuele netwerk van de hub fungeert als host voor infrastructuur onderdelen, zoals een virtueel netwerk apparaat of een VPN-gateway. Alle virtuele spoke-netwerken kunnen vervolgens worden gekoppeld aan het virtuele hub-netwerk. Verkeer loopt via virtuele netwerk apparaten of VPN-gateways in het virtuele hub-netwerk.

Met peering van virtuele netwerken kan de volgende hop op de door de gebruiker gedefinieerde route het IP-adres zijn van een virtuele machine in het gekoppelde virtuele netwerk of VPN-gateway. U kunt geen routeren tussen virtuele netwerken met een door de gebruiker gedefinieerde route waarmee een Azure ExpressRoute-gateway wordt opgegeven als het type van de volgende hop. Zie [Door de gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined) voor meer informatie over door de gebruiker gedefinieerde routes. Zie [hub-spoke-netwerk topologie in azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het maken van een hub-en-spoke-netwerk topologie.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways en on-premises connectiviteit

Elk virtueel netwerk, met inbegrip van een gekoppeld virtueel netwerk, kan een eigen gateway hebben. Een virtueel netwerk kan de gateway gebruiken om verbinding te maken met een on-premises netwerk. U kunt ook [virtuele netwerk-naar-virtuele netwerk verbindingen](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) configureren door gateways te gebruiken, zelfs voor gekoppelde virtuele netwerken.

Wanneer u beide opties configureert voor virtuele netwerk interconnectiviteit, loopt het verkeer tussen de virtuele netwerken via de peering-configuratie. Het verkeer maakt gebruik van de Azure-backbone.

U kunt de gateway in het gekoppelde virtuele netwerk ook configureren als een doorvoer punt naar een on-premises netwerk. In dit geval kan het virtuele netwerk dat gebruikmaakt van een externe gateway, geen eigen gateway hebben. Een virtueel netwerk heeft maar één gateway. De gateway is een lokale of externe gateway in het gekoppelde virtuele netwerk, zoals wordt weer gegeven in het volgende diagram:

![doorvoer bij peering van virtuele netwerken](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Zowel virtuele netwerk peering als globale peering van virtuele netwerken ondersteunen gateway doorvoer.

Gateway overdracht tussen virtuele netwerken die via verschillende implementatie modellen zijn gemaakt, wordt ondersteund. De gateway moet zich in het virtuele netwerk in het Resource Manager-model bevindt. Zie voor meer informatie over het gebruik van een gateway voor de doorvoer, [Een VPN-gateway configureren voor de doorvoer in een virtueel netwerkpeering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wanneer u virtuele netwerken peert die één Azure ExpressRoute-verbinding delen, loopt het verkeer tussen de computers via de peering-relatie. Dat verkeer gebruikmaakt van het Azure-backbone-netwerk. U kunt nog steeds in elk virtueel netwerk lokale gateways gebruiken om verbinding te maken met het on-premises circuit. Anders kunt u een gedeelde gateway gebruiken en transit configureren voor on-premises connectiviteit.

## <a name="troubleshoot"></a>Problemen oplossen

Als u wilt controleren of de virtuele netwerken zijn gekoppeld, kunt u de juiste routes controleren. Controleer de routes voor een netwerk interface in elk subnet van een virtueel netwerk. Als een peering op een virtueel netwerk bestaat, hebben alle subnets binnen het virtuele netwerk routes met het volgende hoptype *VNet-peering* voor elke adresruimte in elk gekoppeld virtueel netwerk. Zie voor meer informatie [een probleem met de route ring van een virtuele machine vaststellen](diagnose-network-routing-problem.md).

U kunt de verbinding met een virtuele machine in een gekoppeld virtueel netwerk ook oplossen met behulp van Azure Network Watcher. Met een connectiviteits controle kunt u zien hoe verkeer wordt gerouteerd van de netwerk interface van een virtuele bron machine naar de netwerk interface van een virtuele doel machine. Zie voor meer informatie [verbindingen met Azure Network Watcher met behulp van de Azure Portal oplossen](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

U kunt ook proberen om problemen [met peering van het virtuele netwerk op te lossen](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Beperkingen voor gekoppelde virtuele netwerken<a name="requirements-and-constraints"></a>

De volgende beperkingen zijn alleen van toepassing wanneer virtuele netwerken wereldwijd zijn gekoppeld:

* Resources in één virtueel netwerk kunnen niet communiceren met het front-end-IP-adres van een Basic Internal Load Balancer (ILB) in een globaal gekoppeld virtueel netwerk.
* Sommige services die gebruikmaken van een basis load balancer, werken niet via globale virtuele netwerk peering. Zie [Wat zijn de beperkingen met betrekking tot globaal VNet-peering en load balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)voor meer informatie.

Zie [vereisten en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints)voor meer informatie. Zie [netwerk limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie over het ondersteunde aantal peerings.

## <a name="permissions"></a>Machtigingen

Zie [machtigingen](virtual-network-manage-peering.md#permissions)voor meer informatie over de vereiste machtigingen voor het maken van peering op een virtueel netwerk.

## <a name="pricing"></a>Prijzen

Er zijn een nominale kosten verbonden aan binnenkomend en uitgaand verkeer dat gebruikmaakt van een virtuele netwerk peering-verbinding. Zie [Virtual Network prijzen](https://azure.microsoft.com/pricing/details/virtual-network)voor meer informatie.

Gateway-door Voer is een peering-eigenschap waarmee een virtueel netwerk een VPN-ExpressRoute gateway kan gebruiken in een gekoppeld virtueel netwerk. Gateway-door Voer werkt voor zowel cross-premises als netwerk-naar-netwerk-connectiviteit. Verkeer naar de gateway (binnenkomend of uitgaand) in het gekoppelde virtuele netwerk maakt de peering van virtuele netwerken op het spoke-VNet (of niet-gateway-VNet). Zie voor meer informatie [VPN gateway prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway/) voor de kosten van VPN-gateways en ExpressRoute gateway prijzen voor ExpressRoute gateway kosten.

>[!NOTE]
> In een vorige versie van dit document is aangegeven dat de kosten voor peering van virtuele netwerken niet van toepassing zijn op de spoke VNet (of niet-gateway VNet) met gateway-door voer. Het weerspiegelt nu nauw keurige prijzen per de pagina met prijzen.

## <a name="next-steps"></a>Volgende stappen

* U kunt een peering tussen twee virtuele netwerken maken. De netwerken kunnen deel uitmaken van hetzelfde abonnement, verschillende implementatie modellen in hetzelfde abonnement of andere abonnementen. Volg een zelfstudie voor een van de volgende scenario's:

    |Azure-implementatiemodel             | Abonnement  |
    |---------                          |---------|
    |Beide in Resource Manager              |[Hetzelfde](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Verschillend](create-peering-different-subscriptions.md)|
    |Eén in Resource Manager, één klassiek  |[Hetzelfde](create-peering-different-deployment-models.md)|
    |                                   |[Verschillend](create-peering-different-deployment-models-subscriptions.md)|

* Zie [hub-spoke-netwerk topologie in azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het maken van een hub-en-spoke-netwerk topologie.
* Zie [een virtuele netwerk peering maken, wijzigen of verwijderen](virtual-network-manage-peering.md)voor meer informatie over alle instellingen voor de peering van virtuele netwerken.
* Zie [VNet-peering](virtual-networks-faq.md#vnet-peering)voor antwoorden op veelvoorkomende virtuele netwerk peering en algemene vragen over peering voor het virtuele netwerk.