---
title: 'Zelfstudie: Een VPN Gateway maken en beheren: Azure Portal'
description: Volg deze zelfstudie om te leren hoe u een Azure VPN Gateway maakt, implementeert en beheert door de portal te gebruiken
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: e2df34b7897ee2543d967d147317859ab26971e9
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510473"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Zelfstudie: Een VPN-gateway maken en beheren met behulp van Azure Portal

Azure VPN-gateways bieden veilige, cross-premises connectiviteit tussen de klanten-premises en Azure. Deze zelfstudie bevat informatie over implementatie-basiselementen voor Azure VPN-gateways, zoals het maken en beheren van een VPN-gateway.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een VPN-gateway maken
> * Het openbare IP-adres van de gateway weergeven
> * Het formaat van een VPN-gateway wijzigen (formaat van SKU wijzigen)
> * Een VPN-gateway opnieuw instellen

Het volgende diagram toont het virtuele netwerk en de VPN-gateway die zijn gemaakt als onderdeel van deze zelfstudie.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagram van VNet en VPN-gateway":::

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. Als u dit niet hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Een virtueel netwerk maken

Maak een VNet met de volgende waarden:

* **Resourcegroep:** TestRG1
* **Naam:** VNet1
* **Regio:** (US) US - oost
* **IPv4-adresruimte:** 10.1.0.0/16
* **Subnetnaam:** FrontEnd
* **Adresruimte van subnet:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Een VPN-gateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

Maak een virtuele netwerkgateway met de volgende waarden:

* **Naam:** VNet1GW
* **Regio:** VS - oost
* **Gatewaytype:** VPN
* **VPN-type:** Op route gebaseerd
* **SKU:** VpnGw1
* **Generatie:** Generation1
* **Virtueel netwerk:** VNet1
* **Adresbereik gatewaysubnet:** 10.1.255.0/27
* **Openbaar IP-adres:** Nieuwe maken
* **Openbare IP-adresnaam:** VNet1GWpip
* **De modus actief-actief inschakelen:** Uitgeschakeld
* **BGP configureren:** Uitgeschakeld

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>Het openbare IP-adres weergeven

U kunt het openbare IP-adres van de gateway weergeven op de pagina **Overzicht** voor uw gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Overzichtspagina":::

Klik voor meer informatie over het object met het openbare IP-adres op de link voor naam/IP-adres naast **Openbaar IP-adres**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Het formaat van een gateway-SKU wijzigen

Er zijn specifieke regels voor het vergroten/verkleinen en het wijzigen van een gateway-SKU. In deze sectie wordt de grootte van de SKU aangepast. Zie [Gateway settings - resizing and changing SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange) (Gateway-instellingen: SKU's vergroten/verkleinen of wijzigen) voor meer informatie.

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Een gateway opnieuw instellen

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken of als u naar de volgende zelfstudie gaat, verwijdert u deze resources door de volgende stappen uit te voeren:

1. Voer de naam van uw resourcegroep in het vak **Zoeken** bovenaan de portal in en selecteer het in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer uw resourcegroep in voor **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zodra u een VPN-gateway hebt, kunt u verbindingen configureren. De onderstaande artikelen helpen u bij het maken van een aantal van de meest voorkomende configuraties:

> [!div class="nextstepaction"]
> [Site-naar-site-VPN-verbindingen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

> [!div class="nextstepaction"]
> [Punt-naar-site-VPN-verbindingen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
