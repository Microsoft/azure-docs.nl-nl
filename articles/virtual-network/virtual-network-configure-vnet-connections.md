---
title: Virtuele netwerk-of VPN-verbindingen configureren en valideren
description: Stapsgewijze instructies voor het configureren en valideren van verschillende Azure VPN-en virtuele-netwerk implementaties
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9a0d06a8f8fa8f68f063404f2b483b817eb0563f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452098"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Virtuele netwerk-of VPN-verbindingen configureren en valideren

Dit overzicht bevat stapsgewijze instructies voor het configureren en valideren van verschillende Azure VPN-en virtuele-netwerk implementaties. Scenario's zijn onder andere transit routering, netwerk-naar-netwerk verbindingen, Border Gateway Protocol (BGP), multi site-verbindingen en Point-to-site-verbindingen.

Met Azure VPN-gateways kunt u bijna elk soort verbonden virtuele netwerk topologie in azure ordenen. U kunt bijvoorbeeld verbinding maken met virtuele netwerken:

- In verschillende regio's.
- Tussen typen virtuele netwerken (Azure Resource Manager versus klassiek).
- Binnen Azure of binnen een on-premises hybride omgeving.
- In verschillende abonnementen. 

## <a name="network-to-network-vpn-connection"></a>VPN-verbinding tussen netwerk en netwerk

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (netwerk-naar-netwerk) via VPN is vergelijkbaar met het verbinden van een virtueel netwerk met een on-premises site locatie. Beide connectiviteits typen gebruiken een VPN-gateway om een beveiligde tunnel via IPsec en IKE te bieden. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren.

![Netwerk-naar-netwerk-verbinding met IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Als uw virtuele netwerken zich in dezelfde regio bevinden, kunt u overwegen verbinding te maken met behulp van de peering van het virtuele netwerk. Peering op virtueel netwerk maakt geen gebruik van een VPN-gateway. Dit verhoogt de door Voer en vermindert de latentie. Als u een peer verbinding met een virtueel netwerk wilt configureren, selecteert u **VNet-peering configureren en valideren**.

Als uw virtuele netwerken zijn gemaakt via het Azure Resource Manager-implementatie model, selecteert u **een resource manager-Vnet configureren en valideren voor een resource manager vnet-verbinding** om een VPN-verbinding te configureren.

Als een van de virtuele netwerken is gemaakt via het klassieke Azure-implementatie model en de andere is gemaakt via Resource Manager, selecteert u **een klassiek Vnet configureren en valideren voor een resource manager VNet-verbinding** om een VPN-verbinding te configureren.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Peering voor het virtuele netwerk configureren voor twee virtuele netwerken in dezelfde regio

Zorg ervoor dat u voldoet aan de volgende vereisten voordat u begint met het implementeren en configureren van peering op het virtuele netwerk van Azure:

* De virtuele netwerken die worden gekoppeld, moeten zich in dezelfde Azure-regio bevinden.
* De gekoppelde virtuele netwerken moeten IP-adres ruimten hebben die elkaar niet overlappen.
* Peering in virtuele netwerken vindt plaats tussen twee virtuele netwerken. Er is geen afgeleide transitieve relatie tussen peerings. Als VNetA bijvoorbeeld is gekoppeld aan VNetB en VNetB is gekoppeld aan VNetC, wordt VNetA *niet* gekoppeld aan VNetC.

Wanneer u aan de vereisten voldoet, kunt u [zelf studie volgen: virtuele netwerken verbinden met virtuele netwerk peering met behulp van de Azure Portal](./tutorial-connect-virtual-networks-portal.md) voor het maken en configureren van de peering.

Gebruik de volgende methode om de peering-configuratie te controleren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen](virtual-network-manage-peering.md#permissions).
2. In het vak met de tekst **zoeken naar resources** bovenaan de portal typt u **virtuele netwerken**. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
3. Selecteer in de Blade **virtuele netwerken** die wordt weer gegeven het virtuele netwerk waarvoor u een peering wilt maken.
4. Selecteer **peerings** in het gedeelte **instellingen** van het deel venster dat wordt weer gegeven voor het virtuele netwerk.
5. Selecteer een peering en Bekijk de configuratie resultaten.

![Selecties voor het controleren van de configuratie van de peering voor het virtuele netwerk](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Voor Azure PowerShell voert u de opdracht [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) uit om de peering van het virtuele netwerk op te halen. Hier volgt een voorbeeld:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Een virtueel netwerk van Resource Manager verbinden met een virtueel netwerk van Resource Manager

U kunt rechtstreeks een verbinding configureren tussen een virtueel netwerk van Resource Manager en een ander virtueel netwerk van Resource Manager. U kunt de verbinding ook configureren met behulp van IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Een VPN-verbinding configureren tussen virtuele netwerken van Resource Manager

Zie [een netwerk-naar-netwerk-VPN-gateway verbinding configureren met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)om een verbinding te configureren tussen virtuele netwerken van Resource Manager zonder IPSec.

Als u een verbinding met IPsec tussen twee virtuele Resource Manager-netwerken wilt configureren, volgt u stap 1 tot en met 5 in [een site-naar-site-verbinding maken in de Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md) voor elk virtueel netwerk.

> [!Note]
> Deze stappen werken alleen voor virtuele netwerken in hetzelfde abonnement. Als uw virtuele netwerken zich in verschillende abonnementen bevinden, moet u Power shell gebruiken om de verbinding te maken. Zie het artikel voor [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>De VPN-verbinding tussen virtuele netwerken van Resource Manager valideren

![Klassieke virtuele netwerk verbinding met een Azure Resource Manager virtueel netwerk](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Volg deze instructies om te controleren of uw VPN-verbinding juist is geconfigureerd.

> [!Note] 
> De getallen na de onderdelen van het virtuele netwerk in deze stappen komen overeen met de getallen in het voor gaande diagram.

1. Zorg ervoor dat er geen overlappende adres ruimten aanwezig zijn in de verbonden virtuele netwerken.
2. Controleer of het adres bereik voor het Azure Resource Manager virtuele netwerk (1) nauw keurig is gedefinieerd in het **Connection-object** exemplaar (4).
3. Controleer of het adres bereik voor de Azure Resource Manager virtuele netwerk (6) nauw keurig is gedefinieerd in het **Connection-object** exemplaar (3).
4. Controleer of de vooraf gedeelde sleutels overeenkomen met de verbindings objecten.
5. Controleer of de Azure Resource Manager virtuele netwerk gateway-VIP (2) nauw keurig is gedefinieerd in het **Connection-object** exemplaar (4).
6. Controleer of de Azure Resource Manager virtuele netwerk gateway-VIP (5) nauw keurig is gedefinieerd in het **Connection-object** exemplaar (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Een klassiek virtueel netwerk verbinden met een virtueel netwerk van Resource Manager

U kunt een verbinding maken tussen virtuele netwerken die zich in verschillende abonnementen en in verschillende regio's bevinden. U kunt ook virtuele netwerken koppelen die al verbindingen met on-premises netwerken hebben, zolang u het gateway type hebt geconfigureerd als op route gebaseerd.

Als u een verbinding wilt configureren tussen een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager, raadpleegt u [virtuele netwerken van verschillende implementatie modellen verbinden met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![Diagram waarin een klassieke virtuele netwerk verbinding met een Azure Resource Manager virtueel netwerk wordt weer gegeven.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Volg deze instructies om de configuratie te controleren wanneer u een klassiek virtueel netwerk verbindt met een Azure Resource Manager virtueel netwerk.

> [!Note] 
> De getallen na de onderdelen van het virtuele netwerk in deze stappen komen overeen met de getallen in het voor gaande diagram. 

1. Zorg ervoor dat er geen overlappende adres ruimten aanwezig zijn in de verbonden virtuele netwerken.
2. Controleer of het adres bereik voor de Azure Resource Manager virtuele netwerk (6) nauw keurig is gedefinieerd in de klassieke lokale netwerk definitie (3).
3. Controleer of het adres bereik voor het klassieke virtuele netwerk (1) nauw keurig is gedefinieerd in de Azure Resource Manager- **verbindings object** instantie (4).
4. Controleer of het klassieke virtuele netwerk gateway-VIP (2) nauw keurig is gedefinieerd in de Azure Resource Manager- **verbindings object** instantie (4).
5. Controleer of de Azure Resource Manager virtuele netwerk gateway (5) nauw keurig is gedefinieerd in het klassieke **lokale netwerk definitie** -exemplaar (3).
6. Controleer of de vooraf gedeelde sleutels overeenkomen met beide verbonden virtuele netwerken:
   - Klassiek virtueel netwerk: **lokale netwerk definitie** (3)
   - Azure Resource Manager virtueel netwerk: **verbindings object** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Een punt-naar-site-VPN-verbinding maken

Met een punt-naar-site (*P2S* in de volgende diagram)-configuratie kunt u een beveiligde verbinding maken tussen een afzonderlijke client computer en een virtueel netwerk. Punt-naar-site-verbindingen zijn handig wanneer u verbinding wilt maken met uw virtuele netwerk vanaf een externe locatie, zoals vanaf thuis of een conferentie. Ze zijn ook handig wanneer u slechts enkele clients hebt die verbinding moeten maken met een virtueel netwerk. 

De punt-naar-site-VPN-verbinding wordt vanaf de client computer geïnitieerd via de systeem eigen Windows VPN-client. Clienten verbinden met certificaten om te verifiëren.

![Punt-naar-site-verbinding](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Voor Point-to-site-verbindingen is geen VPN-apparaat vereist. Ze maken de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol). U kunt verbinding maken met een punt-naar-site-verbinding met een virtueel netwerk met behulp van verschillende implementatie hulpprogramma's en implementatie modellen:

* [Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van de Azure Portal (klassiek)](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van Power shell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Uw punt-naar-site-verbinding valideren

Het artikel voor het oplossen van problemen met problemen met [Point-to-site-verbindingen, worden](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) veelvoorkomende problemen met Point-to-site-verbindingen beschreven.

## <a name="create-a-multisite-vpn-connection"></a>Een VPN-verbinding met meerdere sites maken

U kunt een site-naar-site (*S2S* in het volgende diagram) toevoegen aan een virtueel netwerk dat al een site-naar-site-verbinding, punt-naar-site-verbinding of netwerk-naar-netwerk-verbinding heeft. Dit type verbinding wordt vaak een configuratie met *meerdere locaties* genoemd. 

![Multi site-verbinding](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure werkt momenteel in combinatie met twee implementatiemodellen: Resource Manager en klassiek. De twee modellen zijn niet volledig compatibel met elkaar. Als u een multi site-verbinding met verschillende modellen wilt configureren, raadpleegt u de volgende artikelen:

* [Een site-naar-site-verbinding met een virtueel netwerk met een bestaande VPN-gateway verbinding toevoegen](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Een site-naar-site-verbinding met een virtueel netwerk toevoegen met een bestaande VPN-gateway verbinding (klassiek)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> De stappen in deze artikelen zijn niet van toepassing op Azure ExpressRoute-en site-to-site naast elkaar bestaande verbindings configuraties. Zie [ExpressRoute-en site-to-site-verbindingen naast](../expressroute/expressroute-howto-coexist-resource-manager.md)elkaar voor meer informatie.

## <a name="configure-transit-routing"></a>Transit routering configureren

Transit routering is een specifiek routerings scenario waarin u meerdere netwerken verbindt in een keten met een ringnet werk. Met deze route ring kunnen resources in virtuele netwerken aan beide uiteinden van de keten communiceren met elkaar via virtuele netwerken tussen. Zonder transit routering kunnen netwerken of apparaten die via een hub worden gekoppeld, elkaar niet bereiken.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Transit routering configureren in een punt-naar-site-verbinding

Stel dat u een scenario wilt configureren van een site-naar-site-VPN-verbinding tussen VNetA en VNetB. U wilt ook een punt-naar-site-VPN configureren voor de client om verbinding te maken met de gateway van VNetA. Vervolgens wilt u Transit routering inschakelen voor de punt-naar-site-clients om verbinding te maken met VNetB, die door VNetA wordt door gegeven. 

Dit scenario wordt ondersteund wanneer BGP is ingeschakeld op de site-naar-site-VPN tussen VNetA en VNetB. Zie [about Point-to-site VPN-route ring](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)voor meer informatie.

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Transit routering configureren in een ExpressRoute-verbinding

Met Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft-cloud uitbreiden via een speciale persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Microsoft 365 en Dynamics 365. Raadpleeg [Overzicht van ExpressRoute](../expressroute/expressroute-introduction.md) voor meer informatie.

![ExpressRoute persoonlijke peering-verbinding met virtuele Azure-netwerken](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Als VNetA en VNetB zich in dezelfde geopolitieke regio bevinden, wordt u aangeraden [beide virtuele netwerken aan het ExpressRoute-circuit te koppelen](../expressroute/expressroute-howto-linkvnet-arm.md) in plaats van transit routering te configureren. Als uw virtuele netwerken zich in verschillende geopolitieke regio's bevinden, kunt u ze ook rechtstreeks aan uw circuit koppelen als u [ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium)hebt. 

Als u ExpressRoute en site-naar-site-samen werking hebt, wordt Transit routering niet ondersteund. Zie [ExpressRoute en site-to-site configureren met behulp van Power shell](../expressroute/expressroute-howto-coexist-resource-manager.md)voor meer informatie.

Als u ExpressRoute hebt ingeschakeld om uw lokale netwerken te verbinden met een virtueel Azure-netwerk, kunt u peering inschakelen tussen de virtuele netwerken waarvoor u Transit routering wilt gebruiken. Als u uw lokale netwerken wilt toestaan verbinding te maken met het externe virtuele netwerk, moet u [peering voor het virtuele netwerk](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)configureren. 

> [!Note]
> Peering op virtueel netwerk is alleen beschikbaar voor virtuele netwerken in dezelfde regio.

Volg de volgende instructies om te controleren of u Transit routering hebt geconfigureerd voor peering van virtuele netwerken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen](virtual-network-manage-peering.md#permissions).
2. [Maak een peering tussen VNetA en VNetB](./tutorial-connect-virtual-networks-portal.md) , zoals weer gegeven in het vorige diagram. 
3. Selecteer **peerings** in het gedeelte **instellingen** van het deel venster dat wordt weer gegeven voor het virtuele netwerk.
4. Selecteer de peering die u wilt weer geven. Selecteer vervolgens **configuratie** om te controleren of u **Allow gateway-door Voer** hebt ingeschakeld op het VNetA-netwerk dat is verbonden met het ExpressRoute-circuit en **Gebruik externe gateway** op het externe VNetB-netwerk dat niet is verbonden met het ExpressRoute-circuit.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Transit routering configureren in een peering-verbinding met een virtueel netwerk

Wanneer virtuele netwerken gelijkwaardig zijn gemaakt, kunt u ook de gateway in het gelijkwaardige virtuele netwerk configureren als een doorvoerpunt naar een on-premises netwerk. Zie [netwerk-naar-netwerk-verbindingen](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)als u een doorvoer route wilt configureren in peering op virtueel netwerk.

> [!Note]
> Gateway-door Voer wordt niet ondersteund in de peering-relatie tussen virtuele netwerken die via verschillende implementatie modellen zijn gemaakt. Beide virtuele netwerken in de peering-relatie moeten zijn gemaakt via Resource Manager voor het werken met gateway-door voer.

Volg deze instructies om te controleren of u een doorvoer route hebt geconfigureerd voor peering in virtuele netwerken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen](virtual-network-manage-peering.md#permissions).
2. In het vak met de tekst **zoeken naar resources** bovenaan de portal typt u **virtuele netwerken**. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
3. Selecteer in de Blade **virtuele netwerken** die wordt weer gegeven het virtuele netwerk waarvoor u de instelling voor peering wilt controleren.
4. Selecteer in het deel venster dat wordt weer gegeven voor het virtuele netwerk dat u hebt geselecteerd **peerings** in het gedeelte **instellingen** .
5. Selecteer de peering die u wilt weer geven. Controleer of u **Gateway overdracht toestaan** hebt ingeschakeld en **externe gateways gebruiken** onder **configuratie**.

![Selecties voor het controleren of u een doorvoer route hebt geconfigureerd voor peering op virtueel netwerk](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Transit routering configureren in een netwerk-naar-netwerk-verbinding

Als u Transit routering tussen virtuele netwerken wilt configureren, moet u BGP inschakelen op alle tussenliggende netwerk-naar-netwerk-verbindingen met behulp van het Resource Manager-implementatie model en Power shell. Zie [BGP op Azure VPN-gateways configureren met behulp van Power shell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)voor instructies.

Transit verkeer via Azure VPN-gateways is mogelijk via het klassieke implementatie model, maar dit is afhankelijk van statisch gedefinieerde adres ruimten in het netwerk configuratie bestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways via het klassieke implementatie model. Zonder BGP, het hand matig definiëren van transit adres ruimten is fout gevoelig en wij raden dit niet aan.

> [!Note]
> U kunt klassieke netwerk-naar-netwerk verbindingen configureren met behulp van de klassieke Azure-portal of met behulp van een netwerk configuratie bestand in de klassieke Portal. U kunt een klassiek virtueel netwerk niet maken of wijzigen via het Azure Resource Manager-implementatie model of de Azure Portal. Zie de [micro soft Developer-Blog](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1)voor meer informatie over transit routering voor klassieke virtuele netwerken.

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Transit routering configureren in een site-naar-site-verbinding

Als u Transit routering wilt configureren tussen uw on-premises netwerk en een virtueel netwerk met een site-naar-site-verbinding, moet u BGP inschakelen op alle tussenliggende site-naar-site-verbindingen met behulp van het Resource Manager-implementatie model en Power shell. Zie [BGP op Azure VPN-gateways configureren met behulp van Power shell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) voor instructies.

Transit verkeer via Azure VPN-gateways is mogelijk via het klassieke implementatie model, maar dit is afhankelijk van statisch gedefinieerde adres ruimten in het netwerk configuratie bestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways via het klassieke implementatie model. Zonder BGP, het hand matig definiëren van transit adres ruimten is fout gevoelig en wij raden dit niet aan.

> [!Note]
> U kunt klassieke site-naar-site-verbindingen configureren met behulp van de klassieke Azure-portal of met behulp van een netwerk configuratie bestand in de klassieke Portal. U kunt een klassiek virtueel netwerk niet maken of wijzigen via het Azure Resource Manager-implementatie model of de Azure Portal. Zie de [micro soft Developer-Blog](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1)voor meer informatie over transit routering voor klassieke virtuele netwerken.

## <a name="configure-bgp-for-a-vpn-gateway"></a>Een BGP configureren voor een VPN-gateway

BGP is het standaard routerings protocol dat op Internet wordt gebruikt om routerings-en bereik baarheids gegevens tussen twee of meer netwerken te uitwisselen. Wanneer BGP wordt gebruikt in de context van Azure Virtual Networks, worden de Azure VPN-gateways en uw on-premises VPN-apparaten, ook wel BGP-peers of neighbors, ingeschakeld. Ze wisselen ' routes ' in, waarbij beide gateways worden geïnformeerd over de beschik baarheid en het bereiken van deze voor voegsels om door de betrokken gateways of routers te gaan. 

BGP kan ook transit routering tussen meerdere netwerken inschakelen door routes door te geven die een BGP-gateway van een BGP-peer naar alle andere BGP-peers doorstuurt. Zie [overzicht van BGP met Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie.

### <a name="configure-bgp-for-a-vpn-connection"></a>BGP configureren voor een VPN-verbinding

Zie [BGP op Azure VPN-gateways configureren met behulp van Power shell voor meer informatie over](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)het configureren van een VPN-verbinding die gebruikmaakt van BGP.

Schakel BGP in op de virtuele netwerk gateway door een autonoom systeem (als)-nummer te maken. Basis gateways bieden geen ondersteuning voor BGP. Als u de SKU van de gateway wilt controleren, gaat u naar de sectie **overzicht** van de blade **VPN Gateway** in het Azure Portal. Als uw SKU **Basic** is, wijzigt u de SKU (zie formaat van [de gateway](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway)wijzigen) in **VpnGw1**. 

Het controleren van de SKU leidt tot 20 tot 30 minuten uitval tijd. Zodra de gateway de juiste SKU heeft, kunt u het AS-nummer toevoegen met behulp van de cmdlet [set-azurermvirtualnetworkgateway bijgewerkt](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) Power shell. Nadat u het AS-nummer hebt geconfigureerd, wordt er automatisch een BGP-peer-IP voor de gateway gegeven.

U moet hand matig `LocalNetworkGateway` een as-nummer en een BGP-peer adres opgeven. U kunt de `ASN` waarden en instellen met `-BgpPeeringAddress` behulp van de cmdlet [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) of de Power shell [-commandlet set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) . Sommige AS-nummers zijn gereserveerd voor Azure en u kunt ze niet gebruiken zoals wordt beschreven in [over BGP met Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Voor het verbindings object moet BGP zijn ingeschakeld. U kunt de `-EnableBGP` waarde instellen op `$True` via [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) of [set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection).

### <a name="validate-the-bgp-configuration"></a>De BGP-configuratie valideren

Als u wilt controleren of BGP juist is geconfigureerd, kunt u de `get-AzureRmVirtualNetworkGateway` en `get-AzureRmLocalNetworkGateway` Commandlets uitvoeren. Vervolgens ziet u BGP-gerelateerde uitvoer in het `BgpSettingsText` onderdeel. Bijvoorbeeld:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Een Maxi maal beschik bare actieve/actieve VPN-verbinding maken

De belangrijkste verschillen tussen de actieve/actieve en actieve/stand-by gateways zijn:

* U moet twee IP-configuraties met gateways maken met twee open bare IP-adressen.
* U moet de vlag **EnableActiveActiveFeature** instellen.
* De gateway-SKU moet **VpnGw1**, **VpnGw2** of **VpnGw3** zijn.

Voor een hoge Beschik baarheid van cross-premises en netwerk-naar-netwerk-connectiviteit moet u meerdere VPN-gateways implementeren en meerdere parallelle verbindingen tot stand brengen tussen uw netwerken en Azure. Zie [Maxi maal beschik bare cross-premises en netwerk-naar-netwerk connectiviteit](../vpn-gateway/vpn-gateway-highlyavailable.md)voor een overzicht van connectiviteits opties en topologie.

Als u actieve/actieve cross-premises en netwerk-naar-netwerk verbindingen wilt maken, volgt u de instructies in [Configureer actieve/actieve S2S VPN-verbindingen met Azure VPN-gateways](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) voor het configureren van een Azure VPN-gateway in de modus actief/actief.

> [!Note]  
> * Wanneer u adressen aan de lokale netwerk gateway toevoegt voor de Active/Active-modus met BGP ingeschakeld, *voegt u alleen de/32-adressen van de BGP-peers toe*. Als u meer adressen toevoegt, worden deze beschouwd als statische routes en hebben ze voor rang op BGP-routes.
> * U moet verschillende BGP-nummers gebruiken voor uw on-premises netwerken die verbinding maken met Azure. (Als deze hetzelfde zijn, moet u het virtuele netwerk als nummer wijzigen als uw on-premises VPN-apparaat de ASN al gebruikt voor de peer met andere BGP-neighbors.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Een Azure VPN-gateway type wijzigen na de implementatie

U kunt een gateway type van een virtueel netwerk van Azure niet wijzigen van beleid op basis van route ring of op een andere manier rechtstreeks. U moet eerst de gateway verwijderen. Daarna blijven het IP-adres en de vooraf gedeelde sleutel bewaard. Vervolgens kunt u een nieuwe gateway maken van het gewenste type. 

Als u een gateway wilt verwijderen en maken, volgt u deze stappen:

1. Verwijder alle verbindingen die zijn gekoppeld aan de oorspronkelijke gateway.
2. Verwijder de gateway met behulp van de Azure Portal, Power shell of klassieke Power shell: 
   * [Een virtuele netwerk gateway verwijderen met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Een virtuele netwerk gateway verwijderen met behulp van Power shell](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Een virtuele netwerk gateway verwijderen met behulp van Power shell (klassiek)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Volg de stappen in [de VPN-gateway maken](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) om de nieuwe gateway van het gewenste type te maken en de VPN-installatie te volt ooien.

> [!Note]
> Dit proces duurt ongeveer 60 minuten.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
