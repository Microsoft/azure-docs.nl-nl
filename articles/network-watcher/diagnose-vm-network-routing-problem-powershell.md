---
title: Een VM-netwerk routerings probleem vaststellen-Azure PowerShell
titleSuffix: Azure Network Watcher
description: In dit artikel leert u hoe u een probleem met de netwerk routering van een virtuele machine kunt vaststellen met behulp van de volgende hop-functionaliteit van Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020195"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Een probleem met de route ring van een netwerk van een virtuele machine vaststellen-Azure PowerShell

In dit artikel implementeert u een virtuele machine (VM) en controleert u de communicatie met een IP-adres en een URL. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de `Az` module Azure PowerShell vereist. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.



## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Maak de VM met [New-AzVM](/powershell/module/az.compute/new-azvm). Als deze stap wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door met de resterende stappen totdat de VM is gemaakt en uitvoer wordt geretourneerd met PowerShell.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerk communicatie met Network Watcher wilt testen, moet u eerst een Network Watcher inschakelen in de regio waarin de virtuele machine zich bevindt die u wilt testen en vervolgens de volgende hop-mogelijkheid Network Watcher gebruiken om de communicatie te testen.

## <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een netwerk-watcher hebt ingeschakeld in de regio US - oost, gebruikt u [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) om de netwerk-watcher op te halen. In het volgende voorbeeld wordt een bestaande netwerk-watcher opgehaald met de naam *NetworkWatcher_eastus* die zich in de resourcegroep *NetworkWatcherRG* bevindt:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Als u nog geen netwerk-watcher hebt ingeschakeld in de regio US - oost, gebruikt u [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) om een netwerk-watcher te maken in de regio US - oost:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Volgende hop gebruiken

Azure maakt automatisch routes naar standaardbestemmingen. U kunt uw eigen, aangepaste routes maken om die standaardroutes te overschrijven. Soms hebben aangepaste routes tot gevolg dat de communicatie mislukt. Als u de route ring van een virtuele machine wilt testen, gebruikt u de opdracht [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) om de volgende routerings hop te bepalen wanneer het verkeer voor een specifiek adres bestemd is.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Na een paar seconden is de uitvoer informeert dat de **NextHopType** **Internet** is en dat de **RouteTableId** **systeem route** is. Dit leidt ertoe dat u weet dat er een geldige route naar de bestemming is.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

De uitvoer heeft informeert dat **geen** **NextHopType** is en dat de **RouteTableId** ook **systeem routes** zijn. Hieruit kunt u afleiden dat er wel een geldige systeemroute is naar de bestemming, maar dat er geen volgende hop is voor het routeren van het verkeer naar de bestemming.

## <a name="view-details-of-a-route"></a>Details van een route weergeven

Als u de route ring verder wilt analyseren, controleert u de meest efficiënte routes voor de netwerk interface met de opdracht [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) :

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Uitvoer die de volgende tekst bevat, wordt geretourneerd:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Zoals u in de vorige uitvoer kunt zien, stuurt de route met de **AddressPrefix** van **0.0.0.0/0** al het verkeer dat niet is bestemd voor adressen binnen de adres voorvoegsels van andere routes met de volgende hop van **Internet**. Zoals u in de uitvoer kunt zien, is er echter een standaard route naar het 172.16.0.0/12-voor voegsel, dat het 172.31.0.100-adres bevat, de **nextHopType** is **geen**. Azure maakt een standaardroute naar 172.16.0.0/12, maar stelt geen volgend hoptype in, tenzij daar een reden voor is. Als u bijvoorbeeld het adres bereik 172.16.0.0/12 hebt toegevoegd aan de adres ruimte van het virtuele netwerk, wijzigt Azure de **nextHopType** in het **virtuele netwerk** voor de route. Een controle wordt vervolgens het **virtuele netwerk** weer gegeven als de **nextHopType**.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een virtuele machine gemaakt en een diagnose van de netwerk routering van de virtuele machine. U hebt geleerd dat Azure verschillende standaardroutes maakt en u hebt de routering naar twee verschillende bestemmingen getest. Lees hier meer over [routering in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en hoe u [aangepaste routes maakt](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Voor uitgaande VM-verbindingen kunt u ook de latentie en het toegestane en geweigerde netwerk verkeer tussen de virtuele machine en een eind punt bepalen met behulp van de [verbindings problemen](network-watcher-connectivity-powershell.md) met de verbinding van Network Watcher. U kunt de communicatie tussen een virtuele machine en een eind punt, zoals een IP-adres of URL, na verloop van tijd bewaken met behulp van de Network Watcher-functie verbindings monitor. Zie [een netwerk verbinding bewaken](connection-monitor.md)voor meer informatie.
