---
title: Wederzijdse verificatie op Azure-toepassing gateway via Power shell configureren
description: Meer informatie over het configureren van een Application Gateway voor wederzijdse verificatie via Power shell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231433"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Wederzijdse verificatie met Application Gateway configureren via Power shell (preview)
In dit artikel wordt beschreven hoe u de Power shell gebruikt om wederzijdse verificatie op uw Application Gateway te configureren. Wederzijdse verificatie betekent Application Gateway verificatie van de client die de aanvraag verzendt met behulp van het client certificaat dat u uploadt naar de Application Gateway. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor dit artikel is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="before-you-begin"></a>Voordat u begint

Als u wederzijdse verificatie wilt configureren met een Application Gateway, hebt u een client certificaat nodig om te uploaden naar de gateway. Het client certificaat wordt gebruikt voor het valideren van het certificaat dat door de client wordt weer gegeven voor Application Gateway. Voor test doeleinden kunt u een zelfondertekend certificaat gebruiken. Dit wordt echter niet aanbevolen voor productie werkbelastingen, omdat ze moeilijker te beheren zijn en niet volledig zijn beveiligd.

Zie [overzicht van wederzijdse verificatie met Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)voor meer informatie, met name wat voor soort client certificaten u kunt uploaden.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak eerst een nieuwe resource groep in uw abonnement. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Implementeer een virtueel netwerk voor uw Application Gateway om te worden geïmplementeerd in.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Een openbaar IP-adres maken

Maak een openbaar IP-adres om met uw Application Gateway te gebruiken. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>De Application Gateway IP-configuratie maken

Maak de IP-configuraties en de frontend-poort. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Frontend-SSL configureren 

Configureer de SSL-certificaten voor uw Application Gateway.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Clientverificatie configureren 

Configureer client verificatie op uw Application Gateway. Zie voor meer informatie over het ophalen van de vertrouwde client CA-certificaat ketens om hier een [vertrouwde client CA-certificaat keten te extra](./mutual-authentication-certificate-management.md)heren.

> [!IMPORTANT]
> Zorg ervoor dat u de volledige CA-certificaat keten van de client in één bestand uploadt en slechts één keten per bestand.  

> [!NOTE]
> U kunt het beste TLS 1,2 gebruiken met wederzijdse verificatie als TLS 1,2 in de toekomst. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>De back-end-pool en-instellingen configureren

Stel de back-end-pool en-instellingen voor uw Application Gateway in. Stel optioneel het vertrouwde basis certificaat van de back-end in voor end-to-end SSL-versleuteling.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>De regel configureren

Stel een regel in op uw Application Gateway.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Standaard SSL-beleid voor toekomstige listeners instellen

U hebt een specifiek SSL-beleid voor listener ingesteld tijdens het instellen van wederzijdse verificatie. In deze stap kunt u optioneel het standaard SSL-beleid instellen voor toekomstige listeners die u maakt. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>De Application Gateway maken

Implementeer uw Application Gateway met alles wat u hierboven hebt gemaakt.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, toepassings gateway en alle gerelateerde resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Verlopen client certificaten vernieuwen

In het geval dat het CA-certificaat van de client is verlopen, kunt u het certificaat op uw gateway bijwerken door de volgende stappen uit te voeren: 

1. Aanmelden bij Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. De configuratie van uw Application Gateway ophalen
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Het vertrouwde client certificaat verwijderen van de gateway 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Het nieuwe certificaat toevoegen aan de gateway 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. De gateway bijwerken met het nieuwe certificaat 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Volgende stappen

- [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)