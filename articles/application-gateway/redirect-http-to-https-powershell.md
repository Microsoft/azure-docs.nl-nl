---
title: HTTP-naar-HTTPS-omleiding met Behulp van PowerShell - Azure Application Gateway
description: Meer informatie over het maken van een toepassingsgateway met omgeleid verkeer van HTTP naar HTTPS met behulp Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: c9118d0e5b314f05e89334991c68ec1b3b5751e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866301"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Een toepassingsgateway met HTTP-naar-HTTPS-omleiding maken met Azure PowerShell

U kunt de Azure PowerShell om een toepassingsgateway [te](overview.md) maken met een certificaat voor TLS/SSL-beëindiging. Een regel voor doorsturen wordt gebruikt om HTTP-verkeer om te leiden naar de HTTPS-poort in uw toepassingsgateway. In dit voorbeeld maakt u ook een [virtuele-machineschaalset](../virtual-machine-scale-sets/overview.md) voor de back-endpool van de toepassingsgateway die twee exemplaren van virtuele machines bevat. 

In dit artikel leert u het volgende:

* Een zelfondertekend certificaat maken
* Een netwerk instellen
* Een toepassingsgateway maken met behulp van het certificaat
* Een listener en omleidingsregel toevoegen
* Een virtuele-machineschaalset maken met de standaard back-endgroep

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelfstudie is Azure PowerShell moduleversie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u de opdrachten in deze zelfstudie wilt uitvoeren, moet u ook uitvoeren om `Login-AzAccount` een verbinding met Azure te maken.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor productiegebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](/powershell/module/pki/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U zou iets moeten zien dat lijkt op dit resultaat:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik de vingerafdruk om het PFX-bestand te maken:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een Azure-resourcegroep met *de naam myResourceGroupAG met* behulp van [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Maak de subnetconfiguraties voor *myBackendSubnet* en *myAGSubnet* met [new-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Maak het virtuele netwerk *myVNet met* [behulp van New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met de subnetconfiguraties. Maak ten slotte het openbare IP-adres *myAGPublicIPAddress* met [behulp van New-AzPublicIpAddress.](/powershell/module/az.network/new-azpublicipaddress) Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

```powershell
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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

Koppel *myAGSubnet dat* u eerder hebt gemaakt aan de toepassingsgateway met [behulp van New-AzApplicationGatewayIPConfiguration.](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Wijs *myAGPublicIPAddress toe* aan de toepassingsgateway met [behulp van New-AzApplicationGatewayFrontendIPConfig.](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) Vervolgens kunt u de HTTPS-poort maken met [New-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/new-azapplicationgatewayfrontendport)

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Back-endpool en instellingen maken

Maak de back-endpool met de *naam appGatewayBackendPool* voor de toepassingsgateway met [behulp van New-AzApplicationGatewayBackendAddressPool.](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) Configureer de instellingen voor de [back-endpool met New-AzApplicationGatewayBackendHttpSettings.](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)

```powershell
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

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar de back-endpool door te sturen. In dit voorbeeld maakt u een basis-listener die luistert naar HTTPS-verkeer op de basis-URL. 

Maak een certificaatobject met [New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) en maak vervolgens een listener met de naam *appGatewayHttpListener* met behulp van [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) met de front-end-configuratie, front-endpoort en het certificaat die u eerder hebt gemaakt. Er is een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maak een basisregel met *de naam rule1* [met new-AzApplicationGatewayRequestRoutingRule.](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Nu u de benodigde ondersteunende resources hebt gemaakt, geeft u parameters op voor de toepassingsgateway met de naam *myAppGateway* met behulp van [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)en maakt u deze vervolgens met behulp van [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) met het certificaat.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Een listener en omleidingsregel toevoegen

### <a name="add-the-http-port"></a>De HTTP-poort toevoegen

Voeg de HTTP-poort toe aan de toepassingsgateway [met behulp van Add-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/add-azapplicationgatewayfrontendport)

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>De HTTP-listener toevoegen

Voeg de HTTP-listener met de *naam myListener toe* aan de toepassingsgateway met [behulp van Add-AzApplicationGatewayHttpListener.](/powershell/module/az.network/add-azapplicationgatewayhttplistener)

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>De omleidingsconfiguratie toevoegen

Voeg de configuratie van de HTTP-naar-HTTPS-omleiding toe aan de toepassingsgateway met behulp van [Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration).

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>De regel voor doorsturen toevoegen

Voeg de routeringsregel met de omleidingsconfiguratie toe aan de toepassingsgateway met behulp van [Add-AzApplicationGatewayRequestRoutingRule.](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway. U wijst de schaalset toe aan de back-endpool wanneer u de IP-instellingen configureert.

```powershell
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
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
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

```powershell
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

U kunt [Get-AzPublicIPAddress gebruiken om](/powershell/module/az.network/get-azpublicipaddress) het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld: http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Beveiligingswaarschuwing](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Als u de beveiligingswaarschuwing wilt accepteren als u een zelf-ondertekend certificaat hebt gebruikt, selecteert u **Details** en vervolgens **Ga verder naar de webpagina.** Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

- [HTTP-headers en URL's herschrijven met Application Gateway](rewrite-http-headers-url.md)
