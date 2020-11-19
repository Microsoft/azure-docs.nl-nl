---
title: Voorbeeld van Azure PowerShell-script - VPN Gateway maken | Microsoft Docs
description: Meer informatie over het gebruik van een PowerShell-script voor het maken van een virtueel netwerk, een netwerksubnet en van een op route gebaseerde VPN-gateway.
services: vpn-gateway
documentationcenter: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: alzam
ms.openlocfilehash: a17cd1dcf9bca52936a8b10fa45045ff88df1b01
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660743"
---
# <a name="create-a-vpn-gateway-with-powershell"></a>Een VPN-gateway maken met PowerShell

Dit script maakt een op route gebaseerde VPN-gateway.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de gemaakte resources niet meer nodig hebt, gebruikt u de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep te verwijderen. Hiermee verwijdert u de resourcegroep en alle resources die deze bevat.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie toe. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u de details van een virtueel netwerk op. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u een subnetconfiguratie. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
|[New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een VPN-gateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee stelt u de subnetconfiguratie voor het virtuele netwerk in. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).