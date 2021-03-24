---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953447"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Wordt BGP ondersteund op alle Azure VPN-gateway SKU’s?

BGP wordt ondersteund op alle Azure VPN Gateawy SKU's, behalve Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Kan ik BGP gebruiken met VPN-gateways van Azure?

Nee, BGP wordt alleen ondersteund op route-gebaseerde VPN-gateways.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Welke ASN's (autonome systeemnummers) kan ik gebruiken?

U kunt uw eigen openbare ASN's of persoonlijke ASN's voor zowel uw on-premises netwerken en virtuele netwerken van Azure gebruiken. U kunt de bereiken die door Azure of IANA zijn gereserveerd niet gebruiken.

De volgende ASN's zijn gereserveerd voor Azure of IANA:
* ASN's die zijn gereserveerd voor Azure:

  * Openbare ASN's: 8074, 8075, 12076
  * Privé-ASNs: 65515, 65517, 65518, 65519, 65520
* ASN's [die zijn gereserveerd door IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):

  * 23456, 64496-64511, 65535-65551 en 429496729

U kunt deze ASN's niet opgeven voor uw on-premises VPN-apparaten wanneer u verbinding maakt met Azure VPN-gateways.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan ik 32-bits (4-byte) ASN's gebruiken?

Ja, VPN Gateway ondersteunt nu 32-bits (4-bytes) ASN's. Als u ASN wilt configureren voor gebruik in decimaal formaat, gebruikt u PowerShell, de Azure-CLI of de Azure-SDK.

### <a name="what-private-asns-can-i-use"></a>Welke privé-ASN's kan ik gebruiken?

De bruikbare bereiken van privé-ASN's zijn:

* 64512-65514 en 65521-65534

Deze ASN's zijn niet gereserveerd voor gebruik door IANA of Azure en kunnen daarom worden gebruikt om te worden toegewezen aan uw Azure-VPN Gateway.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Welk adres gebruikt VPN Gateway voor BGP-peer-IP?

Standaard wijst VPN Gateway één IP-adres uit het *GatewaySubnet*-bereik toe voor active-standby VPN-gateways, of twee IP-adressen voor active-active VPN-gateways. Deze adressen worden automatisch toegewezen wanneer u de VPN-gateway maakt. U kunt het werkelijke BGP IP-adres dat is toegewezen ophalen met PowerShell of door het te zoeken in de Azure Portal. Gebruik in PowerShell **Get-AzVirtualNetworkGateway** en zoek naar de eigenschap **bgpPeeringAddress**. Ga in Azure Portal naar de pagina **Gatewayconfiguratie** en zoek naar de eigenschap **BGP ASN configureren**.

Als uw on-premises VPN-routers **APIPA**-IP-adressen gebruiken (169.254.x.x) als het BGP-IP-adres, moet u een aanvullend **Azure APIPA BGP-IP-adres** opgeven in uw Azure VPN-gateway. Azure VPN-gateway selecteert het APIPA-adres dat moet worden gebruikt met de on-premises APIPA BGP-peer die is opgegeven in de lokale netwerkgateway of het privé-IP-adres voor een niet-APIPA on-premises BGP-peer. Raadpleeg [BGP configureren](../articles/vpn-gateway/bgp-howto.md) voor meer informatie.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Wat zijn de vereisten voor de BGP-peer-IP-adressen op mijn VPN-apparaat?

Het adres van uw on-premises BGP-peer mag niet gelijk zijn aan het openbare IP-adres van uw VPN-apparaat of de virtuele netwerkadresruimte van de VPN-gateway. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat (zowel een normaal IP-adres of een APIPA-adres). Als uw apparaat een APIPA-adres gebruikt voor BGP, moet u een APIP BGP-IP-adres opgeven in uw Azure VPN-gateway, zoals is beschreven in [BGP configureren](../articles/vpn-gateway/bgp-howto.md). Specificeer dit adres in de bijbehorende lokale netwerkgateway die de locatie vertegenwoordigt.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Wat moet ik opgeven als adresvoorvoegsels voor mijn lokale netwerkgateway wanneer ik BGP gebruik?

> [!IMPORTANT]
>
>Dit is een wijziging van een eerder vastgelegde vereiste. Als u BGP gebruikt voor een verbinding, moet u het veld **Adresruimte** leeg houden voor de corresponderende lokale netwerkgateway-resource. Azure VPN-gateway voegt een hostroute intern toe aan het IP-adres van de on-premises BGP-peer via de IPsec-tunnel. Voeg de route /32 niet toe in het veld **Adresruimte**. Het is overbodig en als u een APIPA-adres gebruikt als het BGP-IP-adres van het on-premises VPN-apparaat, kan het niet aan het veld worden toegevoegd. Als u andere voorvoegsels toevoegt aan het veld Adresruimte, worden ze toegevoegd als **statische routes** in de Azure VPN-gateway, in aanvulling op de routes die via BGP zijn aangeleerd.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Kan ik hetzelfde ASN gebruiken voor on-premises VPN-netwerken en virtuele Azure-netwerken?

Nee, u moet verschillende ASN’s toewijzen aan uw on-premises netwerken en uw virtuele Azure-netwerken als u ze beide verbindt met BGP. Aan Azure VPN-gateways wordt standaard een ASN van 65515 toegewezen, onafhankelijk van of BGP is ingeschakeld voor verbinding tussen gebouwen. U kunt deze standaardwaarde onderdrukken door een andere ASN toe te wijzen bij het aanmaken van de VPN-gateway of door het ASN te wijzigen nadat de gateway is aangemaakt. U moet uw lokale ASN's toewijzen aan de bijbehorende on-premises netwerkgateways van Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Welke adresvoorvoegsels maakt Azure VPN-gateways aan mij bekend?

De gateway maakt de volgende routes bekend aan uw on-premises BGP-apparaten:

* Voorvoegsels van uw virtuele netwerkadres.
* Adresvoorvoegsels voor alle lokale netwerkgateways die zijn verbonden met de Azure VPN-gateway.
* Routes die afkomstig zijn van andere BGP-peeringsessies die zijn verbonden met de Azure VPN-gateway, behalve standaardroutes of routes die overlappen met een voorvoegsel van een virtueel netwerk.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hoeveel voorvoegsels kan ik adverteren voor Azure VPN Gateway?

Azure VPN Gateway ondersteunt tot 4000 voorvoegsels. De BGP-sessie wordt verwijderd als het aantal voorvoegsels de limiet overschrijdt.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan ik standaardroute (0.0.0.0/0) adverteren naar Azure VPN-gateways?

Ja. Onthoud dat dit al het uitgaand verkeer van het virtueel netwerk richting uw on-premises site dwingt. Het voorkomt ook dat virtuele netwerk-VM's openbare communicatie rechtstreeks van het internet accepteren, zoals RDP of SSH vanaf het internet naar de VM's.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kan ik de exacte voorvoegsels als de voorvoegsels van mijn virtuele netwerk adverteren?

Nee, het adverteren van dezelfde voorvoegsels als een van de adresvoorvoegsels van uw virtuele netwerk wordt door Azure geblokkeerd of gefilterd. U kunt echter een voorvoegsel aankondigen dat een superset is van wat u in Virtual Network hebt. 

Als uw virtueel netwerk bijvoorbeeld adresruimte 10.0.0.0/16 gebruikt, kunt u 10.0.0.0/8 adverteren. U kunt echter niet 10.0.0.0/16 of 10.0.0.0/24 adverteren.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Kan ik BGP gebruiken met mijn verbindingen tussen virtuele netwerken?

Ja, u kunt BGP zowel gebruiken voor verbindingen tussen premises en verbindingen tussen virtuele netwerken.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kan ik BGP combineren met niet-BGP-verbindingen voor mijn Azure VPN-gateways?

Ja, u kunt zowel BGP- als niet-BGP-verbindingen combineren voor dezelfde VPN-gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Biedt Azure VPN Gateway ondersteuning voor BGP-transitroutering?

Ja, BGP-transitroutering wordt ondersteund, met uitzondering dat Azure VPN-gateways geen standaardroutes bekendmaakt aan andere BGP-peers. Om transitroutering op meerdere VPN-gateways mogelijk te maken, moet u BGP op alle tussenliggende verbindingen tussen virtuele netwerken inschakelen. Zie [Over BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) voor meer informatie.

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Kan ik meer dan één tunnel aanbrengen tussen een Azure VPN-gateway en mijn on-premises netwerk?

Ja, u kunt meer dan één S2S-VPN-tunnel (site naar site) aanbrengen tussen een Azure VPN-gateway en uw on-premises netwerk. Houd er wel rekening mee dat deze tunnels meetellen voor het totaal aantal tunnels voor uw Azure VPN-gateways, en dat u BGP op beide tunnels moet inschakelen.

Als u bijvoorbeeld twee redundante tunnels heeft tussen uw Azure VPN-gateway en een van uw on-premises netwerken, gebruiken ze 2 tunnels van het totaalaantal voor uw Azure VPN-gateway.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Kan ik meerdere tunnels tussen twee virtuele Azure-netwerken met BGP hebben?

Ja, maar minimaal één van de gateways voor het virtuele netwerk moet de actief/actief-configuratie hebben.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Kan ik BGP gebruiken voor S2S-VPN in een configuratie waarin zowel Azure ExpressRoute als S2S-VPN wordt gebruikt?

Ja.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Wat moet ik toevoegen aan mijn on-premises VPN-apparaat voor de BGP-peeringsessie?

Voeg een hostroute van het IP-adres van de Azure BGP-peer toe aan uw VPN-apparaat. Deze route verwijst naar de IPsec S2S-VPN-tunnel. Als het Azure VPN-peer-IP-adres bijvoorbeeld 10.12.255.30 is, dient u een hostroute toe te voegen voor 10.12.255.30 met een nexthop-interface van de overeenkomende IPsec-tunnelinterface op uw VPN-apparaat.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Ondersteunt de virtuele netwerkgateway BFD voor S2S-verbindingen met BGP?

Nee. Bidirectional Forwarding Detection (BFD) is een protocol dat u met BGP kunt gebruiken om sneller naburige downtime te detecteren dan u met standaard BGP-keepalives kan. BFD gebruikt timers die zijn ontworpen om te werken in LAN-omgevingen, maar niet in openbare internet- of Wide Area Network-verbindingen.

Voor verbindingen via het openbare internet is het niet ongebruikelijk dat bepaalde pakketten worden vertraagd of zelfs wegvallen. Door deze agressieve timers te introduceren, ontstaat er instabiliteit. Die instabiliteit zorgt er mogelijk voor dat routes worden gedempt door BGP. Als alternatief kunt u uw on-premises apparaat configureren met timers die lager zijn dan de standaard keepalive-interval van 60 seconden en de holdtimer van 180 seconden. Dat leidt tot een snellere convergentietijd.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Initiëren Azure VPN-gateways BGP-peering sessies of verbindingen?

De gateway initieert BGP-peering sessies naar de on-premises BGP-peer-IP-adressen die zijn opgegeven in de lokale netwerk gateway bronnen met behulp van de privé-IP-adressen op de VPN-gateways. Dit is onafhankelijk van het feit of de on-premises BGP IP-adressen zich in het APIPA-bereik of gewone privé-IP-adressen bevinden. Als uw on-premises VPN-apparaten APIPA-adressen gebruiken als BGP IP, moet u de BGP-luid spreker configureren om de verbindingen te initiëren.