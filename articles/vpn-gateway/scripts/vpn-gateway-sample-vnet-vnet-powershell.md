---
title: Voorbeeld van Azure PowerShell-script - Een VPN tussen VNet configureren | Microsoft Docs
description: Meer informatie over het gebruik van een PowerShell-script voor het configureren van een site-naar-site en VNet-naar-VNet VPN-gatewayverbinding.
services: vpn-gateway
documentationcenter: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: alzam
ms.openlocfilehash: 949c2b7810bc6b4f5a80b9932427f4aa3565794e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88030348"
---
# <a name="use-powershell-to-configure-a-vnet-to-vnet-vpn-gateway-connection"></a>PowerShell gebruiken voor het configureren van een VPN-gatewayverbinding tussen VNets

Met dit script worden twee virtuele netwerken met elkaar verbonden via het verbindingstype VNet-naar-VNet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables for VNET 1
  $RG1 = "TestRG1"
  $VNetName1  = "VNet1"
  $FESubName1 = "FrontEnd"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.1.0.0/16"
  $FESubPrefix1 = "10.1.0.0/24"
  $GWSubPrefix1 = "10.1.255.0/27"
  $Location1 = "EastUS"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconfig1"
  $Connection12 = "VNet1toVNet2"

# Declare variables for VNET 2  
  $RG2 = "TestRG2"
  $VNetName2  = "VNet2"
  $FESubName2 = "FrontEnd"
  $GWSubName2 = "GatewaySubnet"
  $VNetPrefix21 = "10.2.0.0/16"
  $FESubPrefix2 = "10.2.0.0/24"
  $GWSubPrefix2 = "10.2.255.0/27"
  $Location2 = "EastUS"
  $GWName2 = "VNet2GW"
  $GWIPName2 = "VNet2GWIP"
  $GWIPconfName2 = "gwipconfig2"
  $Connection21 = "VNet2toVNet1"

# Create first resource group
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create a virtual network 1
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName $RG1 `
  -Location $Location1 `
  -Name $VNetName1 `
  -AddressPrefix $VNetPrefix11

# Create a subnet configuration
Add-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for virtual network 1
$virtualNetwork1 | Set-AzVirtualNetwork

# Add a gateway subnet
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for the virtual network
$virtualNetwork1 | Set-AzVirtualNetwork

# Request a public IP address
$gwpip1= New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -VirtualNetwork $vnet1
$gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
 -Location $Location1 -IpConfigurations $gwipconfig1 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 

# Create the second resource group
New-AzResourceGroup -Name $RG2 -Location $Location2

# Create a virtual network 2
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName $RG2 `
  -Location $Location2 `
  -Name $VNetName2 `
  -AddressPrefix $VNetPrefix21

# Create a subnet configuration
Add-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for virtual network 2
$virtualNetwork2 | Set-AzVirtualNetwork

# Add a gateway subnet
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for the virtual network
$virtualNetwork2 | Set-AzVirtualNetwork

# Request a public IP address
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -VirtualNetwork $vnet2
$gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 `
 -Location $Location2 -IpConfigurations $gwipconfig2 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1

# Create the connections
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 `
-VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
 ```
 
## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de gemaakte resources niet meer nodig hebt, gebruikt u de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep te verwijderen. Hiermee verwijdert u de resourcegroep en alle resources die deze bevat.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
Remove-AzResourceGroup -Name TestRG2
```
 
 
## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u de details van een virtueel netwerk op. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Hiermee haalt u de gatewaydetails van een virtueel netwerk op. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u de configuratiedetails van het subnet van een virtueel netwerk op. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u een subnetconfiguratie. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een nieuwe gateway-IP-configuratie. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een VPN-gateway. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Een VNet-met-VNet-verbinding maken. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee stelt u de subnetconfiguratie voor het virtuele netwerk in. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Hiermee stelt u de configuratie voor de VPN-gateway in. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).
