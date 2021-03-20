---
title: 'Een VNet-naar-VNet-VPN Gateway verbinding configureren: Azure Portal'
description: Een VPN-gatewayverbinding maken tussen VNets met behulp van Resource Manager en Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: 465d877da48e0d7027dbba6615302af32c6bb154
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872397"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Een VPN-gatewayverbinding tussen VNet's configureren met behulp van Azure Portal

Dit artikel helpt u om virtuele netwerken (VNet's) te verbinden met behulp van het verbindingstype VNet-naar-VNet. Virtuele netwerken kunnen zich in verschillende regio's bevinden en tot verschillende abonnementen behoren. Wanneer u VNet's uit verschillende abonnementen koppelt, hoeven de abonnementen niet aan dezelfde Active Directory-tenant gekoppeld te zijn. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="Diagram van VNet naar VNet":::

De stappen in dit artikel zijn van toepassing op het Azure Resource Manager-implementatiemodel en maken gebruik van Azure Portal. U kunt deze configuratie ook maken met een ander implementatieprogramma of -model, met behulp van de opties die worden beschreven in de volgende artikelen:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Over het verbinden van VNet's

In de volgende secties worden de verschillende manieren voor het koppelen van virtuele netwerken beschreven.

### <a name="vnet-to-vnet"></a>VNet-naar-VNet

Het configureren van een VNet-naar-VNet-verbinding is een eenvoudige manier om VNet's te koppelen. Het verbinden van een virtueel netwerk met een ander virtueel netwerk met behulp van het verbindingstype VNet-naar-VNet (VNet2VNet) is vergelijkbaar met het maken van een site-naar-site-IPsec-verbinding met een on-premises locatie. Voor beide verbindingstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden en beide typen werken tijdens het communiceren op dezelfde manier. Het verschil zit hem in de configuratie van de lokale netwerkgateway. 

Als u een VNet-naar-VNet-verbinding maakt, wordt de adresruimte voor de lokale netwerkgateway automatisch gemaakt en gevuld. Wanneer u de adresruimte voor een VNet bijwerkt, wordt de route naar de bijgewerkte adresruimte automatisch ingesteld voor het andere VNet. Het maken van een VNet-naar-VNet-verbinding gaat meestal sneller en makkelijker dan het maken van een site-naar-site-verbinding.

### <a name="site-to-site-ipsec"></a>Site-naar-site (IPsec)

Als u met een gecompliceerde netwerkconfiguratie werkt, kunt u de VNet's wellicht beter koppelen met een [Site-naar-Site](./tutorial-site-to-site-portal.md)-verbinding. Als u de stappen voor site-naar-site-IPsec volgt, maakt en configureert u de lokale netwerkgateways handmatig. De lokale netwerkgateway voor elke VNet behandelt de andere VNet als een lokale site. Hiermee kunt u extra adresruimten voor de lokale netwerkgateway opgeven om het verkeer te routeren. Als de adresruimte voor een VNet wordt gewijzigd, moet u de bijbehorende lokale netwerkgateway handmatig bijwerken.

### <a name="vnet-peering"></a>VNet-peering

U kunt uw Vnet's ook verbinden met behulp van VNet-peering. Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor. Bovendien worden de [prijzen voor VNET-peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berekend dan voor [VNet-naar-VNet-VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="why-create-a-vnet-to-vnet-connection"></a>Waarom een VNet-met-VNet-verbinding maken?

U kunt omwille van de volgende redenen virtuele netwerken koppelen met een VNet-naar-VNet-verbinding:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografische redundantie en aanwezigheid tussen regio's

* U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
* Met Azure Traffic Manager en Azure Load Balancer kunt u workloads met maximale beschikbaarheid instellen met behulp van geografische redundantie over meerdere Azure-regio's. U kunt bijvoorbeeld SQL Server AlwaysOn-beschikbaarheidsgroepen instellen voor meerdere Azure-regio's.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionale toepassingen met meerdere lagen met isolatie- of beheergrenzen

* Binnen dezelfde regio kunt u vanwege isolatie- of beheervereisten toepassingen met meerdere lagen instellen met meerdere virtuele netwerken die met elkaar zijn verbonden.

VNet-naar-VNet-communicatie kan worden gecombineerd met configuraties voor meerdere locaties. Zoals u in het volgende diagram kunt zien, kunt u met dergelijke configuraties netwerktopologieën maken waarin cross-premises connectiviteit wordt gecombineerd met connectiviteit tussen virtuele netwerken:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="Het diagram VNet-verbindingen":::

In dit artikel leest u hoe u VNet's verbindt met behulp van het verbindingstype VNet-naar-VNet. Wanneer u deze stappen uitvoert als oefening, kunt u de volgende voorbeeldinstellingswaarden gebruiken. In het voorbeeld vallen de virtuele netwerken onder hetzelfde abonnement, maar behoren ze tot verschillende resourcegroepen. Als uw VNet's onder verschillende abonnementen vallen, kunt u de verbinding niet via de portal maken. Gebruik in plaats daarvan [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) of de [CLI](vpn-gateway-howto-vnet-vnet-cli.md). Zie [Veelgestelde vragen over VNet-naar-VNet](#vnet-to-vnet-faq) voor meer informatie over VNet-naar-VNet-verbindingen.

### <a name="example-settings"></a>Voorbeeldinstellingen

**Waarden voor VNet1:**

* **Virtuele-netwerkinstellingen**
  * **Naam**: VNet1
  * **Adres ruimte**: 10.1.0.0/16
  * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken.
  * **Resource groep**: TestRG1
  * **Locatie**: VS-Oost
  * **Subnet**
    * **Naam**: front-end
    * **Adres bereik**: 10.1.0.0/24

* **Instellingen voor virtuele-netwerkgateway**
  * **Naam**: VNet1GW
  * **Resource groep**: VS-Oost
  * **Generatie**: generatie 1
  * **Gatewaytype**: selecteer **VPN**.
  * **VPN-type**: Selecteer **route op basis**.
  * **SKU**: VpnGw1
  * **Virtueel netwerk**: VNet1
  * **Adres bereik gateway-subnet**: 10.1.255.0/27
  * **Openbaar IP-adres**: nieuwe maken
  * **Naam van openbaar IP-adres**: VNet1GWpip

* **Verbinding**
  * **Naam**: VNet1toVNet4
  * **Gedeelde sleutel**: u kunt de gedeelde sleutel zelf maken. Wanneer u de verbinding tussen de VNets maakt, moeten de waarden overeenkomen. Voor deze oefening gebruikt u abc123.

**Waarden voor en vnet4:**

* **Virtuele-netwerkinstellingen**
  * **Naam**: en vnet4
  * **Adres ruimte**: 10.41.0.0/16
  * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken.
  * **Resource groep**: TestRG4
  * **Locatie**: VS-West
  * **Subnet**
  * **Naam**: front-end
  * **Adres bereik**: 10.41.0.0/24

* **Instellingen voor virtuele-netwerkgateway**
  * **Naam**: VNet4GW
  * **Resource groep**: VS-West
  * **Generatie**: generatie 1
  * **Gatewaytype**: selecteer **VPN**.
  * **VPN-type**: Selecteer **op route gebaseerd**.
  * **SKU**: VpnGw1
  * **Virtueel netwerk**: en vnet4
  * **Adres bereik gateway-subnet**: 10.41.255.0/27
  * **Openbaar IP-adres**: nieuwe maken
  * **Naam van openbaar IP-adres**: VNet4GWpip

* **Verbinding**
  * **Naam**: VNet4toVNet1
  * **Gedeelde sleutel**: u kunt de gedeelde sleutel zelf maken. Wanneer u de verbinding tussen de VNets maakt, moeten de waarden overeenkomen. Voor deze oefening gebruikt u abc123.

## <a name="create-and-configure-vnet1"></a>VNet1 maken en configureren

Als u al beschikt over een VNet, controleert u of de instellingen compatibel zijn met het ontwerp van de VPN-gateway. Let vooral op eventuele subnetten die met andere netwerken overlappen. Als u overlappende subnetten hebt, werkt de verbinding mogelijk niet goed.

### <a name="to-create-a-virtual-network"></a>Een virtueel netwerk maken

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>De VNet1-gateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU. Zie de [voorbeeldinstellingen](#example-settings) als u deze configuratie bij wijze van oefening maakt.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>De gateway van een virtueel netwerk maken

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>En vnet4 maken en configureren

Nadat u VNet1 hebt geconfigureerd, maakt u en vnet4 en de en vnet4-gateway door de vorige stappen te herhalen en de waarden te vervangen door en vnet4 waarden. U hoeft niet te wachten totdat de virtuele netwerk gateway voor VNet1 is gemaakt voordat u en vnet4 configureert. Als u uw eigen waarden gebruikt, mogen de adresruimten niet overlappen met een van de VNet's waarmee u verbinding wilt maken.

## <a name="configure-the-vnet1-gateway-connection"></a>De VNet1 gateway-verbinding configureren

Wanneer de virtuele netwerk gateways voor zowel VNet1 als en vnet4 zijn voltooid, kunt u de verbindingen van uw virtuele netwerk gateway maken. In deze sectie maakt u een verbinding tussen VNet1 en VNet4. Deze stappen werken alleen voor VNets in hetzelfde abonnement. Als uw VNet's onder verschillende abonnementen vallen, moet u [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) gebruiken om de verbinding te maken. Als uw VNet's echter behoren tot verschillende resourcegroepen die onder hetzelfde abonnement vallen, kunt u deze verbinden met behulp van de portal.

1. Selecteer in Azure Portal **Alle resources**, typ *gateway van virtueel netwerk* in het zoekvak en navigeer vervolgens naar de gateway voor uw VNet. Bijvoorbeeld **VNet1GW**. Selecteer de gateway om de pagina **virtuele netwerk gateway** te openen.
1. Ga op de pagina gateway naar **instellingen->verbindingen**. Selecteer vervolgens **+ toevoegen**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Pagina verbindingen":::
1. De pagina **verbinding toevoegen** wordt geopend.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Verbinding toevoegen":::

   Vul op de pagina **Verbinding toevoegen** de waarden voor uw verbinding in:

   * **Naam**: Voer een naam in voor de verbinding. Bijvoorbeeld *VNet1toVNet4*.

   * **Verbindings type**: Selecteer **VNet-naar-VNet** in de vervolg keuzelijst.

   * **Eerste virtuele netwerk gateway**: deze veld waarde wordt automatisch ingevuld omdat u deze verbinding maakt op basis van de opgegeven virtuele netwerk gateway.

   * **Tweede virtuele netwerk gateway**: dit veld is de virtuele netwerk gateway van het VNet waarmee u een verbinding wilt maken. Selecteer **Een andere virtuele netwerkgateway kiezen** om de pagina **Virtuele netwerkgateway kiezen** te openen.

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Een gateway kiezen":::

     * Bekijk de virtuele netwerkgateways die op deze pagina worden vermeld. U ziet dat er alleen virtuele netwerkgateways worden vermeld die binnen uw abonnement vallen. Als u verbinding wilt maken met een virtuele netwerkgateway die geen deel uitmaakt van uw abonnement, gebruikt u [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     * Selecteer de virtuele netwerkgateway waarmee u verbinding wilt maken.

   * **Gedeelde sleutel (PSK)**: Voer in dit veld een gedeelde sleutel in voor uw verbinding. U kunt deze sleutel ook zelf maken of genereren. In een verbinding tussen sites is de sleutel gelijk aan de sleutel voor het on-premises apparaat en uw virtuele netwerkgatewayverbinding. Het concept is hier vergelijkbaar, maar in plaats van een verbinding te maken met een VPN-apparaat, maakt u verbinding met een andere virtuele netwerkgateway.
1. Selecteer **OK** om uw wijzigingen op te slaan.

## <a name="configure-the-vnet4-gateway-connection"></a>De en vnet4 gateway-verbinding configureren

Maak vervolgens een verbinding van en vnet4 naar VNet1. Zoek in de Portal de virtuele netwerk gateway die is gekoppeld aan en vnet4. Volg de stappen in de vorige sectie, waarbij u de waarden vervangt om een verbinding te maken van en vnet4 naar VNet1. Zorg ervoor dat u dezelfde gedeelde sleutel gebruikt.

## <a name="verify-your-connections"></a>Controleer uw verbindingen

1. Zoek de virtuele netwerkgateway in Azure Portal. 
1. Selecteer **Verbindingen** op de pagina **Virtuele netwerkgateway** om de pagina **Verbindingen** weer te geven voor de virtuele netwerkgateway. Nadat de verbinding tot stand is gebracht, ziet u dat de **status** waarden worden gewijzigd in **verbonden**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Verbindingen controleren":::
1. Selecteer een van de verbindingen onder de kolom **naam** om meer informatie weer te geven. Wanneer de gegevensstromen op gang komen, ziet u waarden voor **Inkomende gegevens** en **Uitgaande gegevens**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Scherm afbeelding toont een resource groep met waarden voor gegevens in en uit gegevens":::

## <a name="add-additional-connections"></a>Extra verbindingen toevoegen

Als u extra verbindingen wilt toevoegen, gaat u naar de virtuele netwerkgateway waarvoor u de verbinding wilt maken en selecteert u **Verbindingen**. U kunt een andere VNet-naar-VNet-verbinding maken, of een site-naar-site-verbinding (IPsec) met een on-premises locatie. Stel **Verbindingstype** in op het gewenste type voor de verbinding die u wilt maken. Controleer voordat u extra verbindingen maakt of de adresruimte voor het virtuele netwerk niet overlapt met een van de adresruimten waarmee u verbinding wilt maken. Zie [Een site-naar-site-verbinding maken](./tutorial-site-to-site-portal.md) voor stappen voor het maken van een site-naar-site-verbinding.

## <a name="vnet-to-vnet-faq"></a>Veelgestelde vragen over VNet-naar-VNet

Bekijk de Veelgestelde vragen voor meer informatie over VNet-naar-VNet-verbindingen.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie [Netwerkbeveiliging](../virtual-network/network-security-groups-overview.md) voor informatie over het beperken van netwerkverkeer tot resources in een virtueel netwerk.

* Zie [Routering van verkeer in virtuele netwerken](../virtual-network/virtual-networks-udr-overview.md) voor informatie over hoe Azure verkeer routeert tussen Azure-resources, on-premises resources en resources op internet.