---
title: 'Azure VPN Gateway: BGP configureren: Power shell'
description: Dit artikel begeleidt u bij het configureren van BGP met Azure VPN-gateways met behulp van Azure Resource Manager en Power shell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659757"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>BGP configureren op Azure VPN-gateways met behulp van Power shell
Dit artikel begeleidt u stapsgewijs door de stappen voor het inschakelen van BGP op een cross-premises site-naar-site (S2S) VPN-verbinding en een VNet-naar-VNet-verbinding met behulp van het Resource Manager-implementatie model en Power shell.



## <a name="about-bgp"></a>Over BGP
BGP is het standaardprotocol voor routering dat doorgaans op internet wordt gebruikt voor het uitwisselen van routerings- en bereikbaarheidsgegevens tussen twee of meer netwerken. BGP maakt het mogelijk dat Azure VPN Gateways en uw on-premises VPN-apparaten, zogenaamde BGP-peers of neighbors, 'routes' kunnen uitwisselen die beide gateways informeren over de beschikbaarheid en bereikbaarheid voor deze voorvoegsels zodat ze via de juiste gateways of routers communiceren. Met BGP kan ook transitroutering tussen meerdere netwerken worden ingeschakeld door routes die een BGP-gateway leert van één BGP te propageren naar alle andere BGP-peers.

Zie [overzicht van BGP met Azure VPN-gateways](vpn-gateway-bgp-overview.md) voor meer informatie over de voor delen van BGP en om inzicht te krijgen in de technische vereisten en overwegingen voor het gebruik van BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Aan de slag met BGP op Azure VPN-gateways

Dit artikel begeleidt u stapsgewijs door de stappen voor het uitvoeren van de volgende taken:

* [Deel 1: BGP inschakelen op uw Azure VPN-gateway](#enablebgp)
* Deel 2: een cross-premises verbinding tot stand brengen met BGP
* [Deel 3: een VNet-naar-VNet-verbinding maken met BGP](#v2vbgp)

Elk deel van de instructies vormt een basis bouwsteen voor het inschakelen van BGP in uw netwerk verbinding. Als u alle drie de onderdelen hebt voltooid, bouwt u de topologie op zoals weer gegeven in het volgende diagram:

![BGP-topologie](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

U kunt delen samen combi neren om een complexere, multi-hop en Transit netwerk te bouwen dat aan uw behoeften voldoet.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Deel 1: BGP configureren op de Azure-VPN Gateway
Met de configuratie stappen worden de BGP-para meters van de Azure VPN-gateway ingesteld, zoals weer gegeven in het volgende diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Voordat u begint
* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Installeer de Azure Resource Manager Power shell-cmdlets. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie over het installeren van de PowerShell-cmdlets. 

### <a name="step-1---create-and-configure-vnet1"></a>Stap 1: VNet1 maken en configureren
#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen
Voor deze oefening beginnen we met het declareren van onze variabelen. In het volgende voor beeld worden de variabelen gedeclareerd met de waarden voor deze oefening. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie. U kunt deze variabelen gebruiken als u de stappen wilt doorlopen om vertrouwd te raken met dit type configuratie. Wijzig de variabelen en kopieer en plak ze in uw PowerShell-console.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Maak verbinding met uw abonnement en maak een nieuwe resource groep
Als u de Resource Manager-cmdlets wilt gebruiken, zorgt u ervoor dat u overschakelt naar de Power Shell-modus. Zie [Using Windows PowerShell with Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken
In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam TestVNet1 en drie subnetten, een met de naam GatewaySubnet, een met de naam FrontEnd en een back-end. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Stap 2: de VPN Gateway maken voor TestVNet1 met BGP-para meters
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. de configuraties van IP en subnet maken
Vraag om een openbaar IP-adres toe te wijzen aan de gateway die u voor uw VNet gaat maken. U definieert ook de vereiste subnet-en IP-configuraties.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Maak de VPN-gateway met het AS-nummer
Maak de gateway van het virtuele netwerk voor TestVNet1. BGP vereist een Route-Based VPN-gateway en ook de toevoeging para meter,-ASN, om het ASN (als getal) in te stellen voor TestVNet1. Als u de ASN-para meter niet instelt, wordt ASN 65515 toegewezen. Het maken van een gateway kan even duren (30 minuten of langer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. het IP-adres van de Azure BGP-peer verkrijgen
Zodra de gateway is gemaakt, moet u het IP-adres van de BGP-peer verkrijgen op de Azure-VPN Gateway. Dit adres is nodig om de Azure VPN Gateway te configureren als een BGP-peer voor uw on-premises VPN-apparaten.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Met de laatste opdracht worden de bijbehorende BGP-configuraties weer gegeven in de Azure-VPN Gateway; bijvoorbeeld:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Zodra de gateway is gemaakt, kunt u deze gateway gebruiken om een cross-premises verbinding of een VNet-naar-VNet-verbinding met BGP in te richten. In de volgende secties worden de stappen beschreven voor het volt ooien van de oefening.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Deel 2: een cross-premises verbinding tot stand brengen met BGP

Als u een cross-premises verbinding tot stand wilt brengen, moet u een lokale netwerk gateway maken om uw on-premises VPN-apparaat aan te duiden, en een verbinding om de VPN-gateway met de lokale netwerk gateway te verbinden. Hoewel er artikelen zijn die u stapsgewijs door lopen, bevat dit artikel de aanvullende eigenschappen die vereist zijn voor het opgeven van de BGP-configuratie parameters.

![BGP voor cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Zorg ervoor dat u [deel 1](#enablebgp) van deze oefening hebt voltooid voordat u doorgaat.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Stap 1: de lokale netwerk gateway maken en configureren

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Deze oefening gaat verder met het bouwen van de configuratie die in het diagram wordt weer gegeven. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Er zijn enkele dingen die u moet weten over de para meters van de lokale netwerk gateway:

* De lokale netwerk gateway kan zich in dezelfde of een andere locatie en resource groep bevindt als de VPN-gateway. In dit voor beeld worden ze weer gegeven in verschillende resource groepen op verschillende locaties.
* Het voor voegsel dat u moet declareren voor de lokale netwerk gateway is het hostadres van het IP-adres van uw BGP-peer op uw VPN-apparaat. In dit geval is het een/32-voor voegsel van "10.52.255.254/32".
* Als herinnering moet u verschillende BGP-Asn's gebruiken tussen uw on-premises netwerken en Azure VNet. Als ze hetzelfde zijn, moet u uw VNet-ASN wijzigen als uw on-premises VPN-apparaat het ASN al gebruikt voor de peer met andere BGP-neighbors.

Controleer voordat u verdergaat of u nog bent verbonden met Abonnement 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. de lokale netwerk gateway maken voor site5

Zorg ervoor dat u de resource groep maakt als deze niet is gemaakt voordat u de lokale netwerk gateway maakt. Let op de twee extra para meters voor de gateway van het lokale netwerk: ASN en BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2: de VNet-gateway en de lokale netwerk gateway verbinden

#### <a name="1-get-the-two-gateways"></a>1. de twee gateways ophalen

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Maak de TestVNet1-verbinding naar site5

In deze stap maakt u de verbinding van TestVNet1 naar site5. U moet '-EnableBGP $True ' opgeven om BGP in te scha kelen voor deze verbinding. Zoals eerder is besproken, is het mogelijk om zowel BGP-als niet-BGP-verbindingen te hebben voor dezelfde Azure-VPN Gateway. Tenzij BGP is ingeschakeld in de eigenschap Connection, wordt BGP voor deze verbinding niet door Azure ingeschakeld, ook al zijn de BGP-para meters al geconfigureerd op beide gateways.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

In het volgende voor beeld ziet u de para meters die u invoert in het gedeelte BGP-configuratie op uw on-premises VPN-apparaat voor deze oefening:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding wordt na een paar minuten tot stand gebracht en de BGP-peering-sessie wordt gestart zodra de IPsec-verbinding tot stand is gebracht.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Deel 3: een VNet-naar-VNet-verbinding maken met BGP

In deze sectie wordt een VNet-naar-VNet-verbinding met BGP toegevoegd, zoals in het volgende diagram wordt weer gegeven:

![Diagram waarin een V net to V net-verbinding wordt weer gegeven.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

De volgende instructies gaan uit van de vorige stappen. U moet [deel I](#enablebgp) volt ooien om TestVNet1 en de VPN gateway met BGP te maken en te configureren. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Stap 1: TestVNet2 en de VPN-gateway maken

Het is belang rijk om ervoor te zorgen dat de IP-adres ruimte van het nieuwe virtuele netwerk, TestVNet2, geen van uw VNet-bereiken overlapt.

In dit voor beeld maken de virtuele netwerken deel uit van hetzelfde abonnement. U kunt VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. Zie [een vnet-naar-VNet-verbinding configureren](vpn-gateway-vnet-vnet-rm-ps.md)voor meer informatie. Zorg ervoor dat u de $True '-EnableBgpt ' toevoegt bij het maken van de verbindingen om BGP in te scha kelen.

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 maken in de nieuwe resource groep

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Maak de VPN-gateway voor TestVNet2 met BGP-para meters

Vraag een openbaar IP-adres aan dat moet worden toegewezen aan de gateway die u voor uw VNet gaat maken en definieer de vereiste subnet-en IP-configuraties.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Maak de VPN-gateway met het AS-nummer. U moet de standaard-ASN op uw Azure VPN-gateways negeren. De Asn's voor de verbonden VNets moet verschillend zijn om BGP en transit routering in te scha kelen.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Stap 2: de gateways voor TestVNet1 en TestVNet2 verbinden

In dit voor beeld bevinden beide gateways zich in hetzelfde abonnement. U kunt deze stap in dezelfde Power shell-sessie volt ooien.

#### <a name="1-get-both-gateways"></a>1. Haal beide gateways op

Zorg dat u zich aanmeldt bij en verbinding maakt met Abonnement 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. beide verbindingen maken

In deze stap maakt u de verbinding van TestVNet1 naar TestVNet2 en de verbinding van TestVNet2 naar TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Zorg ervoor dat u BGP voor beide verbindingen inschakelt.
> 
> 

Na het volt ooien van deze stappen wordt de verbinding na een paar minuten tot stand gebracht. De BGP-peering-sessie is actief zodra de VNet-naar-VNet-verbinding is voltooid.

Als u alle drie delen van deze oefening hebt voltooid, hebt u de volgende netwerk topologie ingesteld:

![BGP voor VNet-naar-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md) voor de stappen.