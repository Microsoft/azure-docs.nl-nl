---
title: 'Zelfstudie: Toegang webtoepassing verbeteren - Azure Application Gateway'
description: In deze zelfstudie leert u hoe u een automatisch schalende, zone-redundante toepassingsgateway met een gereserveerd IP-adres maakt met Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2a756313a4659dfc531289c2c86890371f700367
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452285"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Zelfstudie: een toepassingsgateway maken die de toegang tot de webtoepassing verbetert

Als u als IT-beheerder betrokken bent bij het verbeteren van de toegang tot webtoepassingen, kunt u de toepassingsgateway zodanig optimaliseren dat de schaal ervan wordt aangepast aan de vraag van de klant en dat er meerdere beschikbaarheidszones worden bereikt. In deze zelfstudie wordt uitgelegd hoe u Azure Application Gateway-functies kunt configureren voor automatisch schalen, zone-redundantie en gereserveerde VIP's (statische IP). U maakt gebruik van Azure PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel om het probleem op te lossen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een virtueel netwerk automatisch schalen
> * Een gereserveerd openbaar IP-adres maken
> * De infrastructuur van de toepassingsgateway instellen
> * Automatisch schalen configureren
> * De toepassingsgateway maken
> * De toepassingsgateway testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelf studie moet u lokaal een beheer Azure PowerShell sessie uitvoeren. Versie 1.0.0 of hoger van de Azure PowerShell-module moet zijn geïnstalleerd. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep op een van de beschikbare locaties.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor gebruik in de productie moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](/powershell/module/pkiclient/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

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

Gebruik de vinger afdruk om het pfx-bestand te maken. Vervang door *\<password>* een wacht woord van uw keuze:

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
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
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>Gegevens ophalen

Haal gegevens op van de resourcegroep, het subnet en een IP in een lokaal object om de IP-configuratiegegevens van de toepassingsgateway te maken.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Web-apps maken

Configureer twee web-apps voor de back-end-groep. Vervang *\<site1-name>* en *\<site-2-name>* door unieke namen in het `azurewebsites.net` domein.

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>Infrastructuur configureren

Configureer de waarden voor IP-configuratie, front-end-IP-configuratie, back-endpool, HTTP-instellingen, certificaat, poort, listener en regel met exact dezelfde indeling als de bestaande standaardtoepassingsgateway. De nieuwe SKU volgt hetzelfde objectmodel als de standaard-SKU.

Vervang uw twee web-app-FQDN-namen (bijvoorbeeld: `mywebapp.azurewebsites.net` ) in de definitie van de $pool variabele.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Automatisch schalen configureren

U kunt nu de configuratie voor automatisch schalen opgeven voor de toepassingsgateway. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
In deze modus wordt de schaal van de toepassingsgateway automatisch aangepast op basis van het patroon van het toepassingsverkeer.

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Maak de toepassingsgateway en neem redundantie-zones en de configuratie voor automatisch schalen op.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik Get-AzPublicIPAddress om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres of de DNS-naam en plak het adres of de naam in de adresbalk van de browser.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>Resources opschonen

Verken eerst de resources die samen met de toepassingsgateway zijn gemaakt. U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen met de opdracht `Remove-AzResourceGroup` als u deze niet meer nodig hebt.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een toepassingsgateway maken met omleidingsregels op basis van een URL-pad](./tutorial-url-route-powershell.md)