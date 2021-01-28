---
title: 'Zelfstudie - On-premises netwerk verbinden met een virtueel netwerk: Azure Portal'
description: Maak een site-naar-site-VPN Gateway IPsec-verbinding van uw on-premises netwerk met een virtueel Azure-netwerk via het openbare internet met behulp van de portal.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945213"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Zelfstudie: Een site-naar-site-verbinding maken in Azure Portal

Azure VPN-gateways bieden veilige, cross-premises connectiviteit tussen de klanten-premises en Azure. Deze zelfstudie laat zien hoe u Azure Portal gebruikt om een site-naar-site-VPN-gatewayverbinding te maken vanaf uw on-premises netwerk naar het VNet. U kunt deze configuratie ook maken met behulp van [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) of [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagram: cross-premises site-naar-site-VPN-gatewayverbinding":::

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een VPN-gateway maken
> * Een lokale netwerkgateway maken
> * Een VPN-verbinding maken
> * De verbinding controleren
> * Verbinding maken met een virtuele machine

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u dit niet hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* U hebt een compatibel VPN-apparaat nodig en iemand die dit kan configureren. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie over compatibele VPN-apparaten en -apparaatconfiguratie.
* Controleer of u een extern gericht openbaar IPv4-adres voor het VPN-apparaat hebt.
* Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken. Wanneer u deze configuratie maakt, moet u de IP-adresbereikvoorvoegsels opgeven die Azure naar uw on-premises locatie doorstuurt. Geen van de subnetten van uw on-premises netwerk kan overlappen met de virtuele subnetten waarmee u verbinding wilt maken.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Een virtueel netwerk maken

Maak een virtueel netwerk (VNet) met de volgende waarden:

* **Resourcegroep:** TestRG1
* **Naam:** VNet1
* **Regio:** (US) US - oost
* **IPv4-adresruimte:** 10.1.0.0/16
* **Subnetnaam:** FrontEnd
* **Adresruimte van subnet:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Een VPN-gateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

### <a name="about-the-gateway-subnet"></a>Over het gatewaysubnet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>De gateway maken

Maak een VPN-gateway met de volgende waarden:

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

### <a name="view-the-public-ip-address"></a><a name="view"></a>Het openbare IP-adres weergeven

U kunt het openbare IP-adres van de gateway weergeven op de pagina **Overzicht** voor uw gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Overzichtspagina":::

Klik voor meer informatie over het object met het openbare IP-adres op de link voor naam/IP-adres naast **Openbaar IP-adres**.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Een lokale netwerkgateway maken

De lokale netwerkgateway is een specifiek object dat uw on-premises locatie (de site) voor routering aangeeft. U geeft de site een naam waarmee Azure hiernaar kan verwijzen en geeft vervolgens het IP-adres op van het on-premises VPN-apparaat waarmee u verbinding maakt. U geeft ook de IP-adresvoorvoegsels op die via de VPN-gateway worden doorgestuurd naar het VPN-apparaat. De adresvoorvoegsels die u opgeeft, zijn de voorvoegsels die zich in uw on-premises netwerk bevinden. Als uw on-premises netwerk verandert of als u het openbare IP-adres voor het VPN-apparaat moet wijzigen, kunt u de waarden later eenvoudig bijwerken.

Maak een lokale netwerkgateway met de volgende waarden:

* **Naam:** Site1
* **Resourcegroep:** TestRG1
* **Locatie:** VS - oost

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Uw VPN-apparaat configureren

Voor site-naar-site-verbindingen met een on-premises netwerk is een VPN-apparaat vereist. In deze stap configureert u het VPN-apparaat. Bij de configuratie van uw VPN-apparaat hebt u de volgende waarden nodig:

* Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In onze voorbeelden gebruiken we een eenvoudige gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
* Het openbare IP-adres van de gateway van uw virtuele netwerk. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI. Navigeer naar **Virtuele netwerkgateways** en selecteer de naam van uw VPN-gateway om het openbare IP-adres dat gebruikmaakt van Azure Portal te achterhalen.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Een VPN-verbinding maken

Maak de site-naar-site-VPN-verbinding tussen de gateway van uw virtuele netwerk en het on-premises VPN-apparaat.

Maak een verbinding met de volgende waarden:

* **Naam van lokale netwerkgateway:** Site1
* **Verbindingsnaam:** VNet1toSite1
* **Gedeelde sleutel:** In dit voorbeeld gebruiken we abc123. Maar u kunt datgene gebruiken wat compatibel is met uw VPN-hardware. Het belangrijkste is dat de waarden aan beide zijden van de verbinding met elkaar overeenkomen.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>De VPN-verbinding controleren

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Optionele stappen

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Extra verbindingen toevoegen aan de gateway

U kunt extra verbindingen toevoegen, mits geen van de adresruimten tussen verbindingen met elkaar overlappen.

1. Als u een extra verbinding wilt toevoegen, gaat u naar de VPN-gateway en selecteert u **Verbindingen** om de pagina Verbindingen te openen.
1. Selecteer **+Toevoegen** om uw verbinding toe te voegen. Pas het verbindingstype zodanig aan dat dit overeenkomt met VNet-naar-VNet (als u verbinding wilt maken met een andere VNet-gateway) of met site-naar-site.
1. Als u verbinding maakt met behulp van site-naar-site maar nog geen lokale netwerkgateway hebt gemaakt, kunt u een nieuwe maken.
1. Geef op welke gedeelde sleutel u wilt gebruiken en selecteer **OK** om de verbinding te maken.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Het formaat van een gateway-SKU wijzigen

Er zijn specifieke regels voor het vergroten/verkleinen en het wijzigen van een gateway-SKU. In deze sectie wordt de grootte van de SKU aangepast. Zie [Gateway settings - resizing and changing SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange) (Gateway-instellingen: SKU's vergroten/verkleinen of wijzigen) voor meer informatie.

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Een gateway opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer Site-to-Site VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Aanvullende overwegingen bij de configuratie

S2S-configuraties kunnen op verschillende manieren worden aangepast. Raadpleeg voor meer informatie de volgende artikelen:

* Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).
* Zie [Informatie over geforceerde tunneling](vpn-gateway-forced-tunneling-rm.md) voor meer informatie over geforceerde tunneling.
* Zie [Maximaal beschikbare cross-premises en VNet-naar-VNet-connectiviteit](vpn-gateway-highlyavailable.md) voor meer informatie over maximaal beschikbare actieve verbindingen.
* Zie [Netwerkbeveiliging](../virtual-network/network-security-groups-overview.md) voor informatie over het beperken van netwerkverkeer tot resources in een virtueel netwerk.
* Zie [Routering van verkeer in virtuele netwerken](../virtual-network/virtual-networks-udr-overview.md) voor informatie over hoe Azure verkeer routeert tussen Azure-resources, on-premises resources en resources op internet.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken of als u naar de volgende zelfstudie gaat, verwijdert u deze resources door de volgende stappen uit te voeren:

1. Voer de naam van uw resourcegroep in het vak **Zoeken** bovenaan de portal in en selecteer het in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer uw resourcegroep in voor **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zodra u een S2S-verbinding hebt geconfigureerd, kunt u een P2S-verbinding toevoegen aan dezelfde gateway.

> [!div class="nextstepaction"]
> [Punt-naar-site-VPN-verbindingen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
