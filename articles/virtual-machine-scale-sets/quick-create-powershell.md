---
title: 'Quickstart: Een schaalset voor virtuele machines maken met Azure PowerShell'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een schaalset voor virtuele machines kunt maken met Azure PowerShell.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 11/08/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3f38933c1b11ffca6a9ac26eb11d29387712067f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86495088"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Quickstart: Een virtuele-machineschaalset maken met Azure PowerShell



Met een virtuele-machineschaalset kunt u een reeks automatisch schalende virtuele machines implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gebruikt u Azure PowerShell om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-scale-set"></a>Een schaalset maken
Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Maak nu een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt, die gebruikmaakt van de platforminstallatiekopie van *Windows Server 2016 Datacenter*. De Azure-netwerkresources voor het virtuele netwerk, het openbare IP-adres en de load balancer worden automatisch gemaakt. Wanneer u hierom wordt gevraagd, kunt u uw eigen beheerdersreferenties instellen voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
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
Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. U kunt de aangepaste scriptextensie van Azure downloaden en een script uitvoeren waarmee IIS wordt geïnstalleerd in de VM-exemplaren. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md).

Gebruik de aangepaste scriptextensie om een eenvoudige IIS-webserver te installeren. Pas de aangepaste scriptextensie waarmee IIS wordt geïnstalleerd als volgt toe:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
            -ResourceGroupName "myResourceGroup" `
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
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Verkeer toestaan naar de toepassing

 Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) en [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) om toegang toe te staan tot de eenvoudige webtoepassing. Zie voor meer informatie [Netwerken voor schaalsets voor virtuele Azure-machines](virtual-machine-scale-sets-networking.md).

 ```azurepowershell-interactive
 # Get information about the scale set
 $vmss = Get-AzVmss `
             -ResourceGroupName "myResourceGroup" `
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
   -ResourceGroupName  "myResourceGroup" `
   -Location EastUS `
   -Name myFrontendNSG `
   -SecurityRules $nsgFrontendRule

 $vnet = Get-AzVirtualNetwork `
   -ResourceGroupName  "myResourceGroup" `
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
     -ResourceGroupName "myResourceGroup" `
     -Name "myScaleSet" `
     -VirtualMachineScaleSet $vmss
 ```

## <a name="test-your-scale-set"></a>Uw schaalset testen
U kunt de schaalset in actie zien door in een webbrowser naar de voorbeeldwebtoepassing te gaan. Haal het openbare IP-adres van de load balancer op met [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres weergegeven dat is gemaakt in de resourcegroep *myResourceGroup*:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Voer in een webbrowser het openbare IP-adres van de load balancer in. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Actieve IIS-site](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Resources opschonen
U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, schaalset en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. Dit doet u als volgt: De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u een standaardschaalset gemaakt en de aangepaste scriptextensie gebruikt om een standaard-IIS-webserver te installeren in de VM-exemplaren. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-powershell.md)
