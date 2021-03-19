---
title: Azure Firewall met meerdere open bare IP-adressen implementeren met behulp van Azure PowerShell
description: Een firewall met meerdere open bare IP-adressen implementeren om een virtuele hub te beveiligen
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87006957"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Een Azure Firewall met meerdere open bare IP-adressen implementeren

Als u een virtuele hub wilt beveiligen met Azure Firewall, kunt u de firewall met meerdere open bare IP-adressen implementeren met behulp van Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>De firewall implementeren

Gebruik het volgende Azure PowerShell voor beeld om een Azure Firewall te implementeren met meerdere open bare IP-adressen om een virtuele hub te beveiligen.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>Een openbaar IP-adres bijwerken

U kunt Azure PowerShell gebruiken om een openbaar IP-adres voor een Azure Firewall bij te werken. In het volgende voor beeld wordt één openbaar IP-adres uit een firewall verwijderd. Het begint met drie open bare IP-adressen.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Volgende stappen

[Wat is een beveiligde virtuele hub?](secured-virtual-hub.md)