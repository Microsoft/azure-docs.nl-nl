---
title: Een Azure-toepassing gateway maken & HTTP-headers opnieuw schrijven
description: Dit artikel bevat informatie over het maken van een Azure-toepassing gateway en het herschrijven van HTTP-headers met Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396852"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Een toepassings gateway maken en HTTP-headers opnieuw schrijven

U kunt Azure PowerShell gebruiken om [regels te configureren voor het herschrijven van HTTP-aanvragen en-antwoord headers](rewrite-http-headers.md) wanneer u de nieuwe automatisch [schalen en zone-redundante Application Gateway-SKU](./application-gateway-autoscaling-zone-redundant.md) maakt

In dit artikel leert u het volgende:

* Een virtueel netwerk automatisch schalen
* Een gereserveerd openbaar IP-adres maken
* De infrastructuur van de toepassingsgateway instellen
* De configuratie van de regel voor het opnieuw schrijven van http-headers opgeven
* Automatisch schalen configureren
* De toepassingsgateway maken
* De toepassingsgateway testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor dit artikel moet u Azure PowerShell lokaal uitvoeren. U moet AZ module version 1.0.0 of later hebben geïnstalleerd. Voer uit `Import-Module Az` en `Get-Module Az` Zoek vervolgens naar de versie. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Login-AzAccount` uit om een verbinding op te zetten met Azure.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep op een van de beschikbare locaties.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met één speciaal subnet voor een toepassingsgateway met automatisch schalen. Op dit moment kan er in elk gereserveerd subnet maar één toepassingsgateway met automatisch schalen worden geïmplementeerd.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Een gereserveerd openbaar IP-adres maken

Stel de toewijzingsmethode van PublicIPAddress in op **Statisch**. De VIP van een toepassingsgateway met automatisch schalen kan alleen statisch zijn. Dynamische VIP's worden niet ondersteund. Alleen PublicIpAddress uit de standaard-SKU wordt ondersteund.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Gegevens ophalen

Haal gegevens op van de resourcegroep, het subnet en een IP in een lokaal object om de IP-configuratiegegevens van de toepassingsgateway te maken.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Infrastructuur configureren

Configureer de IP-configuratie, front-end IP-configuratie, back-end-pool, HTTP-instellingen, certificaat, poort en listener in een identieke indeling voor de bestaande standaard toepassings gateway. De nieuwe SKU volgt hetzelfde objectmodel als de standaard-SKU.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>De configuratie van de regel voor het opnieuw schrijven van HTTP-headers opgeven

Configureer de nieuwe objecten die vereist zijn voor het herschrijven van de HTTP-headers:

- **RequestHeaderConfiguration**: dit object wordt gebruikt om de velden van de aanvraag header op te geven die u wilt herschrijven en de nieuwe waarde waarnaar de oorspronkelijke headers moeten worden geschreven.
- **ResponseHeaderConfiguration**: dit object wordt gebruikt om de velden van de antwoord header op te geven die u wilt herschrijven en de nieuwe waarde waarnaar de oorspronkelijke headers moeten worden geschreven.
- **Actieset**: dit object bevat de configuraties van de hierboven opgegeven aanvraag-en antwoord headers. 
- **RewriteRule**: dit object bevat alle hierboven opgegeven *actionSets* . 
- **RewriteRuleSet**: dit object bevat alle *rewriteRules* en moet worden gekoppeld aan een regel voor aanvraag routering-basis-of op basis van een pad.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>De routerings regel opgeven

Een regel voor het door sturen van aanvragen maken. Nadat deze herschrijf configuratie is gemaakt, wordt deze aan de bron-listener gekoppeld via de routerings regel. Wanneer u een eenvoudige routerings regel gebruikt, wordt de configuratie voor het opnieuw schrijven van kopteksten gekoppeld aan een bronhost en is het herschrijven van de globale header. Wanneer een op een pad gebaseerde routerings regel wordt gebruikt, wordt de configuratie voor het opnieuw schrijven van de header gedefinieerd op de URL-pad toewijzing. Dit is dus alleen van toepassing op het specifieke pad naar het gebied van een site. Hieronder wordt een basis routerings regel gemaakt en de regelset herschrijven is gekoppeld.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Automatisch schalen configureren

U kunt nu de configuratie voor automatisch schalen opgeven voor de toepassingsgateway. Er worden twee configuratietypen voor automatisch schalen ondersteund:

* **Modus voor vaste capaciteit**. In deze modus wordt er geen automatisch schalen uitgevoerd voor de toepassingsgateway en werkt deze met een vaste schaaleenheid-capaciteit.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modus voor automatisch schalen**. In deze modus wordt de schaal van de toepassingsgateway automatisch aangepast op basis van het patroon van het toepassingsverkeer.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Maak de toepassingsgateway en neem redundantie-zones en de configuratie voor automatisch schalen op.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik Get-AzPublicIPAddress om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres of de DNS-naam en plak het adres of de naam in de adresbalk van de browser.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Resources opschonen

Verken eerst de resources die samen met de toepassingsgateway zijn gemaakt. U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen met de opdracht `Remove-AzResourceGroup` als u deze niet meer nodig hebt.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Volgende stappen

- [Een toepassingsgateway maken met omleidingsregels op basis van een URL-pad](./tutorial-url-route-powershell.md)