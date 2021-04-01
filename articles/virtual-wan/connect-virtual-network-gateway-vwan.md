---
title: Een virtuele netwerk gateway verbinden met een virtueel WAN van Azure
description: Dit artikel helpt u bij het verbinden van een virtuele Azure-netwerk gateway aan een Azure Virtual WAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 469d7ba9e86751312ebf6a6c82b35f065ee6cb50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880369"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Een VPN Gateway (virtuele netwerk gateway) verbinden met een virtueel WAN

Dit artikel helpt u bij het instellen van de connectiviteit vanuit een Azure-VPN Gateway (virtuele netwerk gateway) naar een virtueel WAN van Azure (VPN-gateway). Het maken van een verbinding van een VPN Gateway (virtuele netwerk gateway) naar een virtueel WAN (VPN-gateway) is vergelijkbaar met het instellen van de verbinding met een virtueel WAN vanuit filiaal sites van filialen.

Om mogelijke Verwar ring tussen twee functies tot een minimum te beperken, gaan we de gateway voor de naam van de functie waarnaar we verwijzen. Bijvoorbeeld VPN Gateway virtuele netwerk gateway en virtuele WAN-gateway.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint, moet u de volgende resources maken:

Azure Virtual WAN

* [Maak een virtueel WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Maak een hub](virtual-wan-site-to-site-portal.md#hub). De virtuele hub bevat de virtuele WAN-VPN-gateway.

Azure Virtual Network

* Maak een virtueel netwerk zonder virtuele netwerk gateways. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie de [snelstart](../virtual-network/quick-create-portal.md) als u een virtueel netwerk in de Azure-portal wilt maken.

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. een gateway voor een VPN Gateway virtuele netwerk maken

Maak een **VPN gateway** virtuele netwerk gateway in de modus actief-actief voor het virtuele netwerk. Wanneer u de gateway maakt, kunt u bestaande open bare IP-adressen gebruiken voor de twee exemplaren van de gateway of u kunt nieuwe open bare Ip's maken. U gebruikt deze open bare IP-adressen bij het instellen van de virtuele WAN-sites. Zie voor meer informatie over actieve en actieve VPN-gateways en configuratie stappen [Active-Active VPN-gateways configureren](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Instelling van de modus actief-actief

Schakel op de pagina **configuratie** van virtuele netwerk gateway de modus actief-actief in.

![actief-actief](./media/connect-virtual-network-gateway-vwan/active.png "actief-actief")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-instelling

Op de pagina **configuratie** van virtuele netwerk gateway kunt u de **BGP ASN** configureren. Wijzig de BGP ASN. De BGP-ASN kan niet 65515 zijn. 65515 wordt gebruikt door virtuele WAN van Azure.

![Scherm afbeelding toont de configuratie pagina van een virtuele netwerk gateway met BGP ASN configureren geselecteerd.](./media/connect-virtual-network-gateway-vwan/bgp.png "BGP")

### <a name="public-ip-addresses"></a><a name="pip"></a>Open bare IP-adressen

Wanneer de gateway is gemaakt, gaat u naar de pagina **Eigenschappen** . De eigenschappen en configuratie-instellingen zijn vergelijkbaar met het volgende voor beeld. Let op de twee open bare IP-adressen die worden gebruikt voor de gateway.

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. virtuele WAN-sites maken

Als u virtuele WAN-sites wilt maken, navigeert u naar uw virtuele WAN en selecteert u onder **connectiviteit** de optie **VPN-sites**. In deze sectie maakt u twee virtuele WAN-VPN-sites die overeenkomen met de virtuele netwerk gateways die u in de vorige sectie hebt gemaakt.

1. Selecteer **+ site maken**.
2. Typ op de pagina **VPN-sites maken** de volgende waarden:

   * **Regio** -dezelfde regio als de Azure VPN gateway virtuele netwerk gateway.
   * **Leverancier van apparaat** : Voer de leverancier van het apparaat (een wille keurige naam) in.
   * **Persoonlijke adres ruimte** : Voer een waarde in of laat het veld leeg wanneer BGP is ingeschakeld.
   * **Border Gateway Protocol** : Stel in **dat moet worden ingeschakeld als** BGP is ingeschakeld voor de Azure VPN gateway virtuele netwerk gateway.
   * **Verbinding maken met hubs** : Selecteer de hub die u hebt gemaakt in de vereisten in de vervolg keuzelijst. Als u geen hub ziet, controleert u of u een site-naar-site-VPN-gateway hebt gemaakt voor uw hub.
3. Voer onder **koppelingen** de volgende waarden in:

   * **Provider naam** : Voer een naam in voor de koppeling en een provider naam (elke naam).
   * **Snelheid** snelheid (wille keurig getal).
   * **IP-adres** : Voer het IP-adres in (hetzelfde als het eerste open bare IP-adres dat wordt weer gegeven onder de eigenschappen van de virtuele netwerk Gateway (VPN gateway)).
   * **BGP-adres** en **ASN** -BGP-adres en ASN. Deze moeten hetzelfde zijn als een van de BGP-peer-IP-adressen en ASN van de VPN Gateway virtuele netwerk gateway die u in [stap 1](#vnetgw)hebt geconfigureerd.
4. Controleer en selecteer **bevestigen** om de site te maken.
5. Herhaal de vorige stappen om de tweede site te maken zodat deze overeenkomt met het tweede exemplaar van de VPN Gateway virtuele netwerk gateway. U behoudt dezelfde instellingen, met uitzonde ring van het gebruik van het tweede open bare IP-adres en het tweede BGP-peer-IP-adres van VPN Gateway configuratie.
6. U hebt nu twee sites ingericht en kunt door gaan met de volgende sectie om de configuratie bestanden te downloaden.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. de VPN-configuratie bestanden downloaden

In deze sectie downloadt u het VPN-configuratie bestand voor elk van de sites die u hebt gemaakt in de vorige sectie.

1. Selecteer boven aan de pagina virtuele WAN **-VPN-sites** de **site** en selecteer vervolgens **site-naar-site-VPN-configuratie downloaden**. Azure maakt een configuratie bestand met de instellingen.

   ![Scherm opname van de pagina ' VPN-sites ' met de actie ' site-naar-site-VPN-configuratie downloaden ' geselecteerd.](./media/connect-virtual-network-gateway-vwan/download.png "downloaden")
2. Down load en open het configuratie bestand.
3. Herhaal deze stappen voor de tweede site. Zodra u beide configuratie bestanden hebt geopend, kunt u door gaan naar de volgende sectie.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. de lokale netwerk gateways maken

In deze sectie maakt u twee Azure VPN Gateway lokale netwerk gateways. De configuratie bestanden uit de vorige stap bevatten de configuratie-instellingen van de gateway. Gebruik deze instellingen om de Azure VPN Gateway lokale netwerk gateways te maken en te configureren.

1. Maak de lokale netwerk gateway met behulp van deze instellingen. Voor informatie over het maken van een VPN Gateway lokale netwerk gateway raadpleegt u het VPN Gateway artikel [een lokale netwerk gateway maken](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway).

   * **IP-adres** : gebruik het IP-adres van de Instance0 dat wordt weer gegeven voor *gatewayconfiguration* uit het configuratie bestand.
   * **BGP** : als de verbinding via BGP wordt uitgevoerd, selecteert u **BGP-instellingen configureren** en voert u het ASN ' 65515 ' in. Voer het IP-adres van de BGP-peer in. Gebruik ' Instance0 BgpPeeringAddresses ' voor *gatewayconfiguration* van het configuratie bestand.
   * Het **abonnement, de resource groep en de locatie** zijn hetzelfde als voor de virtuele WAN-hub.
2. Bekijk en maak de lokale netwerk gateway. Uw lokale netwerk gateway moet er ongeveer uitzien als in dit voor beeld.

   ![Scherm opname van de pagina configuratie met een IP-adres gemarkeerd en BGP-instellingen configureren geselecteerd.](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Herhaal deze stappen voor het maken van een andere lokale netwerk gateway, maar deze keer gebruikt u de ' Exemplaar1-waarden in plaats van ' Instance0-waarden uit het configuratie bestand.

   ![configuratie bestand downloaden](./media/connect-virtual-network-gateway-vwan/lng2.png "exemplaar1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. verbindingen maken

In deze sectie maakt u een verbinding tussen de VPN Gateway lokale netwerk gateways en de gateway van het virtuele netwerk. Zie [Configure a Connection (een verbinding configureren](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)) voor stappen voor het maken van een VPN gateway verbinding.

1. Navigeer in de portal naar de gateway van het virtuele netwerk en klik op **verbindingen**. Klik bovenaan de pagina Verbindingen op **+ Toevoegen** om de pagina **Verbinding toevoegen** te openen.
2. Configureer op de pagina **verbinding toevoegen** de volgende waarden voor de verbinding:

   * **Naam:** de naam van de verbinding.
   * **Verbindings type:** **Site-naar-site (IPSec)** selecteren
   * **Virtuele netwerkgateway:** hiervoor geldt een vaste waarde, omdat u verbinding maakt vanaf deze gateway.
   * **Lokale netwerk gateway:** Deze verbinding maakt verbinding tussen de gateway van het virtuele netwerk en de lokale netwerk gateway. Kies een van de lokale netwerk gateways die u eerder hebt gemaakt.
   * **Gedeelde sleutel:** Voer een gedeelde sleutel in.
   * **IKE-protocol:** Kies het IKE-protocol.
3. Klik op **OK** om uw verbinding te maken.
4. U kunt de verbinding bekijken op de pagina **Verbindingen** van de virtuele netwerkgateway.

   ![Verbinding](./media/connect-virtual-network-gateway-vwan/connect.png "verbinding")
5. Herhaal de voor gaande stappen om een tweede verbinding te maken. Selecteer voor de tweede verbinding de andere lokale netwerk gateway die u hebt gemaakt.
6. Als de verbindingen via BGP zijn, gaat u nadat u uw verbindingen hebt gemaakt naar een verbinding en selecteert u **configuratie**. Selecteer op de pagina **configuratie** voor **BGP** de optie **ingeschakeld**. Klik vervolgens op **Opslaan**. Herhaal dit voor de tweede verbinding.

## <a name="6-test-connections"></a><a name="test"></a>6. verbindingen testen

U kunt de connectiviteit testen door twee virtuele machines te maken, één aan de zijkant van de VPN Gateway virtuele netwerk gateway en één in een virtueel netwerk voor het virtuele WAN. vervolgens pingt u de twee virtuele machines.

1. Maak een virtuele machine in het virtuele netwerk (test1-VNet) voor Azure VPN Gateway (test1-VNG). Maak de virtuele machine niet in de GatewaySubnet.
2. Maak een ander virtueel netwerk om verbinding te maken met het virtuele WAN. Maak een virtuele machine in een subnet van dit virtuele netwerk. Dit virtuele netwerk kan geen virtuele netwerk gateways bevatten. U kunt snel een virtueel netwerk maken met behulp van de Power shell-stappen in het artikel [site-naar-site-verbinding](virtual-wan-site-to-site-portal.md#vnet) . Zorg ervoor dat u de waarden wijzigt voordat u de cmdlets uitvoert.
3. Verbind het VNet met de virtuele WAN-hub. Op de pagina voor uw virtuele WAN selecteert u **virtuele netwerk verbindingen** en vervolgens **+ verbinding toevoegen**. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de virtuele netwerkverbinding te maken.
5. Connectiviteit is nu ingesteld tussen de Vm's. U moet een virtuele machine van de andere kunnen pingen, tenzij er firewalls of andere beleids regels zijn die de communicatie blok keren.

## <a name="next-steps"></a>Volgende stappen

Zie [Configure a Custom IPSec Policy for Virtual WAN](virtual-wan-custom-ipsec-portal.md)(Engelstalig) voor de stappen voor het configureren van een aangepast IPSec-beleid.
Zie [informatie over Azure Virtual WAN](virtual-wan-about.md) en de [Veelgestelde vragen over Azure Virtual WAN](virtual-wan-faq.md)voor meer informatie over Virtual WAN.