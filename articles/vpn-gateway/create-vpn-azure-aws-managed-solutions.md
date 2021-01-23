---
title: Een VPN tussen Azure en AWS maken met behulp van beheerde oplossingen
description: Een VPN-verbinding tussen Azure en AWS maken met behulp van beheerde oplossingen in plaats van Vm's of apparaten.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/22/2021
ms.author: ricmart
ms.openlocfilehash: a0655ce1d2e9939981bb4fd3280af80e359ea1e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737741"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Een VPN-verbinding tussen Azure en AWS maken met behulp van beheerde oplossingen

U kunt een verbinding tot stand brengen tussen Azure en AWS met behulp van beheerde oplossingen. Voorheen moest u een apparaat of VM gebruiken die fungeert als een responder. Nu kunt u de AWS-VPN-gateway rechtstreeks verbinden met VPN Gateway Azure zonder dat u zich zorgen hoeft te maken over het beheer van IaaS-resources zoals virtuele machines. Dit artikel helpt u bij het maken van een VPN-verbinding tussen Azure en AWS met behulp van alleen beheerde oplossingen.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Architectuur diagram":::

## <a name="configure-azure"></a>Azure configureren

### <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Configureer een virtueel netwerk. Zie de [Virtual Network Quick](../virtual-network/quick-create-portal.md)start voor instructies.

De volgende voorbeeld waarden worden in dit artikel gebruikt:

* **Resource groep:** RG-Azure-AWS
* **Regio:** VS - oost
* **Naam van virtueel netwerk:** vnet-Azure
* **IPv4-adres ruimte:** 172.10.0.0/16
* **Subnetnaam:** subnet-01
* **Adres bereik van subnet:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Een VPN-gateway maken

Maak een VPN-gateway voor uw virtuele netwerk. Zie [zelf studie: een VPN-gateway maken en beheren](tutorial-create-gateway-portal.md)voor instructies.

In dit artikel worden de volgende voorbeeld waarden en-instellingen gebruikt:

* **Gateway naam:** VPN-Azure-AWS
* **Regio:** VS - oost
* **Gatewaytype:** VPN
* **VPN-type:** Op route gebaseerd
* **SKU:** VpnGw1
* **Generatie:** Generatie 1
* **Virtueel netwerk:** Moet het VNet zijn waarvoor u de gateway wilt maken.
* **Adres bereik gateway-subnet:** 172.10.0.0/27
* **Openbaar IP-adres:** Nieuwe maken
* **Naam van openbaar IP-adres:** PIP-VPN-Azure-AWS
* **Modus actief-actief inschakelen:** Scha
* **BGP configureren:** Scha

Voorbeeld:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Samen vatting van virtuele netwerk gateway":::

## <a name="configure-aws"></a>AWS configureren

1. Maak de virtuele Privécloud (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="VPC-gegevens maken":::

1. Maak een subnet in de VPC (virtueel netwerk).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Het subnet maken":::

1. Een klant gateway maken die verwijst naar het open bare IP-adres van Azure VPN Gateway. De **klant Gateway** is een AWS-bron die informatie bevat voor AWS over het klant gateway-apparaat, in dit geval de Azure-VPN gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Een klant gateway maken":::

1. Maak de virtuele particuliere gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Virtuele particuliere gateway maken":::

1. Koppel de virtuele particuliere gateway aan de VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="De VPG koppelen aan de VPC":::

1. Selecteer de VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Koppel":::

1. Een site-naar-site-VPN-verbinding maken.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="VPN-verbinding maken":::

1. Stel de routerings optie in op **statisch** en wijs naar het Azure subnet-01-voor voegsel **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Een statische route instellen":::

1. Nadat u de opties hebt gevuld, **maakt** u de verbinding.

1. Down load het configuratie bestand. Als u de juiste configuratie wilt downloaden, wijzigt u de leverancier, het platform en de software in **Algemeen**, omdat Azure geen geldige optie is.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Configuratie downloaden":::

1. Het configuratie bestand bevat de vooraf gedeelde sleutel en het open bare IP-adres voor elk van de twee IPsec-tunnels die zijn gemaakt door AWS.

   **Tunnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Configuratie van tunnel 1":::

   **Tunnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Tunnel 2-configuratie":::

1. Nadat de tunnels zijn gemaakt, ziet u iets dat vergelijkbaar is met dit voor beeld.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Details van AWS-VPN-verbinding":::

## <a name="create-local-network-gateway"></a>Lokale netwerk gateway maken

In Azure is de lokale netwerk gateway een Azure-resource die doorgaans een on-premises locatie vertegenwoordigt. Het wordt ingevuld met informatie die wordt gebruikt om verbinding te maken met het on-premises VPN-apparaat. In deze configuratie wordt de lokale netwerk gateway echter gemaakt en gevuld met de AWS voor de virtuele particuliere gateway verbinding. Zie [azure VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#lng)voor meer informatie over de lokale netwerk gateways van Azure.

Maak een lokale netwerk gateway in Azure. Zie [een lokale netwerk gateway maken](tutorial-site-to-site-portal.md#LocalNetworkGateway)voor instructies.

Geef de volgende waarden op:

* **Naam:** In het voor beeld gebruiken we LNG-Azure-AWS.
* **Eind punt:** IP-adres
* **IP-adres:** Het open bare IP-adres van de AWS-VPN-gateway en het VPC CIDR-voor voegsel. U kunt het open bare IP-adres vinden in het configuratie bestand dat u eerder hebt gedownload.

AWS maakt twee IPsec-tunnels voor hoge Beschik baarheid. In het volgende voor beeld ziet u het open bare IP-adres van de IPsec-tunnel #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Lokale netwerk gateway":::

## <a name="create-vpn-connection"></a>VPN-verbinding maken

In deze sectie maakt u de VPN-verbinding tussen de gateway van het virtuele Azure-netwerk en de AWS-gateway.

1. Maak de Azure-verbinding. Zie [een VPN-verbinding maken](tutorial-site-to-site-portal.md#CreateConnection)voor de stappen voor het maken van een verbinding.

   In het volgende voor beeld is de gedeelde sleutel opgehaald uit het configuratie bestand dat u eerder hebt gedownload. In dit voor beeld gebruiken we de waarden voor de IPsec-tunnel #1 gemaakt door AWS en beschreven in het configuratie bestand.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure-verbindings object":::

1. Bekijk de verbinding. Na een paar minuten wordt de verbinding tot stand gebracht.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Werkende verbinding":::

1. Controleer of AWS IPsec-tunnel #1 **is.**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Controleren of de AWS-tunnel actief is":::

1. Bewerk de route tabel die is gekoppeld aan de VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="De route bewerken":::

1. Voeg de route toe aan het Azure-subnet. Deze route gaat via de VPC-gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="De route configuratie opslaan":::

## <a name="configure-second-connection"></a>Tweede verbinding configureren

In deze sectie maakt u een tweede verbinding om hoge Beschik baarheid te garanderen.

1. Maak een andere lokale netwerk gateway die verwijst naar het open bare IP-adres van de IPsec-tunnel #2 op AWS. Dit is de standby-gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="De lokale netwerkgateway maken":::

1. Maak de tweede VPN-verbinding van Azure naar AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="De stand-by lokale netwerk gateway verbinding maken":::

1. De Azure VPN gateway-verbindingen weer geven.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Status van Azure-verbinding":::

1. Bekijk de AWS-verbindingen. In dit voor beeld kunt u zien dat de verbindingen nu tot stand zijn gebracht.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Status van AWS-verbinding":::

## <a name="to-test-connections"></a>Verbindingen testen

1. Een **Internet gateway** toevoegen aan de VPC op AWS. De Internet gateway is een logische verbinding tussen een Amazon VPN en Internet. Met deze resource kunt u via Internet verbinding maken via de test-VM vanuit het open bare IP-AWS. Deze resource is niet vereist voor de VPN-verbinding. We gebruiken deze alleen om te testen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="De Internet gateway maken":::

1. Selecteer **koppelen aan VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="De Internet gateway koppelen aan VPC":::

1. Selecteer een VPC en **Voeg Internet gateway toe**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="De gateway koppelen":::

1. Een route maken om verbindingen met **0.0.0.0/0** (Internet) via de Internet gateway toe te staan.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="De route via de gateway configureren":::

1. In azure wordt de route automatisch gemaakt. U kunt de route van de virtuele Azure-machine controleren door **VM-> netwerk > netwerk Interface > actieve routes** te selecteren. U ziet 2 routes, 1 route per verbinding.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="De efficiënte routes controleren":::

1. U kunt dit testen vanaf een Linux-VM in Azure. Het resultaat ziet er ongeveer uit zoals in het volgende voor beeld.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Overzicht van Azure vanuit Linux VM":::

1. U kunt dit ook testen vanaf een Linux-VM op AWS. Het resultaat ziet er ongeveer uit zoals in het volgende voor beeld.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Overzicht van AWS van de Linux-VM":::

1. Test de verbinding van de Azure-VM.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Ping testen vanuit Azure":::

1. Test de verbinding van de AWS-VM.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Ping testen vanaf AWS":::

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)voor meer informatie over AWS-ondersteuning voor IKEv2.
