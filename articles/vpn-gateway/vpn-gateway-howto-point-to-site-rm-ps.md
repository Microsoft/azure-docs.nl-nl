---
title: 'Verbinding maken met een VNet vanaf een computer-P2S VPN en systeem eigen Azure-certificaat verificatie: Power shell'
description: Verbind Windows-en macOS-clients veilig met een virtueel Azure-netwerk met behulp van P2S en zelfondertekende of door certificerings instanties uitgegeven certificaten. In dit artikel wordt PowerShell gebruikt.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660403"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Een punt-naar-site-VPN-verbinding met een VNet configureren met behulp van systeem eigen Azure-certificaat verificatie: Power shell

Dit artikel helpt u bij het veilig verbinden van afzonderlijke clients met Windows, Linux of macOS naar een Azure-VNet. Punt-naar-site-VPN-verbindingen zijn nuttig wanneer u verbinding wilt maken met uw VNet vanaf een externe locatie, zoals wanneer u thuis of op een conferentie werkt. U kunt P2S ook in plaats van een site-naar-site-VPN gebruiken wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Voor Point-to-site-verbindingen is geen VPN-apparaat of een openbaar IP-adres vereist. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Verbinding maken tussen een computer en een Azure VNet-Point-to-site-verbindings diagram":::

Zie [about Point-to-site VPN](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site-VPN. Als u deze configuratie wilt maken met behulp van de Azure Portal, raadpleegt u [een punt-naar-site-VPN configureren met behulp van de Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Veel van de stappen in dit artikel kunnen gebruikmaken van de Azure Cloud Shell. U kunt Cloud Shell echter niet gebruiken om certificaten te genereren. Als u de open bare sleutel van het basis certificaat ook wilt uploaden, moet u Azure PowerShell lokaal of op de Azure Portal gebruiken.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. aanmelden

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. Declareer variabelen

We gebruiken variabelen voor dit artikel, zodat u gemakkelijk de waarden kunt wijzigen die op uw eigen omgeving moeten worden toegepast zonder dat u de voor beelden zelf hoeft te wijzigen. Declareer de waarden die u wilt gebruiken. U kunt het volgende voor beeld gebruiken om de waarden te vervangen wanneer dat nodig is. Als u uw Power shell/Cloud Shell-sessie op een wille keurig moment tijdens de oefening sluit, kopieert en plakt u de waarden opnieuw om de variabelen opnieuw te declareren.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. een VNet configureren

1. Een resourcegroep maken.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Maak de subnet-configuraties voor het virtuele netwerk, noem deze *frontend* en *GatewaySubnet*. Deze voorvoegsels moeten deel uitmaken van de VNet-adresruimte die u hebt opgegeven.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Maak het virtuele netwerk.

   In dit voorbeeld is de serverparameter-DnsServer optioneel. Het opgeven van een waarde betekent niet dat er een nieuwe DNS-server wordt gemaakt. Het IP-adres van de DNS-server dat u opgeeft, moet het adres zijn van een DNS-server die de namen kan omzetten voor de resources waarmee u verbinding maakt vanuit uw VNet. In dit voorbeeld wordt een privé-IP-adres gebruikt, maar het is zeer onwaarschijnlijk dat dit het IP-adres van uw DNS-server is. Zorg ervoor dat u uw eigen waarden gebruikt. De waarde die u opgeeft, wordt gebruikt door de bronnen die u in het VNet implementeert, niet door de P2S-verbinding of de VPN-client.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Geef de variabelen op voor het virtuele netwerk dat u hebt gemaakt.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Een VPN Gateway moet een openbaar IP-adres hebben. U vraagt eerst de resource van het IP-adres aan en verwijst hier vervolgens naar bij het maken van uw virtuele netwerkgateway. Het IP-adres wordt dynamisch aan de resource toegewezen wanneer de VPN Gateway wordt gemaakt. VPN Gateway ondersteunt momenteel alleen *dynamische* toewijzing van openbare IP-adressen. U kunt geen toewijzing van een statisch openbaar IP-adres aanvragen. Dit betekent echter niet dat het IP-adres verandert nadat het aan uw VPN-gateway is toegewezen. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

   Vraag een dynamisch toegewezen openbaar IP-adres aan.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. de VPN-gateway maken

In deze stap configureert en maakt u de virtuele netwerk gateway voor uw VNet.

* De -GatewayType moet **Vpn** zijn en het -VpnType moet **RouteBased** zijn.
* -VpnClientProtocol wordt gebruikt om de soorten tunnels op te geven die u wilt inschakelen. De tunnel opties zijn **openvpn, SSTP** en **IKEv2**. U kunt ervoor kiezen om een van deze of een ondersteunde combi natie in te scha kelen. Als u meerdere typen wilt inschakelen, geeft u de namen op, gescheiden door een komma. OpenVPN en SSTP kunnen niet samen worden ingeschakeld. De strongSwan-client op Android en Linux en de systeemeigen IKEv2 VPN-client op iOS en OS x gebruiken alleen de IKEv2-tunnel om verbinding te maken. Windows-clients proberen eerst IKEv2. Als daarmee geen verbinding kan worden gemaakt, vallen ze terug op SSTP. U kunt de OpenVPN-client gebruiken om verbinding te maken met het tunnel type OpenVPN.
* De SKU Basic van de virtuele netwerk gateway biedt geen ondersteuning voor IKEv2-, OpenVPN-of RADIUS-verificatie. Als u van plan bent Mac-clients verbinding te laten maken met uw virtuele netwerk, moet u de basis-SKU niet gebruiken.
* Een VPN-gateway wordt binnen maximaal 45 minuten voltooid. De daadwerkelijke instelduur hangt af van de [gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md) die u selecteert. In dit voorbeeld wordt IKEv2 gebruikt.

1. Configureer en maak de virtuele netwerkgateway voor uw VNet. Het duurt ongeveer 45 minuten voordat de gateway is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Zodra de gateway is gemaakt, kunt u deze bekijken in het volgende voor beeld. Als u Power shell hebt gesloten of als er een time-out is opgetreden tijdens het maken van de gateway, kunt u [de variabelen opnieuw declareren](#declare) .

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. de adres groep voor de VPN-client toevoegen

Nadat de VPN-gateway is gemaakt, kunt u de VPN-clientadrespool toevoegen. De VPN-clientadrespool is het bereik vanwaaruit de VPN-clients een IP-adres ontvangen wanneer er verbinding wordt gemaakt. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken.

In dit voor beeld wordt de adres groep van de VPN-client gedeclareerd als een [variabele](#declare) in een eerdere stap.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. certificaten genereren

>[!IMPORTANT]
> U kunt geen certificaten genereren met behulp van Azure Cloud Shell. U moet een van de methoden gebruiken die in deze sectie worden beschreven. Als u Power shell wilt gebruiken, moet u deze lokaal installeren.
>

Certificaten worden door Azure gebruikt voor het verifiëren van VPN-clients voor punt-naar-site-Vpn's. U uploadt de informatie van de openbare sleutel over het basiscertificaat naar Azure. De openbare sleutel wordt dan beschouwd als 'vertrouwd'. Clientcertificaten moeten worden gegenereerd op basis van het vertrouwde basiscertificaat en geïnstalleerd op elke clientcomputer. Dit gebeurt in het certificaatarchief Certificates-Current user/Personal. Het certificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet. 

Als u zelfondertekende certificaten gebruikt, moeten ze worden gemaakt met behulp van specifieke parameters. U kunt een zelfondertekend certificaat maken met behulp van de instructies voor [PowerShell en Windows 10](vpn-gateway-certificates-point-to-site.md). Als u niet beschikt over Windows 10, kunt u ook [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) gebruiken. Het is belangrijk dat u de stappen in de instructies opvolgt bij het maken van zelfondertekende basiscertificaten en clientcertificaten. Anders zijn de certificaten die u maakt, niet compatibel met P2S-verbindingen en treedt er een verbindingsfout op.

### <a name="root-certificate"></a><a name="cer"></a>Basis certificaat

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Nadat u het basis certificaat hebt gemaakt, [exporteert](vpn-gateway-certificates-point-to-site.md#cer) u de gegevens van het open bare certificaat (niet de persoonlijke sleutel) als een base64-gecodeerd X. 509. cer-bestand.

### <a name="client-certificate"></a><a name="generate"></a>Client certificaat

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Nadat u het client certificaat hebt gemaakt, [exporteert](vpn-gateway-certificates-point-to-site.md#clientexport) u het. Het client certificaat wordt gedistribueerd naar de client computers die verbinding maken.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. de gegevens van de open bare sleutel van het basis certificaat uploaden

Controleer of het maken van de VPN-gateway is voltooid. Als dat het geval is, uploadt u het CER-bestand (met de gegevens van de openbare sleutel) voor een vertrouwd basiscertificaat naar Azure. Nadat het CER-bestand is geüpload, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren. Indien nodig kunt u later aanvullende vertrouwde basiscertificaatbestanden uploaden (maximaal 20).

>[!NOTE]
> U kunt het CER-bestand niet uploaden met behulp van Azure Cloud Shell. U kunt Power shell lokaal op uw computer gebruiken of u kunt de [Azure Portal stappen](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)gebruiken.
>

1. Declareer de variabele voor uw certificaatnaam, waarbij u de waarde vervangt door uw eigen waarde.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Vervang het pad naar het bestand door uw eigen pad en voer vervolgens de cmdlets uit.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Upload de gegevens van de openbare sleutel naar Azure. Zodra de certificaat gegevens zijn geüpload, beschouwt Azure deze als een vertrouwd basis certificaat. Zorg ervoor dat u Power shell lokaal op uw computer uitvoert, of dat u in plaats daarvan de [Azure Portal stappen](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)kunt gebruiken. U kunt niet uploaden met behulp van Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. een geëxporteerd client certificaat installeren

De volgende stappen helpen u te installeren op een Windows-client. Zie [een client certificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor aanvullende clients en meer informatie.

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Zorg ervoor dat het certificaat is geëxporteerd als een PFX-bestand, samen met de volledige certificaatketen (dit is de standaardinstelling). Anders zijn de gegevens van het basiscertificaat niet aanwezig op de clientcomputer en kan de client niet worden geverifieerd.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. de VPN-client configureren

In deze sectie configureert u de systeem eigen client voor de computer om verbinding te maken met de virtuele netwerk gateway. Als u bijvoorbeeld naar de VPN-instellingen op uw Windows-computer gaat, kunt u VPN-verbindingen toevoegen. Een punt-naar-site-verbinding vereist specifieke configuratie-instellingen. Deze stappen helpen u bij het maken van een pakket met de specifieke instellingen die de systeem eigen VPN-client nodig heeft om verbinding te kunnen maken met het virtuele netwerk via een punt-naar-site-verbinding.

U kunt de volgende snelle voor beelden gebruiken om het client configuratie pakket te genereren en te installeren. Zie [VPN-client configuratie bestanden maken en installeren](point-to-site-vpn-client-configuration-azure-cert.md)voor meer informatie over de pakket inhoud en aanvullende instructies voor het genereren en installeren van VPN-client configuratie bestanden.

Als u uw variabelen opnieuw moet declareren, kunt u deze [hier](#declare)vinden.

### <a name="to-generate-configuration-files"></a>Configuratie bestanden genereren

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Het client configuratie pakket installeren

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. verbinding maken met Azure

### <a name="windows-vpn-client"></a>Windows VPN-client

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN-client

Zoek in het dialoogvenster Netwerk het clientprofiel dat u wilt gebruiken en klik op **Verbinding maken**.
Controleer de [installatie-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) voor gedetailleerde instructies. Als u problemen ondervindt bij het verbinding maken, controleert u of de gateway van het virtuele netwerk geen basis-SKU gebruikt. Basis-SKU wordt niet ondersteund voor Mac-clients.

  ![Mac-verbinding](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Een verbinding controleren

Deze instructies zijn van toepassing op Windows-clients.

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen, is een van de adressen in de adres groep van de punt-naar-site-VPN-client die u hebt opgegeven in uw configuratie. De resultaten zijn vergelijkbaar met het volgende voorbeeld:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

Deze instructies zijn van toepassing op Windows-clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Controleer of het configuratiepakket voor de VPN-client is gegenereerd nadat de IP-adressen van de DNS-server zijn opgegeven voor het VNet. Als u de IP-adressen van de DNS-server hebt bijgewerkt, genereert en installeert u een nieuw configuratiepakket voor de VPN-client.

* Gebruik de opdracht 'ipconfig' om het IPv4-adres te controleren dat is toegewezen aan de ethernetadapter op de computer waarmee u de verbinding tot stand brengt. Als het IP-adres zich binnen het adresbereik bevindt van het VNet waarmee u verbinding maakt of binnen het adresbereik van uw VPNClientAddressPool, wordt dit een overlappende adresruimte genoemd. Als uw adresruimte op deze manier overlapt, kan het netwerkverkeer Azure niet bereiken en blijft het in het lokale netwerk.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Een basiscertificaat toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet worden geverifieerd, en kunnen ze dus geen verbinding maken. Als u wilt dat clients kunnen worden geverifieerd en verbinding kunnen maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat wordt vertrouwd (is geüpload) in Azure. Voor deze stappen zijn Azure PowerShell-cmdlets die lokaal op uw computer zijn geïnstalleerd (niet Azure Cloud Shell) vereist. U kunt ook de Azure Portal gebruiken om basis certificaten toe te voegen.

**Toevoegen:**

U kunt maximaal 20 CER-basiscertificaatbestanden toevoegen aan Azure. De volgende stappen helpen u bij het toevoegen van een basis certificaat. 

1. Bereid het CER-bestand voor dat u wilt uploaden:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Upload het bestand. U kunt slechts één bestand tegelijk uploaden.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Controleren of het certificaatbestand is geüpload:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Verwijderen:**

1. Declareer de variabelen. Wijzig de variabelen in het voor beeld zodat deze overeenkomen met het certificaat dat u wilt verwijderen.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Verwijder het certificaat.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Gebruik het volgende voorbeeld om te controleren of het certificaat is verwijderd.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Een client certificaat intrekken of herstellen

U kunt clientcertificaten intrekken. Met de certificaatintrekkingslijst kunt u de punt-naar-site-connectiviteit selectief weigeren op basis van afzonderlijke client certificaten. Dit is anders van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

**Intrekken:**

1. Haal de vingerafdruk voor het clientcertificaat op. Zie [de vinger afdruk van een certificaat ophalen](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)voor meer informatie.

1. Kopieer de gegevens naar een teksteditor en verwijder alle spaties, zodat u een doorlopende tekenreeks overhoudt. Deze reeks wordt in de volgende stap gedeclareerd als een variabele.

1. Declareer de variabelen. Zorg ervoor dat u de vingerafdruk declareert die u in de vorige stap hebt opgehaald.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Voeg de vingerafdruk toe aan de lijst met ingetrokken certificaten. U ziet Geslaagd als de vingerafdruk is toegevoegd.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Controleer of de vingerafdruk is toegevoegd aan de certificaatintrekkingslijst.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Nadat de vingerafdruk is toegevoegd, kan het certificaat niet langer worden gebruikt om te verbinden. Clients die verbinding proberen te maken met het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

**Opnieuw invoeren:**

U kunt een clientcertificaat opnieuw activeren door de vingerafdruk te verwijderen uit de lijst met ingetrokken clientcertificaten.

1. Declareer de variabelen. Zorg ervoor dat u de juiste vingerafdruk declareert voor het certificaat dat u opnieuw wilt activeren.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Verwijder de vingerafdruk van het certificaat uit de lijst met ingetrokken certificaten.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Controleer of de vingerafdruk is verwijderd uit de lijst met ingetrokken vingerafdrukken.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Veelgestelde vragen over punt-naar-site

Zie de [VPN gateway punt-naar-site-Veelgestelde vragen](vpn-gateway-vpn-faq.md#P2S) voor meer informatie over punt-naar-site.

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting: Azure point-to-site connection problemens](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).