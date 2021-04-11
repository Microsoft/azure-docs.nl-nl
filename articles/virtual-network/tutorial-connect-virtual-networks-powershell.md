---
title: Verbinding maken met virtuele netwerken met VNet-peering-Azure PowerShell
description: In dit artikel leert u hoe u virtuele netwerken kunt verbinden met virtuele netwerk peering met behulp van Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5214dbe692584c8b1df2007019c8108fd56dfa38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059340"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuele netwerken verbinden met virtuele netwerk peering met behulp van Power shell

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In dit artikel leert u het volgende:

* Twee virtuele netwerken maken
* Twee virtuele netwerken koppelen met virtueel-netwerkpeering
* Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
* Communiceren tussen VM's

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

Voordat u een virtueel netwerk maakt, moet u een resource groep maken voor het virtuele netwerk en alle andere resources die in dit artikel zijn gemaakt. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork1* gemaakt met het adres voorvoegsel *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Maak een subnet-configuratie met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voor beeld wordt een subnet-configuratie gemaakt met het adres voorvoegsel 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Schrijf de configuratie van het subnet naar het virtuele netwerk met [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), waarmee het subnet wordt gemaakt:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Een virtueel netwerk maken met een 10.1.0.0/16-adres voorvoegsel en één subnet:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

Maak een peering met [add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). In het volgende voor beeld worden peers *myVirtualNetwork1* ingesteld op *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **PeeringState** is *gestart*. De peering blijft in de *geïnitieerde* status totdat u de peering van *myVirtualNetwork2* naar *myVirtualNetwork1* maakt. Maak een peering van *myVirtualNetwork2* naar *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **PeeringState** is *verbonden*. Azure heeft ook de peering-status van de *myVirtualNetwork1-myVirtualNetwork2-* peering gewijzigd in *verbonden*. Bevestig dat de peering-status voor de *myVirtualNetwork1-myVirtualNetwork2-* peering *is* gewijzigd in verbonden met [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Resources in één virtueel netwerk kunnen niet communiceren met resources in het andere virtuele netwerk totdat de **PeeringState** voor de peerings in beide virtuele netwerken is *verbonden*.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voor beeld wordt een VM gemaakt met de naam *myVm1* in het virtuele netwerk *myVirtualNetwork1* . Met deze `-AsJob` optie wordt de virtuele machine op de achtergrond gemaakt, zodat u verder kunt gaan met de volgende stap. Wanneer u hierom wordt gevraagd, voert u de gebruikers naam en het wacht woord in waarmee u zich wilt aanmelden bij de virtuele machine.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>De tweede VM maken

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met de volgende stappen totdat de virtuele machine door Azure wordt gemaakt en de uitvoer naar Power shell wordt geretourneerd.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

U kunt verbinding maken met het open bare IP-adres van een virtuele machine via internet. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van een virtuele machine op te halen. In het volgende voorbeeld wordt het openbare IP-adres van de VM *myVm1* opgehaald:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Gebruik de volgende opdracht om een sessie met een extern bureau blad te maken met de *myVm1* -VM vanaf uw lokale computer. Vervang `<publicIpAddress>` door het IP-adres dat is geretourneerd met de vorige opdracht.

```
mstsc /v:<publicIpAddress>
```

Er wordt een Remote Desktop Protocol-bestand (. RDP) gemaakt, gedownload naar uw computer en geopend. Voer de gebruikers naam en het wacht woord in (mogelijk moet u **meer opties** selecteren, vervolgens **een ander account gebruiken** om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en klik vervolgens op **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om door te gaan met de verbinding.

Schakel op de *myVm1* -VM de Internet Control Message Protocol (ICMP) in via de Windows Firewall zodat u in een latere stap deze VM kunt pingen vanuit *myVm2* met behulp van Power shell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Hoewel ping wordt gebruikt om te communiceren tussen Vm's in dit artikel, wordt het gebruik van ICMP via de Windows Firewall voor productie-implementaties niet aanbevolen.

Maak verbinding met *myVm2* door op *myVm1* de volgende opdracht in te voeren vanaf een opdrachtprompt:

```
mstsc /v:10.1.0.4
```

Sinds u ping hebt ingeschakeld op *myVm1*, kunt u deze nu pingen op IP-adres van een opdracht prompt op de *myVm2* VM:

```
ping 10.0.0.4
```

U ontvangt vier antwoorden. Verbreek de RDP-sessies met zowel *myVm1* als *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, gebruikt u [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) om de resource groep en alle resources die deze bevat, te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u twee netwerken in dezelfde Azure-regio kunt verbinden met virtuele netwerk peering. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region) en in [ verschillende Azure-abonnementen ](create-peering-different-subscriptions.md#powershell) 'peeren', en peering gebruiken om [netwerkontwerpen met een stertopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) te maken. Zie voor meer informatie over virtueel-netwerkpeering [Peering op virtueel netwerk](virtual-network-peering-overview.md) en [Virtueel-netwerkpeerings beheren](virtual-network-manage-peering.md).

U kunt [uw eigen computer verbinden met een virtueel netwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN en communiceren met resources in een virtueel netwerk of in gekoppelde virtuele netwerken. Zie [script voorbeelden](powershell-samples.md)voor herbruikbare scripts voor het volt ooien van veel van de taken die worden behandeld in de artikelen in het virtuele netwerk.
