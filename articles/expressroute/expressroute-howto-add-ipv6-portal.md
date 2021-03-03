---
title: 'Azure ExpressRoute: IPv6-ondersteuning toevoegen met behulp van de Azure Portal'
description: Meer informatie over het toevoegen van IPv6-ondersteuning om verbinding te maken met Azure-implementaties met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 67f296c7584fcf25af79f9125137aca07c9906fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746082"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>IPv6-ondersteuning voor privé-peering toevoegen met behulp van de Azure Portal (preview-versie)

In dit artikel wordt beschreven hoe u IPv6-ondersteuning kunt toevoegen om via ExpressRoute verbinding te maken met uw resources in azure met behulp van de Azure Portal. 

> [!Note]
> Deze functie is momenteel beschikbaar voor preview in [Azure-regio's met Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Uw ExpressRoute-circuit kan daarom worden gemaakt met behulp van een wille keurige locatie, maar de IPv6-implementaties waarmee deze verbinding maakt, moeten zich in een regio met Beschikbaarheidszones bevinden.

## <a name="register-for-public-preview"></a>Registreren voor open bare preview
Voordat u IPv6-ondersteuning toevoegt, moet u uw abonnement eerst registreren. Ga als volgt te werk om de registratie uit te voeren via Azure PowerShell:
1.  Meld u aan bij Azure en selecteer het abonnement. U moet dit doen voor het abonnement dat uw ExpressRoute-circuit bevat, evenals het abonnement dat uw Azure-implementaties bevat (als deze verschillend zijn).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registreer uw abonnement voor open bare Preview met de volgende opdracht:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Uw aanvraag wordt binnen 2-3 werk dagen goedgekeurd door het ExpressRoute-team.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de [Azure Portal](https://portal.azure.com)en meld u vervolgens aan met uw Azure-account.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Persoonlijke IPv6-peering toevoegen aan uw ExpressRoute-circuit

1. [Maak een ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) of navigeer naar het bestaande circuit dat u wilt wijzigen.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Navigeer naar het circuit":::

2. Selecteer de configuratie van de **persoonlijke Azure** -peering.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Ga naar de peering":::

3. Voeg een persoonlijke IPv6-peering toe aan uw bestaande IPv4-persoonlijke peering-configuratie door beide te selecteren voor **subnetten** of alleen IPv6-persoonlijke peering in te scha kelen op het nieuwe circuit door IPv6 te selecteren. Geef een combi natie van/126 IPv6-subnetten op die u voor uw primaire koppeling en secundaire koppelingen hebt. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt. **Sla** uw peering-configuratie op nadat u alle para meters hebt opgegeven.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Persoonlijke IPv6-peering toevoegen":::

4. Nadat de configuratie is geaccepteerd, ziet u iets dat lijkt op het volgende voor beeld.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="De persoonlijke IPv6-peering weer geven":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Uw verbinding met een bestaand virtueel netwerk bijwerken

Voer de volgende stappen uit als u een bestaande omgeving van Azure-resources in een regio hebt met Beschikbaarheidszones waarvoor u uw IPv6-persoonlijke peering wilt gebruiken.

1. Navigeer naar het virtuele netwerk waarmee uw ExpressRoute-circuit is verbonden.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Navigeer naar het vnet":::

2. Navigeer naar het tabblad **adres ruimte** en voeg een IPv6-adres ruimte toe aan het virtuele netwerk. **Sla** uw adres ruimte op.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="IPv6-adres ruimte toevoegen":::

3. Navigeer naar het tabblad **subnetten** en selecteer de **GatewaySubnet**. Controleer de **IPv6-adres ruimte toevoegen** en geef een IPv6-adres ruimte op voor uw subnet. Het IPv6-subnet van de gateway moet/64 of groter zijn. **Sla** uw configuratie op nadat u alle para meters hebt opgegeven.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="IPv6-adres ruimte toevoegen aan het subnet":::

4. Als u een bestaande zone-redundante gateway hebt, navigeert u naar uw ExpressRoute-gateway en vernieuwt u de resource om IPv6-connectiviteit in te scha kelen. Als dat niet het geval is, [maakt u de gateway van het virtuele netwerk](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Als u van plan bent FastPath te gebruiken, gebruikt u ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="De gateway vernieuwen":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Een verbinding maken met een nieuw virtueel netwerk

Volg de onderstaande stappen als u van plan bent om verbinding te maken met een nieuwe set Azure-resources in een regio met Beschikbaarheidszones met behulp van uw IPv6-persoonlijke peering.

1. Maak een virtueel netwerk met dubbele stack met IPv4-en IPv6-adres ruimte. Zie [een virtueel netwerk maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)voor meer informatie.

2. [Maak het subnet met dubbele stack-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Maak de gateway van het virtuele netwerk](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Als u van plan bent FastPath te gebruiken, gebruikt u ErGw3AZ.

4. [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

## <a name="limitations"></a>Beperkingen
Hoewel IPv6-ondersteuning beschikbaar is voor verbindingen met implementaties in regio's met Beschikbaarheidszones, worden de volgende use-cases niet ondersteund:

* Verbindingen met implementaties in azure via een niet-AZ ExpressRoute gateway SKU
* Verbindingen met implementaties in niet-AZ regio's
* Global Reach verbindingen tussen ExpressRoute-circuits

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor het oplossen van problemen met ExpressRoute:

* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)
