---
title: Actieve en actieve S2S Azure-VPN Gateway verbindingen configureren
description: Dit artikel begeleidt u bij het configureren van actieve verbindingen met Azure VPN-gateways met behulp van Azure Resource Manager en Power shell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440776"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Active-Active S2S VPN-verbindingen configureren met Azure VPN-gateways

Dit artikel begeleidt u stapsgewijs door de stappen voor het maken van actieve, cross-premises en VNet-naar-VNet-verbindingen met behulp van het Resource Manager-implementatie model en Power shell. U kunt ook een actief-actief gateway configureren in het Azure Portal.

## <a name="about-highly-available-cross-premises-connections"></a>Over Maxi maal beschik bare cross-premises verbindingen
Voor een hoge Beschik baarheid van cross-premises en VNet-naar-VNet-connectiviteit moet u meerdere VPN-gateways implementeren en meerdere parallelle verbindingen tot stand brengen tussen uw netwerken en Azure. Bekijk [Maxi maal beschik bare cross-premises en vnet-naar-vnet-connectiviteit](vpn-gateway-highlyavailable.md) voor een overzicht van connectiviteits opties en topologie.

In dit artikel vindt u instructies voor het instellen van een actieve, actieve cross-premises VPN-verbinding en een actieve verbinding tussen twee virtuele netwerken.

* [Deel 1: uw Azure VPN-gateway maken en configureren in de modus actief-actief](#aagateway)
* [Deel 2: lokale cross-premises verbindingen tot stand brengen](#aacrossprem)
* [Deel 3: Active-actief VNet-naar-VNet-verbindingen tot stand brengen](#aav2v)

Als u al een VPN-gateway hebt, kunt u het volgende doen:
* [Een bestaande VPN-gateway bijwerken van actief naar stand-by naar actief-actief, of andersom](#aaupdate)

U kunt deze combi neren om een complexere, Maxi maal beschik bare netwerk topologie te bouwen die aan uw behoeften voldoet.

> [!IMPORTANT]
> De modus actief-actief is beschikbaar voor alle Sku's behalve basis.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Deel 1: actieve en actieve VPN-gateways maken en configureren
Met de volgende stappen wordt uw Azure VPN-gateway geconfigureerd in de modus actief-actief. De belangrijkste verschillen tussen de gateways actief en actief en stand-by:

* U moet twee IP-configuraties met gateways maken met twee open bare IP-adressen
* U moet de vlag EnableActiveActiveFeature instellen
* De gateway-SKU moet VpnGw1, VpnGw2, VpnGw3 of High Performance (verouderde SKU) zijn.

De andere eigenschappen zijn gelijk aan die van de niet-actieve gateways. 

### <a name="before-you-begin"></a>Voordat u begint
* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* U moet de Azure Resource Manager Power shell-cmdlets installeren als u Cloud shell niet wilt gebruiken in uw browser. Zie [overzicht van Azure PowerShell](/powershell/azure/) voor meer informatie over het installeren van de Power shell-cmdlets.

### <a name="step-1---create-and-configure-vnet1"></a>Stap 1: VNet1 maken en configureren
#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Voor deze oefening declareert u eerst de variabelen. Als u de Cloud Shell proberen te gebruiken, wordt u automatisch verbonden met uw account. Als u lokaal Power shell gebruikt, moet u het volgende voor beeld gebruiken om verbinding te maken:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

In het volgende voorbeeld worden de variabelen gedeclareerd met de waarden voor deze oefening. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie. U kunt deze variabelen gebruiken als u de stappen wilt doorlopen om vertrouwd te raken met dit type configuratie. Wijzig de variabelen en kopieer en plak ze in uw PowerShell-console.

```azurepowershell-interactive
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-create-a-new-resource-group"></a>2. een nieuwe resource groep maken

Gebruik het onderstaande voor beeld om een nieuwe resource groep te maken:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken
In onderstaand voorbeeld wordt een virtueel netwerk gemaakt met de naam TestVNet1. Er worden ook drie subnetten gemaakt, GatewaySubnet, FrontEnd en BackEnd. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Stap 2: de VPN-gateway maken voor TestVNet1 met de modus actief-actief
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. de open bare IP-adressen en gateway-IP-configuraties maken
Vraag twee open bare IP-adressen aan die moeten worden toegewezen aan de gateway die u voor uw VNet gaat maken. U definieert ook het vereiste subnet en IP-configuratie.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Maak de VPN-gateway met actief/actief-configuratie
Maak de gateway van het virtuele netwerk voor TestVNet1. Houd er rekening mee dat er twee GatewayIpConfig-vermeldingen zijn en dat de vlag EnableActiveActiveFeature is ingesteld. Het maken van een gateway kan even duren (45 minuten of langer).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Verkrijg de open bare IP-adressen van de gateway en het IP-adres van de BGP-peer
Zodra de gateway is gemaakt, moet u het IP-adres van de BGP-peer verkrijgen op de Azure-VPN Gateway. Dit adres is nodig om de Azure VPN Gateway te configureren als een BGP-peer voor uw on-premises VPN-apparaten.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Gebruik de volgende cmdlets om de twee open bare IP-adressen weer te geven die zijn toegewezen voor uw VPN-gateway en de bijbehorende IP-adressen van de BGP-peer voor elk gateway-exemplaar:

```azurepowershell-interactive
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

De volg orde van de open bare IP-adressen voor de gateway exemplaren en de bijbehorende BGP peering-adressen zijn hetzelfde. In dit voor beeld wordt de gateway-VM met het open bare IP-adres 40.112.190.5 gebruikt als BGP-peering en wordt 10.12.255.5 gebruikt voor de gateway met 138.91.156.129. Deze informatie is nodig bij het instellen van uw on-premises VPN-apparaten die verbinding maken met de Active-Active gateway. De gateway wordt weer gegeven in het onderstaande diagram met alle adressen:

![actief-actief gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Zodra de gateway is gemaakt, kunt u deze gateway gebruiken om actief-actief cross-premises of VNet-naar-VNet-verbindingen tot stand te brengen. In de volgende secties worden de stappen beschreven voor het volt ooien van de oefening.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Deel 2: een actieve en actieve cross-premises verbinding tot stand brengen
Als u een cross-premises verbinding tot stand wilt brengen, moet u een lokale netwerk gateway maken om uw on-premises VPN-apparaat aan te duiden, en een verbinding om de Azure VPN-gateway met de lokale netwerk gateway te verbinden. In dit voor beeld is de Azure VPN-gateway in de modus actief-actief. Als gevolg hiervan, zelfs als er slechts één on-premises VPN-apparaat (lokale netwerk gateway) en één verbindings bron bestaat, maken beide exemplaren van de Azure VPN-gateway gebruik van S2S VPN-tunnels met het on-premises apparaat.

Voordat u doorgaat, moet u [deel 1](#aagateway) van deze oefening hebben voltooid.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Stap 1: de lokale netwerk gateway maken en configureren
#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen
In deze oefening kunt u de configuratie die in het diagram wordt weer gegeven, blijven bouwen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Er zijn enkele dingen die u moet weten over de para meters van de lokale netwerk gateway:

* De lokale netwerk gateway kan zich in dezelfde of een andere locatie en resource groep bevindt als de VPN-gateway. In dit voor beeld worden ze weer gegeven in verschillende resource groepen, maar op dezelfde Azure-locatie.
* Als er slechts één on-premises VPN-apparaat is, zoals hierboven wordt weer gegeven, kan de actieve verbinding met of zonder het BGP-protocol worden uitgevoerd. In dit voor beeld wordt BGP gebruikt voor de cross-premises verbinding.
* Als BGP is ingeschakeld, is het voor voegsel dat u moet declareren voor de lokale netwerk gateway het hostadres van uw IP-adres van de BGP-peer op uw VPN-apparaat. In dit geval is het een/32-voor voegsel van "10.52.255.253/32".
* Als herinnering moet u verschillende BGP-Asn's gebruiken tussen uw on-premises netwerken en Azure VNet. Als ze hetzelfde zijn, moet u uw VNet-ASN wijzigen als uw on-premises VPN-apparaat het ASN al gebruikt voor de peer met andere BGP-neighbors.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. de lokale netwerk gateway maken voor site5
Controleer voordat u verder gaat of u nog bent verbonden met abonnement 1. Maak de resource groep als deze nog niet is gemaakt.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2: de VNet-gateway en de lokale netwerk gateway verbinden
#### <a name="1-get-the-two-gateways"></a>1. de twee gateways ophalen

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Maak de TestVNet1-verbinding naar site5
In deze stap maakt u de verbinding van TestVNet1 naar Site5_1 waarvoor EnableBGP is ingesteld op $True.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN-en BGP-para meters voor uw on-premises VPN-apparaat
In het onderstaande voor beeld ziet u de para meters die u invoert in het gedeelte BGP-configuratie op uw on-premises VPN-apparaat voor deze oefening:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding moet na een paar minuten tot stand worden gebracht en de BGP-peering-sessie wordt gestart zodra de IPsec-verbinding tot stand is gebracht. In dit voor beeld is slechts één on-premises VPN-apparaat geconfigureerd, wat resulteert in het onderstaande diagram:

![actief-actief-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Stap 3: twee on-premises VPN-apparaten verbinden met de actief-actief VPN-gateway
Als u twee VPN-apparaten op hetzelfde on-premises netwerk hebt, kunt u dubbele redundantie bezorgen door de Azure VPN-gateway te verbinden met het tweede VPN-apparaat.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. de tweede lokale netwerk gateway maken voor site5
Het IP-adres van de gateway, het adres voorvoegsel en het BGP-peering adres voor de tweede lokale netwerk gateway mag niet overlappen met de vorige lokale netwerk gateway voor hetzelfde on-premises netwerk.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Verbind de VNet-gateway en de tweede lokale netwerk gateway
Maak de verbinding van TestVNet1 naar Site5_2 waarvoor EnableBGP is ingesteld op $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN-en BGP-para meters voor uw tweede on-premises VPN-apparaat
Hieronder vindt u een lijst met de para meters die u in het tweede VPN-apparaat gaat invoeren:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Zodra de verbinding (tunnels) tot stand is gebracht, hebt u twee redundante VPN-apparaten en tunnels die verbinding maken met uw on-premises netwerk en Azure:

![Dual-redundantie-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Deel 3: een actieve VNet-naar-VNet-verbinding maken
In deze sectie wordt een actief-actief VNet-naar-VNet-verbinding met BGP gemaakt. 

De onderstaande instructies volgen op de hierboven beschreven stappen. U moet [deel 1](#aagateway) volt ooien om TestVNet1 en de VPN gateway met BGP te maken en te configureren. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Stap 1: TestVNet2 en de VPN-gateway maken
Het is belang rijk om ervoor te zorgen dat de IP-adres ruimte van het nieuwe virtuele netwerk, TestVNet2, geen van uw VNet-bereiken overlapt.

In dit voor beeld maken de virtuele netwerken deel uit van hetzelfde abonnement. U kunt VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. Raadpleeg [een vnet-naar-vnet-verbinding configureren](vpn-gateway-vnet-vnet-rm-ps.md) voor meer informatie. Zorg ervoor dat u de $True '-EnableBgpt ' toevoegt bij het maken van de verbindingen om BGP in te scha kelen.

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen
Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```azurepowershell-interactive
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 maken in de nieuwe resource groep

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. de Active-Active VPN-gateway maken voor TestVNet2
Vraag twee open bare IP-adressen aan die moeten worden toegewezen aan de gateway die u voor uw VNet gaat maken. U definieert ook het vereiste subnet en IP-configuratie.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Maak de VPN-gateway met het AS-nummer en de vlag ' EnableActiveActiveFeature '. Houd er rekening mee dat u de standaard-ASN op uw Azure VPN-gateways moet overschrijven. De Asn's voor de verbonden VNets moet verschillend zijn om BGP en transit routering in te scha kelen.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Stap 2: de gateways voor TestVNet1 en TestVNet2 verbinden
In dit voor beeld bevinden beide gateways zich in hetzelfde abonnement. U kunt deze stap in dezelfde Power shell-sessie volt ooien.

#### <a name="1-get-both-gateways"></a>1. Haal beide gateways op
Zorg dat u zich aanmeldt bij en verbinding maakt met Abonnement 1.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. beide verbindingen maken
In deze stap maakt u de verbinding van TestVNet1 naar TestVNet2 en de verbinding van TestVNet2 naar TestVNet1.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Zorg ervoor dat u BGP voor beide verbindingen inschakelt.
> 
> 

Na het volt ooien van deze stappen wordt de verbinding binnen enkele minuten tot stand gebracht en wordt de BGP-peering-sessie actief zodra de VNet-naar-VNet-verbinding is voltooid met dubbele redundantie:

![actief-actief-V2V](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Een bestaande VPN-gateway bijwerken

Wanneer u een actieve stand-by-gateway wijzigt in actief-actief, maakt u een ander openbaar IP-adres en voegt u vervolgens een tweede gateway-IP-configuratie toe. Dit gedeelte helpt u bij het wijzigen van een bestaande Azure VPN-gateway van actief naar stand-by modus actief/actief, of andersom met Power shell. U kunt een gateway ook wijzigen in de Azure Portal op de **configuratie** pagina voor de gateway van uw virtuele netwerk.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Een Active-standby-gateway wijzigen in een Active-Active gateway

In het volgende voor beeld wordt een gateway die actief is op stand-by omgezet in een actief-actief gateway. 

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Vervang de volgende para meters voor de voor beelden in combi natie met de instellingen die u nodig hebt voor uw eigen configuratie en Declareer vervolgens deze variabelen.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Nadat u de variabelen hebt gedeclareerd, kunt u dit voor beeld kopiëren en plakken in uw Power shell-console.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Maak het open bare IP-adres en voeg vervolgens de tweede gateway-IP-configuratie toe

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. de modus actief-actief inschakelen en de gateway bijwerken

In deze stap schakelt u de modus actief-actief in en werkt u de gateway bij. In het voor beeld wordt momenteel een verouderde standaard-SKU gebruikt voor de VPN-gateway. Active-Active biedt echter geen ondersteuning voor de standaard-SKU. Als u de verouderde SKU wilt verg Roten of verkleinen (in dit geval High Performance), geeft u eenvoudigweg de ondersteunde verouderde SKU op die u wilt gebruiken.

* Met deze stap kunt u een verouderde SKU niet wijzigen in een van de nieuwe Sku's. U kunt de grootte van een verouderde SKU alleen wijzigen in een andere ondersteunde verouderde SKU. U kunt de SKU bijvoorbeeld niet wijzigen van Standard in VpnGw1 (zelfs als VpnGw1 wordt ondersteund voor actief-actief), omdat Standard een verouderde SKU is en VpnGw1 een huidige SKU is. Zie [Gateway-sku's](vpn-gateway-about-vpngateways.md#gwsku)voor meer informatie over het wijzigen van de grootte en het migreren van sku's.

* Als u de grootte van een huidige SKU wilt wijzigen, bijvoorbeeld VpnGw1 naar VpnGw3, kunt u dit doen met deze stap omdat de Sku's zich in dezelfde SKU-familie bevinden. Hiervoor gebruikt u de waarde: ```-GatewaySku VpnGw3```

Als u dit in uw omgeving gebruikt, hoeft u de-GatewaySku niet op te geven als u het formaat van de gateway niet hoeft te wijzigen. In deze stap moet u het gateway-object instellen in Power shell om de daad werkelijke update te activeren. Deze update kan 30 tot 45 minuten duren, zelfs als u de grootte van de gateway niet wijzigt.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Een Active-Active gateway wijzigen in een gateway die actief is op stand-by
#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Vervang de volgende para meters voor de voor beelden in combi natie met de instellingen die u nodig hebt voor uw eigen configuratie en Declareer vervolgens deze variabelen.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Nadat u de variabelen hebt gedeclareerd, haalt u de naam op van de IP-configuratie die u wilt verwijderen.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. de gateway-IP-configuratie verwijderen en de modus actief-actief uitschakelen

Gebruik dit voor beeld om de gateway-IP-configuratie te verwijderen en de modus actief-actief uit te scha kelen. U moet het gateway-object in Power shell instellen om de werkelijke update te activeren.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Deze update kan Maxi maal 30 tot 45 minuten duren.

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.
