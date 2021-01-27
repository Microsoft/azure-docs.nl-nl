---
title: Site-naar-site-VPN-verbindingen via ExpressRoute persoonlijke peering
description: Dit artikel helpt u site-naar-site VPN in te scha kelen via ExpressRoute private peering om verkeer te versleutelen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880522"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Een site-naar-site-VPN-verbinding configureren via ExpressRoute-persoonlijke peering

U kunt een site-naar-site-VPN configureren voor een virtuele netwerk gateway via een ExpressRoute-particuliere peering met behulp van een RFC 1918 IP-adres. Deze configuratie biedt de volgende voor delen:

* Verkeer via persoonlijke peering is versleuteld.

* Punt-naar-site-gebruikers die verbinding maken met een virtuele netwerk gateway kunnen ExpressRoute (via de site-naar-site-tunnel) gebruiken om toegang te krijgen tot on-premises resources.

>[!NOTE]
>Deze functie wordt alleen ondersteund op zone-redundante gateways. Bijvoorbeeld, VpnGw1AZ, VpnGw2AZ, etc.
>

Controleer of u aan de volgende vereisten voldoet om deze configuratie te volt ooien:

* U hebt een functionerend ExpressRoute-circuit dat is gekoppeld aan het VNet waar de VPN-gateway is (of wordt) gemaakt.

* U kunt via het ExpressRoute-circuit bronnen bereiken via RFC1918 (privé) in het VNet.

## <a name="routing"></a><a name="routing"></a>Routering

In **afbeelding 1** ziet u een voor beeld van VPN-verbinding via ExpressRoute private-peering. In dit voor beeld ziet u een netwerk in het on-premises netwerk dat is verbonden met de Azure hub VPN-gateway via ExpressRoute private peering. Een belang rijk aspect van deze configuratie is de route ring tussen de on-premises netwerken en Azure via de ExpressRoute-en VPN-paden.

Afbeelding 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Afbeelding 1":::

Het tot stand brengen van de verbinding is eenvoudig:

1. Maak een ExpressRoute-verbinding met een ExpressRoute-circuit en privé-peering.

1. Stel de VPN-verbinding in aan de hand van de stappen in dit artikel.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Verkeer van on-premises netwerken naar Azure

Voor verkeer van on-premises netwerken naar Azure, worden de Azure-voor voegsels geadverteerd via de ExpressRoute persoonlijke peering BGP en de VPN BGP. Het resultaat is twee netwerk routes (paden) naar Azure vanuit de on-premises netwerken:

• Eén netwerk route via het met IPsec beveiligde pad.

• Eén netwerk route direct via ExpressRoute zonder IPsec-beveiliging.

Als u versleuteling wilt Toep assen op de communicatie, moet u ervoor zorgen dat voor het netwerk met VPN-verbinding in **afbeelding 1**, Azure-routes via de on-premises VPN-gateway worden voor keur boven het directe ExpressRoute-pad.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Verkeer van Azure naar on-premises netwerken

Dezelfde vereiste is van toepassing op het verkeer van Azure naar on-premises netwerken. U hebt twee opties om ervoor te zorgen dat het IPsec-pad de voor keur heeft boven het pad voor directe ExpressRoute (zonder IPsec):

• **Adverteer meer specifieke voor voegsels voor de VPN BGP-sessie voor het VPN-verbonden netwerk**. U kunt een groter bereik aankondigen dat het VPN-verbonden netwerk omvat via ExpressRoute private peering, en vervolgens meer specifieke bereiken in de VPN BGP-sessie. Bijvoorbeeld, Adverteer 10.0.0.0/16 via ExpressRoute en 10.0.1.0/24 via VPN.

• Adverteer verouderde **voor Voegsels adverteren voor VPN en ExpressRoute**. Als de netwerkbereiken met VPN-verbindingen worden ontkoppeld van andere ExpressRoute verbonden netwerken, kunt u de voor voegsels in respectievelijk de BGP-sessies VPN en ExpressRoute aankondigen. Bijvoorbeeld, Adverteer 10.0.0.0/24 over ExpressRoute en 10.0.1.0/24 via VPN.

In beide voor beelden verzendt Azure verkeer naar 10.0.1.0/24 via de VPN-verbinding in plaats van rechtstreeks via ExpressRoute zonder VPN-beveiliging.

>[!Warning]
>Als u dezelfde voor voegsels aankondigt voor zowel ExpressRoute-als VPN-verbindingen, gebruikt >Azure rechtstreeks zonder VPN-beveiliging het pad ExpressRoute.
>

## <a name="portal-steps"></a><a name="portal"></a>Portal stappen

1. Configureer een site-naar-site-verbinding. Zie het artikel [site-naar-site-configuratie](./tutorial-site-to-site-portal.md) voor een overzicht van de stappen. Zorg ervoor dat u een zone-redundante gateway-SKU kiest voor de gateway. 

   Voor een zone-redundante Sku's is AZ aan het einde van de SKU. Bijvoorbeeld **VpnGw1AZ**. Zone-redundante gateways zijn alleen beschikbaar in regio's waar de beschikbaarheids zone service beschikbaar is. Zie [regio's die beschikbaarheids zones ondersteunen](../availability-zones/az-region.md)voor meer informatie over de regio's waarin we beschikbaarheids zones ondersteunen.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Privé-IP-adressen van Gateway":::
1. Privé-IP-adressen inschakelen op de gateway. Selecteer **configuratie** en stel de **persoonlijke Ip's** van de gateway in op **ingeschakeld**. Selecteer **Opslaan** om uw wijzigingen op te slaan.
1. Selecteer op de pagina **overzicht** de optie **meer weer** geven om het privé-IP-adres te bekijken. Noteer deze informatie om later in de configuratie stappen te gebruiken.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Overzichtspagina" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Selecteer **configuratie** om het **privé-Azure-IP-adres gebruiken** in te scha kelen voor de verbinding. Stel het **privé-IP-adres van Azure gebruiken** in op **ingeschakeld** en selecteer vervolgens **Opslaan**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Privé-IP-adressen van Gateway-ingeschakeld":::
1. Vanuit uw firewall pingt u het privé-IP-adres dat u in stap 3 hebt genoteerd. Het privé-IP-adres moet bereikbaar zijn via de ExpressRoute-persoonlijke peering.
1. Gebruik dit persoonlijke IP-adres als het externe IP-adres van uw on-premises firewall om de site-naar-site-tunnel te maken via de ExpressRoute-persoonlijke peering.

## <a name="powershell-steps"></a><a name="powershell"></a>Power shell-stappen

1. Configureer een site-naar-site-verbinding. Zie het artikel [een site-naar-site-VPN configureren voor een](./tutorial-site-to-site-portal.md) overzicht van de stappen. Zorg ervoor dat u een zone-redundante gateway-SKU kiest voor de gateway. Voor een zone-redundante Sku's is AZ aan het einde van de SKU. Bijvoorbeeld VpnGw1AZ.
1. Stel de vlag in voor het gebruik van het privé-IP-adres op de gateway met behulp van de volgende Power shell-opdrachten:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   U ziet een openbaar en een privé-IP-adres. Noteer het IP-adres onder het gedeelte ' TunnelIpAddresses ' van de uitvoer. U gebruikt deze informatie in een latere stap.
1. Stel de verbinding voor het gebruik van het privé IP-adres in met behulp van de volgende Power shell-opdracht:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Vanuit uw firewall pingt u het privé-IP-adres dat u in stap 2 hebt geschreven. Deze moet bereikbaar zijn via de ExpressRoute-persoonlijke peering.
1. Gebruik dit persoonlijke IP-adres als het externe IP-adres van uw on-premises firewall om de site-naar-site-tunnel te maken via de ExpressRoute-persoonlijke peering.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is VPN gateway?](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN gateway.