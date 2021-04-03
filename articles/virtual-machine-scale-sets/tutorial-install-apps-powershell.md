---
title: Zelfstudie - Toepassingen installeren in een schaalset met Azure PowerShell
description: Informatie over het gebruik van Azure PowerShell om toepassingen te installeren in een virtuele-machineschaalsets met de aangepaste scriptextensie
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 11/08/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: e783f7f0a9be413679e509e4d6124d50bb811821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87059294"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Zelfstudie: Toepassingen installeren in een virtuele-machineschaalset met Azure PowerShell

Als u toepassingen wilt uitvoeren op de exemplaren van een virtuele machine (VM) in een schaalset, moet u eerst de toepassingsonderdelen en de vereiste bestanden installeren. In een vorige zelfstudie hebt u geleerd om een aangepaste VM-installatiekopie te maken en te gebruiken voor het implementeren van uw VM-exemplaren. Deze aangepaste installatiekopie bevat handmatige installaties van toepassingen en configuraties. U kunt de installatie van toepassingen op een schaalset ook automatiseren nadat elk VM-exemplaar is geïmplementeerd. Bovendien kunt u toepassingen bijwerken die al worden uitgevoerd in een schaalset. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Automatisch toepassingen installeren in een schaalset
> * De aangepaste scriptextensie van Azure gebruiken
> * Een actieve toepassing in een schaalset bijwerken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Wat is de aangepaste scriptextensie van Azure?
Met de aangepaste scriptextensie kunnen scripts worden gedownload en uitgevoerd op virtuele machines in Azure. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies.

De aangepaste scriptextensie is geïntegreerd met Azure Resource Manager-sjablonen. Het kan ook worden gebruikt in combinatie met Azure CLI, Azure PowerShell, de Azure-portal of de REST-API. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md).

Als u de aangepaste scriptextensie in actie wilt zien, maakt u een schaalset die de IIS-webserver installeert en de hostnaam levert van het schaalset-VM-exemplaar. De definitie van de aangepaste scriptextensie downloadt een voorbeeldscript vanuit GitHub, installeert de vereiste pakketten en schrijft de hostnaam van het VM-exemplaar naar een standaard-HTML-pagina.


## <a name="create-a-scale-set"></a>Een schaalset maken
Maak nu een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80. Daarnaast is extern bureaubladverkeer op TCP-poort 3389 en externe communicatie met PowerShell op TCP-poort 5985 mogelijk. Wanneer u hierom wordt gevraagd, kunt u uw eigen beheerdersreferenties instellen voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="create-custom-script-extension-definition"></a>Definitie van de aangepaste scriptextensie maken
Azure PowerShell gebruikt een hashtabel voor het opslaan van het bestand dat moet worden gedownload en de opdracht die moet worden uitgevoerd. In het volgende voorbeeld wordt een voorbeeld van een script vanuit GitHub gebruikt. Maak eerst dit configuratieobject. Het moet er zo uitzien:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Pas nu de aangepaste scriptextensie toe met [AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). Het eerder gedefinieerde configuratieobject wordt doorgegeven aan de extensie. Werk de extensie voor de VM-exemplaren bij en voer deze uit met [Update-AzVmss](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Elk VM-exemplaar in de schaalset downloadt het script vanuit GitHub en voert het uit. In een meer complex voorbeeld kunnen meerdere toepassingsonderdelen en bestanden worden geïnstalleerd. Als de schaalset omhoog wordt geschaald, passen de VM-exemplaren automatisch dezelfde definitie van de aangepaste scriptextensie toe en installeren deze de vereiste toepassing.


## <a name="allow-traffic-to-application"></a>Verkeer toestaan naar de toepassing

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) en [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) om toegang toe te staan tot de eenvoudige webtoepassing. Zie voor meer informatie [Netwerken voor schaalsets voor virtuele Azure-machines](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive

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

```



## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u de webserver in actie wilt zien, haalt u het openbare IP-adres van de load balancer op met [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres weergegeven dat is gemaakt in de resourcegroep *myResourceGroup*:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Voer in een webbrowser het openbare IP-adres van de load balancer in. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Basiswebpagina in IIS](media/tutorial-install-apps-powershell/running-iis.png)

Sluit de webbrowser niet af, zodat u in de volgende stap een bijgewerkte versie ziet.


## <a name="update-app-deployment"></a>App-implementatie bijwerken
Gedurende de levenscyclus van een schaalset moet u wellicht een bijgewerkte versie van uw toepassing implementeren. U kunt met de aangepaste scriptextensie verwijzen naar een bijgewerkt implementatiescript en vervolgens de extensie opnieuw op uw schaalset toepassen. Bij het maken van de schaalset in een vorige stap is `-UpgradePolicyMode` ingesteld op *Automatic*. Met deze instelling kunnen de VM-exemplaren in de schaalset automatisch de meest recente versie van uw toepassing bijwerken en toepassen.

Maak een nieuwe definitie van een configuratie met de naam *customConfigv2*. Deze definitie voert een bijgewerkte versie *v2* van het installatiescript van de toepassing uit:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Werk de configuratie van de aangepaste scriptextensie bij op de VM-exemplaren in uw schaalset. De definitie *customConfigv2* wordt gebruikt voor het toepassen van de bijgewerkte versie van de toepassing:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Alle VM-exemplaren in de schaalset worden automatisch bijgewerkt met de meest recente versie van de voorbeeldwebpagina. Vernieuw de website in uw browser voor een overzicht van de bijgewerkte versie:

![Bijgewerkte webpagina in IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u automatisch toepassingen kunt installeren of bijwerken in uw schaalset met Azure PowerShell:

> [!div class="checklist"]
> * Automatisch toepassingen installeren in een schaalset
> * De aangepaste scriptextensie van Azure gebruiken
> * Een actieve toepassing in een schaalset bijwerken

Ga door naar de volgende zelfstudie voor informatie over het automatisch schalen van uw schaalset.

> [!div class="nextstepaction"]
> [Uw schaalsets automatisch schalen](tutorial-autoscale-powershell.md)
