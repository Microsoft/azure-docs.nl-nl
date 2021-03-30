---
title: 'Zelfstudie: Een schaalset voor virtuele Windows-machines maken'
description: U leert hoe u Azure PowerShell gebruikt om een maximaal beschikbare toepassing te maken en implementeren op virtuele Windows-machines met behulp van een virtuele-machineschaalset
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 11/30/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b3853ddc71d1a9be26b2492764a9b341446e0eeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078738"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Zelfstudie: Een virtuele-machineschaalset maken en een toepassing met hoge beschikbaarheid implementeren in Windows met Azure PowerShell
Met een schaalset voor virtuele machines kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal virtuele machines in de schaalset handmatig schalen. U kunt ook regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. In deze zelfstudie implementeert u een virtuele-machineschaalset in Azure en leert u het volgende:

> [!div class="checklist"]
> * De aangepaste scriptextensie gebruiken om een eenvoudige IIS-site op schaal te maken
> * Een load balancer voor uw schaalset maken
> * Een virtuele-machineschaalset maken
> * Het aantal instanties in een schaalset vergroten of verkleinen
> * Regels voor automatisch schalen maken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="scale-set-overview"></a>Overzicht van schaalsets
Met een schaalset voor virtuele machines kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. Virtuele machines in een schaalset worden verdeeld over logische fout- en updatedomeinen in een of meer *plaatsingsgroepen*. Plaatsingsgroepen zijn groepen van op soortgelijke wijze geconfigureerde virtuele machines, vergelijkbaar met [beschikbaarheidssets](tutorial-availability-sets.md).

Virtuele machines worden in een schaalset gemaakt als dat nodig is. U definieert regels voor automatisch schalen om te bepalen hoe en wanneer virtuele machines worden toegevoegd of verwijderd uit de schaalset. Deze regels kunnen worden geactiveerd op basis van de metrische gegevens zoals CPU-belasting, geheugengebruik of netwerkverkeer.

Schaalsets bieden ondersteuning voor maximaal 1000 VM’s wanneer u een Azure-platforminstallatiekopie gebruikt. Voor werkbelastingen met aanzienlijke installatie- of VM-aanpassingsvereisten wilt u mogelijk [een aangepaste VM-installatiekopie maken](tutorial-custom-images.md). U kunt maximaal 600 VM's in een schaalset maken wanneer u een aangepaste installatiekopie gebruikt.


## <a name="create-a-scale-set"></a>Een schaalset maken
Maak een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt, die gebruikmaakt van de platforminstallatiekopie van *Windows Server 2016 Datacenter*. De Azure-netwerkresources voor het virtuele netwerk, het openbare IP-adres en de load balancer worden automatisch gemaakt. Wanneer u hierom wordt gevraagd, kunt u uw eigen beheerdersreferenties instellen voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="deploy-sample-application"></a>Voorbeeldtoepassing implementeren
Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. U kunt de aangepaste scriptextensie van Azure downloaden en een script uitvoeren waarmee IIS wordt geïnstalleerd in de VM-exemplaren. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../extensions/custom-script-windows.md).

Gebruik de aangepaste scriptextensie om een eenvoudige IIS-webserver te installeren. Pas de aangepaste scriptextensie waarmee IIS wordt geïnstalleerd als volgt toe:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Verkeer toestaan naar de toepassing

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) en [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) om toegang toe te staan tot de eenvoudige webtoepassing. Zie voor meer informatie [Netwerken voor schaalsets voor virtuele Azure-machines](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u de schaalset in actie wilt zien, achterhaalt u het openbare IP-adres van de load balancer met [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres voor *myPublicIP* weergegeven, dat is gemaakt als onderdeel van de schaalset:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

Voer in een webbrowser het openbare IP-adres in. De web-app wordt weergegeven, inclusief de hostnaam van de virtuele machine waarnaar de load balancer verkeer heeft verdeeld:

![Actieve IIS-site](./media/tutorial-create-vmss/running-iis-site.png)

Als u de schaalset in actie wilt zien, kunt u vernieuwing van uw webbrowser afdwingen om te zien dat de load balancer verkeer verdeelt over alle virtuele machines waarop uw app wordt uitgevoerd.


## <a name="management-tasks"></a>Beheertaken
Tijdens de levenscyclus van de schaalset moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. Azure PowerShell biedt een snelle manier om deze taken uit te voeren. Hier volgen enkele algemene taken.

### <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Als u een overzicht wilt zien van de VM-exemplaren in een schaalset, gebruikt u [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) als volgt:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

In de volgende voorbeelduitvoer ziet u dat de schaalset twee VM-exemplaren bevat:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Als u extra informatie wilt weergeven over een specifiek VM-exemplaar, voegt u de parameter `-InstanceId` toe aan [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). In het volgende voorbeeld wordt informatie opgevraagd over het VM-exemplaar *1*:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>VM-instanties vergroten of verkleinen
Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [Get-AzVmss](/powershell/module/az.compute/get-azvmss) en voert u een query uit op *sku.capacity*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

U kunt vervolgens het aantal virtuele machines in de schaalset handmatig vergroten of verkleinen met [Update-AzVmss](/powershell/module/az.compute/update-azvmss). In het volgende voorbeeld wordt het aantal VM's in uw schaal ingesteld op *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Het bijwerken van het opgegeven aantal instanties in uw schaalset duurt een paar minuten.


### <a name="configure-autoscale-rules"></a>Regels voor automatisch schalen configureren
In plaats van het aantal instanties in uw schaalset handmatig te schalen, definieert u regels voor automatisch schalen. Deze regels bewaken de instanties in uw schaalset en reageren hierop op basis van metrische gegevens en drempels die u definieert. In het volgende voorbeeld wordt het aantal instanties opgeschaald met één wanneer de gemiddelde CPU-belasting gedurende een periode van 5 minuten hoger is dan 60%. Als de gemiddelde CPU-belasting vervolgens gedurende een periode van 5 minuten onder de 30% komt, worden de instanties omlaag geschaald met één instantie:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Zie [aanbevolen procedures voor automatisch schalen](/azure/architecture/best-practices/auto-scaling) voor meer ontwerpinformatie over het gebruik van automatisch schalen.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een virtuele-machineschaalset gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De aangepaste scriptextensie gebruiken om een eenvoudige IIS-site op schaal te maken
> * Een load balancer voor uw schaalset maken
> * Een virtuele-machineschaalset maken
> * Het aantal instanties in een schaalset vergroten of verkleinen
> * Regels voor automatisch schalen maken

Ga naar de volgende zelfstudie voor meer informatie over load balancing-concepten voor virtuele machines.

> [!div class="nextstepaction"]
> [Werklasten verdelen over virtuele machines](tutorial-load-balancer.md)
