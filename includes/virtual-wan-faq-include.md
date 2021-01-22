---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8cd6f6a32071dc7bbd4aaccb92168cda009933a6
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690044"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Is Azure Virtual WAN algemeen beschikbaar?

Ja, Azure Virtual WAN is algemeen beschikbaar. Virtual WAN bestaat echter uit verschillende functies en scenario's. Er zijn functies of scenario's in Virtual WAN waarop Microsoft de tag Preview toepast. In dergelijke gevallen is de specifieke functie of het scenario zelf een preview-versie. Als u geen specifieke preview-functie gebruikt, is sprake van normale algemene beschikbaarheid. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over de ondersteuning van preview-versies.

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Moet de gebruiker over hub en spoke beschikken met SD-WAN/VPN-apparaten om Azure Virtual WAN te kunnen gebruiken?

Virtual WAN biedt tal van functies die zijn ingebouwd in één onderdeel, zoals site-/site-naar-site-VPN-connectiviteit, gebruikers-/P2S-connectiviteit, ExpressRoute-connectiviteit, Virtual Network-connectiviteit, VPN ExpressRoute-interconnectiviteit, transitieve VNet-naar-VNet-connectiviteit, gecentraliseerde routering, Azure Firewall en Firewall Manager-beveiliging, bewaking, ExpressRoute-versleuteling en vele andere mogelijkheden. U hoeft niet al deze use-cases te hebben om Virtual WAN te gaan gebruiken. U kunt aan de slag met slechts één use-case. De Virtual WAN-architectuur is een hub- en spoke-architectuur met schaal en prestaties ingebouwd waarbij vertakkingen (VPN-/SD-WAN-apparaten), gebruikers (Azure VPN-clients, OpenVPN of IKEv2-clients), ExpressRoute-circuits en virtuele netwerken als spokes fungeren voor virtuele hubs. Alle hubs zijn verbonden in full mesh in een standaard virtuele WAN, waardoor de gebruiker de Microsoft-backbone eenvoudig kan gebruiken voor any-to-any-connectiviteit (elke willekeurige spoke). Voor hub en spoke met SD-WAN-/VPN-apparaten kunnen gebruikers het handmatig instellen in de Azure Virtual WAN-portal of gebruikmaken van de Azure Virtual-partner WAN CPE (SD-WAN/VPN) om connectiviteit met Azure in te stellen. Virtual WAN-partners bieden automatisering voor connectiviteit, dus de mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en verbinding te maken met de Azure Virtual WAN-hub. Voor punt-naar-site-connectiviteit of VPN-connectiviteit voor gebruikers ondersteunen we [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN of IKEv2-client. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Kunnen volledig gemeshede hubs in een virtuele WAN worden uitgeschakeld?

Virtual WAN is beschikbaar in twee soorten: Basic en Standard. In Basic Virtual WAN worden hubs niet gemeshed. In een Standard Virtual WAN worden hubs gemeshed en automatisch verbonden wanneer de virtuele WAN voor het eerst wordt ingesteld. De gebruiker hoeft niets specifieks te doen. De gebruiker hoeft ook de functionaliteit niet in of uit te schakelen om gemeshede hubs te verkrijgen. Virtual WAN biedt een groot aantal routeringsopties om verkeer tussen spokes (VNet, VPN of ExpressRoute) te leiden. Het biedt het gemak van volledig gemeshede hubs en tevens de flexibiliteit om verkeer naar behoefte te leiden. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hoe worden beschikbaarheidszones en tolerantie in Virtual WAN verwerkt?

Virtual WAN is een verzameling hubs en services die beschikbaar zijn in de hub. De gebruiker kan over net zoveel virtuele WAN beschikken als nodig is. In een Virtual WAN-hub komen meerdere services voor, zoals VPN, ExpressRoute, enzovoort. Elk van deze services (met uitzondering van Azure Firewall) wordt geïmplementeerd in een regio met beschikbaarheidszones, dat wil zeggen dat de regio beschikbaarheidszones ondersteunt. Als een regio na de eerste implementatie in de hub een beschikbaarheidszone wordt, kan de gebruiker de gateways opnieuw maken, waardoor de implementatie van een beschikbaarheidszone wordt geactiveerd. Alle gateways worden in een hub als actief-actief ingericht, wat inhoudt dat er tolerantie is ingebouwd in een hub. Gebruikers kunnen verbinding maken met meerdere hubs als ze tolerantie voor verschillende regio's willen. Hoewel het concept van Virtual WAN globaal is, is de daadwerkelijke Virtual WAN-resource gebaseerd op Resource Manager en regionaal gedistribueerd. Als de virtuele WAN-regio zelf een probleem kent, blijven alle hubs in die virtuele WAN functioneren, maar de gebruiker kan pas nieuwe hubs maken als de virtuele WAN-regio beschikbaar is.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Welke client wordt door de VPN voor gebruikers (punt-naar-site) van de Azure Virtual WAN ondersteund?

Virtual WAN ondersteunt [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN Client of een IKEv2-client. Azure AD-verificatie wordt ondersteund met Azure VPN Client. Er is minimaal Windows 10-client OS-versie 17763.0 of hoger vereist.  OpenVPN-client(s) kunnen verificatie op basis van certificaten ondersteunen. Zodra op certificaten gebaseerde verificatie op de gateway is geselecteerd, wordt het bestand *.ovpn* naar uw apparaat gedownload. IKEv2 ondersteunt zowel certificaat- als RADIUS-authenticatie. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Voor VPN voor gebruikers (punt-naar-site): waarom is de P2S-clientpool over twee routes gesplitst?

Elke gateway heeft twee exemplaren. De splitsing treedt op zodat elk exemplaar van de gateway onafhankelijk client-IP's kan toewijzen voor verbonden clients; verkeer afkomstig van het virtuele netwerk wordt teruggestuurd naar het juiste gateway-exemplaar om een exemplaar-hops tussen gateways te voorkomen.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hoe kan ik DNS-servers voor P2S-clients toevoegen?

Er zijn twee opties om DNS-servers voor de P2S-clients toe te voegen. De eerste methode verdient de voorkeur omdat hiermee de aangepaste DNS-servers aan de gateway worden toegevoegd in plaats van de client.

1. Gebruik het volgende PowerShell-script om de aangepaste DNS-servers toe te voegen. Vervang de waarden voor uw omgeving.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Als u de Azure VPN Client voor Windows 10 gebruikt, kunt u het gedownloade XML-profielbestand wijzigen en de tags **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** toevoegen voordat u het importeert.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Voor VPN voor gebruikers (punt-naar-site): hoeveel clients worden ondersteund?

Elke P2S-gateway van een VPN voor gebruikers bevat twee exemplaren en elk exemplaar biedt ondersteuning voor bepaalde gebruikers als de schaaleenheid verandert. Schaaleenheid 1-3 ondersteunt 500 verbindingen, schaaleenheid 4-6 ondersteunt 1000 verbindingen, schaaleenheid 7-12 ondersteunt 5000 verbindingen en schaaleenheid 13-18 ondersteunt maximaal 10.000 verbindingen. 

Stel dat de gebruiker één schaaleenheid kiest. Elke schaaleenheid impliceert een geïmplementeerde actief-actief-gateway en elk exemplaar (in dit geval twee) zou maximaal 500 verbindingen ondersteunen. Omdat u 500 verbindingen x 2 per gateway kunt verkrijgen, betekent dit niet dat u voor deze schaaleenheid voor 1000 gaat plannen in plaats van de 500. Het kan zijn dat er exemplaren moeten worden onderhouden, zodat de connectiviteit voor de extra 500 kan worden onderbroken als u het aantal aanbevolen verbindingen overschrijdt. Houd ook rekening met uitvaltijd voor het geval dat u de schaaleenheid omhoog of omlaag wilt schalen of de punt-naar-site-configuratie op de VPN-gateway wilt wijzigen.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Wat is het verschil tussen een virtuele Azure-netwerkgateway (VPN Gateway) en een Azure Virtual WAN VPN-gateway?

Virtual WAN biedt grootschalige site-naar-site-connectiviteit en is ontworpen met het oog op doorvoer, schaalbaarheid en gebruiksgemak. Wanneer u een site verbindt met een Virtual WAN-VPN-gateway, verschilt deze van een normale virtuele netwerk-gateway, die gebruikmaakt van het gatewaytype VPN. En wanneer u een ExpressRoute-circuit verbindt met een Virtual WAN-hub, wordt er ook een andere resource gebruikt voor de ExpressRoute-gateway dan de normale virtuele netwerk-gateway, die gebruikmaakt van het gatewaytype ExpressRoute. 

Virtual WAN ondersteunt maximaal 20 Gbps geaggregeerde doorvoer voor VPN en ExpressRoute. Virtual WAN beschikt ook over automatisering voor connectiviteit met een ecosysteem van CPE-vertakkingsapparaatpartners. CPE-vertakkingsapparaten beschikken over ingebouwde automatisering waarmee automatisch wordt ingericht en verbinding met Azure Virtual WAN wordt gemaakt. Deze apparaten zijn beschikbaar binnen een groeiend ecosysteem van SD-WAN- en VPN-partners. Zie de [lijst met partners van voorkeur](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Waarin verschilt Virtual WAN van een virtuele Azure-netwerkgateway?

Een VPN in een virtuele netwerkgateway is beperkt tot dertig tunnels. U moet Virtual WAN gebruiken voor grootschalige VPN-verbindingen. U kunt maximaal 1000 vertakkingsverbindingen per regio (virtuele hub) verbinden met een aggregatie van 20 Gbps per hub. Een verbinding is een actief-actief-tunnel van het on-premises VPN-apparaat naar de virtuele hub. Per regio kan er één hub zijn. Dit betekent dat u meer dan 1000 vertakkingen kunt verbinden via de hubs.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Wat is een schaaleenheid van een Virtual WAN-gateway?

Een schaaleenheid is een eenheid die is gedefinieerd om een geaggregeerde doorvoer van een gateway in een virtuele hub te kunnen kiezen. 1 VPN-schaaleenheid = 500 Mbps. 1 ExpressRoute-schaaleenheid = 2 Gbps. Voorbeeld: Voor 10 VPN-schaaleenheden geldt: 500 Mbps x 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Welke apparaatproviders (Virtual WAN-partners) worden ondersteund?

Veel partners bieden momenteel ondersteuning voor de volledig geautomatiseerde Virtual WAN-ervaring. Zie [Virtual WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md) voor meer informatie.

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Wat zijn de stappen voor automatisering voor Virtual WAN-partners?

Zie [Automatisering voor Virtual WAN-partners](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) voor meer informatie.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Moet ik een apparaat van een voorkeurspartner gebruiken?

Nee. U kunt ieder VPN-apparaat gebruiken dat voldoet aan de Azure-vereisten voor IKEv2/IKEv1 IPSec-ondersteuning. Virtual WAN heeft ook CPE-partneroplossingen waarmee de connectiviteit met Azure Virtual WAN wordt geautomatiseerd, waardoor het eenvoudiger wordt om IPsec VPN-verbindingen op schaal in te stellen.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hoe automatiseren Virtual WAN-partners connectiviteit met Azure Virtual WAN?

Softwarematige oplossingen voor netwerkconnectiviteit beheren hun vertakkingsapparaten doorgaans met behulp van een controller of een knooppunt voor apparaatinrichting. De controller kan Azure API’s gebruiken om de connectiviteit met Azure Virtual WAN te automatiseren. De automatisering omvat het uploaden van vertakkingsgegevens, het downloaden van de Azure-configuratie, het instellen van IPSec-tunnels in Azure Virtual-hubs en het automatisch instellen van connectiviteit van het vertakkingsapparaat naar Azure Virtual WAN. Wanneer u honderden vertakkingen hebt, kunt u eenvoudig verbinding maken met behulp van Virtual WAN CPE-partners, omdat dankzij het onboarden het niet meer nodig is grootschalige IPsec-connectiviteit in te stellen, te configureren en te beheren. Raadpleeg [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatisering voor Virtual WAN-partners) voor meer informatie.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Wat gebeurt er als een apparaat dat ik gebruik niet voorkomt in de lijst met Virtual WAN-partners? Kan ik het nog steeds gebruiken om verbinding te maken met Azure Virtual WAN-VPN?

Ja, als het apparaat IPsec IKEv1 of IKEv2 ondersteunt. Virtual WAN-partners automatiseren de connectiviteit vanaf het apparaat tot Azure VPN-eindpunten. Dit impliceert de automatisering van stappen als het uploaden van vertakkingsgegevens, IPsec en configuratie en connectiviteit. Omdat uw apparaat niet afkomstig is van een Virtual WAN-partnerecosysteem, moet u het zware werk doen van het handmatig configureren van Azure en het bijwerken van uw apparaat om IPsec-connectiviteit in te kunnen stellen.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hoe worden nieuwe partners die nog niet zijn vermeld in de startlijst van partners, opgenomen?

Alle virtuele WAN API's zijn open API's. U kunt de documentatie [Automatisering van Virtual WAN-partners](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) raadplegen om de technische haalbaarheid vast te stellen. Een ideale partner is iemand die een apparaat heeft dat kan worden ingericht voor een IKEv1 of IKEv2 IPSec-verbinding. Zodra het bedrijf de automatiseringswerkzaamheden voor het CPE-apparaat heeft voltooid op basis van de hierboven beschreven automatiseringsrichtlijnen, kunt u contact opnemen met azurevirtualwan@microsoft.com om hier te worden vermeld: [Connectiviteit via partners]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Als u een klant bent die een bepaalde bedrijfsoplossing wilt laten vermelden als een Virtual WAN-partner, laat u het bedrijf contact opnemen met Virtual WAN door een e-mail te sturen naar azurevirtualwan@microsoft.com.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hoe biedt Virtual WAN ondersteuning voor SD-WAN-apparaten?

Virtual WAN-partners automatiseren IPsec-connectiviteit voor Azure VPN-eindpunten. Als de Virtual WAN-partner een SD-WAN-provider is, wordt ervan uitgegaan dat de SD-WAN-controller de automatisering en IPsec-verbinding voor Azure VPN-eindpunten beheert. Als het SD-WAN-apparaat in plaats van Azure VPN een eigen eindpunt nodig heeft voor een eigen SD-WAN-functionaliteit, kunt u het SD-WAN-eindpunt in een Azure-VNet implementeren dat naast Azure Virtual WAN aanwezig kan zijn.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hoeveel VPN-apparaten kunnen verbinding maken met één hub?

Per virtuele hub worden maximaal 1000 verbindingen ondersteund. Elke verbinding bestaat uit vier koppelingen en elke koppelingsverbinding ondersteunt twee tunnels die zich in een actief-actief-configuratie bevinden. De tunnels eindigen in een VPN-gateway van de virtuele Azure-hub. Koppelingen vertegenwoordigen de fysieke ISP-koppeling op het vertakkings-/VPN-apparaat.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Wat is een vertakkingsverbinding met Azure Virtual WAN?

Een verbinding vanaf een vertakkings- of VPN-apparaat in Azure Virtual WAN is niets anders dan een VPN-verbinding die virtueel verbinding maakt met de VPN-site en de Azure-VPN Gateway in een virtuele hub.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan het on-premises VPN-apparaat verbinding maken met meerdere hubs?

Ja. In het begin loopt de verkeersstroom van het on-premises apparaat naar de dichtstbijzijnde Microsoft-netwerkrand en vervolgens naar de virtuele hub.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Zijn er nieuwe Resource Manager-resources beschikbaar voor Virtual WAN?
  
Ja, Virtual WAN beschikt over nieuwe Resource Manager-resources. Zie voor meer informatie het [Overzicht](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan ik mijn favoriete virtuele netwerkapparaat implementeren en gebruiken (in een NVA-VNet) met Azure Virtual WAN?

Ja, u kunt uw favoriete virtuele netwerkapparaat (NVA)-VNet verbinden met de Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan ik een virtueel netwerkapparaat binnen de virtuele hub maken?

Een virtueel netwerkapparaat (NVA) kan niet binnen een virtuele hub worden geïmplementeerd. U kunt het echter maken in een spoke-VNet dat is verbonden met de virtuele hub en de juiste routering inschakelen zodat het verkeer naar behoefte wordt doorgestuurd.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan een spoke-VNet een virtuele netwerkgateway hebben?

Nee. Het spoke-VNet kan geen virtuele netwerkgateway hebben als deze is verbonden met de virtuele hub.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Is er ondersteuning voor BGP in VPN-connectiviteit?

Ja, BGP wordt ondersteund. Wanneer u een VPN-site maakt, kunt u er de BGP-parameters opgeven. Dit betekent dat alle verbindingen die in Azure zijn gemaakt voor die site, voor BGP worden ingeschakeld.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Is er informatie over licentieverlening of prijzen beschikbaar voor Virtual WAN?

Ja. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Is het mogelijk om een Azure Virtual WAN te maken met een Resource Manager-sjabloon?

Een eenvoudige configuratie van één virtuele WAN met één hub en één VPN-site kan met behulp van een [quickstart-sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) worden gemaakt. Virtual WAN is voornamelijk een service op basis van REST of de portal.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kunnen spoke-VNets die zijn verbonden met een virtuele hub met elkaar communiceren (V2V Transit)?

Ja. Standard Virtual WAN ondersteunt transitieve VNet-naar-VNet-connectiviteit via de Virtual WAN-hub waarmee de VNets zijn verbonden. In de terminologie van het virtuele WAN wordt naar deze paden verwezen als 'lokaal overbrengen van Virtual WAN-VNet' voor VNets die zijn verbonden met een Virtual WAN-hub in één regio, en 'globaal overbrengen van Virtual WAN-VNet' voor VNets die zijn verbonden via meerdere Virtual WAN-hubs in twee of meer regio's. In sommige scenario's kunnen, naast het lokaal of globaal overbrengen van een Virtual WAN-VNet, spoke-VNets ook rechtstreeks met elkaar worden gepeerd met behulp van [peering van virtuele netwerken](../articles/virtual-network/virtual-network-peering-overview.md). In dit geval heeft VNet-peering voorrang op de transitieve verbinding via de Virtual WAN-hub.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Is een vertakking-naar-vertakking-verbinding toegestaan in Virtual WAN?

Ja, een vertakking-naar-vertakking-verbinding is beschikbaar in Virtual WAN. Vertakking is conceptueel toepasbaar op gebruikers van VPN-sites, ExpressRoute-circuits of punt-naar-site-/gebruikers-VPN's. Het inschakelen van vertakking naar vertakking is standaard ingeschakeld en kan worden gevonden in de configuratie-instellingen voor de WAN. Hierdoor kunnen VPN-filialen/-gebruikers verbinding maken met andere VPN-vertakkingen en wordt de overgangsconnectiviteit ingeschakeld tussen VPN- en ExpressRoute-gebruikers.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Loopt vertakking-naar-vertakking-verkeer via Azure Virtual WAN?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Is voor Virtual WAN vanaf elke site ExpressRoute nodig?

Nee. Voor Virtual WAN is niet vanaf elke locatie ExpressRoute nodig. Uw sites kunnen worden verbonden met een providernetwerk met behulp van een ExpressRoute-circuit. Voor sites die via ExpressRoute zijn verbonden met een virtuele hub en via een IPsec-VPN in dezelfde hub zijn verbonden, biedt de virtuele hub overgangsconnectiviteit tussen de VPN- en ExpressRoute-gebruiker.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Is er een netwerkdoorvoer- of verbindingslimiet bij gebruik van Azure Virtual WAN?

Netwerkdoorvoer vindt per service in een virtuele WAN-hub plaats. Hoewel u zoveel virtuele WAN's kunt hebben als u wilt, staat elke virtuele WAN één hub per regio toe. In elke hub is de geaggregeerde VPN-doorvoer maximaal 20 Gbps. De geaggregeerde ExpressRoute-doorvoer is maximaal 20 Gbps en de geaggregeerde gebruikers-VPN-/punt-naar-sitedoorvoer is maximaal 20 Gbps. De router in een virtuele hub ondersteunt maximaal 50 Gbps voor VNet-naar-VNet-verkeersstromen en gaat uit van een totale belasting van 2000 VM's voor alle VNets die zijn verbonden met één virtuele hub.

Wanneer VPN-sites verbinding maken met een hub, doen ze dit via verbindingen. Virtual WAN ondersteunt maximaal 1000 verbindingen of 2000 IPsec-tunnels per virtuele hub. Wanneer externe gebruikers verbinding maken met de virtuele hub, maken ze verbinding met de P2S VPN-gateway. Deze biedt ondersteuning voor maximaal 10.000 gebruikers, afhankelijk van de schaaleenheid (bandbreedte) die voor de P2S VPN-gateway in de virtuele hub is gekozen.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wat is de totale VPN-doorvoer van een VPN-tunnel en een -verbinding?

De totale VPN-doorvoer van een hub is maximaal 20 Gbps op basis van de gekozen schaaleenheid van de VPN-gateway. De doorvoer wordt gedeeld door alle bestaande verbindingen. Elke tunnel in een verbinding kan maximaal 1 Gbps ondersteunen.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kan ik NAT-T op mijn VPN-verbindingen gebruiken?
Ja, NAT-Traversal (NAT-T) wordt ondersteund. Met virtuele WAN-VPN Gateway wordt geen NAT-achtige functionaliteit uitgevoerd voor de interne pakketten van/naar de IPsec-tunnels. Controleer in deze configuratie of het on-premises apparaat de IPSec-tunnel initieert.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>De instelling van 20 Gbps wordt niet weergeven voor de virtuele hub in de portal. Hoe kan ik dit configureren?

Ga naar de VPN-gateway in een hub op de portal en klik op de schaaleenheid om deze naar de juiste instelling te wijzigen.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Kan het on-premises apparaat in Virtual WAN meerde ISP's parallel gebruiken of is er altijd sprake van één VPN-tunnel?

On-premises apparaat-oplossingen kunnen verkeersbeleid toepassen om verkeer via meerdere tunnels naar de Azure Virtual WAN-hub (VPN-gateway in de virtuele hub) te sturen.

### <a name="what-is-global-transit-architecture"></a>Wat is wereldwijde doorvoerarchitectuur?

Zie [Wereldwijde architectuur voor doorvoernetwerk en Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md) voor meer informatie over wereldwijde doorvoerarchitectuur.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hoe wordt het verkeer naar de Azure-backbone geleid?

Het verkeer volgt het volgende patroon: vertakkingsapparaat->ISP->Microsoft-netwerkrand>Microsoft DC (hub-VNet)->Microsoft-netwerkrand->ISP->vertakkingsapparaat

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Wat is er met dit model op elke locatie nodig? Alleen een internetverbinding?

Ja. Een internetverbinding en een fysiek apparaat dat IPsec ondersteunt, bij voorkeur van onze geïntegreerde [Virtual WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md). Optioneel kunt u de configuratie en verbinding met Azure vanaf het apparaat van voorkeur handmatig beheren.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hoe schakel ik de standaardroute (0.0.0.0/0) in een verbinding in (VPN, ExpressRoute of Virtual Network)?

In een virtuele hub kan een inmiddels bekende standaardroute worden doorgegeven naar een virtuele netwerk-/site-naar-site-VPN-/ExpressRoute-verbinding als de vlag voor de verbinding de status Ingeschakeld heeft. Deze vlag wordt weergegeven als de gebruiker een verbinding met een virtueel netwerk, een VPN-verbinding of een ExpressRoute aanpast. Deze vlag wordt standaard uitgeschakeld wanneer een site of een ExpressRoute-circuit met een hub wordt verbonden. De vlag is standaard ingeschakeld wanneer een virtuele netwerkverbinding wordt toegevoegd om een VNet te verbinden met een virtuele hub. De standaardroute is niet afkomstig van de Virtual WAN-hub. De standaardroute wordt doorgegeven als deze al bekend is bij de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub, of als voor een andere verbonden site geforceerd tunnelen is ingeschakeld.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hoe selecteert de virtuele hub in een virtuele WAN uit meerdere hubs het beste pad voor een route?

Als een virtuele hub dezelfde route van meerdere externe hubs bewaart, is de volgorde waarover wordt beslist als volgt:

1. Overeenkomst met langste voorvoegsel.
2. Lokale routes in plaats van interhub (virtuele hub wijst 65520-65520 toe voor interhub AS)
3. Statische routes in plaats van BGP: Dit is in overeenstemming met de beslissing van de virtuele hub-router. Als de beslisser echter de VPN-gateway is, waarbij een site routes via BGP aankondigt of statische-adresvoorvoegsels opgeeft, kunnen statische routes de voorkeur hebben boven BGP-routes.
4. ExpressRoute (ER) in plaats van VPN: ER heeft de voorkeur boven VPN wanneer de context een lokale hub is. Overgangsconnectiviteit tussen ExpressRoute-circuits is alleen beschikbaar via Global Reach. In scenario's waarin een ExpressRoute-circuit is verbonden met één hub en een ander ExpressRoute-circuit is verbonden met een andere hub met VPN-verbinding, kan VPN mogelijk de voorkeur hebben in scenario's met overdragingen tussen hubs.
5. AS-padlengte.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Staat een Virtual WAN-hub connectiviteit tussen ExpressRoute-circuits toe?

Overgang van ER-naar-ER vindt altijd via Global Reach plaats. Virtuele hubgateways worden geïmplementeerd in DC- of Azure-regio's. Wanneer twee ExpressRoute-circuits verbinding maken via Global Reach, hoeft het verkeer niet de hele weg af te leggen van de Edge-routers tot de virtuele hub-DC.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Speelt het concept gewicht een rol bij Azure Virtual WAN ExpressRoute-circuits or VPN-verbindingen

Wanneer meerdere ExpressRoute-circuits met een virtuele hub zijn verbonden, biedt de mate van routering (gewicht) voor de verbinding een mechanisme waarmee ExpressRoute in de virtuele hub een voorkeur heeft voor het ene circuit boven het andere. Er is geen mechanisme om een gewicht in te stellen voor een VPN-verbinding. Azure geeft binnen één hub altijd de voorkeur aan een ExpressRoute-verbinding boven een VPN-verbinding.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Is er een voorkeur van Virtual WAN voor ExpressRoute boven VPN voor verkeer dat Azure verlaat?

Ja.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Als een Virtual WAN-hub verbonden is met zowel een ExpressRoute-circuit als een VPN-site, wat zou dan de reden zijn dat er een voorkeur voor de route via de VPN-verbinding is boven ExpressRoute?

Wanneer een ExpressRoute-circuit is verbonden met een virtuele hub, vormen de Microsoft-randrouters het eerste knooppunt voor communicatie tussen on-premises en Azure. Deze randrouters communiceren met de Virtual WAN ExpressRoute-gateways die op hun beurt routes bewaren van de virtuele hubrouter waarmee alle routes tussen gateways in Virtual WAN worden beheerd. De Microsoft-randrouters verwerken virtuele ExpressRoute-hubroutes met prioriteit hoger dan die voor routes die on-premises worden overgenomen. Als de VPN-verbinding het primaire medium voor de virtuele hub wordt om routes van over te nemen (bijvoorbeeld failover-scenario's tussen ExpressRoute en VPN), maar tenzij de VPN-site een langere padlengte heeft, blijft de virtuele hub van VPN overgenomen routes met ExpressRoute gateway delen, waardoor de Microsoft-randrouters de voorkeur geven aan VPN-routes via on-premises routes.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Wanneer twee hubs (hub 1 en 2) zijn verbonden en er een ExpressRoute-circuit als een strik met beide hubs is verbonden, wat is dan het pad voor een VNet dat is verbonden met hub 1 om een VNet te bereiken dat is aangesloten op hub 2?

Het huidige gedrag is om de voorkeur te geven aan het ExpressRoute-circuit boven hub-naar-hub voor VNet-naar-VNet-connectiviteit. Dit wordt echter niet aanbevolen in een virtuele WAN-installatie. Het virtuele WAN-team werkt aan een oplossing om de voorkeur voor hub-naar-hub via het ExpressRoute-pad in te schakelen. Het wordt aanbevolen meerdere ExpressRoute-circuits (verschillende providers) met één hub te verbinden en de hub-naar-hub-connectiviteit van Virtual WAN te gebruiken voor regio-overschrijdende verkeersstromen.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Kunnen hubs worden gemaakt in een andere resourcegroep in Virtual WAN?
Ja. Deze optie is momenteel alleen beschikbaar via PowerShell. De Virtual WAN-portal vereist dat de hubs zich in dezelfde resourcegroep bevinden als de Virtual WAN-resource zelf.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Is er ondersteuning voor IPv6 in Virtual WAN?

IPv6 wordt niet ondersteund in Virtual WAN-hub en de bijbehorende gateways. Dit scenario wordt momenteel niet ondersteund als u een VNet hebt met IPv4- en IPv6-ondersteuning en u het VNet wilt verbinden met Virtual WAN. 

Voor het punt-naar-site-scenario voor VPN (gebruiker) met internetonderbreking via Azure Firewall, moet u waarschijnlijk IPv6-connectiviteit uitschakelen op het clientapparaat, om verkeer naar de Virtual WAN-hub te sturen. Dit komt omdat moderne apparaten standaard IPv6-adressen gebruiken.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Wat is de aanbevolen API-versie die door scripts moet worden gebruikt om verschillende Virtual WAN-functionaliteit te automatiseren?

Er is minimaal de versie van 05-01-2020 (1 mei 2020) vereist. 

### <a name="are-there-any-virtual-wan-limits"></a>Zijn er limieten voor Virtual WAN van toepassing?

Raadpleeg het gedeelte [Limieten voor Virtual WAN](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) op de pagina Abonnements- en servicebeperkingen.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Wat zijn de verschillen tussen de Virtual WAN-typen (Basic en Standard)?

Zie [Virtual WAN's: Basic en Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Zie de pagina [Prijzen](https://azure.microsoft.com/pricing/details/virtual-wan/) voor prijsopgaven.

### <a name="does-virtual-wan-store-customer-data"></a>Slaat Virtual WAN klantgegevens op? 

Nee. Virtual WAN slaat geen klantgegevens op.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Zijn er Managed Service Providers die Virtual WAN voor gebruikers kunnen beheren als een service? 

Ja. Raadpleeg [Azure Marketplace-aanbiedingen van Azure-netwerken MSP-partners](../articles/networking/networking-partners-msp.md#msp) voor een lijst met MSP-oplossingen (Managed Service Provider) die beschikbaar zijn via Azure Marketplace.
