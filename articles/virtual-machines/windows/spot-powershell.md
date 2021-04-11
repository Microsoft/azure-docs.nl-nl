---
title: Power shell gebruiken voor het implementeren van Azure Spot Virtual Machines
description: Meer informatie over het gebruik van Azure PowerShell voor het implementeren van Azure Spot Virtual Machines om kosten op te slaan.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802740"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure Spot Virtual Machines implementeren met behulp van Azure PowerShell


Met behulp van [Azure Spot Virtual Machines](../spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines. Daarom zijn Azure Spot Virtual Machines geweldig voor workloads die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor Azure Spot Virtual Machines is variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Azure Spot Virtual Machines-prijzen](../spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. U kunt de maximale prijs voor een virtuele machine van Azure spot instellen in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.


## <a name="create-the-vm"></a>De VM maken

Maak een spotVM met [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) om de configuratie te maken. Insluiten `-Priority Spot` en instellen `-MaxPrice` op:
- `-1` de virtuele machine wordt dus niet verwijderd op basis van de prijs.
- een dollar bedrag, Maxi maal vijf cijfers. `-MaxPrice .98765`Betekent bijvoorbeeld dat de toewijzing van de virtuele machine ongedaan wordt gemaakt wanneer de prijs voor een spotVM ongeveer $. 98765 per uur duurt.


In dit voor beeld wordt een spotVM gemaakt die niet wordt toegewezen op basis van de prijzen (alleen wanneer Azure de capaciteit nodig heeft). Het verwijderings beleid is ingesteld om de toewijzing van de virtuele machine ongedaan te maken, zodat deze op een later tijdstip opnieuw kan worden opgestart. Als u de virtuele machine en de onderliggende schijf wilt verwijderen wanneer de virtuele machine wordt verwijderd, stelt `-EvictionPolicy` u `Delete` in op in `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Nadat de VM is gemaakt, kunt u een query uitvoeren om de maximum prijs voor alle virtuele machines in de resource groep weer te geven.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Een verwijdering simuleren

U kunt een virtuele machine van Azure spot simuleren met behulp van REST, Power shell of de CLI om te testen hoe goed uw toepassing reageert op een plotselinge verwijdering.

In de meeste gevallen moet u de REST API [virtual machines simuleren](/rest/api/compute/virtualmachines/simulateeviction) gebruiken om te helpen bij het automatisch testen van toepassingen. Voor REST, een `Response Code: 204` betekent dat de gesimuleerde verwijdering is geslaagd. U kunt gesimuleerde verwijderingen combi neren met de [geplande gebeurtenis service](scheduled-events.md), om te automatiseren hoe uw app reageert wanneer de virtuele machine wordt verwijderd.

Als u geplande gebeurtenissen in actie wilt zien, kunt u [Azure vrijdag bekijken-azure Scheduled Events gebruiken om het onderhoud van vm's voor te bereiden](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Snelle test

Voor een snelle test om te laten zien hoe een gesimuleerde verwijdering werkt, gaat u verder met het uitvoeren van query's op de geplande gebeurtenis service om te zien hoe deze eruitziet wanneer u een verwijdering simuleert met behulp van Power shell.

De geplande gebeurtenis service is ingeschakeld voor uw service, de eerste keer dat u een aanvraag voor gebeurtenissen doet. 

Extern in uw virtuele machine en open vervolgens een opdracht prompt. 

Typ het volgende vanaf de opdracht prompt op uw virtuele machine:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Dit eerste antwoord kan Maxi maal twee minuten duren. Vanaf nu wordt de uitvoer bijna onmiddellijk weer gegeven.

Op een computer waarop de AZ Power shell-module is geïnstalleerd (zoals uw lokale machine), simuleert u een verwijdering met [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Vervang de naam van de resource groep en de virtuele machine door uw eigen naam. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Als de aanvraag is uitgevoerd, is de reactie-uitvoer `Status: Succeeded` .

Ga snel terug naar uw externe verbinding met de virtuele spot machine en zoek het Scheduled Events-eind punt opnieuw op. Herhaal de volgende opdracht totdat u een uitvoer krijgt die meer informatie bevat:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Wanneer de geplande gebeurtenis service het verwijderings bericht ontvangt, krijgt u een antwoord dat er ongeveer als volgt uitziet:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

U ziet dat `"EventType":"Preempt"` en de resource de VM-resource is `"Resources":["myspotvm"]` . 

U kunt ook zien wanneer de virtuele machine wordt verwijderd door de waarde te controleren `"NotBefore"` . De virtuele machine wordt niet vóór de opgegeven tijd verwijderd `NotBefore` , zodat uw toepassing zonder problemen kan worden afgesloten.


## <a name="next-steps"></a>Volgende stappen

U kunt ook een virtuele Azure-machine maken met behulp van de [Azure cli](../linux/spot-cli.md), [Portal](../spot-portal.md) of een [sjabloon](../linux/spot-template.md).

Zoek actuele prijs informatie met behulp van de [Azure retail-prijs-API](/rest/api/cost-management/retail-prices/azure-retail-prices) voor informatie over de prijzen van Azure Spot Virtual Machine. De `meterName` en `skuName` zijn beide opgenomen `Spot` .

Als er een fout optreedt, raadpleegt u [fout codes](../error-codes-spot.md).
