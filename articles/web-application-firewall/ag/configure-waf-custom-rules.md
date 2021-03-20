---
title: Aangepaste v2-regels configureren met Power shell
titleSuffix: Azure Web Application Firewall
description: Meer informatie over het configureren van aangepaste regels voor WAF-v2 (Web Application firewall) met behulp van Azure PowerShell. U kunt uw eigen regels maken die worden geëvalueerd voor elke aanvraag die via de firewall wordt door gegeven.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/21/2020
ms.author: victorh
ms.openlocfilehash: 2572e30c02552859eb5c61915a9ef524c0c6cc70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83758959"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Web Application firewall v2 op Application Gateway configureren met een aangepaste regel met behulp van Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Met aangepaste regels kunt u uw eigen regels maken die worden geëvalueerd voor elke aanvraag die wordt door gegeven via de Web Application firewall (WAF) v2. Deze regels hebben een hogere prioriteit dan de rest van de regels in de beheerde regelsets. De aangepaste regels hebben een actie (voor toestaan of blok keren), een match voorwaarde en een operator om de volledige aanpassing toe te staan.

In dit artikel maakt u een Application Gateway WAF v2 die gebruikmaakt van een aangepaste regel. Met de aangepaste regel wordt verkeer geblokkeerd als de aanvraagheader User-Agent *evilbot* bevat.

Zie [aangepaste Web Application firewall regels maken en gebruiken](create-custom-waf-rules.md) voor meer informatie over de voor beelden van aangepaste regels

Als u de Azure PowerShell in dit artikel wilt uitvoeren in één doorlopend script dat u kunt kopiëren, plakken en uitvoeren, raadpleegt u [Azure-toepassing gateway Power shell](powershell-samples.md)-voor beelden.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-powershell-module"></a>Azure PowerShell-module

Als u Azure PowerShell lokaal wilt installeren en gebruiken, is voor dit script moduleversie 2.1.0 of hoger van Azure PowerShell vereist.

1. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).
2. Voer `Connect-AzAccount` uit om een verbinding met Azure tot stand te brengen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Voorbeeldscript

### <a name="set-up-variables"></a>Variabelen instellen

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Een VNet maken

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Een statisch openbaar VIP maken

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Groep en front-end-poort maken

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Een listener, http-instelling, regel en automatisch schalen maken

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>Twee aangepaste regels maken en deze Toep assen op het WAF-beleid

```azurepowershell
# Create WAF config
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention" -RuleSetType "OWASP" -RuleSetVersion "3.0"
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>De Application Gateway maken

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="update-your-waf"></a>Uw WAF bijwerken

Nadat u uw WAF hebt gemaakt, kunt u deze bijwerken met behulp van een procedure die vergelijkbaar is met de volgende code:

```azurepowershell
# Get the existing policy
$policy = Get-AzApplicationGatewayFirewallPolicy -Name $policyName -ResourceGroupName $RGname
# Add an existing rule named $rule
$policy.CustomRules.Add($rule)
# Update the policy
Set-AzApplicationGatewayFirewallPolicy -InputObject $policy
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Web Application firewall op Application Gateway](ag-overview.md)
