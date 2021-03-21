---
title: 'Een computer verbinden met een virtueel netwerk met behulp van punt-naar-site-en RADIUS-verificatie: Power shell | Azure'
description: Verbind Windows-en OS X-clients veilig met een virtueel netwerk met behulp van P2S en RADIUS-verificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964844"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Een punt-naar-site-verbinding met een VNet configureren met RADIUS-verificatie: Power shell

In dit artikel wordt beschreven hoe u een VNet maakt met een punt-naar-site-verbinding die gebruikmaakt van RADIUS-verificatie. Deze configuratie is alleen beschikbaar voor het Resource Manager-implementatie model.

Met een punt-naar-site-VPN-gateway (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Punt-naar-site-VPN-verbindingen zijn nuttig wanneer u verbinding wilt maken met uw VNet vanaf een externe locatie, zoals wanneer u thuis of op een conferentie werkt. Een P2S-VPN is ook een uitstekende oplossing in plaats van een site-naar-site-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet.

Er wordt een P2S-VPN-verbinding gestart vanaf Windows en Mac-apparaten. Clients die verbinding maken, kunnen de volgende verificatiemethoden gebruiken: 

* RADIUS-server
* Verificatie van systeem eigen certificaten VPN Gateway
* Systeem eigen Azure Active Directory-verificatie (alleen Windows 10)

Dit artikel helpt u bij het configureren van een P2S-configuratie met verificatie met behulp van RADIUS-server. Zie [een punt-naar-site-verbinding met een VNet configureren met native certificaat authenticatie van de VPN-gateway](vpn-gateway-howto-point-to-site-rm-ps.md) of [een Azure Active Directory TENANT maken voor P2S openvpn-protocol verbindingen](openvpn-azure-ad-tenant.md) voor Azure Active Directory-verificatie als u in plaats daarvan wilt verifiëren met behulp van gegenereerde certificaten en native certificaat verificatie voor de VPN-gateway.

![Diagram waarin de P2S-configuratie wordt weer gegeven met verificatie met behulp van een RADIUS-server.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol), OpenVPN of IKEv2.

* SSTP is een op TLS gebaseerde VPN-tunnel die alleen wordt ondersteund op Windows-client platforms. Omdat met SSTP firewalls kunnen worden gepasseerd, is dit een ideale optie om vanaf elke locatie verbinding te maken met Azure. Wij ondersteunen SSTP versies 1.0, 1.1 en 1.2 aan de serverzijde. De client besluit welke versie moet worden gebruikt. Voor Windows 8.1 en hoger, gebruikt SSTP standaard 1.2.

* OpenVPN®-protocol, een op SSL/TLS gebaseerd VPN-protocol. Een TLS-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls open TCP-poort 443 uitgaand, die TLS gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versie 11,0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10,13 en hoger).

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).

Voor P2S-verbindingen is het volgende vereist:

* Een RouteBased VPN-gateway. 
* Een RADIUS-server voor het afhandelen van gebruikers verificatie. De RADIUS-server kan on-premises of in het Azure-VNet worden geïmplementeerd. U kunt ook twee RADIUS-servers configureren voor maximale Beschik baarheid.
* Een configuratie pakket voor de VPN-client voor de Windows-apparaten die verbinding kunnen maken met het VNet. Een VPN-client configuratie pakket bevat de instellingen die zijn vereist voor een VPN-client om verbinding te maken via P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Over Active Directory (AD) domein authenticatie voor P2S Vpn's

Met AD-domein verificatie kunnen gebruikers zich aanmelden bij Azure met behulp van de referenties van hun organisatie domein. Hiervoor is een RADIUS-server vereist die met de AD-server kan worden geïntegreerd. Organisaties kunnen ook gebruikmaken van de bestaande RADIUS-implementatie.
 
De RADIUS-server kan on-premises of in uw Azure VNet aanwezig zijn. Tijdens de verificatie fungeert de VPN-gateway als een doorgangs-en doorstuur verificatie berichten tussen de RADIUS-server en het apparaat waarmee de verbinding wordt gemaakt. Het is belang rijk dat de VPN-gateway de RADIUS-server kan bereiken. Als de RADIUS-server zich lokaal bevindt, is een VPN-site-naar-site-verbinding van Azure naar de on-premises site vereist.

Naast Active Directory kan een RADIUS-server ook worden geïntegreerd met andere externe identiteits systemen. Hiermee opent u veel verificatie opties voor punt-naar-site-Vpn's, met inbegrip van MFA-opties. Raadpleeg de documentatie van de leverancier van de RADIUS-server voor de lijst met identiteits systemen waarmee IT kan worden geïntegreerd.

![Verbindings diagram-RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Alleen een VPN site-naar-site-verbinding kan worden gebruikt om on-premises verbinding te maken met een RADIUS-server. Er kan geen ExpressRoute-verbinding worden gebruikt.
>
>

## <a name="before-beginning"></a><a name="before"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

U kunt de volgende voorbeeldwaarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel. U kunt de stappen gebruiken als een overzicht en de waarden ongewijzigd gebruiken, of u kunt ze wijzigen zodat ze overeenkomen met uw omgeving.

* **Naam: VNet1**
* **Adresruimte: 192.168.0.0/16** en **10.254.0.0/16**<br>Voor dit voorbeeld gebruiken we meer dan één adresruimte om te verduidelijken dat deze configuratie met meerdere adresruimten werkt. Meerdere adresruimten zijn echter niet vereist voor deze configuratie.
* **Subnetnaam: front-end**
  * **Subnetadresbereik: 192.168.1.0/24**
* **Subnetnaam: BackEnd**
  * **Subnetadresbereik: 10.254.1.0/24**
* **Subnetnaam: GatewaySubnet**<br>De naam van het subnet *GatewaySubnet* is verplicht voor een goede werking van de VPN-gateway.
  * **Adresbereik GatewaySubnet: 192.168.200.0/24** 
* **VPN-client adres groep: 172.16.201.0/24**<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de VPN-clientadresgroep.
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep: TestRG**
* **Locatie: VS-Oost**
* **DNS-server: IP-adres** van de DNS-server die u wilt gebruiken voor naam omzetting voor uw VNet. (optioneel)
* **Gatewaynaam: Vnet1GW**
* **Openbare IP-naam: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. de variabelen instellen

Declareer de waarden die u wilt gebruiken. Gebruik het volgende voorbeeld, en vervang zo nodig de waarden door uw eigen waarden. Als u uw Power shell/Cloud Shell-sessie op een wille keurig moment tijdens de oefening sluit, kopieert en plakt u de waarden opnieuw om de variabelen opnieuw te declareren.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> de resource groep, het VNet en het open bare IP-adres maken

Met de volgende stappen maakt u een resource groep en een virtueel netwerk in de resource groep met drie subnetten. Bij het vervangen van waarden is het belang rijk dat u de naam van het subnet van de gateway altijd ' GatewaySubnet '. Als u een andere naam kiest, mislukt het maken van de gateway.

1. Een resourcegroep maken.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Maak de subnetconfiguraties voor het virtuele netwerk, noem deze *FrontEnd*, *BackEnd* en *GatewaySubnet*. Deze voorvoegsels moeten deel uitmaken van de VNet-adresruimte die u hebt opgegeven.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Maak het virtuele netwerk.

   In dit voorbeeld is de serverparameter-DnsServer optioneel. Het opgeven van een waarde betekent niet dat er een nieuwe DNS-server wordt gemaakt. Het IP-adres van de DNS-server dat u opgeeft, moet het adres zijn van een DNS-server die de namen kan omzetten voor de resources waarmee u verbinding maakt vanuit uw VNet. Voor dit voorbeeld hebben we een privé-IP-adres gebruikt, maar het is zeer onwaarschijnlijk dat dit het IP-adres van uw DNS-server is. Zorg ervoor dat u uw eigen waarden gebruikt. De waarde die u opgeeft, wordt gebruikt door de resources die u implementeert op het VNet, niet door de P2S-verbinding.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Een VPN Gateway moet een openbaar IP-adres hebben. U vraagt eerst de resource van het IP-adres aan en verwijst hier vervolgens naar bij het maken van uw virtuele netwerkgateway. Het IP-adres wordt dynamisch aan de resource toegewezen wanneer de VPN Gateway wordt gemaakt. VPN Gateway ondersteunt momenteel alleen *dynamische* toewijzing van openbare IP-adressen. U kunt geen toewijzing van een statisch openbaar IP-adres aanvragen. Dit betekent echter niet dat het IP-adres wordt gewijzigd nadat het aan uw VPN Gateway is toegewezen. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

   Geef de variabelen op om een dynamisch toegewezen openbaar IP-adres aan te vragen.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> uw RADIUS-server instellen

Voordat u de gateway van het virtuele netwerk maakt en configureert, moet uw RADIUS-server correct worden geconfigureerd voor authenticatie.

1. Als er geen RADIUS-server is geïmplementeerd, implementeert u er een. Raadpleeg de installatie handleiding van uw RADIUS-leverancier voor implementaties tappen.  
2. Configureer de VPN-gateway als een RADIUS-client op de RADIUS. Wanneer u deze RADIUS-client toevoegt, geeft u de GatewaySubnet van het virtuele netwerk op die u hebt gemaakt. 
3. Zodra de RADIUS-server is ingesteld, haalt u het IP-adres van de RADIUS-server en het gedeelde geheim op dat RADIUS-clients moeten gebruiken om met de RADIUS-server te communiceren. Als de RADIUS-server zich in het Azure-VNet bevindt, gebruikt u het CA-IP-adres van de RADIUS-server-VM.

Het artikel [Network Policy Server (NPS)](/windows-server/networking/technologies/nps/nps-top) bevat richt lijnen over het configureren van een Windows RADIUS-server (NPS) voor AD-domein authenticatie.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> de VPN-gateway maken

Configureer en maak de VPN-gateway voor uw VNet.

* De-gateway type moet VPN zijn en-VpnType moet RouteBased zijn.
* Het kan tot 45 minuten duren voordat een VPN-gateway is voltooid, afhankelijk van de [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)   die u selecteert.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> de adres groep voor de RADIUS-server en de client toevoegen
 
* De-RadiusServer kan worden opgegeven met de naam of IP-adres. Als u de naam en de server on-premises opgeeft, kan de VPN-gateway de naam mogelijk niet omzetten. Als dat het geval is, is het beter om het IP-adres van de server op te geven. 
* De-RadiusSecret moet overeenkomen met wat is geconfigureerd op uw RADIUS-server.
* De-VpnClientAddressPool is het bereik van waaruit de verbinding met VPN-clients een IP-adres ontvangt. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Zorg ervoor dat er een grote adres groep is geconfigureerd.  

1. Maak een beveiligde teken reeks voor het RADIUS-geheim.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. U wordt gevraagd om het RADIUS-geheim in te voeren. De tekens die u invoert, worden niet weer gegeven en worden in plaats daarvan vervangen door het teken ' * '.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Voeg de adres groep voor de VPN-client en de RADIUS-server toe.

   Voor SSTP-configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Voor OpenVPN-® configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Voor IKEv2-configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Voor SSTP en IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Als u **twee** RADIUS-servers wilt opgeven, gebruikt u de volgende syntaxis. Wijzig de waarde **-vpnclientprotocol toegevoegd** naar wens

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> het configuratie pakket voor de VPN-client downloaden en de VPN-client instellen

Met de VPN-client configuratie kunnen apparaten verbinding maken met een VNet via een P2S-verbinding. Als u een VPN-client configuratie pakket wilt genereren en de VPN-client wilt instellen, raadpleegt u [een VPN-client configuratie voor RADIUS-verificatie maken](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Maak verbinding met Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Verbinding maken vanaf een Windows-VPN-client

1. Als u met uw VNet wilt verbinden, gaat u op de clientcomputer naar de VPN-verbindingen en zoekt u de VPN-verbinding die u hebt gemaakt. Deze heeft dezelfde naam als het virtuele netwerk. Voer uw domein referenties in en klik op verbinding maken. Er wordt een pop-upbericht met aanvragen van verhoogde rechten weer gegeven. Accepteer deze en voer de referenties in.

   ![VPN-client maakt verbinding met Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. De verbinding is tot stand gebracht.

   ![Verbinding tot stand gebracht](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Verbinding maken vanaf een Mac VPN-client

Zoek in het dialoogvenster Netwerk het clientprofiel dat u wilt gebruiken en klik op **Verbinding maken**.

  ![Mac-verbinding](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>De verbinding verifiëren

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen is een van de adressen binnen het adresbereik van de punt-naar-site-VPN-clientadresgroep die u hebt opgegeven in uw configuratie. De resultaten zijn vergelijkbaar met het volgende voorbeeld:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Zie [problemen met Azure Point-to-site-verbindingen oplossen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor het oplossen van problemen met een P2S-verbinding.

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Controleer of het configuratiepakket voor de VPN-client is gegenereerd nadat de IP-adressen van de DNS-server zijn opgegeven voor het VNet. Als u de IP-adressen van de DNS-server hebt bijgewerkt, genereert en installeert u een nieuw configuratiepakket voor de VPN-client.

* Gebruik de opdracht 'ipconfig' om het IPv4-adres te controleren dat is toegewezen aan de ethernetadapter op de computer waarmee u de verbinding tot stand brengt. Als het IP-adres zich binnen het adresbereik bevindt van het VNet waarmee u verbinding maakt of binnen het adresbereik van uw VPNClientAddressPool, wordt dit een overlappende adresruimte genoemd. Als uw adresruimte op deze manier overlapt, kan het netwerkverkeer Azure niet bereiken en blijft het in het lokale netwerk.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

Deze veelgestelde vragen zijn van toepassing op P2S met behulp van RADIUS-verificatie

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.
