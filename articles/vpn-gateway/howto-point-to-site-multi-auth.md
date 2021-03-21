---
title: 'Verbinding maken met een VNet met behulp van P2S VPN & meerdere verificatie typen: Portal'
titleSuffix: Azure VPN Gateway
description: Verbinding maken met een VNet via P2S met meerdere verificatie typen in de Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745687"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Configureer een punt-naar-site-VPN-verbinding met een VNet met behulp van meerdere verificatie typen: Azure Portal

Dit artikel helpt u bij het veilig verbinden van afzonderlijke clients met Windows, Linux of macOS naar een Azure-VNet. P2S-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie, bijvoorbeeld als u ook thuis werkt of op een congres verbinding wilt maken. U kunt P2S ook in plaats van een site-naar-site-VPN gebruiken wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKEv2. Voor meer informatie over punt-naar-site-VPN leest u [About Point-to-Site VPN](point-to-site-about.md) (Over punt-naar-site-VPN).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Verbinding maken tussen een computer en een Azure VNet-Point-to-site-verbindings diagram":::

Zie [about Point-to-site VPN](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site-VPN. Als u deze configuratie wilt maken met behulp van de Azure PowerShell, raadpleegt u [een punt-naar-site-VPN configureren met behulp van Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

Meerdere verificatie typen op dezelfde VPN-gateway worden alleen ondersteund met het tunnel type OpenVPN.

### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

U kunt de volgende waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel:

* **VNet-naam:** VNet1
* **Adres ruimte:** 10.1.0.0/16<br>In dit voorbeeld gebruiken we slechts één adresruimte. U kunt meer dan één adresruimte voor uw VNet hebben.
* **Subnetnaam:** FrontEnd
* **Adres bereik van subnet:** 10.1.0.0/24
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep:** TestRG1
* **Locatie:** VS - oost
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Naam van de virtuele netwerk gateway:** VNet1GW
* **Gatewaytype:** VPN
* **VPN-type:** Op route gebaseerd
* **Openbare IP-adresnaam:** VNet1GWpip
* **Verbindings type:** Punt-naar-site
* **Client-adres groep:** 172.16.201.0/24<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de clientadresgroep.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Een virtueel netwerk maken

Controleer eerst of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Gateway van een virtueel netwerk

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

>[!NOTE]
>De basis gateway-SKU biedt geen ondersteuning voor het tunnel type OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Clientadresgroep

De clientadrespool bestaat uit een privé-IP-adresbereik dat u opgeeft. De clients die dynamisch verbinding maken via een punt-naar-site-VPN-verbinding krijgen een IP-adres uit dit bereik. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Als u meerdere protocollen configureert en SSTP een van de protocollen is, wordt de geconfigureerde adres groep op dezelfde manier verdeeld tussen de geconfigureerde protocollen.

1. Nadat de virtuele netwerkgateway is gemaakt, gaat u naar de sectie **Instellingen** van de pagina van de virtuele netwerkgateway. Selecteer bij **instellingen** de optie **punt-naar-site-configuratie**. Selecteer **nu configureren** om de configuratie pagina te openen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Scherm afbeelding van punt-naar-site-configuratie pagina." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Op de pagina **punt-naar-site-configuratie** kunt u verschillende instellingen configureren. Voeg in het vak **adres groep** het privé-IP-adres bereik toe dat u wilt gebruiken. VPN-clients ontvangen dynamisch een IP-adres uit het bereik dat u opgeeft. Het minimale subnetmasker is 29 bits voor actief/passief en 28 bits voor actieve/actieve configuratie.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Scherm opname van de adres groep.":::

1. Ga door naar de volgende sectie voor het configureren van verificatie-en tunnel typen.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Verificatie-en tunnel typen

In deze sectie configureert u het verificatie type en het tunnel type. Als u het **Tunnel Type** of **verificatie type** niet ziet op de pagina **punt-naar-site-configuratie** , wordt de basis-SKU gebruikt voor uw gateway. De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie. Als u deze instellingen wilt gebruiken, moet u de gateway verwijderen en opnieuw maken met behulp van een andere gateway-SKU.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Scherm opname van het verificatie type.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltype

Selecteer op de pagina **punt-naar-site** -configuratie **openvpn (SSL)** als het tunnel type.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Verificatie type

Selecteer de gewenste typen bij **verificatie type**. Opties zijn:

* Azure-certificaat
* RADIUS
* Azure Active Directory

Afhankelijk van de geselecteerde verificatie type (n), ziet u verschillende configuratie-instellingen velden die moeten worden ingevuld. Vul de vereiste gegevens in en selecteer **Opslaan** boven aan de pagina om alle configuratie-instellingen op te slaan.

Zie voor meer informatie over verificatie type:

* [Azure-certificaat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Configuratie pakket voor de VPN-client

VPN-clients moeten worden geconfigureerd met client configuratie-instellingen. Het configuratie pakket voor de VPN-client bevat bestanden met de instellingen voor het configureren van VPN-clients om verbinding te maken met een VNet via een P2S-verbinding.

Voor instructies voor het genereren en installeren van VPN-client configuratie bestanden gebruikt u het artikel dat betrekking heeft op uw configuratie:

* [VPN-client configuratie bestanden maken en installeren voor systeem eigen Azure-certificaat verificatie P2S-configuraties](point-to-site-vpn-client-configuration-azure-cert.md).
* [Azure Active Directory verificatie: Configureer een VPN-client voor P2S openvpn-protocol verbindingen](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Veelgestelde vragen over punt-naar-site

Deze sectie bevat informatie over veelgestelde vragen over punt-naar-site-configuraties. U kunt ook de [Veelgestelde vragen over VPN gateway](vpn-gateway-vpn-faq.md) bekijken voor meer informatie over VPN gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).
