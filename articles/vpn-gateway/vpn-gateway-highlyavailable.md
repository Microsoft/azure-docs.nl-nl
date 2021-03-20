---
title: 'Azure VPN Gateway: overzicht-configuraties met Maxi maal beschik bare gateways'
description: Dit artikel bevat een overzicht van maximaal beschikbare configuratieopties met Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 48756b43e64576a5dd38467bb1dd97e91c168a06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91360851"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Maximaal beschikbare cross-premises en VNet-naar-VNet-connectiviteit
Dit artikel bevat een overzicht van maximaal beschikbare configuratieopties voor uw cross-premises en VNet-naar-VNet-connectiviteit met Azure VPN-gateways.

## <a name="about-azure-vpn-gateway-redundancy"></a><a name = "activestandby"></a>Over Azure VPN-gatewayredundantie
Elke Azure VPN-gateway bestaat uit twee instanties in een actieve stand-byconfiguratie. Bij gepland onderhoud of niet-geplande onderbrekingen die met het actieve exemplaar plaatsvinden, neemt de stand-byinstantie de activiteiten automatisch over (failover) en worden de S2S VPN- of VNet-naar-VNet-verbindingen hervat. De omschakeling veroorzaakt een korte onderbreking. Bij gepland onderhoud moet de connectiviteit binnen 10 tot 15 seconden worden hersteld. Bij niet-geplande problemen duurt het herstellen van de verbinding langer, in het ergste geval tot 1,5 minuut. Bij P2S VPN-clientverbindingen met de gateway worden de P2S-verbindingen verbroken en moeten de gebruikers opnieuw verbinding maken vanaf de clientcomputers.

![Diagram toont een on-premises site met persoonlijke I P-subnetten en on-premises V P N die zijn verbonden met een actieve Azure V P N-gateway om verbinding te maken met subnetten die worden gehost in azure, met een stand-by-gateway beschikbaar.](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Maximaal beschikbare cross-premises connectiviteit
Er zijn een aantal opties beschikbaar om betere beschikbaarheid te bieden voor uw cross-premises verbindingen:

* Meerdere on-premises VPN-apparaten
* Actief/actief Azure VPN-gateway
* Combinatie van beide

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>Meerdere on-premises VPN-apparaten
U kunt meerdere VPN-apparaten van uw on-premises netwerk gebruiken om verbinding te maken met uw Azure VPN-gateway, zoals in het volgende diagram wordt weergegeven:

![Meerdere on-premises VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Deze configuratie biedt meerdere actieve tunnels van dezelfde Azure VPN-gateway op on-premises apparaten op dezelfde locatie. Er zijn enkele vereisten en beperkingen:

1. U moet meerdere S2S VPN-verbindingen maken van uw VPN-apparaten naar Azure. Wanneer u meerdere VPN-apparaten van hetzelfde on-premises netwerk verbindt met Azure, moet u één lokale netwerk gateway maken voor elk VPN-apparaat en één verbinding van uw Azure VPN-gateway naar elke lokale netwerk gateway.
2. De lokale netwerkgateways die overeenkomen met uw VPN-apparaten moeten unieke openbare IP-adressen hebben in de eigenschap 'GatewayIpAddress'.
3. BGP is vereist voor deze configuratie. Elke lokale netwerkgateway voor een VPN-apparaat moet een uniek IP-adres voor BGP-peering hebben in de eigenschap 'BgpPeerIpAddress'.
4. Het veld van de eigenschap AddressPrefix in elke lokale netwerkgateway mag niet overlappen. U moet 'BgpPeerIpAddress' in /32 CIDR-indeling opgeven in het veld AddressPrefix, bijvoorbeeld 10.200.200.254/32.
5. Gebruik BGP om dezelfde voorvoegsels van dezelfde on-premises netwerkvoorvoegsels te adverteren naar uw Azure VPN-gateway. Het verkeer wordt tegelijkertijd doorgestuurd via deze tunnels.
6. U moet een gelijke-kosten multi-path routing (ECMP) gebruiken.
7. Elke verbinding wordt geteld tegen het maximale aantal tunnels voor uw Azure VPN-gateway, 10 voor de Basic en Standaard SKU's en 30 voor de HighPerformance SKU. 

In deze configuratie bevindt de Azure VPN-gateway zich nog steeds in de actieve stand-bymodus, dus hetzelfde failovergedrag en de korte onderbreking die [hierboven](#activestandby) worden beschreven, zullen optreden. Deze installatie beschermt echter tegen storingen of onderbrekingen op uw on-premises netwerk en VPN-apparaten.

### <a name="active-active-azure-vpn-gateway"></a>Actief/actief Azure VPN-gateway
U kunt nu een Azure VPN-gateway maken in een actief/actief-configuratie, waarbij beide exemplaren van de gateway-VM’s S2S VPN-tunnels tot stand brengen op uw on-premises VPN-apparaat, zoals wordt weergegeven in het volgende diagram:

![Diagram toont een on-premises site met persoonlijke I P-subnetten en on-premises V P N die zijn verbonden met twee actieve Azure V P N gateway om verbinding te maken met subnetten die worden gehost in Azure.](./media/vpn-gateway-highlyavailable/active-active.png)

In deze configuratie heeft elk Azure-gatewayexemplaar een uniek openbaar IP-adres en elk exemplaar brengt een IPsec/IKE S2S VPN-tunnel tot stand met uw on-premises VPN-apparaat dat is opgegeven in uw lokale netwerkgateway en verbinding. Houd er rekening mee dat beide VPN-tunnels deel uitmaken van dezelfde verbinding. U moet alsnog uw on-premises VPN-apparaat zo configureren dat er twee S2S VPN-tunnels naar deze twee openbare IP-adressen voor de Azure VPN-gateway tot stand worden gebracht of worden geaccepteerd.

Omdat de Azure-gatewayexemplaren zich in de configuratie actief/actief bevinden, wordt het verkeer van uw virtuele netwerk van Azure naar uw on-premises netwerk gerouteerd. Dit gebeurt via beide tunnels tegelijkertijd, ook als uw lokale VPN-apparaat een tunnel prioriteit geeft. Houd er rekening mee dat de dezelfde TCP- of UDP-stroom altijd via dezelfde tunnel of hetzelfde pad loopt, tenzij er onderhoud wordt uitgevoerd op een van de exemplaren.

Wanneer er gepland onderhoud wordt uitgevoerd op een gatewayexemplaar of er zich een niet-gepland voorval voordoet, wordt de verbinding met de IPsec-tunnel vanuit het betreffende exemplaar met uw on-premises VPN-apparaat verbroken. De bijbehorende routes op uw VPN-apparaten moeten automatisch worden verwijderd of ingetrokken zodat het verkeer wordt omgeleid naar de andere actieve IPsec-tunnel. Vanuit Azure gebeurt de overschakeling van het betreffende exemplaar naar het actieve exemplaar automatisch.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Dubbele redundantie: actief/actief VPN-gateways voor Azure en on-premises netwerken
De meest betrouwbare optie is om de actief/actief-gateways op uw netwerk en in Azure te combineren, zoals wordt weergegeven in het volgende diagram.

![Dubbele redundantie](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Hier maakt u de Azure VPN-gateway in een actief/actief-configuratie en stelt u deze in. Daarnaast maakt u twee lokale netwerkgateways en twee verbindingen voor uw twee on-premises VPN-apparaten zoals hierboven beschreven. Het resultaat is een volledig connectiviteitsraster van vier IPsec-tunnels tussen uw virtuele Azure-netwerk en uw on-premises netwerk.

Alle gateways en tunnels zijn actief in Azure, zodat het verkeer wordt verdeeld tussen de vier tunnels tegelijkertijd. Alle TCP- of UDP-stromen volgen echter weer de dezelfde tunnel of hetzelfde pad vanuit Azure. Ondanks dat de doorvoer via de IPsec-tunnels mogelijk iets verbetert doordat het verkeer wordt verspreid, is het voornaamste doel van deze configuratie maximale beschikbaarheid. Door de statistische aard van de verspreiding is het daarnaast moeilijk vast te stellen in hoeverre de verkeersvoorwaarden van verschillende toepassingen de geaggregeerde doorvoer zullen beïnvloeden.

Voor deze topologie zijn twee lokale netwerkgateways en twee verbindingen vereist om het paar on-premises VPN-apparaten te ondersteunen. Daarnaast is BGP vereist zodat de twee verbindingen op hetzelfde on-premises lokale netwerk worden toegestaan. Deze vereisten zijn hetzelfde als die [hierboven](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Maximaal beschikbare VNet-naar-VNet-connectiviteit via VPN Azure-gateways
Dezelfde actief/actief-configuratie kan ook worden toegepast op VNet-naar-VNet-verbindingen van Azure. U kunt actief/actief VPN-gateways voor beide virtuele netwerken maken en ze met elkaar verbinden voor hetzelfde connectiviteitsraster met vier tunnels tussen de twee VNets, zoals wordt weergegeven in het diagram hieronder:

![Diagram toont twee Azure-regio's die als host fungeren voor persoonlijke I P-subnetten en twee Azure V P N gateways waarmee de twee virtuele sites verbinding maken.](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Hiermee zorgt u ervoor dat er altijd een paar tunnels tussen de twee virtuele netwerken actief is voor gepland onderhoud, waardoor de beschikbaarheid nog beter wordt. Hoewel dezelfde topologie voor cross-premises connectiviteit twee verbindingen vereist, is er voor de VNet-naar-VNet-topologie die hierboven wordt weergegeven slechts één verbinding per gateway nodig. BGP is bovendien optioneel, tenzij doorvoerroutering via de VNet-naar-VNet-verbinding vereist is.

## <a name="next-steps"></a>Volgende stappen
Zie [Actief/actief VPN-gateways configureren voor cross-premises en VNet-naar-VNet-verbindingen](vpn-gateway-activeactive-rm-powershell.md) voor de stappen om actief/actief on-premises en VNet-naar-VNet-verbindingen te configureren.

