---
title: Azure Virtual Network
description: Meer informatie over Azure Virtual Network-concepten en -functies, met inbegrip van adresruimte, subnetten, regio's en abonnementen.
services: virtual-network
documentationcenter: na
author: KumudD
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 849127ed0846928a77795ac0a1ea3f7a091468b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635945"
---
# <a name="what-is-azure-virtual-network"></a>Wat is Azure Virtual Network?

Azure Virtual Network (VNet) is de basisbouwsteen voor uw privénetwerk in Azure. Via VNet kunnen veel soorten Azure-resources, zoals virtuele Azure-machines, veilig communiceren met elkaar, internet en on-premises netwerken. VNet is vergelijkbaar met een traditioneel netwerk dat u in uw eigen datacentrum zou uitvoeren, maar biedt daarnaast de voordelen van de infrastructuur van Azure, zoals schaal, beschikbaarheid en isolatie.

## <a name="why-use-an-azure-virtual-network"></a>Waarom een virtueel Azure-netwerk gebruiken?
Via een virtueel Azure-netwerk kunnen Azure-resources veilig communiceren met elkaar, internet, en on-premises netwerken. De belangrijkste scenario's die u kunt uitvoeren met een virtueel netwerk, zijn: communicatie van Azure-resources met het Internet, communicatie tussen Azure-resources, communicatie met on-premises resources, het filteren van netwerk verkeer, route ring van netwerk verkeer en integratie met Azure-Services.

### <a name="communicate-with-the-internet"></a>Communiceren met internet

Alle resources in een VNet kunnen standaard uitgaand communiceren met internet. U kunt binnenkomend communiceren met een resource door er een openbaar IP-adres of een openbare Load Balancer aan toe te wijzen. U kunt ook een openbaar IP-adres of een openbare Load Balancer gebruiken voor het beheren van uw uitgaande verbindingen.  Zie [Uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md), [Openbare IP-adressen](virtual-network-public-ip-address.md) en [Load Balancer](../load-balancer/load-balancer-overview.md) voor meer informatie over uitgaande verbindingen in Azure.

>[!NOTE]
>Wanneer u alleen een interne [Standard Load Balancer](../load-balancer/load-balancer-overview.md) gebruikt, is uitgaande connectiviteit niet beschikbaar totdat u definieert hoe u wilt dat [uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md) werken met een op exemplaarniveau openbaar IP-adres of een openbare Load Balancer.

### <a name="communicate-between-azure-resources"></a>Communicatie tussen Azure-resources

Azure-resources communiceren veilig met elkaar op een van de volgende manieren:

- **Via een virtueel netwerk**: U kunt virtuele machines en diverse andere soorten Azure-resources implementeren op een virtueel netwerk, zoals Azure App Service-omgevingen, de Azure Kubernetes Service (AKS) en Azure Virtual Machine Scale Sets. Zie [Integratie van virtuele netwerkservices](virtual-network-for-azure-services.md) voor een volledige lijst met Azure-resources die u in een virtueel netwerk kunt implementeren.
- **Via een service-eindpunt voor een virtueel netwerk**: Breid de openbare adresruimte van uw virtuele netwerk en de identiteit van uw virtuele netwerk uit naar Azure-serviceresources, zoals Azure Storage-accounts en Azure SQL Database, via een directe verbinding. Met service-eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar een virtueel netwerk. Zie [Overzicht van service-eindpunten voor virtuele netwerken](virtual-network-service-endpoints-overview.md) voor meer informatie.
- **Via VNet-peering**: U kunt virtuele netwerken met elkaar verbinden, zodat resources in beide virtuele netwerken met elkaar kunnen communiceren, met behulp van peering voor virtuele netwerken. De virtuele netwerken die u met elkaar verbindt, kunnen zich in dezelfde of verschillende Azure-regio's bevinden. Zie [Peering van virtuele netwerken](virtual-network-peering-overview.md) voor meer informatie.

### <a name="communicate-with-on-premises-resources"></a>Communiceren met on-premises resources

U kunt uw on-premises computers en netwerken verbinden met een virtueel netwerk via een combinatie van de volgende opties:

- **Punt-naar-site virtueel particulier netwerk (VPN):** Wordt tot stand gebracht tussen een virtueel netwerk en een enkele computer in uw netwerk. Elke computer die verbinding wil met een virtueel netwerk moet de verbinding hiervoor configureren. Dit verbindingstype is handig als u net aan de slag gaat met Azure of voor ontwikkelaars, omdat hiervoor weinig of geen wijzigingen in uw bestaande netwerk nodig zijn. De communicatie tussen uw computer en een virtueel netwerk wordt verzonden via een gecodeerde tunnel via internet. Zie [Point-to-site VPN](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#) voor meer informatie.
- **Site-to-site VPN:** Wordt tot stand gebracht tussen uw on-premises VPN-apparaat en een Azure VPN Gateway die is geïmplementeerd in een virtueel netwerk. Met dit verbindingstype krijgen alle on-premises resource die u toestemming geeft toegang tot een virtueel netwerk. De communicatie tussen uw on-premises VPN-apparaat en een Azure VPN Gateway wordt verzonden via een gecodeerde tunnel via internet. Zie [Site-to-site VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) voor meer informatie.
- **Azure ExpressRoute:** Wordt tot stand gebracht tussen uw netwerk en Azure, via een ExpressRoute-partner. Deze verbinding is een privéverbinding. Verkeer gaat niet via internet. Zie [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie.

### <a name="filter-network-traffic"></a>Netwerkverkeer filteren

U kunt netwerkverkeer filteren tussen subnetten met behulp van een of beide van de volgende opties:

- **Netwerkbeveiligingsgroepen:** Netwerkbeveiligingsgroepen en toepassingsbeveiligingsgroepen kunnen meerdere binnenkomende en uitgaande beveiligingsregels bevatten waarmee u verkeer van en naar resources kunt filteren op bron- en doel-IP-adres, poort en protocol. Zie [Netwerkbeveiligingsgroepen](./network-security-groups-overview.md#network-security-groups) of [Toepassingsbeveiligingsgroepen](./network-security-groups-overview.md#application-security-groups) voor meer informatie.
- **Virtuele netwerkapparaten:** Een virtueel netwerkapparaat is een virtuele machine die een netwerkfunctie uitvoert, zoals een firewall, WAN-optimalisatie of een andere netwerkfunctie. Zie [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) voor meer informatie over het bekijken van een lijst met beschikbare virtuele netwerkapparaten die u in een virtueel netwerk kunt implementeren.

### <a name="route-network-traffic"></a>Netwerkverkeer routeren

Azure routeert verkeer standaard tussen subnetten, verbonden virtuele netwerken, on-premises netwerken en internet. U kunt een of beide van de volgende opties implementeren om de standaardroutes die Azure maakt te onderdrukken:

- **Routetabellen:** U kunt aangepaste routetabellen maken met routes die bepalen waar verkeer naartoe wordt doorgestuurd voor elk subnet. Meer informatie over [routetabellen](virtual-networks-udr-overview.md#user-defined).
- **BGP-routes (Border Gateway Protocol):** Als u uw virtuele netwerk verbindt met uw on-premises netwerk via een Azure VPN Gateway- of ExpressRoute-verbinding, kunt u uw lokale BGP-routes doorgegeven aan uw virtuele netwerken. Lees meer over het gebruik van BGP met [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integratie van virtuele netwerken voor Azure-services

Door Azure-services te integreren in een virtueel Azure-netwerk, kunt u persoonlijke toegang tot de service krijgen via virtuele machines of rekenresources in het virtuele netwerk.
U kunt Azure-services in uw virtuele netwerk integreren met de volgende opties:
- Implementatie van [toegewezen exemplaren van de service](virtual-network-for-azure-services.md) in een virtueel netwerk. De services kunnen vervolgens worden geopend in het virtuele netwerk en vanuit on-premises netwerken.
- Gebruik [Private Link](../private-link/private-link-overview.md) om toegang te krijgen tot een specifiek exemplaar van de service vanuit uw virtuele netwerk en vanuit on-premises netwerken.
- U kunt de service ook openen met behulp van openbare eindpunten door een virtueel netwerk uit te breiden naar de service via [service-eindpunten](virtual-network-service-endpoints-overview.md). Met service-eindpunten kunnen serviceresources worden beveiligd naar het virtuele netwerk.
 

## <a name="azure-vnet-limits"></a>Limieten voor Azure VNet

Er zijn bepaalde limieten voor het aantal Azure-resources dat u kunt implementeren. De meeste Azure-netwerklimieten zijn de maximumwaarden. U kunt echter [bepaalde netwerklimieten verhogen](../azure-portal/supportability/networking-quota-requests.md) zoals opgegeven op de pagina [VNet-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Prijzen

Er worden geen kosten in rekening gebracht voor het gebruik van Azure VNet. Het is gratis. Er zijn standaardkosten van toepassing op resources, zoals virtuele machines (VM's) en andere producten. Zie [VNet-prijzen](https://azure.microsoft.com/pricing/details/virtual-network/) en de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) van Azure voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
 - Meer informatie over [concepten en best practices voor Azure Virtual Network](concepts-and-best-practices.md).
 - Als u aan de slag wilt gaan met een virtueel netwerk, maakt u een virtueel netwerk, implementeert u een paar VM's en laat u de virtuele machines met elkaar communiceren. Zie de snelstart [Een virtueel netwerk maken](quick-create-portal.md) voor meer informatie.
