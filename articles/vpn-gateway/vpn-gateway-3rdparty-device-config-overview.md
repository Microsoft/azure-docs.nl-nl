---
title: VPN-configuraties van partners voor verbinding maken met Azure VPN-gateways
description: In dit artikel vindt u een overzicht van VPN-configuraties van partners voor het maken van verbinding met Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880149"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Overzicht van configuraties van VPN-apparaten voor partners
Dit artikel bevat een overzicht van het configureren van on-premises VPN-apparaten om verbinding te maken met Azure VPN-gateways. Een voor beeld van een virtueel Azure-netwerk en een VPN-gateway Setup wordt gebruikt om te laten zien hoe u verbinding kunt maken met verschillende on-premises VPN-configuraties met dezelfde para meters.



## <a name="device-requirements"></a>Vereisten voor apparaten
Azure VPN-gateways gebruiken standaard IPsec/IKE-protocol suites voor VPN-tunnels van het site-naar-site (S2S). Zie [over VPN-apparaten](vpn-gateway-about-vpn-devices.md)voor een lijst met IPSec/IKE-para meters en cryptografische algoritmen voor Azure VPN-gateways. U kunt ook de exacte algoritmen en belangrijkste sterke punten voor een specifieke verbinding opgeven, zoals wordt beschreven in [informatie over cryptografische vereisten](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Eén VPN-tunnel
De eerste configuratie in het voor beeld bestaat uit één S2S VPN-tunnel tussen een Azure VPN-gateway en een on-premises VPN-apparaat. U kunt eventueel de [Border Gateway Protocol (BGP) configureren via de VPN-tunnel](#bgp).

![Diagram van één S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Zie [een site-naar-site-verbinding configureren](./tutorial-site-to-site-portal.md)voor stapsgewijze instructies voor het instellen van één VPN-tunnel. In de volgende secties worden de verbindings parameters voor de voorbeeld configuratie opgegeven en wordt een Power shell-script geboden waarmee u aan de slag kunt.

### <a name="connection-parameters"></a>Verbindingsparameters
In deze sectie vindt u de para meters voor de voor beelden die in de vorige secties worden beschreven.

| **Parameter**                | **Waarde**                    |
| ---                          | ---                          |
| Adres voorvoegsels van virtuele netwerken        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN-gateway-IP         | Azure VPN Gateway IP         |
| On-premises adres voorvoegsels | 10.51.0.0/16<br>10.52.0.0/16 |
| IP-adres van on-premises VPN-apparaat    | IP-adres van on-premises VPN-apparaat    |
| * BGP ASN van virtueel netwerk                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP-peer-IP     | 10.52.255.254                |

\* Optionele para meter voor alleen BGP.

### <a name="sample-powershell-script"></a>Power shell-voorbeeld script
Deze sectie bevat een voorbeeld script om aan de slag te gaan. Zie [een S2S-VPN-verbinding maken met behulp van Power shell](vpn-gateway-create-site-to-site-rm-powershell.md)voor gedetailleerde instructies.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>Beschrijving Aangepast IPsec/IKE-beleid gebruiken met UsePolicyBasedTrafficSelectors
Als uw VPN-apparaten geen ondersteuning bieden voor een-op-geen-verkeer selecters, zoals op route gebaseerde of VTI configuraties, maakt u een aangepast IPsec/IKE-beleid met de optie [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

> [!IMPORTANT]
> U moet een IPsec/IKE-beleid maken om de **UsePolicyBasedTrafficSelectors** -optie voor de verbinding in te scha kelen.


Met het voorbeeld script maakt u een IPsec/IKE-beleid met de volgende algoritmen en para meters:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA-levens duur van 7.200 seconden en 20.480.000 KB (20 GB)

Met het script wordt het IPsec/IKE-beleid toegepast en wordt de optie **UsePolicyBasedTrafficSelectors** ingeschakeld op de verbinding.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>Beschrijving BGP gebruiken in S2S VPN-verbinding
Wanneer u de S2S-VPN-verbinding maakt, kunt u eventueel [BGP gebruiken voor de VPN-gateway](vpn-gateway-bgp-resource-manager-ps.md). Deze aanpak heeft twee verschillen:

* De on-premises adres voorvoegsels kunnen één hostadres zijn. Het IP-adres van de on-premises BGP-peer wordt als volgt opgegeven:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Wanneer u de verbinding maakt, moet u de optie **-EnableBGP** instellen op $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Volgende stappen
Zie voor stapsgewijze instructies voor het instellen van actieve VPN-gateways [Active/Active VPN-gateways configureren voor cross-premises en vnet-naar-vnet-verbindingen](vpn-gateway-activeactive-rm-powershell.md).