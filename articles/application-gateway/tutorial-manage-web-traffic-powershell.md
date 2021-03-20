---
title: Webverkeer beheren - Azure PowerShell
description: Leer hier hoe u een toepassingsgateway met een virtuele-machineschaalset maakt om webverkeer te beheren met behulp van Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7e410218117aa0d21167b6d615a3835aeec470e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397107"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-azure-powershell"></a>Webverkeer met een toepassingsgateway beheren met behulp van Azure PowerShell

De toepassingsgateway wordt gebruikt voor het beheren en beveiligen van webverkeer naar servers die door u worden onderhouden. U kunt Azure PowerShell gebruiken om een ​​[toepassingsgateway](overview.md) te maken die gebruikmaakt van een [virtuele-machineschaalset](../virtual-machine-scale-sets/overview.md) voor back-endservers om webverkeer te beheren. In dit voorbeeld bevat de schaalset twee virtuele-machine-instanties die zijn toegevoegd aan de standaard back-endgroep van de toepassingsgateway.

In dit artikel leert u het volgende:

* Het netwerk instellen
* Een toepassingsgateway maken
* Een virtuele-machineschaalset maken met de standaard back-endgroep

Als u wilt, kunt u deze procedure uitvoeren met behulp van [Azure cli](tutorial-manage-web-traffic-cli.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een Azure-resource groep met behulp van [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Configureer de subnetten met de naam *myBackendSubnet* en *myAGSubnet* met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Maak de *myVNet* van het virtuele netwerk met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met de subnet-configuraties. En ten slotte maakt u het open bare IP-adres met de naam *myAGPublicIPAddress* met behulp van [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

In deze sectie kunt u resources maken die ondersteuning bieden voor de toepassingsgateway. Ten slotte maakt u deze. De resources die u maakt, zijn onder andere:

- *IP-configuraties en front-endpoort*: hiermee koppelt u het subnet dat u eerder hebt gemaakt aan de toepassingsgateway en wijst u een poort toe die u gebruikt om de gateway te openen.
- *Standaardpool*: alle toepassingsgateways moeten ten minste één back-endpool met servers hebben.
- *Standaard-listener en regel*: de standaard-listener luistert naar verkeer op de poort die is toegewezen en de standaardregel verzendt verkeer naar de standaardpool.

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

Koppel *myAGSubnet* die u eerder hebt gemaakt voor de toepassings gateway met behulp van [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Wijs *myAGPublicIPAddress* toe aan de toepassings gateway met behulp van [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Back-endpool en instellingen maken

Maak de back-end-groep met de naam *appGatewayBackendPool* voor de toepassings gateway met behulp van [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configureer de instellingen voor de back-end-adres groepen met behulp van [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Standaardlistener en -regel maken

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar de back-endpool door te sturen. In dit voorbeeld maakt u een basis-listener die luistert naar verkeer op de basis-URL. 

Maak een listener met de naam *mydefaultListener* met behulp van [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) met de front-end-configuratie en de frontend-poort die u eerder hebt gemaakt. Er is een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maak een basis regel met de naam *firewallregel1* met behulp van [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Nu u de nodige ondersteunende resources hebt gemaakt, geeft u para meters op voor de toepassings gateway met behulp van [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)en maakt u deze met behulp van [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway. U wijst de schaalset toe aan de back-endpool wanneer u de IP-instellingen configureert.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[0].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2_v2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>IIS installeren

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) om het open bare IP-adres van de toepassings gateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Basis-URL testen in de toepassingsgateway](./media/tutorial-manage-web-traffic-powershell/tutorial-iistest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, toepassings gateway en alle gerelateerde resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

[Webverkeer beperken met een firewall voor webtoepassingen](../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md)