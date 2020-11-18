---
title: 'On-premises netwerk verbinden met een virtueel Azure-netwerk: site-naar-site-VPN: Portal'
description: Maak een IPsec-site-naar-site-VPN Gateway verbinding van uw on-premises netwerk met een virtueel Azure-netwerk via het open bare Internet met behulp van de portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: 10cc97692e43cb46bd26597317b7f05ae6e19f83
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657190"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Een site-naar-site-verbinding maken in Azure Portal

In dit artikel leest u hoe u Azure Portal gebruikt om een site-naar-site-VPN-gatewayverbinding te maken vanaf uw on-premises netwerk naar het VNet. De stappen in dit artikel zijn van toepassing op het Resource Manager-implementatiemodel. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

Een site-naar-site-VPN-gatewayverbinding wordt gebruikt om een on-premises netwerk via een IPsec-/IKE-VPN-tunnel (IKEv1 of IKEv2) te verbinden met een virtueel Azure-netwerk. Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie [Overzicht van VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN-gateways.

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Diagram: cross-premises site-naar-site-VPN-gatewayverbinding":::

## <a name="prerequisites"></a>Vereisten

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

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

Maak een VPN-gateway met behulp van de volgende waarden:

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

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Een lokale netwerkgateway maken

De lokale netwerk gateway is een specifiek object dat uw on-premises locatie (de site) voor route ring vertegenwoordigt. U geeft de site een naam waarmee Azure hiernaar kan verwijzen en geeft vervolgens het IP-adres op van het on-premises VPN-apparaat waarmee u verbinding maakt. U geeft ook de IP-adresvoorvoegsels op die via de VPN-gateway worden doorgestuurd naar het VPN-apparaat. De adresvoorvoegsels die u opgeeft, zijn de voorvoegsels die zich in uw on-premises netwerk bevinden. Als uw on-premises netwerk verandert of als u het openbare IP-adres voor het VPN-apparaat moet wijzigen, kunt u de waarden later eenvoudig bijwerken.

Maak een lokale netwerk gateway met behulp van de volgende waarden:

* **Naam:** Site1
* **Resource groep:** TestRG1
* **Locatie:** VS-Oost

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Uw VPN-apparaat configureren

Voor site-naar-site-verbindingen met een on-premises netwerk is een VPN-apparaat vereist. In deze stap configureert u het VPN-apparaat. Bij het configureren van uw VPN-apparaat hebt u de volgende waarden nodig:

* Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In onze voorbeelden gebruiken we een eenvoudige gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
* Het openbare IP-adres van de gateway van uw virtuele netwerk. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI. Als u het open bare IP-adres van uw VPN-gateway wilt zoeken met behulp van de Azure Portal, navigeert u naar **virtuele netwerk gateways** en selecteert u de naam van uw gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Een VPN-verbinding maken

Maak de site-naar-site-VPN-verbinding tussen de gateway van uw virtuele netwerk en het on-premises VPN-apparaat.

Maak een verbinding met de volgende waarden:

* **Naam van lokale netwerk gateway:** Site1
* **Verbindings naam:** VNet1toSite1
* **Gedeelde sleutel:** In dit voorbeeld gebruiken we abc123. Maar u kunt datgene gebruiken wat compatibel is met uw VPN-hardware. Het belangrijkste is dat de waarden aan beide zijden van de verbinding met elkaar overeenkomen.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>De VPN-verbinding controleren

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Een VPN-gateway opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer Site-to-Site VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Zie [Een VPN-gateway opnieuw instellen](reset-gateway.md) voor de stappen.

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Een gateway-SKU wijzigen (formaat van een gateway wijzigen)

Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor de stappen voor het wijzigen van een gateway-SKU.

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Een extra verbinding toevoegen aan een VPN-gateway

U kunt extra verbindingen toevoegen, mits geen van de adresruimten tussen verbindingen met elkaar overlappen.

1. Als u een extra verbinding wilt toevoegen, gaat u naar de VPN-gateway en selecteert u **verbindingen** om de pagina verbindingen te openen.
1. Selecteer **+ toevoegen** om uw verbinding toe te voegen. Pas het verbindingstype zodanig aan dat dit overeenkomt met VNet-naar-VNet (als u verbinding wilt maken met een andere VNet-gateway) of met site-naar-site.
1. Als u verbinding maakt met behulp van site-naar-site maar nog geen lokale netwerkgateway hebt gemaakt, kunt u een nieuwe maken.
1. Geef de gedeelde sleutel op die u wilt gebruiken en selecteer **OK** om de verbinding te maken.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).
* Zie [Informatie over geforceerde tunneling](vpn-gateway-forced-tunneling-rm.md) voor meer informatie over geforceerde tunneling.
* Zie [Maximaal beschikbare cross-premises en VNet-naar-VNet-connectiviteit](vpn-gateway-highlyavailable.md) voor meer informatie over maximaal beschikbare actieve verbindingen.
* Zie [Netwerkbeveiliging](../virtual-network/network-security-groups-overview.md) voor informatie over het beperken van netwerkverkeer tot resources in een virtueel netwerk.
* Zie [Routering van verkeer in virtuele netwerken](../virtual-network/virtual-networks-udr-overview.md) voor informatie over hoe Azure verkeer routeert tussen Azure-resources, on-premises resources en resources op internet.
* Zie [een site-naar-site-VPN-verbinding maken](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)voor meer informatie over het maken van een site-naar-site-VPN-verbinding met behulp van Azure Resource Manager sjabloon.
* Zie [HBase geo-replicatie implementeren](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)voor meer informatie over het maken van een vnet-naar-VNET-VPN-verbinding met behulp van Azure Resource Manager sjabloon.