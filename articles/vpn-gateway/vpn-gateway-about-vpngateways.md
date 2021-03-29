---
title: Over Azure VPN Gateway
description: Meer informatie over wat een VPN Gateway is en hoe u een VPN Gateway kunt gebruiken om verbinding te maken met de virtuele VPN-netwerken Site-naar-site (IPsec IKE), VNet-naar-VNet en Punt-naar-site.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom:
- contperf-fy21q1
- e2e-hybrid
ms.openlocfilehash: f143ed3a8e739f4a79367694e933b28d717230dd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640863"
---
# <a name="what-is-vpn-gateway"></a>Wat is VPN Gateway?

Een VPN-gateway is een speciaal soort virtueel-netwerkgateway die wordt gebruikt om versleuteld verkeer te verzenden tussen een virtueel Azure-netwerk en een on-premises locatie via het openbare internet. U kunt een VPN-gateway ook gebruiken om versleuteld verkeer tussen virtuele Azure-netwerken te verzenden via het Microsoft-netwerk. Elk virtueel netwerk kan slechts één VPN-gateway hebben. U kunt echter meerdere verbindingen met dezelfde VPN-gateway maken. Wanneer u meerdere verbindingen naar dezelfde VPN-gateway hebt gemaakt, delen alle VPN-tunnels de bandbreedte die voor de gateway beschikbaar is.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Wat is een virtuele netwerkgateway?

Een virtuele netwerkgateway bestaat uit twee of meer virtuele machines die zijn geïmplementeerd voor een specifiek subnet dat u maakt, *gatewaysubnet* genaamd. VM's voor virtuele netwerkgateways bevatten routeringstabellen en voeren specifieke gatewayservices uit. Deze VM's worden gemaakt wanneer u de virtuele netwerkgateway maakt. U kunt de VM's die deel uitmaken van de gateway van de virtuele netwerkgateway niet rechtstreeks configureren.

Wanneer u een virtuele netwerkgateway configureert, configureert u een instelling die het type gateway specificeert. Het type gateway bepaalt hoe de virtuele netwerkgateway wordt gebruikt en welke acties worden uitgevoerd door de gateway. Het gatewaytype 'Vpn' geeft aan dat het type van de gemaakte virtuele netwerkgateway, een 'VPN-gateway' is. Dit onderscheidt het van een ExpressRoute-gateway, die gebruikmaakt van een ander type gateway. Een virtueel netwerk kan twee virtuele netwerkgateways hebben: één VPN-gateway en één ExpressRoute-gateway. Zie [Soorten gateways](vpn-gateway-about-vpn-gateway-settings.md#gwtype) voor meer informatie.

Het maken van een gateway voor een virtueel netwerk kan tot 45 minuten duren. Wanneer u een gateway voor een virtueel netwerk maakt, worden gateway-VM's in het gatewaysubnet geïmplementeerd en geconfigureerd met de instellingen die u opgeeft. Nadat u een VPN-gateway hebt gemaakt, kunt u een IPsec/IKE-VPN-tunnelverbinding maken tussen die VPN-gateway en een andere VPN-gateway (VNet-naar-VNet) of een cross-premises IPsec/IKE-VPN-tunnelverbinding maken tussen de VPN-gateway en een on-premises VPN-apparaat (site-naar-site). U kunt ook een punt-naar-site-VPN-verbinding maken (VPN via OpenVPN, IKEv2 of SSTP), waarmee u verbinding maakt met het virtuele netwerk vanaf een externe locatie, zoals vanuit een conferentie of thuis.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Een VPN-gateway configureren

Een VPN-gatewayverbinding is afhankelijk van meerdere resources die zijn geconfigureerd met specifieke instellingen. De meeste resources kunnen afzonderlijk worden geconfigureerd, hoewel sommige resources in een bepaalde volgorde moeten worden geconfigureerd.

### <a name="design"></a><a name="diagrams"></a>Ontwerp

Het is belangrijk te weten dat er verschillende configuraties beschikbaar zijn voor VPN-gatewayverbindingen. U moet bepalen welke configuratie het beste aansluit bij uw behoeften. Punt-naar-site-, site-naar-site-en naast elkaar bestaande ExpressRoute/site-naar-site-verbindingen hebben bijvoorbeeld allemaal verschillende instructies en configuratievereisten. Zie [Ontwerp](design.md) voor meer informatie over het ontwerpen en weergeven van verbindingstopologiediagrammen.

### <a name="planning-table"></a><a name="planningtable"></a>Tabel plannen

De volgende tabel kan u helpen bij het kiezen van de beste connectiviteitsoptie voor uw oplossing.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Instellingen

De instellingen die u voor elke resource hebt gekozen, zijn essentieel om een geslaagde verbinding te maken. Zie voor meer informatie over afzonderlijke resources en de instellingen voor VPN Gateway [Over VPN Gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md). Dit artikel bevat informatie over de gatewaytypen, gateway-SKU's, VPN-typen, typen verbindingen, gatewaysubnetten, lokale netwerkgateways en verschillende andere resource-instellingen die u misschien wilt gebruiken.

### <a name="deployment-tools"></a><a name="tools"></a>Implementatiehulpmiddelen

U kunt beginnen met het maken en configureren van resources met een configuratiehulpprogramma, zoals Azure Portal. U kunt later besluiten over te schakelen naar een ander hulpprogramma, zoals PowerShell, om aanvullende resources te configureren, of om desgewenst bestaande resources te wijzigen. Op dit moment is het niet mogelijk om elke resource en resource-instelling in Azure Portal te configureren. De instructies in de artikelen voor elke verbindingstopologie geven aan of een specifiek confihuratiehulpprogramma nodig is.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's

Wanneer u een virtuele netwerkgateway maakt, geeft u de gewenste gateway-SKU op. Selecteer de SKU die aan uw vereisten voldoet op basis van de typen werkbelasting, doorvoer, functies en SLA's.

* Zie het artikel [Instellingen voor VPN-gateways - gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie over gateway-SKU's, met inbegrip van ondersteunde functies, productie- en ontwikkelingstests en configuratiestappen.
* Zie [Werken met verouderde SKU's](vpn-gateway-about-skus-legacy.md) voor informatie over verouderde SKU's.

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU's per tunnel, verbinding en doorvoer

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Beschikbaarheidszones

VPN-gateways kunnen in Azure-beschikbaarheidszones worden geïmplementeerd. Dit zorgt voor tolerantie, schaalbaarheid en hogere beschikbaarheid voor virtuele netwerkgateways. Door gateways in Azure-beschikbaarheidszones te implementeren, worden gateways binnen een regio fysiek en logisch van elkaar gescheiden, terwijl uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen storingen op zoneniveau. Zie [Zone-redundante virtuele netwerkgateways in Azure-beschikbaarheidszones](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Prijzen

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie over gateway-SKU's voor VPN Gateway.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

Zie [Veelgestelde vragen VPN Gateway](vpn-gateway-vpn-faq.md) voor veelgestelde vragen over VPN Gateway.

## <a name="whats-new"></a><a name="new"></a>Wat is er nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor VPN Gateway-functies op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway).

## <a name="next-steps"></a>Volgende stappen

- Zie de [Veelgestelde vragen over VPN Gateway](vpn-gateway-vpn-faq.md) voor meer informatie.
- Zie de [Abonnements- en servicebeperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
