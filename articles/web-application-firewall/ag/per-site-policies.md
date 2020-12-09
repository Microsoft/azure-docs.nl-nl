---
title: Beleid per site WAF configureren met behulp van Power shell
titleSuffix: Azure Web Application Firewall
description: Meer informatie over het configureren van firewall beleid per site voor een toepassings gateway met behulp van Azure PowerShell.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 12/08/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: f282cfa6347dd6e6d591ac5cd8b1785e405c6c02
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905975"
---
# <a name="configure-per-site-waf-policies-using-azure-powershell"></a>WAF-beleid per site configureren met behulp van Azure PowerShell

WAF-instellingen (Web Application firewall) bevinden zich in WAF-beleid en om uw WAF-configuratie te wijzigen, wijzigt u het WAF-beleid.

Wanneer het beleid is gekoppeld aan uw Application Gateway, worden de beleids regels en alle instellingen globaal weer gegeven. Als u dus vijf sites achter uw WAF hebt, worden alle vijf de sites beschermd door hetzelfde WAF-beleid. Dit is handig als u dezelfde beveiligings instellingen nodig hebt voor elke site. Maar u kunt ook WAF-beleid Toep assen op afzonderlijke listeners om een sitespecifieke WAF-configuratie toe te staan.

Door WAF-beleid toe te passen op een listener, kunt u WAF-instellingen configureren voor afzonderlijke sites zonder de wijzigingen die van invloed zijn op elke site. Het meest specifieke beleid neemt broncel. Als er een globaal beleid is en een beleid per site (een WAF-beleid dat is gekoppeld aan een listener), overschrijft het beleid per site het globale beleid voor WAF voor die listener. Andere listeners zonder eigen beleid worden alleen beïnvloed door het beleid voor globale WAF.

In dit artikel leert u het volgende:

* Het netwerk instellen
* Een WAF-beleid maken
* Een toepassingsgateway maken met WAF ingeschakeld
* Het WAF-beleid globaal, per site en per URI Toep assen 
* Een virtuele-machineschaalset maken
* Een opslagaccount maken en diagnostische gegevens configureren
* De toepassingsgateway testen

![Voorbeeld van een WAF (Web Application Firewall)](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een Azure-resource groep met behulp van [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
$rgname = New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Maak de subnet-configuraties met de naam *myBackendSubnet* en *myAGSubnet* met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Maak het virtuele netwerk met de naam *myVNet* met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met de subnet-configuraties. En ten slotte maakt u het open bare IP-adres met de naam *myAGPublicIPAddress* met behulp van [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

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

In deze sectie maakt u resources die ondersteuning bieden voor de toepassings gateway en maakt u deze vervolgens en een WAF. De resources die u maakt, zijn onder andere:

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

$frontendport80 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
  
$frontendport8080 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 8080
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

### <a name="create-two-waf-policies"></a>Twee WAF-beleids regels maken

Maak twee WAF-beleids regels, een globaal en één per locatie en voeg aangepaste regels toe. 

Het beleid per site beperkt de upload limiet voor bestanden tot 5 MB. Alle andere opties zijn hetzelfde.

```azurepowershell-interactive
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "globalAllow" 
$rule = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition -Action Allow

$variable1 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable1 -Operator Contains -MatchValue "globalBlock" 
$rule1 = New-AzApplicationGatewayFirewallCustomRule -Name globalBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition1 -Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable2 -Operator Contains -MatchValue "siteAllow" 
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name siteAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

$variable3 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition3 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable3 -Operator Contains -MatchValue "siteBlock" 
$rule3 = New-AzApplicationGatewayFirewallCustomRule -Name siteBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition3 -Action Block

$variable4 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition4 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable4 -Operator Contains -MatchValue "URIAllow" 
$rule4 = New-AzApplicationGatewayFirewallCustomRule -Name URIAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition4 -Action Allow

$variable5 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition5 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable5 -Operator Contains -MatchValue "URIBlock" 
$rule5 = New-AzApplicationGatewayFirewallCustomRule -Name URIBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition5 -Action Block

$policySettingGlobal = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 256

$wafPolicyGlobal = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicyGlobal `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingGlobal `
  -CustomRule $rule, $rule1

$policySettingSite = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicySite = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingSite `
  -CustomRule $rule2, $rule3
```

### <a name="create-the-default-listener-and-rule"></a>Standaardlistener en -regel maken

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier te routeren naar de back-end-adrespools. In dit voorbeeld maakt u een basis-listener die luistert naar verkeer op de basis-URL. 

Maak een listener met de naam *mydefaultListener* met behulp van [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) met de front-end-configuratie en de frontend-poort die u eerder hebt gemaakt. Er is een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maak een basis regel met de naam *firewallregel1* met behulp van [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$globalListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport80

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $globallistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
  
$siteListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport8080 `
  -FirewallPolicy $wafPolicySite
  
$frontendRuleSite = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $siteListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>De toepassingsgateway maken met de WAF

Nu u de nodige ondersteunende resources hebt gemaakt, geeft u para meters op voor de toepassings gateway met behulp van [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku). Geef het firewall beleid op met behulp van [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy). En maak vervolgens de toepassings gateway met de naam *myAppGateway* met behulp van [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport80 `
  -HttpListeners $globallistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicyGlobal
```

### <a name="apply-a-per-uri-policy"></a>Beleid per URI Toep assen

Als u een per-URI-beleid wilt Toep assen, maakt u gewoon een nieuw beleid en past u het toe op de regel configuratie van het pad. 

```azurepowershell-interactive
$policySettingURI = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicyURI = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingURI `
  -CustomRule $rule4, $rule5

$Gateway = Get-AzApplicationGateway -Name "myAppGateway"

$PathRuleConfig = New-AzApplicationGatewayPathRuleConfig -Name "base" `
  -Paths "/base" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings `
  -FirewallPolicy $wafPolicyURI

$PathRuleConfig1 = New-AzApplicationGatewayPathRuleConfig `
  -Name "base" -Paths "/test" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings

$URLPathMap = New-AzApplicationGatewayUrlPathMapConfig -Name "PathMap" `
  -PathRules $PathRuleConfig, $PathRuleConfig1 `
  -DefaultBackendAddressPoolId $defaultPool.Id `
  -DefaultBackendHttpSettingsId $poolSettings.Id

Add-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $AppGw `
  -Name "RequestRoutingRule" `
  -RuleType PathBasedRouting `
  -HttpListener $siteListener `
  -UrlPathMap $URLPathMap
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

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Een opslagaccount maken en diagnostische gegevens configureren

In dit artikel gebruikt de toepassingsgateway een opslagaccount voor het opslaan van gegevens voor detectie- en preventiedoeleinden. U kunt ook Azure Monitor-logboeken of Event Hub gebruiken om gegevens vast te leggen.

### <a name="create-the-storage-account"></a>Het opslagaccount maken

Maak een opslag account met de naam *myagstore1* met behulp van [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Diagnostische gegevens configureren

Configureer diagnoses om gegevens in de ApplicationGatewayAccessLog-, ApplicationGatewayPerformanceLog-en ApplicationGatewayFirewallLog-logboeken op te nemen met [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Category ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

U kunt [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) gebruiken om het open bare IP-adres van de toepassings gateway op te halen. Gebruik dit IP-adres vervolgens om te krul (Vervang de 1.1.1.1 die hieronder worden weer gegeven). 

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress

#should be blocked
curl 1.1.1.1/globalBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/globalAllow?1=1

#should be blocked
curl 1.1.1.1:8080/siteBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1:8080/siteAllow?1=1

#should be blocked
curl 1.1.1.1/URIBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/URIAllow?1=1
```

![Basis-URL testen in de toepassingsgateway](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, toepassings gateway en alle gerelateerde resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

[Regels voor Web Application Firewall aanpassen](application-gateway-customize-waf-rules-portal.md)
