---
title: Netwerk verkeer omleiden Azure PowerShell | Microsoft Docs
description: In dit artikel leest u hoe u netwerk verkeer met een route tabel met behulp van Power shell kunt routeren.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bd52e8865bb704497740851f6a0e3c886ed9d6d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790184"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Netwerk verkeer routeren met een route tabel met behulp van Power shell

Azure routeert standaard automatisch verkeer tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. De mogelijkheid voor het maken van aangepaste routes is handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In dit artikel leert u het volgende:

* Een routetabel maken
* Een route maken
* Een virtueel netwerk met meerdere subnetten maken
* Een routetabel aan een subnet koppelen
* Een NVA maken voor het routeren van verkeer
* Virtuele machines (VM's) implementeren in verschillende subnetten
* Verkeer van het ene subnet naar het andere leiden via een NVA

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-route-table"></a>Een routetabel maken

Voordat u een route tabel kunt maken, moet u een resource groep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt voor alle resources die in dit artikel zijn gemaakt.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een route tabel met [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). In het volgende voor beeld wordt een route tabel gemaakt met de naam *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Een route maken

Maak een route door het object route tabel op te halen met [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), een route te maken met [add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)en de route configuratie vervolgens naar de route tabel te schrijven met [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken. Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt met het adres voorvoegsel *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Maak drie subnetten door drie subnet configuraties te maken met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voor beeld worden drie subnet configuraties gemaakt voor *open bare*, *particuliere* en *DMZ* -subnetten:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Schrijf de subnet-configuraties naar het virtuele netwerk met [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), waarmee de subnetten in het virtuele netwerk worden gemaakt:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Koppel de *myRouteTablePublic* -route tabel aan het *open bare* subnet met [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) en schrijf vervolgens de configuratie van het subnet naar het virtuele netwerk met [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Een NVA maken

Een NVA is een VM die een netwerkfunctie uitvoert, zoals routering, firewall of WAN-optimalisatie.

Voordat u een virtuele machine maakt, maakt u een netwerk interface.

### <a name="create-a-network-interface"></a>Een netwerk interface maken

Voordat u een netwerk interface maakt, moet u de id van het virtuele netwerk ophalen met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)en vervolgens de subnet-id met [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Een netwerk interface maken met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) in het *DMZ* -subnet waarvoor door sturen via IP is ingeschakeld:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Een virtuele machine maken

Als u een virtuele machine wilt maken en hieraan een bestaande netwerk interface wilt koppelen, moet u eerst een VM-configuratie maken met [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). De configuratie bevat de netwerk interface die in de vorige stap is gemaakt. Selecteer de gebruikers naam en het wacht woord waarmee u zich wilt aanmelden bij de virtuele machine.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Maak de virtuele machine met behulp van de VM-configuratie met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voor beeld wordt een VM gemaakt met de naam *VM myvmnva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Met deze `-AsJob` optie wordt de virtuele machine op de achtergrond gemaakt, zodat u verder kunt gaan met de volgende stap.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee Vm's in het virtuele netwerk, zodat u kunt valideren dat verkeer van het *open bare* subnet wordt doorgestuurd naar het *privé* subnet via het virtuele netwerk apparaat in een latere stap.

Maak een virtuele machine in het *open bare* subnet met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voor beeld wordt een virtuele machine met de naam *VM myvmpublic* gemaakt in het *open bare* subnet van het virtuele netwerk *myVirtualNetwork* .

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Maak een virtuele machine in het *privé* -subnet.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met de volgende stap totdat de virtuele machine is gemaakt en Azure de uitvoer naar Power shell retourneert.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het open bare IP-adres van de *VM myvmprivate* -VM te retour neren. In het volgende voor beeld wordt het open bare IP-adres van de *VM myvmprivate* -VM geretourneerd:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Gebruik de volgende opdracht om een sessie met een extern bureau blad te maken met de *VM myvmprivate* -VM vanaf uw lokale computer. Vervang `<publicIpAddress>` door het IP-adres dat is geretourneerd met de vorige opdracht.

```
mstsc /v:<publicIpAddress>
```

Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

Voer de gebruikersnaam en het wachtwoord in die u bij het maken van de VM hebt opgegeven (mogelijk moet u **Meer opties** en **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en selecteer **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.

In een latere stap wordt de `tracert.exe` opdracht gebruikt om de route ring te testen. Tracert gebruikt de Internet Control Message Protocol (ICMP), die wordt geweigerd via de Windows Firewall. Schakel ICMP via de Windows-firewall in met de volgende opdracht vanuit PowerShell op de VM *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Hoewel tracerings route wordt gebruikt om de route ring in dit artikel te testen, wordt het gebruik van ICMP via de Windows Firewall voor productie-implementaties niet aanbevolen.

U hebt doorsturen via IP in Azure voor de netwerkinterface van de VM ingeschakeld in Doorsturen via IP inschakelen. In de VM moet het besturingssysteem of een toepassing die wordt uitgevoerd op de virtuele machine, ook netwerkverkeer kunnen doorsturen. Schakel door sturen via IP in het besturings systeem van de *VM myvmnva*.

Vanaf een opdracht prompt op de *VM myvmprivate* -VM, extern bureau blad naar de *VM myvmnva*:

``` 
mstsc /v:myvmnva
```

Om Doorsturen via IP binnen het besturingssysteem in te schakelen, voert u de volgende opdracht in PowerShell in vanaf de VM *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Start *myVmNva* opnieuw op. Hierdoor wordt ook de extern-bureaubladsessie verbroken.

Maak terwijl u nog verbinding hebt met de VM *myVmPrivate* een extern-bureaubladsessie met de VM *myVmPublic* nadat de VM *myVmNva* opnieuw is opgestart:

``` 
mstsc /v:myVmPublic
```

Schakel ICMP via de Windows-firewall in met de volgende opdracht vanuit PowerShell op de VM *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Om de routering van netwerkverkeer naar de VM *myVmPrivate* van de VM *myVmPublic* te testen, voert u op de VM *myVmPublic* de volgende opdracht van PowerShell uit:

```
tracert myVmPrivate
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

U ziet dat de eerste hop 10.0.2.4 is, het privé IP-adres van het NVA. De tweede hop is 10.0.1.4, het privé- IP-adres van de VM *myVmPrivate*. Door de route die is toegevoegd aan de routetabel *myRouteTablePublic* en gekoppeld aan het *Openbare* subnet leidt Azure het verkeer via het NVA in plaats van rechtstreeks naar het *Privé*-subnet.

Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

Om de routering van netwerkverkeer naar de VM *myVmPublic* van de VM *myVmPrivate* VM te testen, voert u op de VM *myVmPrivate* de volgende opdracht uit vanaf een opdrachtprompt:

```
tracert myVmPublic
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

U ziet dat verkeer rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, gebruikt u [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) om de resource groep en alle resources die deze bevat, te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een route tabel gemaakt en gekoppeld aan een subnet. U hebt een eenvoudig virtueel netwerk apparaat gemaakt dat verkeer van een openbaar subnet gerouteerd naar een privé subnet. Implementeer een aantal vooraf geconfigureerde virtuele netwerk apparaten die netwerk functies, zoals firewall-en WAN-optimalisatie, uitvoeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen resources voor sommige Azure PaaS-diensten niet in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot de resources van sommige Azure PaaS-diensten echter nog steeds beperken tot alleen verkeer vanaf een subnet van een virtueel netwerk. Zie [netwerk toegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources-powershell.md)voor meer informatie.
