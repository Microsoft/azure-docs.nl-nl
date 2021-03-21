---
title: 'Een virtueel netwerk maken: quickstart - Azure PowerShell'
titlesuffix: Azure Virtual Network
description: In deze quickstart maakt u een virtueel netwerk met Azure Portal. Met een virtueel netwerk kunnen Azure-resources communiceren met elkaar en met internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199030"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Quickstart: Een virtueel netwerk maken met PowerShell

Met een virtueel netwerk kunnen Azure-resources, zoals virtuele machines (VM's), privé met elkaar en met internet communiceren. 

In deze snelstart leert u hoe u een virtueel netwerk maakt. Nadat u een virtueel netwerk hebt gemaakt, implementeert u twee virtuele machines in het virtuele netwerk. Vervolgens maakt u verbinding met de virtuele machines via internet en is er privécommunicatie via het virtuele netwerk mogelijk.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Een resourcegroep en een virtueel netwerk maken

U kunt de resourcegroep en het virtuele netwerk configureren door een aantal stappen te doorlopen.

### <a name="create-the-resource-group"></a>De resourcegroep maken

Voordat u een virtueel netwerk kunt maken, moet u een resourcegroep maken die het virtuele netwerk host. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In dit voor beeld wordt een resource groep met de naam **CreateVNetQS-RG** gemaakt op de locatie **eastus** :

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In dit voor beeld wordt een standaard virtueel netwerk met de naam **myVNet** gemaakt op de locatie **eastus** :

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Een subnet toevoegen

Azure implementeert resources in een subnet binnen een virtueel netwerk. U moet daarom een subnet maken. Maak een subnetconfiguratie genaamd **default** met [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Het subnet aan het virtuele netwerk koppelen

U kunt de subnetconfiguratie naar het virtuele netwerk schrijven met [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Met deze opdracht maakt u het subnet:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM’s in het virtuele netwerk.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak de eerste VM met [New-AzVM](/powershell/module/az.compute/new-azvm). Wanneer u de volgende opdracht uitvoert, wordt u gevraagd referenties op te geven. Voer een gebruikersnaam en wachtwoord voor de virtuele machine in:

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt. U kunt doorgaan met de volgende stap.

Wanneer er op de achtergrond met het maken van de virtuele machine wordt begonnen, wordt er iets dergelijks weergegeven:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>De tweede VM maken

Maak de tweede virtuele machine met de volgende opdracht:

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

U moet een andere gebruiker en wachtwoord maken. Het duurt een paar minuten om de virtuele machine te maken.

> [!IMPORTANT]
> Ga pas verder met de volgende stap als Azure klaar is.  Wanneer er uitvoer wordt geretourneerd naar PowerShell, weet u dat de virtuele machine is gemaakt.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de virtuele machine op te halen.

In dit voorbeeld wordt het openbare IP-adres van de VM **myVm1** opgehaald:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Open een opdrachtprompt op de lokale computer. Voer de opdracht `mstsc` uit. Vervang `<publicIpAddress>` door het openbare IP-adres dat in de laatste stap is geretourneerd:

> [!NOTE]
> Als u deze opdrachten vanuit een PowerShell-prompt op de lokale computer hebt uitgevoerd en u met de Azure PowerShell-module versie 1.0 of hoger werkt, kunt u doorgaan in die interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

    > [!NOTE]
    > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er kan een certificaatwaarschuwing worden weergegeven. Als dit het geval is, selecteert u **Ja** of **Doorgaan**.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Open PowerShell in het externe bureaublad van **myVm1**.

1. Voer `ping myVm2` in.

    Er wordt vervolgens iets dergelijks weergegeven:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    De ping mislukt, omdat deze gebruikmaakt van het Internet Control Message Protocol (ICMP). ICMP wordt standaard niet toegestaan via de Windows-firewall.

1. Voer de volgende opdracht in om **myVm2** toe te staan **myVm1** in een latere stap te pingen:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Met die opdracht is binnenkomend verkeer van ICMP via de Windows-firewall toegestaan.

1. Sluit de externe bureaubladverbinding met **myVm1**.

1. Herhaal de stappen in [Verbinding maken met een virtuele machine via internet](#connect-to-a-vm-from-the-internet). Deze keer maakt u verbinding met **myVm2**.

1. Voer vanaf een opdrachtprompt op de virtuele machine **myVm2**`ping myvm1` in.

    Er wordt vervolgens iets dergelijks weergegeven:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    U ontvangt antwoorden van **myVm1** omdat u ICMP hebt toegestaan via de Windows-firewall op de VM **myVm1** in de vorige stap.

1. Sluit de externe bureaubladverbinding met **myVm2**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het virtuele netwerk en de VM's, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle resources die deze bevat te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Volgende stappen

Voor deze snelstart geldt het volgende: 

* U hebt een standaard virtueel netwerk en twee virtuele machines gemaakt. 
* U hebt met één virtuele machine verbinding gemaakt via internet en er is er privécommunicatie tussen de twee virtuele machines geweest.

Privé communicatie tussen Vm's is onbeperkt in een virtueel netwerk. 

Ga verder naar het volgende artikel voor meer informatie over het configureren van verschillende typen VM-netwerkcommunicatie:
> [!div class="nextstepaction"]
> [Netwerkverkeer filteren](tutorial-filter-network-traffic.md)
