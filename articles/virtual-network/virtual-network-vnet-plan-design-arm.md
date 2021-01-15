---
title: Virtuele netwerken van Azure plannen | Microsoft Docs
description: Leer hoe u virtuele netwerken plant op basis van de vereisten met betrekking tot isolatie, connectiviteit en locatie.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 9ed060c1f3fc47aa7050c0f9b25c757953fa22fe
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220334"
---
# <a name="plan-virtual-networks"></a>Virtuele netwerken plannen

Het maken van een virtueel netwerk om te experimenteren met is eenvoudig genoeg, maar waarschijnlijk is het dat u meerdere virtuele netwerken gedurende een periode implementeert om de productie behoeften van uw organisatie te ondersteunen. Met sommige planningen kunt u virtuele netwerken implementeren en de bronnen die u nodig hebt, aansluiten. De informatie in dit artikel is het handigst als u al bekend bent met virtuele netwerken en een aantal ervaring hebt met het werken. Als u niet bekend bent met virtuele netwerken, is het raadzaam om het overzicht van het [virtuele netwerk](virtual-networks-overview.md)te lezen.

## <a name="naming"></a>Naamgeving

Alle Azure-resources hebben een naam. De naam moet uniek zijn binnen een bereik en kan variëren voor elk resource type. De naam van een virtueel netwerk moet bijvoorbeeld uniek zijn binnen een [resource groep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), maar kan worden gedupliceerd binnen een [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) of Azure- [regio](https://azure.microsoft.com/regions/#services). Het definiëren van een naamgevings Conventie die u consistent kunt gebruiken bij het benoemen van resources is handig bij het beheren van verschillende netwerk bronnen gedurende een bepaalde periode. Zie [naamgevings conventies](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork)voor suggesties.

## <a name="regions"></a>Regio's

Alle Azure-resources worden gemaakt in een Azure-regio en-abonnement. Een resource kan alleen worden gemaakt in een virtueel netwerk dat zich in dezelfde regio en hetzelfde abonnement als de resource bevindt. U kunt echter virtuele netwerken koppelen die zich in verschillende abonnementen en regio's bevinden. Zie [connectiviteit](#connectivity)voor meer informatie. Houd bij het bepalen van de regio ('s) voor het implementeren van resources in rekening met de fysieke locatie van de consumenten van de resources:

- Consumenten van resources willen doorgaans de laagste netwerk latentie voor hun resources. Zie [relatieve wacht tijden weer geven](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)om relatieve wacht tijden te bepalen tussen een opgegeven locatie en Azure-regio's.
- Hebt u de vereisten voor gegevens locatie, soevereiniteit, naleving of tolerantie? Als dit het geval is, kiest u de regio die aan de vereisten voldoet. Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)(Engelstalig) voor meer informatie.
- Hebt u tolerantie nodig over Azure-beschikbaarheidszones in dezelfde Azure-regio voor de resources die u implementeert? U kunt resources, zoals virtuele machines (VM), implementeren in verschillende beschikbaarheids zones binnen hetzelfde virtuele netwerk. Niet alle Azure-regio's ondersteunen echter beschikbaarheids zones. Zie [beschikbaarheids zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over beschikbaarheids zones en de regio's die deze ondersteunen.

## <a name="subscriptions"></a>Abonnementen

U kunt zoveel virtuele netwerken implementeren als vereist in elk abonnement, tot aan de [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Sommige organisaties hebben bijvoorbeeld verschillende abonnementen voor verschillende afdelingen. Zie voor meer informatie en overwegingen met betrekking tot abonnementen het beheer van [abonnementen](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentatie

U kunt meerdere virtuele netwerken per abonnement en per regio maken. U kunt meerdere subnetten binnen elk virtueel netwerk maken. De volgende overwegingen helpen u bij het bepalen van het aantal virtuele netwerken en subnetten dat u nodig hebt:

### <a name="virtual-networks"></a>Virtuele netwerken

Een virtueel netwerk is een virtueel, geïsoleerd deel van het open bare Azure-netwerk. Elk virtueel netwerk is toegewezen aan uw abonnement. Denk aan het volgende om te bepalen of u één virtueel netwerk of meerdere virtuele netwerken wilt maken in een abonnement:

- Bestaan er beveiligings vereisten voor de organisatie voor het isoleren van verkeer in afzonderlijke virtuele netwerken? U kunt ervoor kiezen om virtuele netwerken te verbinden. Als u virtuele netwerken koppelt, kunt u een virtueel netwerk apparaat, zoals een firewall, implementeren om de stroom van verkeer tussen de virtuele netwerken te beheren. Zie [beveiliging](#security) en [connectiviteit](#connectivity)voor meer informatie.
- Bestaan er organisatorische vereisten voor het isoleren van virtuele netwerken in afzonderlijke [abonnementen](#subscriptions) of [regio's](#regions)?
- Met een [netwerk interface](virtual-network-network-interface.md) kan een virtuele machine communiceren met andere resources. Aan elke netwerk interface zijn een of meer privé-IP-adressen toegewezen. Hoeveel netwerk interfaces en [privé-IP-adressen](./private-ip-addresses.md) hebt u nodig in een virtueel netwerk? Er gelden [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor het aantal netwerk interfaces en privé-IP-adressen dat u binnen een virtueel netwerk kunt hebben.
- Wilt u het virtuele netwerk verbinden met een ander virtueel netwerk of een on-premises netwerk? U kunt ervoor kiezen om een aantal virtuele netwerken te koppelen aan elke andere of on-premises netwerken, maar niet op andere. Zie [connectiviteit](#connectivity)voor meer informatie. Elk virtueel netwerk waarmee u verbinding maakt met een ander virtueel netwerk of een on-premises netwerk, moet een unieke adres ruimte hebben. Elk virtueel netwerk heeft een of meer open bare of privé adresbereiken toegewezen aan de adres ruimte. Een adres bereik is opgegeven in CIDR-indeling (Classless Internet Domain Routing), zoals 10.0.0.0/16. Meer [informatie over](manage-virtual-network.md#add-or-remove-an-address-range) adresbereiken voor virtuele netwerken.
- Hebt u bedrijfs beheer vereisten voor bronnen in verschillende virtuele netwerken? Als dat het geval is, kunt u resources scheiden in afzonderlijke virtuele netwerken om de [machtigings toewijzing](#permissions) te vereenvoudigen voor personen in uw organisatie of om verschillende beleids regels toe te wijzen aan verschillende virtuele netwerken.
- Wanneer u een aantal Azure-service resources in een virtueel netwerk implementeert, maken ze hun eigen virtuele netwerk. Als u wilt bepalen of een Azure-service een eigen virtueel netwerk maakt, raadpleegt u de informatie voor elke [Azure-service die in een virtueel netwerk kan worden geïmplementeerd](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Subnetten

Een virtueel netwerk kan worden gesegmenteerd in een of meer subnetten tot aan de [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Denk aan het volgende om te bepalen of u één subnet of meerdere virtuele netwerken wilt maken in een abonnement:

- Elk subnet moet een uniek adres bereik hebben, opgegeven in CIDR-notatie, binnen de adres ruimte van het virtuele netwerk. Het adres bereik mag niet overlappen met andere subnetten in het virtuele netwerk.
- Als u van plan bent om bepaalde Azure-service resources te implementeren in een virtueel netwerk, kunnen ze een eigen subnet vereisen of maken, zodat er voldoende niet-toegewezen ruimte is. Als u wilt bepalen of een Azure-service een eigen subnet maakt, raadpleegt u de informatie voor elke [Azure-service die in een virtueel netwerk kan worden geïmplementeerd](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Als u bijvoorbeeld een virtueel netwerk verbindt met een on-premises netwerk met behulp van een Azure-VPN Gateway, moet het virtuele netwerk een specifiek subnet voor de gateway hebben. Meer informatie over [Gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure routeert netwerk verkeer tussen alle subnetten in een virtueel netwerk standaard. U kunt de standaard routering van Azure onderdrukken om Azure-route ring tussen subnetten te voor komen of om verkeer tussen subnetten te routeren via een virtueel netwerk apparaat, bijvoorbeeld. Als u wilt dat verkeer tussen bronnen in dezelfde virtuele netwerk stroom via een virtueel netwerk apparaat (NVA) wordt vereist, implementeert u de bronnen op verschillende subnetten. Meer informatie vindt u in [beveiliging](#security).
- U kunt de toegang tot Azure-resources, zoals een Azure-opslag account of Azure SQL Database, beperken tot specifieke subnetten met een service-eind punt voor een virtueel netwerk. Verder kunt u de toegang tot de bronnen van Internet weigeren. U kunt meerdere subnetten maken en een service-eind punt inschakelen voor sommige subnetten, maar niet voor anderen. Meer informatie over [service-eind punten](virtual-network-service-endpoints-overview.md)en de Azure-resources die u kunt inschakelen voor.
- U kunt nul of één netwerk beveiligings groep koppelen aan elk subnet in een virtueel netwerk. U kunt dezelfde of een andere netwerk beveiligings groep koppelen aan elk subnet. Elke netwerk beveiligings groep bevat regels die verkeer naar en van bronnen en bestemmingen toestaan of weigeren. Meer informatie over [netwerk beveiligings groepen](#traffic-filtering).

## <a name="security"></a>Beveiliging

U kunt netwerk verkeer van en naar resources in een virtueel netwerk filteren met behulp van netwerk beveiligings groepen en virtuele netwerk apparaten. U kunt bepalen hoe Azure verkeer routeert van subnetten. U kunt ook beperken wie in uw organisatie kan werken met resources in virtuele netwerken.

### <a name="traffic-filtering"></a>Verkeer filteren

- U kunt netwerk verkeer tussen bronnen in een virtueel netwerk filteren met behulp van een netwerk beveiligings groep, een NVA die netwerk verkeer filtert of beide. Als u een NVA, zoals een firewall, wilt implementeren om netwerk verkeer te filteren, raadpleegt u de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Wanneer u een NVA gebruikt, maakt u ook aangepaste routes voor het routeren van verkeer van subnetten naar de NVA. Meer informatie over [verkeers routering](#traffic-routing).
- Een netwerk beveiligings groep bevat verschillende standaard beveiligings regels die verkeer naar of van resources toestaan of weigeren. Een netwerk beveiligings groep kan worden gekoppeld aan een netwerk interface, het subnet waarin de netwerk interface zich bevindt of beide. Om het beheer van beveiligings regels te vereenvoudigen, is het raadzaam om, indien mogelijk, een netwerk beveiligings groep te koppelen aan afzonderlijke subnetten, in plaats van afzonderlijke netwerk interfaces binnen het subnet.
- Als voor verschillende Vm's in een subnet verschillende beveiligings regels moeten worden toegepast, kunt u de netwerk interface in de virtuele machine koppelen aan een of meer toepassings beveiligings groepen. Met een beveiligings regel kan een toepassings beveiligings groep worden opgegeven in de bron, het doel of beide. Deze regel geldt vervolgens alleen voor de netwerk interfaces die lid zijn van de toepassings beveiligings groep. Meer informatie over [netwerk beveiligings groepen](./network-security-groups-overview.md) en [toepassings beveiligings groepen](./network-security-groups-overview.md#application-security-groups).
- Azure maakt diverse standaard beveiligings regels in elke netwerk beveiligings groep. Met één standaard regel kan al het verkeer tussen alle resources in een virtueel netwerk stromen. U kunt dit gedrag negeren door netwerk beveiligings groepen, aangepaste route ring te gebruiken om verkeer door te sturen naar een NVA of beide. Het is raadzaam om vertrouwd te raken met alle [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules) van Azure en inzicht te krijgen in de manier waarop de regels voor netwerk beveiligings groepen worden toegepast op een bron.

U kunt voorbeeld ontwerpen weer geven voor het implementeren van een perimeter netwerk (ook wel DMZ genoemd) tussen Azure en Internet met behulp van een [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Verkeers routering

Azure maakt verschillende standaard routes voor uitgaand verkeer van een subnet. U kunt de standaard routering van Azure onderdrukken door een route tabel te maken en deze te koppelen aan een subnet. Veelvoorkomende redenen voor het overschrijven van de standaard routering van Azure:
- Omdat u verkeer tussen subnetten wilt laten lopen via een NVA. Voor meer informatie over het [configureren van route tabellen om verkeer af te dwingen via een NVA](tutorial-create-route-table-portal.md).
- Omdat u alle met Internet gebonden verkeer via een NVA of on-premises via een Azure VPN-gateway wilt afdwingen. Het afdwingen van Internet verkeer voor inspectie en logboek registratie wordt vaak geforceerde tunneling genoemd. Meer informatie over het configureren van [geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Als u een aangepaste route ring moet implementeren, is het raadzaam om de [route ring in azure](virtual-networks-udr-overview.md)te verkennen.

## <a name="connectivity"></a>Connectiviteit

U kunt een virtueel netwerk verbinden met andere virtuele netwerken met behulp van virtuele netwerk peering of met uw on-premises netwerk met behulp van een Azure VPN-gateway.

### <a name="peering"></a>Peering

Wanneer u [virtuele netwerk peering](virtual-network-peering-overview.md)gebruikt, kunnen de virtuele netwerken zich in dezelfde of verschillende ondersteunde Azure-regio's bevinden. De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-abonnementen behoren (zelfs abonnementen die tot verschillende Azure Active Directory tenants behoren). Voordat u een peering maakt, is het raadzaam om vertrouwd te raken met alle [vereisten en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints)van de peering. De band breedte tussen resources in virtuele netwerken die zijn gekoppeld aan dezelfde regio is hetzelfde als die van de resources in hetzelfde virtuele netwerk.

### <a name="vpn-gateway"></a>VPN-gateway

U kunt een Azure- [VPN gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om een virtueel netwerk te verbinden met uw on-premises netwerk met behulp van een [site-naar-site-VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)of door gebruik te maken van een speciale verbinding met Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

U kunt peering en een VPN-gateway combi neren om [hub-en spoke-netwerken](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)te maken, waarbij spoke-virtuele netwerken verbinding maken met een hub virtueel netwerk en de hub verbinding maakt met een on-premises netwerk.

### <a name="name-resolution"></a>Naamomzetting

Resources in één virtueel netwerk kunnen de namen van resources in een gekoppeld virtueel netwerk niet omzetten met behulp van de [ingebouwde DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)van Azure. Als u namen in een gekoppeld virtueel netwerk wilt omzetten, [implementeert u uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)of gebruikt u Azure DNS [particuliere domeinen](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Voor het omzetten van namen tussen bronnen in een virtueel netwerk en on-premises netwerken moet u ook uw eigen DNS-server implementeren.

## <a name="permissions"></a>Machtigingen

Azure maakt gebruik [van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor bronnen. Machtigingen worden toegewezen aan een [bereik](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) in de volgende hiërarchie: beheer groep, abonnement, resource groep en afzonderlijke resource. Zie [uw resources organiseren](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over de hiërarchie. Als u wilt werken met virtuele netwerken van Azure en alle bijbehorende mogelijkheden, zoals peering, netwerk beveiligings groepen, service-eind punten en route tabellen, kunt u leden van uw organisatie toewijzen aan de ingebouwde rol van [eigenaar](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)of [netwerk Inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) en vervolgens de rol toewijzen aan het juiste bereik. Als u specifieke machtigingen wilt toewijzen voor een subset van virtuele netwerk mogelijkheden, maakt u een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en wijst u de specifieke machtigingen toe die vereist zijn voor [virtuele netwerken](manage-virtual-network.md#permissions), [subnetten en service-eind punten](virtual-network-manage-subnet.md#permissions), [netwerk interfaces](virtual-network-network-interface.md#permissions), [peering](virtual-network-manage-peering.md#permissions), [netwerk-en toepassings beveiligings groepen](manage-network-security-group.md#permissions)of [routerings tabellen](manage-route-table.md#permissions) naar de rol.

## <a name="policy"></a>Beleid

Met Azure Policy kunt u beleids definities maken, toewijzen en beheren. Met beleids definities worden verschillende regels voor uw resources afgedwongen, zodat de resources niet blijven voldoen aan de standaarden van uw organisatie en service overeenkomsten. Azure Policy voert een evaluatie uit van uw resources, waarbij wordt gescand op resources die niet compatibel zijn met de beleids definities die u hebt. U kunt bijvoorbeeld een beleid definiëren en Toep assen waarmee u alleen virtuele netwerken in een specifieke resource groep of regio kunt maken. Een ander beleid kan vereisen dat aan elk subnet een netwerk beveiligings groep is gekoppeld. De beleids regels worden vervolgens geëvalueerd wanneer u resources maakt en bijwerkt.

Beleids regels worden toegepast op de volgende hiërarchie: beheer groep, abonnement en resource groep. Meer informatie over [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of het implementeren van een of meer virtuele netwerk [Azure Policy definities](./policy-reference.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over alle taken, instellingen en opties voor een [virtueel netwerk](manage-virtual-network.md), [subnet-en service-eind punt](virtual-network-manage-subnet.md), [netwerk interface](virtual-network-network-interface.md), [peering](virtual-network-manage-peering.md),  [netwerk-en toepassings beveiligings groep](manage-network-security-group.md)of [route tabel](manage-route-table.md).
