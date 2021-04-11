---
title: Poorten naar een virtuele machine openen met Azure PowerShell
description: Meer informatie over het openen van een poort/het maken van een eind punt voor uw VM met behulp van Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8390b5c779e6aa053e1af2754c436dd51e410b06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550413"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-using-powershell"></a>Poorten en eind punten openen voor een virtuele machine met behulp van Power shell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snelle opdrachten
Als u een netwerk beveiligings groep en ACL-regels wilt maken, moet u [de nieuwste versie van Azure PowerShell geïnstalleerd](/powershell/azure/)hebben. U kunt [deze stappen ook uitvoeren met behulp van de Azure Portal](nsg-quickstart-portal.md).

Meld u aan bij uw Azure-account:

```powershell
Connect-AzAccount
```

Vervang in de volgende voor beelden parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myNetworkSecurityGroup* en *myVnet*.

Maak een regel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). In het volgende voor beeld wordt een regel gemaakt met de naam *myNetworkSecurityGroupRule* om *TCP* -verkeer toe te staan op poort *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Vervolgens maakt u de netwerk beveiligings groep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) en wijst u de http-regel die u zojuist hebt gemaakt als volgt toe. In het volgende voor beeld wordt een netwerk beveiligings groep gemaakt met de naam *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

We gaan nu uw netwerk beveiligings groep toewijzen aan een subnet. In het volgende voor beeld wordt een bestaand virtueel netwerk met de naam *myVnet* toegewezen aan de variabele *$vnet* met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Koppel uw netwerk beveiligings groep aan uw subnet met [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig). In het volgende voor beeld wordt het subnet met de naam *mySubnet* gekoppeld aan de netwerk beveiligings groep:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Werk tot slot het virtuele netwerk bij met [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) om de wijzigingen van kracht te laten worden:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Meer informatie over netwerk beveiligings groepen
Met de snelle opdrachten kunt u aan de slag gaan met verkeer dat naar uw virtuele machine wordt gestroomd. Netwerk beveiligings groepen bieden veel fantastische functies en granulatie voor het beheren van de toegang tot uw resources. U kunt hier meer lezen over het [maken van een netwerk beveiligings groep en ACL-regels](tutorial-virtual-network.md#secure-network-traffic).

Voor Maxi maal beschik bare webtoepassingen moet u uw virtuele machines achter een Azure Load Balancer plaatsen. De load balancer distribueert verkeer naar Vm's, met een netwerk beveiligings groep die verkeer filtering biedt. Zie [Load Balancing Linux virtual machines in azure (Engelstalig) voor meer informatie over het maken van een Maxi maal beschik bare toepassing](tutorial-load-balancer.md).

## <a name="next-steps"></a>Volgende stappen
In dit voor beeld hebt u een eenvoudige regel gemaakt om HTTP-verkeer toe te staan. In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:

* [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Wat is een netwerk beveiligings groep?](../../virtual-network/network-security-groups-overview.md)
* [Overzicht van Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
