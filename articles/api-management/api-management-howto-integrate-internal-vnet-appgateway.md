---
title: API Management gebruiken in Virtual Network met Application Gateway
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure-API Management in interne Virtual Network met Application Gateway (WAF) als front-end
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
ms.openlocfilehash: 3db1c8bfc3a11151342589af0873d88e3d90c6a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91825633"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>API Management in een intern VNET integreren met Application Gateway

## <a name="overview"></a><a name="overview"></a> Overzicht

De API Management-service kan worden geconfigureerd in een Virtual Network in de interne modus, waardoor deze alleen toegankelijk is vanuit de Virtual Network. Azure-toepassing gateway is een PAAS-service, die een Layer-7-load balancer biedt. Het fungeert als een omgekeerde proxy service en biedt een WAF (Web Application firewall).

Als u API Management die is ingericht in een intern VNET combineert met de Application Gateway front-end, worden de volgende scenario's ingeschakeld:

* Gebruik dezelfde API Management resource voor verbruik door interne consumenten en externe consumenten.
* Gebruik één API Management resource en een subset van Api's gedefinieerd in API Management die beschikbaar is voor externe gebruikers.
* Bieden een andere manier om de toegang tot API Management van het open bare Internet te scha kelen in-en uitschakelen.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het volgen van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

* Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificaten: pfx en CER voor de API-hostnaam en pfx voor de hostnaam van de ontwikkelaars Portal.

## <a name="scenario"></a><a name="scenario"></a> Scenario

In dit artikel wordt beschreven hoe u een single API Management-service gebruikt voor zowel interne als externe consumenten en hoe u deze kunt gebruiken als één frontend voor zowel on-premises als Cloud-Api's. U ziet ook hoe u slechts een subset van uw Api's beschikbaar maakt (in het voor beeld dat ze in het groen zijn gemarkeerd) voor extern verbruik met behulp van route ring beschikbaar in Application Gateway.

In het eerste voor beeld van Setup worden al uw Api's alleen beheerd vanuit uw Virtual Network. Interne consumenten (oranje gemarkeerd) hebben toegang tot al uw interne en externe Api's. Verkeer gaat nooit naar Internet. Connectiviteit met hoge prestaties wordt geleverd via Express route-circuits.

![URL-route](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Voordat u begint

* Zorg ervoor dat u de nieuwste versie van Azure PowerShell gebruikt. Zie de installatie-instructies bij de [installatie Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Wat is er vereist voor het maken van een integratie tussen API Management en Application Gateway?

* **Back-end-server groep:** Dit is het interne virtuele IP-adres van de API Management service.
* **Instellingen voor de back-end-server groep:** Elke pool heeft instellingen, zoals poort, protocol en affiniteit op basis van cookies. Deze instellingen worden toegepast op alle servers in de groep.
* **Front-end-poort:** Dit is de open bare poort die wordt geopend op de Application Gateway. Aangestuurd verkeer wordt omgeleid naar een van de back-endservers.
* **Listener:** De listener heeft een front-end-poort, een protocol (http of HTTPS, deze waarden zijn hoofdletter gevoelig) en de TLS/SSL-certificaat naam (bij het configureren van TLS-offload).
* **Regel:** De regel verbindt een listener met een back-endserver-Server groep.
* **Aangepaste status test:** Application Gateway maakt standaard gebruik van tests op basis van IP-adressen om erachter te komen welke servers in de BackendAddressPool actief zijn. De API Management-service reageert alleen op aanvragen met de juiste host-header, daarom mislukken de standaard tests. Er moet een aangepaste status test worden gedefinieerd om de toepassings gateway te helpen bepalen dat de service actief is en dat aanvragen worden doorgestuurd.
* **Aangepaste domein certificaten:** Om toegang te krijgen tot API Management vanaf internet, moet u een CNAME-toewijzing van de hostnaam maken naar de Application Gateway front-end-DNS-naam. Dit zorgt ervoor dat de naam van de host-header en het certificaat die worden verzonden naar Application Gateway die naar API Management worden doorgestuurd, één APIM kan herkennen als geldig. In dit voor beeld gebruiken we twee certificaten: voor de back-end en voor de ontwikkelaars Portal.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Stappen die vereist zijn voor het integreren van API Management en Application Gateway

1. Maak een resourcegroep voor Resource Manager.
2. Maak een Virtual Network, subnet en openbaar IP-adres voor de Application Gateway. Maak een ander subnet voor API Management.
3. Maak een API Management-service in het eerder gemaakte VNET-subnet en zorg ervoor dat u de interne modus gebruikt.
4. Stel een aangepaste domein naam in de API Management-service in.
5. Maak een Application Gateway-configuratie object.
6. Een Application Gateway-resource maken.
7. Maak een CNAME van de open bare DNS-naam van de Application Gateway naar de API Management proxy-hostnaam.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>De ontwikkelaars Portal extern beschikbaar maken via Application Gateway

In deze hand leiding wordt de **ontwikkelaars Portal** ook beschikbaar voor externe doel groepen via de Application Gateway. Hiervoor zijn aanvullende stappen vereist voor het maken van de listener, test, instellingen en regels van de ontwikkelaars Portal. Alle details vindt u in de verschillende stappen.

> [!WARNING]
> Als u Azure AD of authenticatie van derden gebruikt, schakelt u de [cookie-functie voor sessie affiniteit op basis van cookies](../application-gateway/features.md#session-affinity) in Application Gateway.

> [!WARNING]
> Als u wilt voor komen dat Application Gateway WAF het downloaden van de OpenAPI-specificatie in de ontwikkelaars Portal verbreekt, moet u de firewall regel uitschakelen `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway WAF-regels, die de functionaliteit van de portal kunnen verstoren zijn:
> 
> - `920300`,,,,,,,, `920330` `931130` `942100` `942110` `942180` `942200` `942260` `942340` `942370` voor de beheer modus
> - `942200`,,,, `942260` `942370` `942430` `942440` voor de gepubliceerde Portal

## <a name="create-a-resource-group-for-resource-manager"></a>Een resourcegroep maken voor Resource Manager

### <a name="step-1"></a>Stap 1

Meld u aan bij Azure.

```powershell
Connect-AzAccount
```

Verifieer met uw referenties.

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

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Zorg ervoor dat alle opdrachten voor het maken van een toepassings gateway dezelfde resource groep gebruiken.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een Virtual Network en een subnet voor de toepassings gateway maken

In het volgende voor beeld ziet u hoe u een Virtual Network maakt met Resource Manager.

### <a name="step-1"></a>Stap 1

Wijs het adres bereik 10.0.0.0/24 toe aan de subnet-variabele die moet worden gebruikt voor Application Gateway tijdens het maken van een Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Stap 2

Wijs het adres bereik 10.0.1.0/24 toe aan de subnet-variabele die moet worden gebruikt voor API Management tijdens het maken van een Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Stap 3

Maak een Virtual Network met de naam **appgwvnet** in de resource groep **APIM-appGw-RG** voor de regio vs-West. Gebruik het voor voegsel 10.0.0.0/16 met subnetten 10.0.0.0/24 en 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Stap 4

Een subnet-variabele toewijzen voor de volgende stappen

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Een API Management-service maken binnen een VNET dat is geconfigureerd in de interne modus

In het volgende voor beeld ziet u hoe u een API Management-service maakt in een VNET dat is geconfigureerd voor alleen interne toegang.

### <a name="step-1"></a>Stap 1

Maak een API Management Virtual Network-object met behulp van het subnet $apimsubnetdata dat hierboven is gemaakt.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Stap 2

Maak een API Management-service in de Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Nadat de bovenstaande opdracht is uitgevoerd, raadpleegt u de [DNS-configuratie die is vereist om toegang te krijgen tot de interne VNET-API Management service](api-management-using-with-internal-vnet.md#apim-dns-configuration) om deze te openen. Deze stap kan meer dan een helft van een uur duren.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Een aangepaste domein naam instellen in API Management

> [!IMPORTANT]
> De [nieuwe ontwikkelaars Portal](api-management-howto-developer-portal.md) moet ook verbinding met het beheer eindpunt van de API Management inschakelen, naast de onderstaande stappen.

### <a name="step-1"></a>Stap 1

Initialiseer de volgende variabelen met de details van de certificaten met persoonlijke sleutels voor de domeinen. In dit voor beeld gebruiken we `api.contoso.net` en `portal.contoso.net` .  

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

Maak en stel de hostnamen-configuratie objecten voor de proxy en voor de portal in.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Als u de verouderde ontwikkelaars Portal connectiviteit wilt configureren, moet u vervangen door `-HostnameType DeveloperPortal` `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Een openbaar IP-adres maken voor de front-endconfiguratie

Maak een **publicIP01** voor de open bare IP-resource in de resource groep.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Er wordt een IP-adres toegewezen aan de toepassingsgateway wanneer de service wordt gestart.

## <a name="create-application-gateway-configuration"></a>De configuratie van de toepassings gateway maken

Alle configuratie-items moeten zijn ingesteld voordat u de toepassingsgateway maakt. Volg de onderstaande stappen om de configuratie-items te maken die nodig zijn voor een toepassingsgatewayresource.

### <a name="step-1"></a>Stap 1

Een IP-configuratie voor de toepassings gateway maken met de naam **gatewayIP01**. Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Onthoud dat elk exemplaar één IP-adres gebruikt.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Stap 2

Configureer de front-end-IP-poort voor het open bare IP-eind punt. Deze poort is de poort waarmee eind gebruikers verbinding maken.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Stap 3

Configureer het front-end-IP-adres met openbaar IP-eindpunt.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Stap 4

Configureer de certificaten voor de Application Gateway, die wordt gebruikt voor het ontsleutelen en opnieuw versleutelen van het verkeer dat wordt door gegeven.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Stap 5

Maak de HTTP-listeners voor de Application Gateway. Wijs de front-end-IP-configuratie, poort en TLS/SSL-certificaten aan deze toe.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Stap 6

Aangepaste tests maken voor het domein eindpunt van de API Management-service `ContosoApi` proxy. Het pad `/status-0123456789abcdef` is een standaard status eindpunt dat wordt gehost op alle API Management Services. Stel in `api.contoso.net` als een aangepaste test-hostnaam om deze te beveiligen met het TLS/SSL-certificaat.

> [!NOTE]
> De hostnaam `contosoapi.azure-api.net` is de standaard hostnaam van de proxy die is geconfigureerd wanneer een service met de naam `contosoapi` wordt gemaakt in open bare Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Stap 7

Upload het certificaat dat u wilt gebruiken voor de back-endservers van de voor TLS ingeschakelde bronnen. Dit is het certificaat dat u in stap 4 hierboven hebt gegeven.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Stap 8

Configureer de HTTP-back-end-instellingen voor de Application Gateway. Dit omvat het instellen van een time-outlimiet voor de back-end-aanvraag, waarna deze wordt geannuleerd. Deze waarde wijkt af van de time-out van de test.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Stap 9

Configureer een back-end-IP-adres groep met de naam **apimbackend**  met het interne virtuele IP-adres van de API Management-service die hierboven is gemaakt.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Stap 10

Regels maken voor de Application Gateway om basis routering te gebruiken.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Wijzig de-RuleType en-route ring om de toegang tot bepaalde pagina's van de ontwikkelaars portal te beperken.

### <a name="step-11"></a>Stap 11

Configureer het aantal exemplaren en de grootte voor de Application Gateway. In dit voor beeld gebruiken we de [WAF-SKU](../web-application-firewall/ag/ag-overview.md) voor een betere beveiliging van de API Management resource.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Stap 12

Configureer WAF in de modus voor preventie.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een Application Gateway met alle configuratie objecten uit de voor gaande stappen.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME de hostnaam van de API Management proxy naar de open bare DNS-naam van de Application Gateway resource

Wanneer de gateway is gemaakt, gaat u in de volgende stap de front-end voor communicatie configureren. Bij het gebruik van een openbaar IP-adres Application Gateway een dynamisch toegewezen DNS-naam vereist, die mogelijk niet eenvoudig te gebruiken is.

De DNS-naam van de Application Gateway moet worden gebruikt om een CNAME-record te maken die verwijst naar de hostnaam van de APIM-proxy (bijvoorbeeld `api.contoso.net` in de bovenstaande voor beelden) naar deze DNS-naam. Als u de frontend-IP CNAME-record wilt configureren, haalt u de details van de Application Gateway en de bijbehorende IP/DNS-naam op met het element PublicIPAddress. Het gebruik van A-records wordt niet aanbevolen, omdat het VIP kan worden gewijzigd bij het opnieuw starten van de gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Samen vatting
Azure API Management geconfigureerd in een VNET biedt één gateway-interface voor alle geconfigureerde Api's, ongeacht of deze on-premises of in de cloud worden gehost. Het integreren van Application Gateway met API Management biedt de flexibiliteit om specifieke Api's selectief toegankelijk te maken op internet en biedt een firewall voor webtoepassingen als een front-end voor uw API Management-exemplaar.

## <a name="next-steps"></a><a name="next-steps"></a> Volgende stappen
* Meer informatie over Azure-toepassing gateway
  * [Overzicht van Application Gateway](../application-gateway/overview.md)
  * [Application Gateway Web Application firewall](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway met behulp van op pad gebaseerde route ring](../application-gateway/tutorial-url-route-powershell.md)
* Meer informatie over API Management en VNETs
  * [API Management gebruiken die alleen beschikbaar zijn in het VNET](api-management-using-with-internal-vnet.md)
  * [API Management in VNET gebruiken](api-management-using-with-vnet.md)
