---
title: Veelgestelde vragen over Azure VPN Gateway
description: Veelgestelde vragen over VPN-gateways. Veelgestelde vragen over cross-premises verbindingen, verbindingen voor hybride configuraties en VPN-gateways voor Microsoft Azure Virtual Network.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: 8ca50ae77d9d9e200db3318b8e087b72697c343a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953449"
---
# <a name="vpn-gateway-faq"></a>Veelgestelde vragen VPN-gateways

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>Verbinding maken met virtuele netwerken

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kan ik virtuele netwerken in verschillende Azure-regio's verbinden?

Ja. Er is zelfs helemaal geen regiobeperking. Een virtueel netwerk kan verbinding maken met een ander virtueel netwerk in dezelfde regio of in een andere Azure-regio.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kan ik virtuele netwerken uit verschillende abonnementen verbinden?

Ja.

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>Kan ik privé-DNS-servers in mijn VNet opgeven bij het configureren van VPN Gateway?

Als u een DNS-server of-servers hebt opgegeven tijdens het maken van uw VNet, worden VPN Gateway de opgegeven DNS-servers gebruikt. Als u een DNS-server opgeeft, controleert u of de DNS-server de domein namen kan omzetten die nodig zijn voor Azure.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kan ik vanuit één virtueel netwerk verbinding maken met meerdere sites?

U kunt verbinding maken met meerdere sites met behulp van Windows PowerShell en de REST-API's van Azure. Raadpleeg de sectie Veelgestelde vragen bij [Multi-site- en VNet-naar-VNet-connectiviteit](#V2VMulti).

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>Zijn er extra kosten verbonden aan het instellen van een VPN-gateway als actief-actief?

Nee.

### <a name="what-are-my-cross-premises-connection-options"></a>Wat zijn de opties voor cross-premises-verbinding?

De volgende cross-premises verbindingen worden ondersteund:

* Site-naar-site: VPN-verbinding via IPsec (IKE v1 en IKE v2). Voor dit type verbinding is een VPN-apparaat of RRAS vereist. Zie [Site-naar-site](./tutorial-site-to-site-portal.md) voor meer informatie.
* Punt-naar-site: VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKE v2. Voor deze verbinding is geen VPN-apparaat vereist. Zie [Punt-naar-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) voor meer informatie.
* VNet-naar-VNet: dit type verbinding is hetzelfde als de site-naar-site-configuratie. VNet-naar-VNet is een VPN-verbinding via IPsec (IKE v1 en IKE v2). Hiervoor is geen VPN-apparaat vereist. Zie [VNet-naar-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) voor meer informatie.
* Multi-site: dit is een variant op een site-naar-site-configuratie waarmee u meerdere on-premises sites kunt verbinden met een virtueel netwerk. Zie [Multi-site](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) voor meer informatie.
* ExpressRoute: ExpressRoute is een particuliere verbinding met Azure van uw WAN, en niet een VPN-verbinding via het open bare Internet. Raadpleeg het [Technisch overzicht van ExpressRoute](../expressroute/expressroute-introduction.md) en de [Veelgestelde vragen over ExpressRoute](../expressroute/expressroute-faqs.md) voor meer informatie.

Zie [Informatie over VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN-gatewayverbindingen.

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Wat is het verschil tussen een site-naar-site-verbinding en een punt-naar-site?

Er is sprake van **site-naar-site**-configuraties (IPsec-/IKE VPN-tunnel) tussen uw on-premises locatie en Azure. Dit betekent dat u vanaf elke computer op uw locatie verbinding kunt maken met elke virtuele machine of rolinstantie binnen uw virtuele netwerk, afhankelijk van hoe u routering en machtigingen wilt configureren. Het is een uitstekende optie voor een niet-beschik bare cross-premises verbinding en is goed geschikt voor hybride configuraties. Dit type verbinding is afhankelijk van een IPsec-VPN-apparaat (hardwareapparaat of software) dat aan de rand van uw netwerk moet worden geïmplementeerd. Als u dit type verbinding wilt maken, moet u een extern gericht IPv4-adres hebben.

Met **punt-naar-site**-configuraties (VPN via SSTP) kunt u vanaf één computer waar dan ook verbinding maken met alles binnen het virtuele netwerk. Hiervoor wordt de met Windows meegeleverde VPN-client gebruikt. Als onderdeel van de punt-naar-site-configuratie installeert u een certificaat en een VPN-clientconfiguratiepakket. Dit pakket bevat de instellingen waarmee de computer verbinding kan maken met elke virtuele machine of rolinstantie in het virtuele netwerk. Het is ideaal wanneer u verbinding wilt maken met een virtueel netwerk maar u zich niet on-premises bevindt. Het is ook een goede optie wanneer u geen toegang hebt tot VPN-hardware of een extern gericht IPv4-adres, twee zaken die vereist zijn voor een site-naar-site-verbinding.

U kunt uw virtuele netwerk configureren om tegelijkertijd gebruik te maken van site-naar-site en punt-naar-site, mits u de site-naar-site-verbinding maakt met een op route gebaseerd VPN-type voor uw gateway. Op route gebaseerde VPN-typen worden in het klassieke implementatiemodel dynamische gateways genoemd.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>Virtuele netwerkgateways

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Is een VPN-gateway een virtuele netwerkgateway?

Een VPN-gateway is een type virtuele netwerkgateway. Een VPN-gateway verzendt via een openbare verbinding versleuteld verkeer tussen uw virtuele netwerk en uw on-premises locatie. U kunt ook een VPN-gateway gebruiken om verkeer te verzenden tussen virtuele netwerken. Wanneer u een VPN-gateway maakt, kunt u de waarde -GatewayType-waarde Vpn gebruiken. Zie [Informatie over VPN-gatewayconfiguratie-instellingen](vpn-gateway-about-vpn-gateway-settings.md) voor meer informatie.

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Wat is een op beleid gebaseerde gateway (statische routering)?

Op beleid gebaseerde gateways implementeren op beleid gebaseerde VPN's. Op beleid gebaseerde VPN-verbindingen versleutelen pakketten en versturen deze op basis van de combinaties van adresvoorvoegsels tussen uw on-premises netwerk en het Azure-VNET. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de VPN-configuratie.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Wat is een op route gebaseerde gateway (dynamische routering)?

Op route gebaseerde gateways implementeren op route gebaseerde VPN's. VPN-verbindingen op basis van een route gebruiken "routes" in de IP-doorstuurtabel of routeringstabel om pakketten naar de bijbehorende tunnelinterfaces te sturen. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. De beleids-of verkeers selectie voor op route gebaseerde Vpn's worden geconfigureerd als een wille keurig (of Joker).

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Kan ik mijn op beleid gebaseerde VPN-gateway bijwerken naar op route gebaseerd?

Nee. Een gateway type kan niet worden gewijzigd van beleid op basis van route ring of van route gebaseerd op op basis van beleid. Als u een gateway type wilt wijzigen, moet de gateway worden verwijderd en opnieuw worden gemaakt. Dit proces duurt ongeveer 60 minuten. Wanneer u de nieuwe gateway maakt, kunt u het IP-adres van de oorspronkelijke gateway niet behouden.

1. Verwijder alle verbindingen die zijn gekoppeld aan de gateway.

1. Verwijder de gateway met behulp van een van de volgende artikelen:

   * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell-klassiek](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. Maak een nieuwe gateway met behulp van het gewenste gateway type en voltooi vervolgens de VPN-installatie. Zie de [site-naar-site-zelf studie](./tutorial-site-to-site-portal.md#VNetGateway)voor instructies.

### <a name="do-i-need-a-gatewaysubnet"></a>Heb ik een gatewaysubnet nodig?

Ja. Het gatewaysubnet bevat de IP-adressen waarvan de virtuele-netwerkgatewayservices gebruik van maken. U moet een gatewaysubnet maken voor uw VNet om een virtuele netwerkgateway te kunnen configureren. Voor een goede werking moeten alle gatewaysubnetten de naam GatewaySubnet krijgen. Geef het gatewaysubnet geen andere naam. Implementeer ook geen VM's of iets anders in het gatewaysubnet.

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gatewaysubnet worden toegewezen aan de gatewayservice. Bij sommige configuraties moeten er meer IP-adressen worden toegewezen aan de gatewayservices dan bij andere. U moet ervoor zorgen dat uw gatewaysubnet voldoende IP-adressen bevat om groei mogelijk te maken en om mogelijke nieuwe verbindingsconfiguraties toe te kunnen voegen. U kunt dus wel een gatewaysubnet maken met een grootte van slechts /29, maar het wordt aangeraden om een gatewaysubnet van /27 of groter te maken (/27, /26, /25 enz.). Bekijk de vereisten voor de configuratie die u wilt gebruiken en controleer of het gatewaysubnet dat u hebt, voldoet aan deze vereisten.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kan ik Virtual Machines of rolinstanties implementeren in het gatewaysubnet?

Nee.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kan ik het IP-adres van de VPN-gateway verkrijgen voordat ik de gateway maak?

Zone-redundante en zonegebonden-gateways (gateway-Sku's die _AZ_ in de naam hebben) beide vertrouwen op een _standaard-SKU_ Azure open bare IP-resource. Open bare IP-resources voor Azure Standard SKU moeten een statische toewijzings methode gebruiken. Daarom hebt u het open bare IP-adres voor uw VPN-gateway wanneer u de open bare standaard-SKU-resource maakt die u wilt gebruiken.

Voor niet-zone-redundante en niet-zonegebonden gateways (gateway-Sku's _zonder_ _AZ_ in de naam), kunt u het IP-adres van de VPN-gateway niet ophalen voordat het wordt gemaakt. Het IP-adres wordt alleen gewijzigd als u de VPN-gateway verwijdert en opnieuw maakt.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Kan ik een statisch openbaar IP-adres voor mijn VPN-gateway aanvragen?

Zoals hierboven vermeld, zijn zone-redundante en zonegebonden-gateways (gateway-Sku's met _AZ_ in de naam) beide gebaseerd op een _standaard-SKU_ Azure open bare IP-resource. Open bare IP-resources voor Azure Standard SKU moeten een statische toewijzings methode gebruiken.

Voor niet-zone-redundante en niet-zonegebonden gateways (gateway-Sku's _zonder_ _AZ_ in de naam) wordt alleen dynamische IP-adres toewijzing ondersteund. Dit betekent echter niet dat het IP-adres verandert nadat het aan uw VPN-gateway is toegewezen. De enige keer dat het IP-adres van de VPN-gateway verandert wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het open bare IP-adres van de VPN-gateway verandert niet wanneer u het formaat van het apparaat wijzigt, opnieuw instelt of een ander intern onderhoud en upgrades van uw VPN-gateway uitvoert.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hoe wordt mijn VPN-tunnel geverifieerd?

Azure VPN maakt gebruik van PSK-verificatie (verificatie op basis van een vooraf gedeelde sleutel). Er wordt een PSK (vooraf gedeelde sleutel) gegenereerd wanneer de VPN-tunnel wordt gemaakt. U kunt de automatisch gegenereerde PSK voor uw eigen sleutel wijzigen met de Power shell-cmdlet voor vooraf gedeelde sleutels instellen of REST API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kan ik de API Set Pre-Shared Key gebruiken om een op beleid gebaseerde VPN-gateway (statische routering) te configureren?

Ja, u kunt de API en PowerShell-cmdlet Set Pre-Shared Key gebruiken om zowel op beleid gebaseerde (statische) VPN's als op route gebaseerde VPN's (dynamische routering) van Azure te configureren.

### <a name="can-i-use-other-authentication-options"></a>Kan ik andere verificatieopties gebruiken?

U kunt alleen PSK-verificatie (vooraf gedeelde sleutels) gebruiken.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hoe geef ik op welk verkeer via de VPN-gateway loopt?

#### <a name="resource-manager-deployment-model"></a>Resource Manager-implementatiemodel

* PowerShell: gebruik 'AddressPrefix' om verkeer voor de gateway van het lokale netwerk op te geven.
* Azure Portal: ga naar de gateway van het lokale netwerk > Configuratie > Adresruimte.

#### <a name="classic-deployment-model"></a>Klassiek implementatiemodel

* Azure Portal: ga naar het klassieke virtuele netwerk > VPN-verbindingen > Site-naar-site VPN-verbindingen > Naam lokale site > Lokale site > Adresruimte van client.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kan ik NAT-T op mijn VPN-verbindingen gebruiken?

Ja, NAT-Traversal (NAT-T) wordt ondersteund. Er wordt door Azure VPN Gateway geen NAT-achtige functionaliteit uitgevoerd voor de interne pakketten van/naar de IPsec-tunnels.  Controleer in deze configuratie of het on-premises apparaat de IPSec-tunnel initieert.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kan ik mijn eigen VPN-server in Azure instellen en deze gebruiken om verbinding te maken met mijn on-premises netwerk?

Ja, kunt u uw eigen VPN-gateways of -servers in Azure implementeren vanuit de Azure Marketplace of door uw eigen VPN-routers te implementeren. U moet in het virtuele netwerk zelfgedefinieerde routes configureren om ervoor te zorgen dat verkeer juist wordt gerouteerd tussen uw on-premises netwerken en de subnetten van het virtuele netwerk.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>Waarom worden bepaalde poorten geopend op mijn virtuele netwerk gateway?

Deze zijn nodig voor communicatie met de Azure-infrastructuur. Ze zijn beveiligd (vergrendeld) met Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten, waaronder de klanten van deze gateways, niets uitvoeren op de eindpunten.

Een virtuele netwerk gateway is fundamenteel een multi-Home apparaat met één NIC die op het privé netwerk van de klant tikt en één NIC gericht op het open bare netwerk. Infrastructuurentiteiten van Azure hebben om wettelijke redenen geen toegang tot particuliere netwerken van klanten. Daarom moeten ze voor infrastructuurcommunicatie gebruikmaken van openbare eindpunten. De openbare eindpunten worden periodiek gescand door de beveiligingscontrole van Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Meer informatie over gatewaytypen, vereisten en doorvoer

Zie [Informatie over VPN-gatewayconfiguratie-instellingen](vpn-gateway-about-vpn-gateway-settings.md) voor meer informatie.

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>Site-naar-site-verbindingen en VPN-apparaten

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Waaraan moet ik denken bij het selecteren van een VPN-apparaat?

We hebben samen met apparaatleveranciers een reeks standaard site-naar-site-VPN-apparaten gevalideerd. In het artikel [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) vindt u een lijst met bekende compatibele VPN-apparaten, de bijbehorende configuratie-instructies of -voorbeelden en apparaatspecificaties. Alle apparaten in de vermelde apparaatfamilies die als compatibel worden weergegeven, zouden met Virtual Network moeten werken. Als hulp bij de configuratie van uw VPN-apparaat kunt u het voorbeeld van de apparaatconfiguratie raadplegen of de koppeling bij de betreffende apparaatfamilie.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Waar vind ik configuratie-instellingen voor VPN-apparaten?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Hoe bewerk ik voorbeelden van VPN-apparaatconfiguraties?

Zie [Bewerkingsvoorbeelden](vpn-gateway-about-vpn-devices.md#editing) voor voorbeelden van het bewerken van de apparaatconfiguratie.

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Waar vind ik IPsec- en IKE-parameters?

Zie [Parameters](vpn-gateway-about-vpn-devices.md#ipsec) voor de IPsec/IKE-parameters.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Waarom wordt mijn op beleid gebaseerde VPN-tunnel inactief als er geen verkeer is?

Dit is normaal voor op beleid gebaseerde VPN-gateways (ook wel bekend als statische routering genoemd). Wanneer er langer dan vijf minuten geen verkeer is via de tunnel, wordt de tunnel verwijderd. Zodra er verkeer is in een van de richtingen, wordt de tunnel onmiddellijk opnieuw ingesteld.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Kan ik software-VPN's gebruiken om verbinding te maken met Azure?

Windows Server 2012 RRAS-servers (Routering en RAS) worden ondersteund voor site-naar-site-cross-premises-configuratie.

Andere VPN-softwareoplossingen zouden in principe met onze gateway moeten werken zolang ze voldoen aan de standaard-IPSec-implementaties. Neem contact op met de leverancier van de software voor configuratie- en ondersteuningsinstructies.

## <a name="how-do-i-change-the-authentication-type-for-my-point-to-site-connections"></a>Hoe kan ik het verificatie type voor mijn punt-naar-site-verbindingen wijzigen?

U kunt de verificatie methode voor uw punt-naar-site-verbindingen wijzigen door te gaan naar de sectie **punt-naar-site-configuratie** onder het VPN gateway en het gewenste keuze rondje te controleren. De huidige opties zijn **Azure-certificaat, RADIUS-verificatie en Azure Active Directory**. Houd er rekening mee dat de huidige clients **mogelijk geen verbinding kunnen maken** na de wijziging totdat het nieuwe profiel is gedownload en geconfigureerd op de client.

## <a name="point-to-site-using-native-azure-certificate-authentication"></a><a name="P2S"></a>Point-to-site met behulp van systeemeigen Azure-certificaatverificatie

Deze sectie is van toepassing op het Resource Manager-implementatiemodel.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site-using-radius-authentication"></a><a name="P2SRADIUS"></a>Point-to-site met behulp van RADIUS-verificatie

Deze sectie is van toepassing op het Resource Manager-implementatiemodel.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>VNet-naar-VNET- en multi-site-verbindingen

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kan ik Azure VPN-gateway gebruiken om verkeer tussen mijn on-premises sites of naar een ander virtueel netwerk over te brengen?

**Resource Manager-implementatiemodel**<br>
Ja. Raadpleeg de sectie [BGP](#bgp) voor meer informatie.

**Klassiek implementatiemodel**<br>
Transitverkeer via Azure VPN-gateway is mogelijk met het klassieke implementatiemodel, maar is afhankelijk van statisch gedefinieerde adresruimten in het netwerkconfiguratiebestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways via het klassieke implementatiemodel. Zonder BGP is het handmatig definiëren van adresruimten voor doorvoer zeer foutgevoelig en het wordt daarom niet aanbevolen.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Genereert Azure dezelfde vooraf gedeelde IPsec/IKE-sleutel voor al mijn VPN-verbindingen voor hetzelfde virtuele netwerk?

Nee, Azure genereert standaard verschillende vooraf gedeelde sleutels voor verschillende VPN-verbindingen. U kunt echter de REST-API of PowerShell-cmdlet Set VPN Gateway gebruiken om de gewenste sleutelwaarde in te stellen. De sleutel moet Afdruk bare ASCII-tekens zijn.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Krijg ik meer bandbreedte met meerdere site-naar-site-VPN-verbindingen dan met één virtueel netwerk?

Nee, alle VPN-tunnels, inclusief punt-naar-site-VPN-verbindingen, delen dezelfde Azure VPN-gateway en beschikbare bandbreedte.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kan ik meerdere tunnels tussen mijn virtuele netwerk en mijn on-premises site configureren met multi-site-VPN?

Ja, maar u moet BGP op beide tunnels op dezelfde locatie instellen.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kan ik punt-naar-site-VPN-verbindingen met het virtuele netwerk gebruiken met meerdere VPN-tunnels?

Ja, P2S-VPN-verbindingen (punt-naar-site) kunnen worden gebruikt met de VPN-gateways die verbinding maken met meerdere on-premises sites en andere virtuele netwerken.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Kan ik een virtueel netwerk met IPsec-VPN's verbinden met mijn ExpressRoute-circuit?

Ja, dit wordt ondersteund. Voor meer informatie raadpleegt u [Expressroute en site-naar-site-VPN-verbindingen die naast elkaar kunnen worden gebruikt configureren](../expressroute/expressroute-howto-coexist-classic.md)

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/IKE-beleid

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP en route ring

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>Kan ik geforceerde tunneling configureren?

Ja. Zie [Configure forced tunneling](vpn-gateway-about-forced-tunneling.md) (Geforceerde tunneling configureren).

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>Cross-premises-connectiviteit en virtuele machines

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Als de virtuele machine zich in een virtueel netwerk bevindt en ik een cross-premises-verbinding heb, hoe moet ik dan verbinding maken met de virtuele machine?

U hebt een aantal opties. Als u RDP hebt ingeschakeld voor uw virtuele machine, kunt u het particuliere IP-adres gebruiken om verbinding te maken met uw virtuele machine. In dat geval geeft u het particuliere IP-adres op en de poort waarmee u verbinding wilt maken (meestal 3389). U moet de poort op de virtuele machine configureren voor het verkeer.

U kunt uw virtuele machine ook verbinden met behulp van het particuliere IP-adres van een andere virtuele machine die zich in hetzelfde virtuele netwerk bevindt. Het is niet mogelijk om met het particuliere IP-adres een RDP-verbinding met de virtuele machine te maken als u verbinding maakt vanaf een locatie buiten het virtuele netwerk. Als u bijvoorbeeld een virtueel punt-naar-site-netwerk hebt geconfigureerd en u geen verbinding maakt vanaf uw computer, dan kunt u het particuliere IP-adres niet gebruiken om verbinding te maken met de virtuele machine.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Als mijn virtuele machine zich in een virtueel netwerk met cross-premises-connectiviteit bevindt, gaat al het verkeer vanuit mijn VM dan langs die verbinding?

Nee. Alleen het verkeer met een doel-IP dat zich bevindt in de door u opgegeven IP-adresbereiken van het lokale netwerk van het virtuele netwerk, loopt via de gateway van het virtuele netwerk. Verkeer met een doel-IP binnen het virtuele netwerk blijft in het virtuele netwerk. Ander verkeer wordt via de load balancer verzonden naar de openbare netwerken, tenzij geforceerde tunneling wordt gebruikt. In dat geval wordt het verzonden via de Azure VPN-gateway.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Hoe los ik problemen met de RDP-verbinding met een VM op?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>Veelgestelde vragen over Virtual Network

Aanvullende informatie over virtuele netwerken vindt u in de [Veelgestelde vragen over Virtual Network](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [Over VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN Gateway.
* Zie [Informatie over VPN-gatewayconfiguratie-instellingen](vpn-gateway-about-vpn-gateway-settings.md) voor meer informatie over VPN-gatewayconfiguratie-instellingen.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
