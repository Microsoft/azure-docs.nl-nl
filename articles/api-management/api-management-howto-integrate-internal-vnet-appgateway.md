---
title: Hoe u API Management in Virtual Network met Application Gateway
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure-API Management in Interne Virtual Network met Application Gateway (WAF) als FrontEnd
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6500ecdb811306239951cb339abe2043d77b8cf2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813052"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>API Management in een intern VNET integreren met Application Gateway

## <a name="overview"></a><a name="overview"></a> Overzicht

De API Management-service kan worden geconfigureerd in een Virtual Network in de interne modus, waardoor deze alleen toegankelijk is vanuit de Virtual Network. Azure Application Gateway is een PAAS-service die een Laag-7-load balancer. Het fungeert als een omgekeerde proxyservice en biedt onder andere een Web Application Firewall (WAF).

Als u API Management in een intern VNET combineert met Application Gateway front-end, zijn de volgende scenario's mogelijk:

* Gebruik dezelfde API Management voor gebruik door zowel interne als externe consumenten.
* Gebruik één resource API Management en er is een subset api's gedefinieerd in API Management beschikbaar zijn voor externe gebruikers.
* Een gebruikssleutel bieden om de toegang tot uw API Management via het openbare internet in of uit te schakelen.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u de stappen wilt volgen die in dit artikel worden beschreven, hebt u het volgende nodig:

* Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificaten: pfx en cer voor de API-hostnaam en pfx voor de hostnaam van de ontwikkelaarsportal.

## <a name="scenario"></a><a name="scenario"></a> Scenario

In dit artikel wordt beschreven hoe u één API Management-service gebruikt voor zowel interne als externe gebruikers en hoe u deze als één front-en-service laat fungeren voor zowel on-premises API's als cloud-API's. U ziet ook hoe u alleen een subset van uw API's beschikbaar maakt (in het voorbeeld dat ze groen zijn gemarkeerd) voor Extern verbruik met behulp van routeringsfunctionaliteit die beschikbaar is in Application Gateway.

In het eerste installatievoorbeeld worden al uw API's alleen beheerd vanuit uw Virtual Network. Interne consumenten (oranje gemarkeerd) hebben toegang tot al uw interne en externe API's. Verkeer gaat nooit naar internet. Connectiviteit met hoge prestaties wordt geleverd via Express Route-circuits.

![URL-route](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Voordat u begint

* Zorg ervoor dat u de nieuwste versie van Azure PowerShell gebruikt. Zie de installatie-instructies in [Azure PowerShell.](/powershell/azure/install-az-ps) 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Wat is er vereist om een integratie tussen API Management en Application Gateway?

* **Back-endserverpool:** Dit is het interne virtuele IP-adres van de API Management service.
* **Instellingen voor back-endserverpools:** Elke pool heeft instellingen zoals poort, protocol en op cookies gebaseerde affiniteit. Deze instellingen worden toegepast op alle servers in de groep.
* **Front-endpoort:** Dit is de openbare poort die wordt geopend op de toepassingsgateway. Verkeer dat wordt gebruikt, wordt omgeleid naar een van de back-endservers.
* **Listener:** De listener heeft een front-endpoort, een protocol (Http of Https; deze waarden zijn case-gevoelig) en de TLS/SSL-certificaatnaam (als u TLS-offload configureert).
* **Regel:** De regel verbindt een listener met een back-endservergroep.
* **Aangepaste statustest:** Application Gateway gebruikt standaard tests op basis van IP-adressen om te achterhalen welke servers in de BackendAddressPool actief zijn. De API Management-service reageert alleen op aanvragen met de juiste hostheader, waardoor de standaardtests mislukken. Er moet een aangepaste statustest worden gedefinieerd om application gateway te helpen bepalen of de service springlevend is en aanvragen moet doorsturen.
* **Aangepaste domeincertificaten:** Als u API Management internet wilt openen, moet u een CNAME-toewijzing maken van de hostnaam ervan aan Application Gateway front-end-DNS-naam. Dit zorgt ervoor dat de hostnaamheader en het certificaat die worden verzonden naar Application Gateway die worden doorgestuurd naar API Management één APIM als geldig kan worden herkend. In dit voorbeeld gebruiken we twee certificaten: voor de back-en de ontwikkelaarsportal.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Vereiste stappen voor het integreren van API Management en Application Gateway

1. Maak een resourcegroep voor Resource Manager.
2. Maak een Virtual Network, subnet en openbaar IP-adres voor de Application Gateway. Maak nog een subnet voor API Management.
3. Maak een API Management-service in het hierboven gemaakte VNET-subnet en zorg ervoor dat u de interne modus gebruikt.
4. Stel een aangepaste domeinnaam in de API Management service.
5. Maak een Application Gateway-configuratieobject.
6. Maak een Application Gateway resource.
7. Maak een CNAME van de openbare DNS-naam van de Application Gateway naar API Management proxyhostnaam.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>De ontwikkelaarsportal extern via een Application Gateway

In deze handleiding wordt de ontwikkelaarsportal **ook** voor externe doelgroepen via de Application Gateway. Er zijn extra stappen nodig om de listener, test, instellingen en regels van de ontwikkelaarsportal te maken. Alle details worden opgegeven in de desbetreffende stappen.

> [!WARNING]
> Als u Azure AD of verificatie van derden gebruikt, moet u de functie [Sessie-affiniteit](../application-gateway/features.md#session-affinity) op basis van cookies inschakelen in Application Gateway.

> [!WARNING]
> Om te voorkomen Application Gateway WAF het downloaden van OpenAPI-specificatie in de ontwikkelaarsportal kan verbreken, moet u de firewallregel `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` uitschakelen.
> 
> Application Gateway WAF-regels, die de functionaliteit van de portal kunnen breken, zijn onder andere:
> 
> - `920300`, `920330` , , , , , , , , voor de `931130` `942100` `942110` `942180` `942200` `942260` `942340` `942370` beheermodus
> - `942200`, `942260` , , , voor de `942370` `942430` `942440` gepubliceerde portal

## <a name="create-a-resource-group-for-resource-manager"></a>Een resourcegroep maken voor Resource Manager

### <a name="step-1"></a>Stap 1

Meld u aan bij Azure.

```powershell
Connect-AzAccount
```

Verifieert u met uw referenties.

### <a name="step-2"></a>Stap 2

Selecteer het gewenste abonnement.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Stap 3

Maak een resourcegroep (u kunt deze stap overslaan als u een bestaande resourcegroep gebruikt).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Zorg ervoor dat alle opdrachten voor het maken van een toepassingsgateway dezelfde resourcegroep gebruiken.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een Virtual Network en een subnet voor de toepassingsgateway maken

In het volgende voorbeeld ziet u hoe u een Virtual Network maakt met Resource Manager.

### <a name="step-1"></a>Stap 1

Wijs het adresbereik 10.0.0.0/24 toe aan de subnetvariabele die moet worden gebruikt voor Application Gateway tijdens het maken van een Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Stap 2

Wijs het adresbereik 10.0.1.0/24 toe aan de subnetvariabele die moet worden gebruikt voor API Management tijdens het maken van een Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Stap 3

Maak een Virtual Network met de **naam appgwvnet** in de resourcegroep **apim-appGw-RG** voor de regio VS - west. Gebruik het voorvoegsel 10.0.0.0/16 met subnetten 10.0.0.0/24 en 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Stap 4

Een subnetvariabele toewijzen voor de volgende stappen

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Een API Management-service binnen een VNET maken die is geconfigureerd in de interne modus

In het volgende voorbeeld ziet u hoe u een API Management maakt in een VNET dat alleen is geconfigureerd voor interne toegang.

### <a name="step-1"></a>Stap 1

Maak een API Management Virtual Network-object met behulp van het subnet $apimsubnetdata hierboven is gemaakt.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Stap 2

Maak een API Management service in de Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Nadat de bovenstaande opdracht is geslaagd, raadpleegt u DE DNS-configuratie die is vereist voor toegang tot de interne [VNET-API Management-service](api-management-using-with-internal-vnet.md#apim-dns-configuration) om er toegang toe te krijgen. Deze stap kan meer dan een half uur duren.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Een aangepaste domeinnaam instellen in API Management

> [!IMPORTANT]
> Voor [de nieuwe ontwikkelaarsportal](api-management-howto-developer-portal.md) moet naast de onderstaande stappen ook connectiviteit worden API Management het beheer-eindpunt van de API Management inschakelen.

### <a name="step-1"></a>Stap 1

Initialiseer de volgende variabelen met de details van de certificaten met persoonlijke sleutels voor de domeinen. In dit voorbeeld gebruiken we `api.contoso.net` en `portal.contoso.net` .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Stap 2

Maak en stel de hostnaamconfiguratieobjecten voor de proxy en voor de portal in.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Als u de connectiviteit van de verouderde ontwikkelaarsportal wilt configureren, moet u `-HostnameType DeveloperPortal` vervangen door `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Een openbaar IP-adres maken voor de front-endconfiguratie

Maak een openbare IP-resource **publicIP01** in de resourcegroep.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Er wordt een IP-adres toegewezen aan de toepassingsgateway wanneer de service wordt gestart.

## <a name="create-application-gateway-configuration"></a>Configuratie van toepassingsgateway maken

Alle configuratie-items moeten zijn ingesteld voordat u de toepassingsgateway maakt. Volg de onderstaande stappen om de configuratie-items te maken die nodig zijn voor een toepassingsgatewayresource.

### <a name="step-1"></a>Stap 1

Maak een IP-configuratie voor de toepassingsgateway **met de naam gatewayIP01.** Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Onthoud dat elk exemplaar één IP-adres gebruikt.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Stap 2

Configureer de front-end-IP-poort voor het openbare IP-eindpunt. Deze poort is de poort waar eindgebruikers verbinding mee maken.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Stap 3

Configureer het front-end-IP-adres met openbaar IP-eindpunt.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Stap 4

Configureer de certificaten voor de Application Gateway, die worden gebruikt voor het ontsleutelen en opnieuw versleutelen van het verkeer dat wordt passeren.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Stap 5

Maak de HTTP-listeners voor de Application Gateway. Wijs de front-end-IP-configuratie, poort en TLS/SSL-certificaten aan deze certificaten toe.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Stap 6

Maak aangepaste tests naar het API Management proxydomein `ContosoApi` van de service. Het pad `/status-0123456789abcdef` is een standaard health-eindpunt dat wordt gehost op alle API Management services. Stel `api.contoso.net` in als een aangepaste testhostnaam om deze te beveiligen met het TLS/SSL-certificaat.

> [!NOTE]
> De hostnaam is de standaardhostnaam van de proxy die is geconfigureerd wanneer een service met de naam `contosoapi.azure-api.net` wordt gemaakt in openbare `contosoapi` Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Stap 7

Upload het certificaat dat moet worden gebruikt voor de resources in de back-uppool met TLS-mogelijkheden. Dit is hetzelfde certificaat dat u hebt opgegeven in stap 4 hierboven.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Stap 8

Configureer de HTTP-back-Application Gateway. Dit omvat het instellen van een time-outlimiet voor back-end-aanvragen, waarna ze worden geannuleerd. Deze waarde verschilt van de time-out van de test.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Stap 9

Configureer een back-end-IP-adresgroep met de naam **apimbackend**  met het interne virtuele IP-adres van de API Management service die hierboven is gemaakt.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Stap 10

Maak regels voor de Application Gateway basisroutering te gebruiken.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Wijzig het -RuleType en de routering om de toegang tot bepaalde pagina's van de ontwikkelaarsportal te beperken.

### <a name="step-11"></a>Stap 11

Configureer het aantal exemplaren en de grootte voor de Application Gateway. In dit voorbeeld gebruiken we de [WAF-SKU](../web-application-firewall/ag/ag-overview.md) voor een betere beveiliging van de API Management resource.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Stap 12

Configureer WAF in de modus Preventie.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een Application Gateway met alle configuratieobjecten uit de voorgaande stappen.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME de API Management proxyhostnaam voor de openbare DNS-naam van de Application Gateway resource

Wanneer de gateway is gemaakt, gaat u in de volgende stap de front-end voor communicatie configureren. Wanneer u een openbaar IP-adres gebruikt, Application Gateway een dynamisch toegewezen DNS-naam vereist, die mogelijk niet eenvoudig te gebruiken is.

De DNS Application Gateway naam van de Application Gateway moet worden gebruikt om een CNAME-record te maken die de HOSTnaam van de APIM-proxy (bijvoorbeeld in de bovenstaande voorbeelden) naar deze `api.contoso.net` DNS-naam wijst. Als u de CNAME-record voor de front-end-IP wilt configureren, haalt u de details van de Application Gateway en de bijbehorende IP-/DNS-naam op met behulp van het publicIPAddress-element. Het gebruik van A-records wordt niet aanbevolen omdat het VIP kan worden gewijzigd bij het opnieuw opstarten van de gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Samenvatting
Azure API Management geconfigureerd in een VNET biedt één gatewayinterface voor alle geconfigureerde API's, ongeacht of deze on-premises of in de cloud worden gehost. De integratie van Application Gateway met API Management biedt de flexibiliteit om bepaalde API's selectief toegankelijk te maken op internet en een Web Application Firewall als een front-API Management te bieden aan uw API Management-exemplaar.

## <a name="next-steps"></a><a name="next-steps"></a> Volgende stappen
* Meer informatie over Azure Application Gateway
  * [Application Gateway overzicht](../application-gateway/overview.md)
  * [Application Gateway Web Application Firewall](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway padgebaseerde routering gebruiken](../application-gateway/tutorial-url-route-powershell.md)
* Meer informatie over API Management en VTE's
  * [Gebruik API Management alleen beschikbaar binnen het VNET](api-management-using-with-internal-vnet.md)
  * [Een API Management gebruiken in VNET](api-management-using-with-vnet.md)
