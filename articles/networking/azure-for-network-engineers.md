---
title: 'Azure ExpressRoute: Azure voor netwerk technici'
description: Op deze pagina wordt uitgelegd hoe netwerken in azure werken met traditionele netwerk technici.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: e13dc868512dba15c5b335c1774aaa540eef8157
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861412"
---
# <a name="azure-for-network-engineers"></a>Azure voor netwerktechnici
Als een conventionele netwerk technicus hebt u fysieke activa, zoals routers, switches, kabels, firewalls voor het bouwen van een infra structuur. U hebt op een logische laag het virtuele LAN (VLAN) geconfigureerd, het STP (ring Tree Protocol), routerings protocollen (RIP, OSPF, BGP). U hebt uw netwerk beheerd met beheer hulpprogramma's en CLI. Netwerken in de Cloud wijken af van de locatie van de netwerk eindpunten logische en het gebruik van routerings protocollen mini maal. U werkt met Azure Resource Manager-API, Azure CLI en Power shell voor het configureren en beheren van assets in Azure. U begint uw netwerk traject in de Cloud door basis tenants van Azure-netwerken te weten te komen. 
## <a name="virtual-network"></a>Virtueel netwerk
Wanneer u een netwerk van beneden naar boven ontwerpt, verzamelt u enkele basis gegevens. Deze informatie kan het aantal hosts, netwerk apparaten, het aantal subnetten, route ring tussen subnetten, isolatie domeinen zoals VLAN'S zijn. Deze informatie helpt bij het aanpassen van de grootte van de netwerk-en beveiligings apparaten en het maken van de architectuur voor de ondersteuning van toepassingen en services.

Wanneer u van plan bent om uw toepassingen en services in azure te implementeren, maakt u eerst een logische grens in Azure. dit wordt een virtueel netwerk genoemd. Dit virtuele netwerk is Akin naar een fysieke netwerk grens. Omdat het een virtueel netwerk is, hebt u geen fysiek vistuig nodig, maar moet u nog steeds de logische entiteiten plannen, zoals IP-adressen, IP-subnetten, route ring en beleid.

Wanneer u een virtueel netwerk in azure maakt, is het vooraf geconfigureerd met een IP-bereik (10.0.0.0/16). Dit bereik is niet vast, u kunt uw eigen IP-bereik definiëren. U kunt zowel IPv4-als IPv6-adresbereiken definiëren. IP-adresbereiken die zijn gedefinieerd voor het virtuele netwerk, worden niet geadverteerd naar Internet. U kunt meerdere subnetten maken op basis van uw IP-bereik. Deze subnetten worden gebruikt om IP-adressen toe te wijzen aan virtuele netwerk interfaces (Vnic's). De eerste vier IP-adressen van elk subnet zijn gereserveerd en kunnen niet worden gebruikt voor IP-toewijzing. Er is geen concept van VLAN'S in een open bare Cloud. U kunt echter isolatie maken binnen een virtueel netwerk op basis van uw gedefinieerde subnetten.

U kunt één groot subnet maken dat de adres ruimte van het virtuele netwerk omvat of meerdere subnetten maakt. Als u echter een virtuele netwerk gateway gebruikt, moet u in azure een subnet met de naam ' gateway-subnet ' maken. Azure gebruikt dit subnet om IP-adressen toe te wijzen aan virtuele netwerk gateways. 

## <a name="ip-allocation"></a>IP-toewijzing

Wanneer u een IP-adres aan een host toewijst, wijst u IP toe aan een netwerk interface kaart (NIC). U kunt twee typen IP-adressen toewijzen aan een NIC in Azure:

- Open bare IP-adressen: wordt gebruikt om binnenkomende en uitgaande (zonder Network Address Translation (NAT)) te communiceren met internet en andere Azure-bronnen die niet zijn verbonden met een virtueel netwerk. Het toewijzen van een openbaar IP-adres aan een NIC is optioneel. Open bare IP-adressen behoren tot de IP-adres ruimte van micro soft.
- Privé-IP-adressen: wordt gebruikt voor communicatie binnen een virtueel netwerk, uw on-premises netwerk en het internet (met NAT). IP-adres ruimte die u in het virtuele netwerk definieert, wordt als privé beschouwd, zelfs als u uw open bare IP-adres ruimte configureert. Micro soft adverteert deze ruimte niet op internet. U moet ten minste één privé IP-adres toewijzen aan een virtuele machine.

Net als bij fysieke hosts of apparaten zijn er twee manieren om een IP-adres toe te wijzen aan een resource: dynamisch of statisch. In Azure is de standaard toewijzings methode dynamisch, waarbij een IP-adres wordt toegewezen wanneer u een virtuele machine (VM) maakt of een gestopt VM start. Het IP-adres wordt weer vrijgegeven wanneer u de virtuele machine stopt of verwijdert. Als u wilt dat het IP-adres voor de virtuele machine hetzelfde blijft, kunt u de toewijzingsmethode expliciet instellen op statisch. In dat geval wordt er onmiddellijk een IP-adres toegewezen. Het IP-adres wordt alleen vrijgegeven wanneer u de virtuele machine verwijdert of de toewijzingsmethode wijzigt in dynamisch. 

Privé-IP-adressen worden toegewezen vanuit de subnetten die u in een virtueel netwerk hebt gedefinieerd. Voor een virtuele machine kiest u een subnet voor de IP-toewijzing. Als een virtuele machine meerdere Nic's bevat, kunt u voor elke NIC een ander subnet kiezen.

## <a name="routing"></a>Routering
Wanneer u een virtueel netwerk maakt, maakt Azure een routerings tabel voor uw netwerk. Deze routerings tabel bevat de volgende typen routes.
- Systeemroutes
- Standaard routes van het subnet
- Routes van andere virtuele netwerken
- BGP-routes
- Service-eindpunt routes
- Door de gebruiker gedefinieerde routes (UDR)

Wanneer u voor de eerste keer een virtueel netwerk maakt zonder subnetten te definiëren, worden in azure routerings vermeldingen in de routerings tabel gemaakt. Deze routes worden systeem routes genoemd. Er zijn systeem routes gedefinieerd op deze locatie. U kunt deze routes niet wijzigen. U kunt echter systeem routes overschrijven door Udr's te configureren.

Wanneer u een of meerdere subnetten binnen een virtueel netwerk maakt, maakt Azure standaard vermeldingen in de routerings tabel om communicatie mogelijk te maken tussen deze subnetten in een virtueel netwerk. Deze routes kunnen worden gewijzigd met behulp van statische routes, die door de gebruiker gedefinieerde routes (UDR) in Azure worden gebruikt.

Wanneer u een VNet-peering maakt tussen twee virtuele netwerken, wordt er een route toegevoegd voor elk adresbereik in de adresruimte van elk virtueel netwerk waarvoor een peering wordt gemaakt.

Als uw on-premises netwerk gateway BGP-routes (Border Gateway Protocol) met een virtuele netwerk gateway van Azure uitwisselt, wordt er een route toegevoegd voor elke route die wordt door gegeven vanaf de on-premises netwerk gateway. Deze routes worden als BGP-routes in de routerings tabel weer gegeven.

De openbare IP-adressen voor bepaalde services worden met Azure aan de routetabel toegevoegd wanneer u een service-eindpunt voor de service inschakelt. Service-eind punten zijn ingeschakeld voor afzonderlijke subnetten binnen een virtueel netwerk. Wanneer u een service-eind punt inschakelt, wordt route alleen toegevoegd aan de route tabel van voor het subnet dat bij deze service hoort. Azure beheert de adressen in de routetabel automatisch als de adressen worden gewijzigd.

Door de gebruiker gedefinieerde routes worden ook aangepaste routes genoemd. U maakt UDR in azure om de standaard systeem routes van Azure te overschrijven, of om extra routes toe te voegen aan de route tabel van een subnet. In azure maakt u een route tabel en koppelt u de route tabel aan subnetten van het virtuele netwerk.

Wanneer u concurrerende vermeldingen in een routerings tabel hebt, selecteert Azure de volgende hop op basis van het langste voor voegsel dat overeenkomt met de traditionele routers. Als er echter meerdere volgende hop-items met hetzelfde adres voorvoegsel zijn, selecteert Azure de routes in de volgende volg orde.
1. Door de gebruiker gedefinieerde routes (UDR)
1. BGP-routes
1. Systeemroutes

## <a name="security"></a>Beveiliging

U kunt netwerk verkeer filteren van en naar bronnen in een virtueel netwerk met behulp van netwerk beveiligings groepen. U kunt ook virtuele netwerk apparaten (NVA) gebruiken, zoals Azure Firewall of firewalls van andere leveranciers. U kunt bepalen hoe Azure verkeer routeert van subnetten. U kunt ook beperken wie in uw organisatie kan werken met resources in virtuele netwerken.

Een netwerkbeveiligingsgroep (NSG) bevat een lijst met ACL-regels (Access Control List, toegangsbeheerlijst) waarmee netwerkverkeer naar subnetten, NIC’s of beide wordt toegestaan of geweigerd. NSG's kunnen worden gekoppeld aan subnetten of afzonderlijke NIC’s die zijn verbonden met een subnet. Als een NSG is gekoppeld aan een subnet, zijn de ACL-regels van toepassing op alle virtuele machines in dat subnet. U kunt het verkeer naar een afzonderlijke NIC beperken door een NSG rechtstreeks aan een NIC te koppelen.

NSG's bevatten twee sets met regels: een set met regels voor binnenkomend verkeer en een set met regels voor uitgaand verkeer. De prioriteit voor een regel moet uniek zijn binnen elke set. Elke regel heeft eigenschappen voor protocol, bron- en doelpoortbereik, adresvoorvoegsels, richting van verkeer, prioriteit en toegangstype. Alle NSG's bevatten een set met standaardregels. De standaardregels kunnen niet worden verwijderd, maar omdat ze de laagste prioriteit hebben, kunnen ze worden overschreven door de regels die u maakt.

## <a name="verification"></a>Verificatie
### <a name="routes-in-virtual-network"></a>Routes in het virtuele netwerk
De combi natie van routes die u maakt, de standaard routes van Azure en alle routes die zijn door gegeven vanaf uw on-premises netwerk via een Azure VPN-gateway (als uw virtuele netwerk is verbonden met uw on-premises netwerk) via het Border Gateway Protocol (BGP), zijn de efficiënte routes voor alle netwerk interfaces in een subnet. U kunt deze efficiënte routes zien door te navigeren naar de NIC via Portal, Power shell of CLI.
### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
De geldende beveiligings regels die worden toegepast op een netwerk interface zijn een aggregatie van de regels die bestaan in de NSG die zijn gekoppeld aan een netwerk interface en het subnet waarin de netwerk interface zich bevindt. U kunt alle effectief beveiligings regels weer geven van Nsg's die worden toegepast op de netwerk interfaces van uw virtuele machine door te navigeren naar de NIC via Portal, Power shell of CLI.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [virtuele netwerk][VNet].

Meer informatie over [virtuele netwerk routering][vnet-routing].

Meer informatie over de [netwerk beveiligings groepen][network-security].

<!--Link References-->
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[vnet-routing]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview
[network-security]: https://docs.microsoft.com/azure/virtual-network/security-overview

