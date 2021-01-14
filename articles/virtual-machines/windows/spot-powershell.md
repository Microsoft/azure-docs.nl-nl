---
title: Power shell gebruiken voor het implementeren van Azure spot-Vm's
description: Meer informatie over het gebruik van Azure PowerShell voor het implementeren van spot-Vm's om kosten op te slaan.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0ca3c99aed8160161c125a89da3cb176c6e745f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202059"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Plaats virtuele machines met behulp van Azure PowerShell


Met behulp van [Spot vm's](../spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot Vm's. Daarom zijn de virtuele machines geschikt voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor spot Vm's zijn variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Spot vm's-prijzen](../spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. De maximale prijs voor een steun-VM kan worden ingesteld in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.


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

# Create a virtual machine configuration and set this to be a Spot VM

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

U kunt een weghalen van een spot-VM [simuleren](/rest/api/compute/virtualmachines/simulateeviction) om te testen hoe goed uw toepassing kan worden gepaard met een plotselinge verwijdering. 

Vervang het volgende door uw gegevens: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook een spot-VM maken met behulp van de [Azure cli](../linux/spot-cli.md), [Portal](../spot-portal.md) of een [sjabloon](../linux/spot-template.md).

Zoek actuele prijs informatie met behulp van de [Azure retail-prijs-API](/rest/api/cost-management/retail-prices/azure-retail-prices) voor meer informatie over de prijzen. De `meterName` en `skuName` zijn beide opgenomen `Spot` .

Als er een fout optreedt, raadpleegt u [fout codes](../error-codes-spot.md).