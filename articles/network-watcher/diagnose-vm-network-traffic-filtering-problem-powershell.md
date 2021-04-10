---
title: 'Quickstart: Een diagnose uitvoeren voor een probleem met netwerkverkeersfilters op een VM - Azure PowerShell'
titleSuffix: Azure Network Watcher
description: U leert hoe u met Azure PowerShell een diagnose uitvoert voor een probleem met netwerkverkeersfilters op een virtuele machine met behulp van de functie IP-stroomverificatie in Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9cccd6d17cb741a616d428db5b318fab2334f73b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065562"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Snelstart: Diagnose uitvoeren voor een probleem met netwerkverkeersfilters op een virtuele machine - Azure PowerShell

In deze snelstart implementeert u een VM (virtuele machine) en controleert u vervolgens de communicatie naar een IP-adres en URL, en vanaf een IP-adres. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze quickstart de module `Az` van Azure PowerShell vereist. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.



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

Als u de netwerkcommunicatie met Network Watcher wilt testen, moet u eerst een netwerk-watcher inschakelen in de regio met de VM die u wilt testen. Gebruik vervolgens de functie IP-stroomverificatie in Network Watcher om de communicatie te testen.

### <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

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

### <a name="use-ip-flow-verify"></a>IP-stroomverificatie gebruiken

Als u een VM maakt, wordt netwerkverkeer van en naar de VM standaard toegestaan en geweigerd in Azure. U kunt de standaardinstellingen in Azure later negeren en extra typen verkeer toestaan en weigeren. Gebruik de opdracht [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) om te testen of verkeer naar verschillende bestemmingen en vanaf een bron-IP-adres is toegestaan of wordt geweigerd.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Na enkele seconden blijkt uit het resultaat dat toegang is toegestaan op basis van een beveiligingsregel met de naam **AllowInternetOutbound**.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Uit het resultaat blijkt dat toegang wordt geweigerd op basis van een beveiligingsregel met de naam **DefaultOutboundDenyAll**.

Binnenkomende communicatie van 172.31.0.100 naar de VM testen:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Uit het resultaat blijkt dat toegang wordt geweigerd vanwege een beveiligingsregel met de naam **DefaultInboundDenyAll**. Nu u weet op basis van welke beveiligingsregels verkeer van en naar een VM is toegestaan of wordt geweigerd, kunt u bepalen hoe u de problemen wilt oplossen.

## <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

Als u wilt vaststellen waarom communicatie is toegestaan of wordt geblokkeerd op basis van de regels in [Netwerkcommunicatie testen](#test-network-communication), controleert u de betreffende beveiligingsregels voor de netwerkinterface met [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

De geretourneerde uitvoer bevat de volgende tekst voor de regel **AllowInternetOutbound** op basis waarvan uitgaand verkeer naar www.bing.com is toegestaan in [IP-stroomverificatie gebruiken](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

In de uitvoer ziet u dat **DestinationAddressPrefix****Internet** is. Het is niet duidelijk hoe 13.107.21.200, het adres dat u hebt getest in [IP-stroomverificatie gebruiken](#use-ip-flow-verify), is gerelateerd aan **Internet**. Er worden verschillende adresvoorvoegsels weergegeven onder **ExpandedDestinationAddressPrefix**. Een van de voorvoegsels in de lijst is **12.0.0.0/6**, dat het bereik met IP-adressen 12.0.0.1-15.255.255.254 bevat. Aangezien 13.107.21.200 zich binnen dit adresbereik bevindt, is uitgaand verkeer toegestaan op basis van de regel **AllowInternetOutBound**. Daarnaast worden in de met `Get-AzEffectiveNetworkSecurityGroup` geretourneerde uitvoer geen regels met hogere **prioriteit** (lagere waarde) weergegeven op basis waarvan deze regel wordt genegeerd. Als u uitgaande communicatie naar 13.107.21.200 wilt weigeren, kunt u een beveiligingsregel met een hogere prioriteit toevoegen op basis waarvan uitgaand verkeer naar het IP-adres wordt geweigerd op poort 80.

Toen u de opdracht `Test-AzNetworkWatcherIPFlow` in [IP-stroomverificatie gebruiken](#use-ip-flow-verify) uitvoerde om uitgaande communicatie van 172.131.0.100 te testen, bleek uit de uitvoer dat communicatie wordt geweigerd op basis van de regel **DefaultOutboundDenyAll**. De regel **DefaultOutboundDenyAll** is gelijk aan de regel **DenyAllOutBound** die wordt weergegeven in de volgende uitvoer via de opdracht `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

In de regel wordt **0.0.0.0/0** vermeld als **DestinationAddressPrefix**. Op basis van de regel wordt uitgaande communicatie naar 172.131.0.100 geweigerd, omdat het adres zich niet in het **DestinationAddressPrefix** bevindt van een van de overige regels voor uitgaand verkeer met de opdracht `Get-AzEffectiveNetworkSecurityGroup`. Als u de uitgaande communicatie wilt toestaan, kunt u een beveiligingsregel toevoegen met een hogere prioriteit, op basis waarvan uitgaand verkeer naar poort 80 is toegestaan op 172.131.0.100.

Toen u de opdracht `Test-AzNetworkWatcherIPFlow` uitvoerde om binnenkomende communicatie van 172.131.0.100 te testen in [IP-stroomverificatie gebruiken](#use-ip-flow-verify), bleek uit de uitvoer dat communicatie wordt geweigerd op basis van de regel **DefaultOutboundDenyAll**. De regel **DefaultInboundDenyAll** is gelijk aan de regel **DenyAllInBound** die wordt weergegeven in de volgende uitvoer via de opdracht `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

De regel **DenyAllInBound** is toegepast omdat, zoals wordt weergegeven in de uitvoer, de uitvoer via de opdracht `Get-AzEffectiveNetworkSecurityGroup` geen andere regel met hoge prioriteit bevat, op basis waarvan binnenkomend verkeer via poort 80 van 172.131.0.100 naar de VM is toegestaan. Als u de binnenkomende communicatie wilt toestaan, kunt u een beveiligingsregel met een hogere prioriteit toevoegen, op basis waarvan binnenkomend verkeer van 172.131.0.100 via poort 80 is toegestaan.

Met de controles in deze snelstart is de Azure-configuratie getest. Als de controles onverwachte resultaten retourneren en u nog steeds netwerkproblemen ondervindt, controleert u of er geen firewall is geplaatst tussen de VM en het eindpunt waarmee u communiceert, en of het besturingssysteem op de VM geen firewall heeft waardoor communicatie is toegestaan of wordt geweigerd.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een VM gemaakt en diagnose uitgevoerd voor netwerkfilters voor binnenkomend en uitgaand verkeer. U hebt geleerd dat verkeer van en naar een VM kan zijn toegestaan of worden geweigerd op basis van regels voor netwerkbeveiligingsgroepen. Meer informatie over [beveiligingsregels](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en het [maken van beveiligingsregels](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Zelfs als de juiste netwerkverkeersfilters zijn toegepast, kan communicatie naar een VM mislukken, vanwege de configuratie van de routering. Zie [Diagnose uitvoeren voor problemen met VM-routering](diagnose-vm-network-routing-problem-powershell.md) voor informatie over het vaststellen van routeringsproblemen met VM-netwerken. Of zie [Problemen met de verbinding oplossen ](network-watcher-connectivity-powershell.md) om met één hulpprogramma de problemen vast te stellen met routering van uitgaand verkeer, latentie en verkeersfilters.