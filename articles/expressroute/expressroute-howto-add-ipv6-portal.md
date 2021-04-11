---
title: 'Azure ExpressRoute: IPv6-ondersteuning toevoegen met behulp van de Azure Portal'
description: Meer informatie over het toevoegen van IPv6-ondersteuning om verbinding te maken met Azure-implementaties met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561658"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>IPv6-ondersteuning voor privé-peering toevoegen met behulp van de Azure Portal (preview-versie)

In dit artikel wordt beschreven hoe u IPv6-ondersteuning kunt toevoegen om via ExpressRoute verbinding te maken met uw resources in azure met behulp van de Azure Portal. 

> [!Note]
> Deze functie is momenteel beschikbaar voor preview in [Azure-regio's met Beschikbaarheidszones](../availability-zones/az-region.md#azure-regions-with-availability-zones). Uw ExpressRoute-circuit kan daarom worden gemaakt met behulp van een wille keurige locatie, maar de IPv6-implementaties waarmee deze verbinding maakt, moeten zich in een regio met Beschikbaarheidszones bevinden.

## <a name="register-for-public-preview"></a>Registreren voor open bare preview
Voordat u IPv6-ondersteuning toevoegt, moet u uw abonnement eerst registreren. Als u wilt inschrijven, voert u de volgende opdrachten uit via Azure PowerShell:

1.  Meld u aan bij Azure en selecteer het abonnement. Voer deze opdrachten uit voor het abonnement met uw ExpressRoute-circuit en het abonnement met uw Azure-implementaties (als deze niet overeenkomen).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registreer uw abonnement voor open bare Preview met de volgende opdracht:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Uw aanvraag wordt binnen 2-3 werk dagen goedgekeurd door het ExpressRoute-team.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de [Azure Portal](https://portal.azure.com)en meld u vervolgens aan met uw Azure-account.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Persoonlijke IPv6-peering toevoegen aan uw ExpressRoute-circuit

1. [Maak een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) of navigeer naar het bestaande circuit dat u wilt wijzigen.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Scherm opname van de lijst met ExpressRoute-circuits.":::

1. Selecteer de configuratie van de **persoonlijke Azure** -peering.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Scherm opname van de pagina overzicht van ExpressRoute.":::

1. Voeg een persoonlijke IPv6-peering toe aan uw bestaande IPv4-persoonlijke peering-configuratie door beide te selecteren voor **subnetten** of alleen IPv6-persoonlijke peering in te scha kelen op het nieuwe circuit door IPv6 te selecteren. Geef een combi natie van/126 IPv6-subnetten op die u voor uw primaire koppeling en secundaire koppelingen hebt. Vanuit elk van deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van de tweede bruikbare IP voor de router. **Sla** uw peering-configuratie op nadat u alle para meters hebt opgegeven.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Scherm afbeelding van het toevoegen van IPv6 op een pagina met persoonlijke peering.":::

1. Nadat de configuratie is geaccepteerd, ziet u iets dat lijkt op het volgende voor beeld.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Scherm afbeelding van IPv6 geconfigureerd voor privé-peering.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Uw verbinding met een bestaand virtueel netwerk bijwerken

Voer de volgende stappen uit als u een bestaande omgeving van Azure-resources in een regio hebt met Beschikbaarheidszones waarvoor u uw IPv6-persoonlijke peering wilt gebruiken.

1. Navigeer naar het virtuele netwerk waarmee uw ExpressRoute-circuit is verbonden.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Scherm opname van de lijst met virtuele netwerken.":::

1. Navigeer naar het tabblad **adres ruimte** en voeg een IPv6-adres ruimte toe aan het virtuele netwerk. **Sla** uw adres ruimte op.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Scherm opname van het toevoegen van een IPv6-adres ruimte aan een virtueel netwerk.":::

1. Navigeer naar het tabblad **subnetten** en selecteer de **GatewaySubnet**. Controleer de **IPv6-adres ruimte toevoegen** en geef een IPv6-adres ruimte op voor uw subnet. Het IPv6-subnet van de gateway moet/64 of groter zijn. **Sla** uw configuratie op nadat u alle para meters hebt opgegeven.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Scherm opname van het toevoegen van een IPv6-adres ruimte aan het subnet.":::

1. Als u een bestaande zone-redundante gateway hebt, navigeert u naar uw ExpressRoute-gateway en vernieuwt u de resource om IPv6-connectiviteit in te scha kelen. Als dat niet het geval is, [maakt u de gateway van het virtuele netwerk](expressroute-howto-add-gateway-portal-resource-manager.md) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Als u van plan bent FastPath te gebruiken, gebruikt u ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Scherm opname van het vernieuwen van de gateway.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Een verbinding maken met een nieuw virtueel netwerk

Volg de onderstaande stappen als u van plan bent om verbinding te maken met een nieuwe set Azure-resources in een regio met Beschikbaarheidszones met behulp van uw IPv6-persoonlijke peering.

1. Maak een virtueel netwerk met dubbele stack met IPv4-en IPv6-adres ruimte. Zie [een virtueel netwerk maken](../virtual-network/quick-create-portal.md#create-a-virtual-network)voor meer informatie.

1. [Maak het subnet met dubbele stack-gateways](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Maak de gateway van het virtuele netwerk](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Als u van plan bent FastPath te gebruiken, gebruikt u ErGw3AZ (Houd er rekening mee dat deze optie alleen beschikbaar is voor circuits met behulp van ExpressRoute direct).

1. [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Beperkingen
Hoewel IPv6-ondersteuning beschikbaar is voor verbindingen met implementaties in regio's met Beschikbaarheidszones, biedt het geen ondersteuning voor de volgende use-cases:

* Verbindingen met implementaties in azure via een niet-AZ ExpressRoute gateway SKU
* Verbindingen met implementaties in niet-AZ regio's
* Global Reach verbindingen tussen ExpressRoute-circuits
* Gebruik van ExpressRoute met Virtual WAN
* FastPath met niet-ExpressRoute directe circuits
* FastPath met circuits in de volgende peering-locaties: Dubai
* Samen werking met VPN Gateway

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor het oplossen van problemen met ExpressRoute:

* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)