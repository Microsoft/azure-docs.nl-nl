---
title: Veelgestelde vragen over Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Antwoorden op veelgestelde vragen over Microsoft Azure virtuele netwerken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 61076b49d396c83a67635e46eb4f1b928b3f9e1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933978"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Veelgestelde vragen over virtuele Azure-netwerken (FAQ)

## <a name="virtual-network-basics"></a>Basis beginselen van Virtual Network

### <a name="what-is-an-azure-virtual-network-vnet"></a>Wat is een Azure-Virtual Network (VNet)?
Een Azure Virtual Network (VNet) is een weer gave van uw eigen netwerk in de Cloud. Het is een logische isolatie van de Azure-cloud die is toegewezen aan uw abonnement. U kunt VNets gebruiken voor het inrichten en beheren van virtuele particuliere netwerken (Vpn's) in Azure en, optioneel, koppel de VNets met andere VNets in azure of met uw on-premises IT-infra structuur voor het maken van hybride of cross-premises oplossingen. Elk VNet dat u maakt, heeft een eigen CIDR-blok en kan worden gekoppeld aan andere VNets-en on-premises netwerken, zolang de CIDR-blokken elkaar niet overlappen. U hebt ook de controle over de DNS-server instellingen voor VNets en segmentatie van het VNet in subnetten.

Gebruik VNets voor het volgende:

* Maak een speciaal VNet voor de privécloud. Soms hebt u geen cross-premises configuratie nodig voor uw oplossing. Wanneer u een VNet maakt, kunnen uw services en virtuele machines binnen uw VNet rechtstreeks en veilig met elkaar communiceren in de Cloud. U kunt nog steeds eindpunt verbindingen configureren voor de Vm's en services waarvoor Internet communicatie is vereist, als onderdeel van uw oplossing.

* Breid uw Data Center veilig uit. Met VNets kunt u traditionele site-naar-site-Vpn's (S2S) bouwen om de capaciteit van uw Data Center veilig te schalen. S2S-Vpn's gebruiken IPSEC om een beveiligde verbinding te bieden tussen uw bedrijfs-VPN-gateway en Azure.

* Schakel hybride Cloud scenario's in. VNets biedt u de flexibiliteit om een reeks hybride Cloud scenario's te ondersteunen. U kunt op een veilige manier Cloud toepassingen verbinden met elk type on-premises systeem, zoals mainframes en UNIX-systemen.

### <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?
Ga naar de documentatie van het [virtuele netwerk](./index.yml) om aan de slag te gaan. Deze inhoud bevat overzichts-en implementatie-informatie voor alle VNet-functies.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan ik VNets gebruiken zonder cross-premises-connectiviteit?
Ja. U kunt een VNet gebruiken zonder het aan uw locatie te koppelen. U kunt bijvoorbeeld micro soft Windows Server Active Directory domein controllers en share point-Farms alleen uitvoeren in een Azure-VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan ik WAN-optimalisatie uitvoeren tussen VNets of een VNet en mijn on-premises Data Center?
Ja. U kunt een [WAN-optimalisatie netwerk virtueel apparaat](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) van verschillende leveranciers implementeren via Azure Marketplace.

## <a name="configuration"></a>Configuratie

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Welke hulpprogram ma's kan ik gebruiken om een VNet te maken?
U kunt de volgende hulpprogram ma's gebruiken om een VNet te maken of configureren:

* Azure Portal
* PowerShell
* Azure CLI
* Een netwerk configuratie bestand (netcfg-alleen voor klassieke VNets). Zie het artikel [een VNet configureren met behulp van een netwerk configuratie bestand](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) .

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Welke adresbereiken kan ik gebruiken in mijn VNets?
We raden u aan de adresbereiken te gebruiken die zijn geïnventariseerd in [RFC 1918](https://tools.ietf.org/html/rfc1918), die zijn gereserveerd door de IETF voor persoonlijke, niet-Routeer bare adres ruimten:
* 10.0.0.0-10.255.255.255 (10/8-voor voegsel)
* 172.16.0.0-172.31.255.255 (172.16/12-voor voegsel)
* 192.168.0.0-192.168.255.255 (192.168/16-voor voegsel)

Andere adres ruimten werken mogelijk wel, maar kunnen ongewenste neven effecten hebben.

Daarnaast kunt u de volgende adresbereiken niet toevoegen:
* 224.0.0.0/4 (multi cast)
* 255.255.255.255/32 (broadcast)
* 127.0.0.0/8 (loop back)
* 169.254.0.0/16 (link-local)
* 168.63.129.16/32 (interne DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan ik open bare IP-adressen hebben in mijn VNets?
Ja. Zie [een virtueel netwerk maken](manage-virtual-network.md#create-a-virtual-network)voor meer informatie over open bare IP-adresbereiken. Open bare IP-adressen zijn niet rechtstreeks toegankelijk via internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Geldt er een limiet voor het aantal subnetten in mijn VNet?
Ja. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor meer informatie. De adres ruimten van het subnet mogen elkaar niet overlappen.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?
Ja. In Azure worden vijf IP-adressen gereserveerd in elk subnet. Dit zijn x. x. x. 0-x. x. x. 3 en het laatste adres van het subnet. x. x. x. 1-x. x. x. 3 is gereserveerd in elk subnet voor Azure-Services.   
- x. x. x. 0: netwerk adres
- x. x. x. 1: gereserveerd door Azure voor de standaard gateway
- x. x. x. 2, x. x. x. 3: gereserveerd door Azure om de Azure DNS IP-adressen toe te wijzen aan de VNet-ruimte
- x. x. x. 255: netwerk broadcast adres

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hoe klein en hoe groot kan VNets en subnetten?
Het kleinste ondersteunde IPv4-subnet is/29 en de grootste is/8 (met behulp van CIDR-subnet-definities).  IPv6-subnetten moeten exact/64 groot zijn.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan ik mijn VLAN'S naar Azure brengen met behulp van VNets?
Nee. VNets zijn laag-3-overlays. Azure biedt geen ondersteuning voor laag-2-semantiek.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan ik aangepaste routerings beleidsregels opgeven voor mijn VNets en subnetten?
Ja. U kunt een route tabel maken en deze koppelen aan een subnet. Zie [route ring Overview](virtual-networks-udr-overview.md#custom-routes)(Engelstalig) voor meer informatie over route ring in Azure.

### <a name="do-vnets-support-multicast-or-broadcast"></a>Biedt VNets ondersteuning voor multi cast of broadcast?
Nee. Multi cast en broadcast worden niet ondersteund.

### <a name="what-protocols-can-i-use-within-vnets"></a>Welke protocollen kan ik gebruiken in VNets?
U kunt TCP-, UDP- en ICMP TCP/IP-protocollen gebruiken in VNets. Unicast wordt ondersteund in VNets, met uitzondering van DHCP (Dynamic Host Configuration Protocol) via Unicast (bronpoort UDP/68 of doelpoort UDP/67) en UDP-bronpoort 65330, die is gereserveerd voor de host. Multicast-, broadcast-, IP-in-IP-ingekapselde pakketten en GRE-pakketten (General Routing Encapsulation) worden geblokkeerd in VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan ik mijn standaard routers in een VNet pingen?
Nee.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan ik tracert gebruiken voor het vaststellen van de connectiviteit?
Nee.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan ik subnetten toevoegen nadat het VNet is gemaakt?
Ja. Subnetten kunnen op elk gewenst moment worden toegevoegd aan VNets, zolang het adres bereik van het subnet geen deel uitmaakt van een ander subnet en er ruimte beschikbaar is in het adres bereik van het virtuele netwerk.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan ik de grootte van mijn subnet wijzigen nadat ik het heb gemaakt?
Ja. U kunt een subnet toevoegen, verwijderen, uitbreiden of verkleinen als hierin geen VM's of services zijn geïmplementeerd.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Kan ik Vnet wijzigen nadat ik deze heb gemaakt?
Ja. U kunt de CIDR-blokken die door een VNet worden gebruikt, toevoegen, verwijderen en wijzigen.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Kan ik verbinding maken met internet als ik mijn Services in een VNet uitvoer?
Ja. Alle services die binnen een VNet zijn geïmplementeerd, kunnen uitgaande verbindingen met internet maken. Zie [uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over uitgaande Internet verbindingen in Azure. Als u inkomend wilt verbinden met een resource die via Resource Manager is geïmplementeerd, moet aan de resource een openbaar IP-adres zijn toegewezen. Zie [open bare IP-adressen](virtual-network-public-ip-address.md)voor meer informatie over open bare IP-adressen. Voor elke Azure-Cloud service die in Azure is geïmplementeerd, is een openbaar adresseerbaar VIP toegewezen. U definieert invoer eindpunten voor PaaS-rollen en-eind punten voor virtuele machines om deze services in staat te stellen verbindingen van Internet te accepteren.

### <a name="do-vnets-support-ipv6"></a>Bieden VNets ondersteuning voor IPv6?
Ja, VNets kan alleen IPv4 of dual stack (IPv4 + IPv6) zijn.  Zie [overzicht van IPv6 voor Azure Virtual Networks](./ipv6-overview.md)voor meer informatie.

### <a name="can-a-vnet-span-regions"></a>Kunt u een VNet-bereik regio's?
Nee. Een VNet is beperkt tot één regio. Een virtueel netwerk omvat echter beschikbaarheids zones. Zie [Overzicht van beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over beschikbaarheidszones. U kunt virtuele netwerken in verschillende regio's verbinden met de peering van het virtuele netwerk. Zie [overzicht van virtuele netwerk peering](virtual-network-peering-overview.md) voor meer informatie

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan ik een VNet verbinden met een ander VNet in azure?
Ja. U kunt verbinding maken met een VNet met een ander VNet met behulp van:
- **Peering van het virtuele netwerk**: Zie [overzicht van VNet-peering](virtual-network-peering-overview.md) voor meer informatie
- **Een Azure-VPN gateway**: Zie [een vnet-naar-VNet-verbinding configureren](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie. 

## <a name="name-resolution-dns"></a>Naam omzetting (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Wat zijn mijn DNS-opties voor VNets?
Gebruik de tabel beslissing op de pagina [naam omzetting voor vm's en rolinstanties](virtual-networks-name-resolution-for-vms-and-role-instances.md) om u te helpen bij alle beschik bare DNS-opties.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan ik DNS-servers voor een VNet opgeven?
Ja. U kunt IP-adressen van de DNS-server opgeven in de VNet-instellingen. De instelling wordt toegepast als de standaard DNS-server (s) voor alle virtuele machines in het VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Hoeveel DNS-servers kan ik opgeven?
Referentie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan ik mijn DNS-servers wijzigen nadat ik het netwerk heb gemaakt?
Ja. U kunt de DNS-server lijst voor uw VNet op elk gewenst moment wijzigen. Als u de lijst met DNS-servers wijzigt, moet u een DHCP-lease vernieuwing uitvoeren op alle betrokken Vm's in het VNet, zodat de nieuwe DNS-instellingen van kracht worden. Voor Vm's met Windows OS kunt u dit doen door `ipconfig /renew` rechtstreeks op de virtuele machine te typen. Raadpleeg de documentatie over het verlengen van DHCP-leases voor het specifieke type besturings systeem voor andere typen besturings systemen. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Wat is Azure DNS en werkt samen met VNets?
Door Azure geleverde DNS is een DNS-service voor meerdere tenants die door micro soft wordt aangeboden. Azure registreert al uw Vm's en Cloud service-rolinstanties in deze service. Deze service biedt naam omzetting per hostnaam voor Vm's en rolinstanties die zich in dezelfde Cloud service bevinden, en op FQDN voor Vm's en rolinstanties in hetzelfde VNet. Zie [naam omzetting voor vm's en Cloud Services rolinstanties voor](virtual-networks-name-resolution-for-vms-and-role-instances.md)meer informatie over DNS.

Er geldt een beperking voor de eerste 100-Cloud Services in een VNet voor het omzetten van cross-Tenant namen met behulp van Azure DNS. Als u uw eigen DNS-server gebruikt, is deze beperking niet van toepassing.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan ik mijn DNS-instellingen overschrijven op basis van per VM of Cloud service?
Ja. U kunt DNS-servers per VM of Cloud service zo instellen dat de standaard netwerk instellingen worden overschreven. Het is echter raadzaam om zo veel mogelijk netwerk-DNS te gebruiken.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan ik mijn eigen DNS-achtervoegsel plaatsen?
Nee. U kunt geen aangepast DNS-achtervoegsel opgeven voor uw VNets.

## <a name="connecting-virtual-machines"></a>Virtuele machines verbinden

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan ik Vm's implementeren in een VNet?
Ja. Alle netwerk interfaces (NIC) die zijn gekoppeld aan een virtuele machine die is geïmplementeerd via het Resource Manager-implementatie model, moeten zijn verbonden met een VNet. Vm's die via het klassieke implementatie model worden geïmplementeerd, kunnen eventueel worden verbonden met een VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Wat zijn de verschillende typen IP-adressen die ik aan Vm's kan toewijzen?
* **Privé:** Toegewezen aan elke NIC binnen elke VM. Het adres wordt toegewezen met behulp van de statische of dynamische methode. Privé-IP-adressen worden toegewezen uit het bereik dat u hebt opgegeven in de instellingen van het subnet van uw VNet. Voor resources die zijn geïmplementeerd via het klassieke implementatie model, worden privé IP-adressen toegewezen, zelfs als ze niet zijn verbonden met een VNet. Het gedrag van de toewijzings methode verschilt, afhankelijk van het feit of een resource is geïmplementeerd met de Resource Manager of het klassieke implementatie model: 

  - **Resource Manager**: een privé-IP-adres dat is toegewezen aan de dynamische of statische methode blijft toegewezen aan een virtuele machine (Resource Manager) totdat de resource wordt verwijderd. Het verschil is dat u het adres selecteert dat u wilt toewijzen wanneer u statische gebruikt en Azure kiest wanneer u dynamische gebruikt. 
  - **Klassiek**: een privé-IP-adres dat is toegewezen aan de dynamische methode kan worden gewijzigd wanneer een virtuele machine (klassieke) VM opnieuw wordt opgestart nadat de status gestopt (toewijzing ongedaan gemaakt) is bereikt. Als u er zeker van wilt zijn dat het privé-IP-adres voor een bron die via het klassieke implementatie model wordt geïmplementeerd, nooit wordt gewijzigd, wijst u een privé-IP-adres toe met de statische methode.

* **Openbaar:** Optioneel toegewezen aan Nic's die zijn gekoppeld aan virtuele machines die zijn geïmplementeerd via het Azure Resource Manager-implementatie model. Het adres kan worden toegewezen met de statische of dynamische toewijzings methode. Alle Vm's en Cloud Services rolinstanties die via het klassieke implementatie model zijn geïmplementeerd, bestaan in een Cloud service, waaraan een *dynamisch*, openbaar virtueel IP-adres (VIP) is toegewezen. Een openbaar *statisch* IP-adres, een [gereserveerd IP adres](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)genoemd, kan optioneel als VIP worden toegewezen. U kunt open bare IP-adressen toewijzen aan afzonderlijke Vm's of Cloud Services rolinstanties die worden geïmplementeerd via het klassieke implementatie model. Deze adressen worden een [openbaar IP-adres op exemplaar niveau](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) genoemd en kunnen dynamisch worden toegewezen.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan ik een persoonlijk IP-adres reserveren voor een virtuele machine die ik op een later tijdstip moet maken?
Nee. U kunt geen privé-IP-adres reserveren. Als er een privé-IP-adres beschikbaar is, wordt het toegewezen aan een virtuele machine of rolinstantie door de DHCP-server. De virtuele machine kan al dan niet de VM zijn waaraan u het privé-IP-adres wilt toewijzen. U kunt echter het privé-IP-adres van een al gemaakte virtuele machine wijzigen in elk beschikbaar privé-IP-adres.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Worden privé-IP-adressen gewijzigd voor Vm's in een VNet?
Dat hangt ervan af. Als de virtuele machine is geïmplementeerd via Resource Manager, Nee, ongeacht of het IP-adres is toegewezen met de statische of dynamische toewijzings methode. Als de virtuele machine via het klassieke implementatie model is geïmplementeerd, kunnen dynamische IP-adressen worden gewijzigd wanneer een virtuele machine wordt gestart nadat de status gestopt (toewijzing ongedaan gemaakt) is bereikt. Het adres wordt vrijgegeven van een virtuele machine die is geïmplementeerd via een van beide implementatie modellen wanneer de virtuele machine wordt verwijderd.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan ik hand matig IP-adressen toewijzen aan Nic's in het VM-besturings systeem?
Ja, maar dit wordt niet aanbevolen, tenzij nodig, zoals bij het toewijzen van meerdere IP-adressen aan een virtuele machine. Zie [meerdere IP-adressen toevoegen aan een virtuele machine](virtual-network-multiple-ip-addresses-portal.md#os-config)voor meer informatie. Als het IP-adres dat is toegewezen aan een Azure-NIC dat is gekoppeld aan een VM wordt gewijzigd en het IP-adres in het VM-besturings systeem verschilt, verliest u de verbinding met de virtuele machine.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Wat gebeurt er met mijn IP-adressen als ik een implementatie site voor de Cloud service stop of een virtuele machine afsluit vanuit het besturings systeem?
Niets. De IP-adressen (openbaar VIP, openbaar en privé) blijven toegewezen aan de implementatie site van de Cloud service of virtuele machine.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan ik Vm's van het ene subnet verplaatsen naar een ander subnet in een VNet zonder opnieuw te implementeren?
Ja. Meer informatie kunt u vinden in het artikel [een VM of rolinstantie verplaatsen naar een ander subnet](/previous-versions/azure/virtual-network/virtual-networks-move-vm-role-to-subnet) .

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan ik een statisch MAC-adres voor mijn virtuele machine configureren?
Nee. Een MAC-adres kan niet statisch worden geconfigureerd.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Blijft het MAC-adres hetzelfde voor mijn virtuele machine wanneer het wordt gemaakt?
Ja, het MAC-adres blijft hetzelfde voor een virtuele machine die is geïmplementeerd via de Resource Manager en de klassieke implementatie modellen totdat deze wordt verwijderd. Voorheen werd het MAC-adres vrijgegeven als de virtuele machine is gestopt (toewijzing ongedaan gemaakt), maar nu wordt het MAC-adres bewaard, zelfs wanneer de virtuele machine de status disallocated heeft. Het MAC-adres blijft toegewezen aan de netwerk interface totdat de netwerk interface is verwijderd of het privé-IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerk interface, wordt gewijzigd. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan ik verbinding maken met Internet vanaf een virtuele machine in een VNet?
Ja. Alle Vm's en Cloud Services rolinstanties die in een VNet zijn geïmplementeerd, kunnen verbinding maken met internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-Services die verbinding maken met VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan ik Azure App Service Web Apps met een VNet gebruiken?
Ja. U kunt Web Apps implementeren in een VNet met behulp van een ASE (App Service Environment), de back-end van uw apps verbinden met uw VNets met VNet-integratie en het inkomende verkeer naar uw app vergren delen met Service-eind punten. Raadpleeg voor meer informatie de volgende artikelen:

* [App Service-netwerk functies](../app-service/networking-features.md)
* [Web Apps maken in een App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Een app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Toegangs beperkingen App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan ik Cloud Services implementeren met web-en werk rollen (PaaS) in een VNet?
Ja. U kunt ook Cloud Services rolinstanties implementeren in VNets. Hiervoor geeft u de VNet-naam en de rol/subnet toewijzingen op in de sectie netwerk configuratie van uw service configuratie. U hoeft uw binaire bestanden niet bij te werken.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Kan ik een schaalset voor een virtuele machine verbinden met een VNet?
Ja. U moet een schaalset voor een virtuele machine verbinden met een VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Is er een volledige lijst met Azure-Services waarmee u resources kunt implementeren in een VNet?
Ja, Zie [virtuele netwerk integratie voor Azure-Services](virtual-network-for-azure-services.md)voor meer informatie.

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hoe kan ik de toegang tot Azure PaaS-resources beperken vanuit een VNet?

Resources die via sommige Azure PaaS-Services (zoals Azure Storage en Azure SQL Database) zijn geïmplementeerd, kunnen de netwerk toegang tot VNet beperken via het gebruik van de service-eind punten voor virtuele netwerken of met een persoonlijke Azure-koppeling. Zie [overzicht van virtuele netwerk service-eind punten](virtual-network-service-endpoints-overview.md), [overzicht van Azure private link](../private-link/private-link-overview.md) voor meer informatie

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan ik mijn Services in en uit VNets verplaatsen?
Nee. U kunt Services in en uit VNets niet verplaatsen. Als u een resource wilt verplaatsen naar een ander VNet, moet u de resource verwijderen en opnieuw implementeren.

## <a name="security"></a>Beveiliging

### <a name="what-is-the-security-model-for-vnets"></a>Wat is het beveiligings model voor VNets?
VNets zijn geïsoleerd van elkaar en andere services die in de Azure-infra structuur worden gehost. Een VNet is een grens van een vertrouwens relatie.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan ik de binnenkomende of uitgaande verkeers stroom beperken tot resources die zijn verbonden met het VNet?
Ja. U kunt [netwerk beveiligings groepen](./network-security-groups-overview.md) Toep assen op afzonderlijke subnetten binnen een Vnet, nic's die zijn gekoppeld aan een vnet of beide.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan ik een firewall implementeren tussen bronnen die zijn verbonden met VNet?
Ja. U kunt via Azure Marketplace een [virtueel netwerk](https://azure.microsoft.com/marketplace/?term=firewall) van een firewall van verschillende leveranciers implementeren.

### <a name="is-there-information-available-about-securing-vnets"></a>Is er informatie beschikbaar over het beveiligen van VNets?
Ja. Zie [overzicht van Azure-netwerk beveiliging](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie.

### <a name="do-virtual-networks-store-customer-data"></a>Slaan virtuele netwerken klant gegevens op?
Nee. Virtuele netwerken slaan geen klant gegevens op. 

## <a name="apis-schemas-and-tools"></a>Api's, schema's en hulpprogram ma's

### <a name="can-i-manage-vnets-from-code"></a>Kan ik VNets beheren vanuit code?
Ja. U kunt REST-Api's gebruiken voor VNets in de [Azure Resource Manager](/rest/api/virtual-network) en [klassieke](/previous-versions/azure/ee460799(v=azure.100)) implementatie modellen.

### <a name="is-there-tooling-support-for-vnets"></a>Is er hulp programma beschikbaar voor VNets?
Ja. Meer informatie over het gebruik van:
- Het Azure Portal om VNets te implementeren via de [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) en [klassieke](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) implementatie modellen.
- Power shell voor het beheren van VNets die zijn geïmplementeerd via de [Resource Manager](/powershell/module/az.network) en de [klassieke](/powershell/module/servicemanagement/azure.service/) implementatie modellen.
- De Azure-opdracht regel interface (CLI) voor het implementeren en beheren van VNets die zijn geïmplementeerd via de [Resource Manager](/cli/azure/network/vnet) en de [klassieke](/previous-versions/azure/virtual-machines/azure-cli-arm-commands?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) implementatie modellen.  

## <a name="vnet-peering"></a>VNet-peering

### <a name="what-is-vnet-peering"></a>Wat is VNet-peering?
Met VNet-peering (of peering op virtueel netwerk) kunt u verbinding maken met virtuele netwerken. Met een VNet-peering-verbinding tussen virtuele netwerken kunt u verkeer via IPv4-adressen routeren tussen hun eigen gegevens. Virtuele machines in de peered VNets kunnen met elkaar communiceren alsof ze zich in hetzelfde netwerk bevinden. Deze virtuele netwerken kunnen zich in dezelfde regio bevinden of in verschillende regio's (ook bekend als Global VNet-peering). VNet-peering verbindingen kunnen ook worden gemaakt in azure-abonnementen.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kan ik een peering-verbinding maken met een VNet in een andere regio?
Ja. Met wereld wijde VNet-peering kunt u VNets in verschillende regio's. Wereld wijde VNet-peering is beschikbaar in alle open bare Azure-regio's, China-Cloud regio's en overheids Cloud regio's. U kunt geen wereld wijde peer van open bare Azure-regio's naar nationale Cloud regio's.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Wat zijn de beperkingen met betrekking tot globaal VNet-peering en load balancers?
Als de twee virtuele netwerken in twee verschillende regio's worden gekoppeld via globale VNet-peering, kunt u geen verbinding maken met bronnen die zich achter een basis Load Balancer bevinden via het front-end-IP-adres van de Load Balancer. Deze beperking bestaat niet voor een Standard Load Balancer.
De volgende resources kunnen Basic load balancers gebruiken. Dit betekent dat u deze niet kunt bereiken via het front-end-IP-adres van de Load Balancer via globale VNet-peering. U kunt ook globale VNet-peering gebruiken om de bronnen rechtstreeks te bereiken via hun eigen VNet-Ip's, indien toegestaan. 
- Vm's achter Basic load balancers
- Schaal sets voor virtuele machines met Basic load balancers 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- API Management
- Active Directory-domein-service (toevoegen)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service-omgeving

U kunt via VNet-gateways verbinding maken met deze resources via ExpressRoute of VNet-naar-VNet.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kan ik VNet-peering inschakelen als mijn virtuele netwerken deel uitmaken van abonnementen binnen verschillende Azure Active Directory tenants?
Ja. Het is mogelijk om VNet-peering (lokaal of globaal) tot stand te brengen als uw abonnementen deel uitmaken van verschillende Azure Active Directory tenants. U kunt dit doen via Portal, Power shell of CLI.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mijn VNet-peering-verbinding heeft de status *gestart* , waarom kan ik geen verbinding maken?
Als uw peering-verbinding een *geïnitieerde* status heeft, betekent dit dat u slechts één koppeling hebt gemaakt. Er moet een bidirectionele koppeling worden gemaakt om een geslaagde verbinding te kunnen maken. Voor bijvoorbeeld een peer VNet A naar VNet B, moet er een koppeling worden gemaakt van VNetA naar VNetB en van VNetB naar VNetA. Als u beide koppelingen maakt, wordt de status gewijzigd in *verbonden*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mijn VNet-peering-verbinding heeft de status niet *verbonden* , waarom kan ik geen peering-verbinding maken?
Als uw VNet-peering-verbinding de status *verbroken* heeft, betekent dit dat een van de gemaakte koppelingen is verwijderd. Als u een peering-verbinding wilt herstellen, moet u de koppeling verwijderen en opnieuw maken.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kan ik mijn VNet koppelen aan een VNet in een ander abonnement?
Ja. U kunt VNets op verschillende abonnementen en in verschillende regio's.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kan ik twee VNets met overeenkomende of overlappende adresbereiken?
Nee. Adres ruimten mogen niet overlappen om VNet-peering in te scha kelen.

### <a name="can-i-peer-a-vnet-to-two-different-vnets-with-the-the-use-remote-gateway-option-enabled-on-both-the-peerings"></a>Kan ik een VNet met twee verschillende VNets gelijkwaardig met de optie externe gateway gebruiken ingeschakeld op beide peerings?
Nee. U kunt de optie externe gateway gebruiken alleen inschakelen voor één peering op een van de VNets.

### <a name="how-much-do-vnet-peering-links-cost"></a>Wat zijn de kosten voor VNet-peering koppelingen?
Er worden geen kosten in rekening gebracht voor het maken van een VNet-peering-verbinding. Gegevens overdracht tussen peering-verbindingen wordt in rekening gebracht. [Hier weer geven](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Is het VNet-peering verkeer versleuteld?
Als Azure-verkeer wordt verplaatst tussen Data Centers (buiten fysieke grenzen die niet worden beheerd door micro soft of namens micro soft), wordt [MACsec Data Link Layer encryption](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) gebruikt op de onderliggende netwerkhardware.  Dit is van toepassing op VNet-peering verkeer.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Waarom is mijn peering-verbinding met een *verbroken* status?
VNet-peering-verbindingen gaan in de status *verbroken* wanneer één VNet-peering koppeling wordt verwijderd. U moet beide koppelingen verwijderen om een succes volle peering-verbinding tot stand te brengen.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Als ik VNetA naar VNetB en ik VNetB aan VNetC, betekent dat VNetA en VNetC dan ook gelijkwaardig?
Nee. Transitieve peering wordt niet ondersteund. U moet peer-VNetA en VNetC om dit te kunnen doen.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Zijn er bandbreedte beperkingen voor peering-verbindingen?
Nee. VNet-peering, lokaal of globaal, houdt geen beperkingen voor de band breedte op. Band breedte wordt alleen beperkt door de virtuele machine of de compute-resource.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hoe kan ik problemen met VNet-peering oplossen?
Hier volgt een [gids voor probleem oplossing](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) die u kunt proberen.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Welke Azure-regio's zijn beschikbaar voor virtuele netwerk tikken?
Het voor beeld van een virtueel netwerk Tik is beschikbaar in alle Azure-regio's. De bewaakte netwerk interfaces, het virtuele netwerk Tik op resource en de collector-of analyse oplossing moeten in dezelfde regio worden geïmplementeerd.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Ondersteunt Virtual Network op het ondersteunen van filter mogelijkheden voor gespiegelde pakketten?
Filter functies worden niet ondersteund met het virtuele netwerk Tik op Preview. Wanneer een tik-configuratie wordt toegevoegd aan een netwerk interface, wordt een diepe kopie van alle binnenkomend en uitgaand verkeer op de netwerk interface gestreamd naar het kraan doel.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Kunnen meerdere tikken configuraties worden toegevoegd aan een bewaakte netwerk interface?
Een bewaakte netwerk interface kan slechts één tik-configuratie hebben. Neem contact op met de individuele [partner oplossing](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) voor de mogelijkheid om meerdere exemplaren van het tikte verkeer naar de analyse hulpprogramma's van uw keuze te streamen.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Kan hetzelfde virtuele netwerk het aggregatie verkeer van de resource van bewaakte netwerk interfaces in meer dan één virtueel netwerk hebben?
Ja. U kunt hetzelfde virtuele netwerk op Resource gebruiken om gespiegeld verkeer van bewaakte netwerk interfaces in gekoppelde virtuele netwerken in hetzelfde abonnement of een ander abonnement samen te voegen. Het virtuele netwerk Tik op resource en de doel-load balancer of de doelnet-netwerk interface moeten zich in hetzelfde abonnement bevallen. Alle abonnementen moeten onder hetzelfde Azure Active Directory Tenant staan.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Zijn er prestatie overwegingen voor het productie verkeer als ik een virtueel netwerk Tik op een netwerk interface inschakel?

Virtual Network TIKT is in preview. Tijdens de preview-periode is er geen service level agreement. De mogelijkheid mag niet worden gebruikt voor werk belastingen voor de productie. Wanneer de netwerk interface van een virtuele machine is ingeschakeld met een tik-configuratie, worden dezelfde resources op de Azure-host die is toegewezen aan de virtuele machine voor het verzenden van het productie verkeer, gebruikt om de mirroring-functie uit te voeren en de gespiegelde pakketten te verzenden. Selecteer de juiste grootte voor de virtuele [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine om ervoor te zorgen dat er voldoende bronnen beschikbaar zijn voor de virtuele machine om het productie verkeer en het gespiegelde verkeer te verzenden.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Wordt versneld netwerken ondersteund voor [Linux](create-vm-accelerated-networking-cli.md) -of [Windows](create-vm-accelerated-networking-powershell.md) -ondersteuning met een virtueel netwerk tikken?

U kunt een tik-configuratie toevoegen op een netwerk interface die is gekoppeld aan een virtuele machine die is ingeschakeld met versneld netwerken. Maar de prestaties en latentie van de virtuele machine worden beïnvloed door het toevoegen van TIKT configuratie, omdat de offload van mirroring verkeer momenteel niet wordt ondersteund door Azure versneld netwerken.

## <a name="virtual-network-service-endpoints"></a>Service-eindpunten voor virtueel netwerk

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Wat is de juiste volg orde van bewerkingen voor het instellen van service-eind punten voor een Azure-service?
Er zijn twee stappen voor het beveiligen van een Azure-Service Resource via service-eind punten:
1. Schakel service-eind punten voor de Azure-service in.
2. VNet-Acl's instellen voor de Azure-service.

De eerste stap is een bewerking aan het netwerk en de tweede stap is een bewerking aan de service bron naast elkaar. Beide stappen kunnen door dezelfde beheerder of verschillende beheerders worden uitgevoerd op basis van de Azure RBAC-machtigingen die zijn verleend aan de beheerdersrol. We raden u aan eerst Service-eind punten voor uw virtuele netwerk in te scha kelen voordat u VNet-Acl's op de Azure-service kant instelt. Daarom moeten de stappen in de bovenstaande volg orde worden uitgevoerd om de VNet-service-eind punten in te stellen.

>[!NOTE]
> Beide bewerkingen die hierboven worden beschreven, moeten worden voltooid voordat u de toegang tot de Azure-service kunt beperken tot het toegestane VNet en subnet. Alleen het inschakelen van service-eind punten voor de Azure-service aan de kant van het netwerk biedt geen beperkte toegang. Daarnaast moet u ook VNet-Acl's instellen op de service kant van Azure.

Bepaalde services (zoals SQL en CosmosDB) maken uitzonde ringen op de bovenstaande volg orde mogelijk via de vlag **IgnoreMissingVnetServiceEndpoint** . Zodra de vlag is ingesteld op **True**, kunnen VNet-acl's worden ingesteld op de Azure-service naast het instellen van de service-eind punten aan de kant van het netwerk. Azure-Services bieden deze vlag om klanten te helpen in gevallen waarin de specifieke IP-firewalls zijn geconfigureerd in Azure-Services en de service-eind punten aan de kant van het netwerk in te scha kelen, kunnen leiden tot een connectiviteits vertraging sinds de bron-IP van een openbaar IPv4-adres naar een particulier adres wordt gewijzigd. Het instellen van VNet-Acl's op de service kant van Azure voordat u service-eind punten instelt op de netwerk zijde, kunt u helpen om een verbinding te verbreken.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Bevinden alle Azure-Services zich in het virtuele Azure-netwerk van de klant? Hoe werkt het VNet-service-eind punt met Azure-Services?

Nee, niet alle Azure-Services bevinden zich in het virtuele netwerk van de klant. Het meren deel van Azure Data Services, zoals Azure Storage, Azure SQL en Azure Cosmos DB, zijn multi tenant-services die toegankelijk zijn via open bare IP-adressen. [Hier](virtual-network-for-azure-services.md)vindt u meer informatie over de integratie van virtuele netwerken voor Azure-Services. 

Wanneer u de functie voor de VNet-service-eind punten gebruikt (het VNet-service-eind punt op de netwerk zijde inschakelt en de juiste VNet-Acl's instelt op de Azure-service kant), is toegang tot een Azure-service beperkt van een toegestaan VNet en subnet.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hoe biedt VNet-service-eind punten beveiliging?

De functie van het VNet-service-eind punt (door het VNet-service-eind punt in te scha kelen op de netwerk zijde en de juiste VNet-Acl's in te stellen op de Azure-service zijde) beperkt de toegang tot de Azure-service tot het toegestane VNet en subnet, waardoor het Azure-service verkeer kan worden beveiligd en geïsoleerd. Al het verkeer met behulp van VNet-service-eind punten stromen over micro soft-backbone, waardoor een andere laag van isolatie van het open bare Internet wordt geboden. Daarnaast kunnen klanten de open bare Internet toegang tot de Azure-service bronnen volledig verwijderen en alleen verkeer vanaf hun virtuele netwerk toestaan via een combi natie van IP-firewall-en VNet-Acl's, waardoor de Azure-service bronnen worden beschermd tegen onbevoegde toegang.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Wat is de VNet-service Endpoint beveiligen-VNet-bronnen of Azure-service?
Met de VNet-service-eind punten kunt u Azure-service resources beveiligen. VNet-bronnen worden beveiligd door netwerk beveiligings groepen (Nsg's).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Zijn er kosten verbonden aan het gebruik van VNet-service-eind punten?

Nee, er zijn geen extra kosten verbonden aan het gebruik van VNet-service-eind punten.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Kan ik VNet-service-eind punten inschakelen en VNet-Acl's instellen als het virtuele netwerk en de Azure-service resources deel uitmaken van verschillende abonnementen?

Ja, dat is mogelijk. Virtuele netwerken en Azure-service resources kunnen zich in dezelfde of een andere abonnementen bevindt. De enige vereiste is dat zowel het virtuele netwerk als de Azure-service resources onder dezelfde Active Directory (AD)-Tenant moeten zijn.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Kan ik VNet-service-eind punten inschakelen en VNet-Acl's instellen als het virtuele netwerk en de Azure-service bronnen deel uitmaken van verschillende AD-tenants?
Ja, het is mogelijk bij het gebruik van service-eind punten voor Azure Storage en Azure Key Vault. Voor rest van services worden VNet-service-eind punten en VNet-Acl's niet ondersteund in AD-tenants.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Kan een on-premises IP-adres van het apparaat zijn dat is verbonden via Azure Virtual Network gateway (VPN) of ExpressRoute gateway toegang Azure PaaS service via VNet-service-eind punten?
Standaard zijn Azure-serviceresources die zijn beveiligd naar virtuele netwerken, niet bereikbaar vanaf on-premises netwerken. Als u verkeer van on-premises wilt toestaan, moet u ook open bare (doorgaans NAT) IP-adressen van uw on-premises of ExpressRoute toestaan. Deze IP-adressen kunnen worden toegevoegd via de IP-firewall configuratie voor de Azure-service resources.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Kan ik de functie van het VNet-service-eind punt gebruiken om de Azure-service te beveiligen voor meerdere subnetten binnen een virtueel netwerk of over meerdere virtuele netwerken?
Als u Azure-Services wilt beveiligen voor meerdere subnetten binnen een virtueel netwerk of meerdere virtuele netwerken, schakelt u service-eind punten in op het netwerk onafhankelijk van elkaar en vervolgens de Azure-service resources naar alle subnetten door de juiste VNet-Acl's in te stellen op de Azure-service kant.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hoe kan ik uitgaand verkeer van een virtueel netwerk filteren naar Azure-Services en nog steeds service-eind punten gebruiken?
Als u het verkeer dat is bestemd voor een Azure-service vanaf het virtuele netwerk, wilt controleren of filteren, kunt u een virtueel netwerkapparaat implementeren binnen het virtuele netwerk. U kunt vervolgens service-eind punten Toep assen op het subnet waar het virtuele netwerk apparaat wordt geïmplementeerd en Azure-service resources alleen naar dit subnet worden gedistribueerd via VNet-Acl's. Dit scenario kan ook nuttig zijn als u de toegang tot Azure-Services vanuit uw virtuele netwerk wilt beperken tot specifieke Azure-resources met behulp van het filteren van virtuele netwerk apparaten. Zie [Egress with network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha) (Uitgaand verkeer met virtueel-netwerkapparaten) voor meer informatie.

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Wat gebeurt er wanneer u toegang hebt tot een Azure-service account met een toegangs beheer lijst (ACL) voor virtuele netwerken die buiten het VNet is ingeschakeld?
De HTTP-fout 403 of HTTP 404 wordt geretourneerd.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Zijn er subnetten van een virtueel netwerk die in verschillende regio's zijn gemaakt, toegang tot een Azure-service account in een andere regio toegestaan? 
Ja, voor de meeste Azure-Services hebben virtuele netwerken die in verschillende regio's zijn gemaakt toegang tot Azure-Services in een andere regio via de VNet-service-eind punten. Als een Azure Cosmos DB-account bijvoorbeeld staat vs-West of VS-Oost en virtuele netwerken zich in meerdere regio's bevinden, heeft het virtuele netwerk toegang tot Azure Cosmos DB. Opslag en SQL zijn uitzonde ringen en zijn in de regionale aard en zowel het virtuele netwerk als de Azure-service moeten zich in dezelfde regio bevinden.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Kan een Azure-service een VNet-ACL en een IP-firewall hebben?
Ja, een VNet-ACL en een IP-firewall kunnen naast elkaar bestaan. Beide functies vormen een aanvulling op elkaar om te zorgen voor isolatie en beveiliging.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Wat gebeurt er als u een virtueel netwerk of subnet verwijdert waarvoor service-eind punt is ingeschakeld voor de Azure-service?
Het verwijderen van VNets en subnetten zijn onafhankelijke bewerkingen en worden ondersteund, zelfs wanneer service-eind punten zijn ingeschakeld voor Azure-Services. In gevallen waarin de Azure-Services VNet-Acl's hebben ingesteld voor die VNets en subnetten, wordt de VNet-ACL-informatie die is gekoppeld aan die Azure-service uitgeschakeld wanneer een VNet of subnet waarvoor VNet-service-eind punt is ingeschakeld, wordt verwijderd.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Wat gebeurt er als een Azure-service account waarvoor een VNet-service-eind punt is ingeschakeld, is verwijderd?
Het verwijderen van een Azure-service account is een onafhankelijke bewerking en wordt ondersteund, zelfs wanneer het service-eind punt is ingeschakeld op de netwerk-en VNet-Acl's worden ingesteld op de service kant van Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Wat gebeurt er met het bron-IP-adres van een bron (zoals een virtuele machine in een subnet) waarvoor het VNet-service-eind punt is ingeschakeld?
Wanneer service-eind punten van het virtuele netwerk zijn ingeschakeld, worden de bron-IP-adressen van de resources in het subnet van het virtuele netwerk gewijzigd van het gebruik van open bare IPV4-adressen naar de privé-IP-adressen van het virtuele Azure-netwerk voor verkeer naar de Azure-service. Houd er rekening mee dat dit kan leiden tot specifieke IP-firewalls die eerder op de Azure-Services zijn ingesteld op openbaar IPV4-adres. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Heeft de route van het service-eind punt altijd voor rang?
Service-eind punten voegen een systeem route toe die voor rang heeft boven BGP-routes en een optimale route ring biedt voor het verkeer van service-eind punten. Service-eind punten nemen altijd direct service verkeer van uw virtuele netwerk naar de service in het backbone-netwerk Microsoft Azure. Voor meer informatie over hoe Azure een route selecteert, Zie [route ring van virtueel netwerk verkeer van Azure](virtual-networks-udr-overview.md).

### <a name="do-service-endpoints-work-with-icmp"></a>Werken service-eind punten met ICMP?
Nee, ICMP-verkeer dat is gebrond vanuit een subnet met Service-eind punten ingeschakeld, neemt het pad naar de service tunnel niet naar het gewenste eind punt. Service-eind punten verwerken alleen TCP-verkeer. Dit betekent dat als u de latentie of connectiviteit met een eind punt via service-eind punten wilt testen, de hulpprogram ma's zoals ping en tracert niet het werkelijke pad weer geven dat de bronnen in het subnet zullen overnemen.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hoe werkt NSG op een subnet met Service-eind punten?
Voor het bereiken van de Azure-service moet Nsg's de uitgaande connectiviteit toestaan. Als uw Nsg's zijn geopend voor al het uitgaande verkeer van Internet, zou het service-eindpunt verkeer moeten werken. U kunt het uitgaande verkeer ook beperken tot service-Ip's alleen met behulp van de service tags.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Welke machtigingen heb ik nodig om service-eind punten in te stellen?
Service-eind punten kunnen worden geconfigureerd op een virtueel netwerk onafhankelijk van een gebruiker met schrijf toegang tot het virtuele netwerk. Als u Azure-service resources wilt beveiligen met een VNet, moet de gebruiker beschikken over de machtiging **micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action** voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde rol service beheerder en kan worden gewijzigd door aangepaste rollen te maken. Meer informatie over ingebouwde rollen en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Kan ik virtueel netwerk verkeer filteren op Azure-Services, zodat er alleen specifieke Azure-service resources meer zijn dan VNet-service-eind punten? 

Met de beleids regels voor het beheer van virtuele netwerken (VNet) kunt u virtueel netwerk verkeer filteren op Azure-Services, zodat alleen specifieke Azure-service resources via de service-eind punten worden toegestaan. Endpoint-beleids regels bieden gedetailleerd toegangs beheer van het virtuele netwerk verkeer naar de Azure-Services. [Hier](virtual-network-service-endpoint-policies-overview.md)vindt u meer informatie over de service-eindpunt beleidsregels.

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Ondersteunt Azure Active Directory (Azure AD) VNet-service-eind punten?

Azure Active Directory (Azure AD) biedt geen ondersteuning voor service-eind punten systeem eigen. Volledige lijst met Azure-Services die de VNet-service-eind punten ondersteunen, is [hier](./virtual-network-service-endpoints-overview.md)te zien. Houd er rekening mee dat de tag ' micro soft. AzureActiveDirectory ' onder Services die service-eind punten ondersteunen wordt gebruikt voor het ondersteunen van service-eind punten voor ADLS gen 1. Voor ADLS gen 1 maakt de integratie van virtuele netwerken voor Azure Data Lake Storage Gen1 gebruik van de eindpunt beveiliging van het virtuele netwerk tussen uw virtuele netwerk en Azure Active Directory (Azure AD) voor het genereren van extra beveiligings claims in het toegangs token. Deze claims worden vervolgens gebruikt om het virtuele netwerk te verifiëren bij het Data Lake Storage Gen1-account en toegang toe te staan. Meer informatie over de [integratie van Azure data Lake Store gen 1-VNet](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Gelden er limieten voor het aantal VNet-service-eind punten dat ik kan instellen vanuit mijn VNet?
Er is geen limiet voor het totale aantal VNet-service-eind punten in een virtueel netwerk. Voor een Azure-service resource (zoals een Azure Storage account) kunnen services beperkingen afdwingen voor het aantal subnetten dat wordt gebruikt voor het beveiligen van de bron. In de volgende tabel ziet u enkele voor beelden van limieten: 

|Azure-service| Limieten voor VNet-regels|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> De limieten worden onderhevig aan wijzigingen in de keuze van de Azure-service. Raadpleeg de relevante service documentatie voor meer informatie over services. 




