---
title: Gebruiken met interne Load Balancer-Azure-toepassing gateway
description: Op deze pagina vindt u instructies voor het maken, configureren, openen en verwijderen van een Azure-toepassingsgateway met een interne load balancer (ILB) voor Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 3d663dc4e2bd860ec9494785ecbf6dbf10a4c5b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397753"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Een toepassings gateway maken met een interne load balancer (ILB)

Azure Application Gateway kan worden geconfigureerd met een internetgerichte VIP of met een intern eindpunt dat geen toegang heeft tot het internet. Dit heet ook wel een ILB-eindpunt (interne load balancer). Het is een goed idee om de gateway te configureren met een ILB als u interne line-of-business-toepassingen gebruikt die geen toegang hebben tot het internet. Het is ook nuttig voor services en lagen in een toepassing met meerdere lagen die zich in een beveiligings grens bevinden die niet beschikbaar is op internet, maar wel een Round Robin-taak verdeling, sessie persistentie of Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), is beëindigd.

In dit artikel worden de stappen beschreven voor het configureren van een toepassingsgateway met een ILB.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installeer de nieuwste versie van de module Azure PowerShell door de [installatie-instructies](/powershell/azure/install-az-ps)te volgen.
2. U maakt een virtueel netwerk en een subnet voor de toepassingsgateway. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u voor gebruik van de toepassingsgateway configureert, moeten al bestaan in het virtuele netwerk of hier hun eindpunten hebben. Een andere optie is om er een openbaar IP- of VIP-adres aan toe te wijzen.

## <a name="what-is-required-to-create-an-application-gateway"></a>Wat is er vereist om een toepassingsgateway te maken?

* **Back-endserverpool:** de lijst met IP-adressen van de back-endservers. De IP-adressen moeten ofwel deel uitmaken van het virtueel netwerk, maar zich bevinden in een ander subnet voor de toepassingsgateway, ofwel openbare IP-/VIP-adressen zijn.
* **Instellingen voor de back-end-server groep:** Elke pool heeft instellingen, zoals poort, protocol en affiniteit op basis van cookies. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
* **Front-endpoort:** dit is de openbare poort die in de toepassingsgateway wordt geopend. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
* **Listener:** de listener beschikt over een front-endpoort, een protocol (Http of Https; deze zijn hoofdlettergevoelig) en de SSL-certificaatnaam (als u SSL-offloading configureert).
* **Regel:** de regel verbindt de listener met de back-endserverpool en definieert naar welke back-endserverpool het verkeer moet worden omgeleid wanneer dit bij een bepaalde listener aankomt. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Het verschil tussen het gebruik van Azure Classic en Azure Resource Manager zit hem in de volgorde waarin u de toepassingsgateway maakt en in de items die u moet configureren.
Met Resource Manager worden alle items waaruit een toepassingsgateway bestaat afzonderlijk geconfigureerd en vervolgens samengesteld om de toepassingsgatewayresource te maken.

Dit zijn de stappen voor het maken van een toepassingsgateway:

1. Een resourcegroep maken voor Resource Manager
2. Een virtueel netwerk en een subnet maken voor de toepassingsgateway
3. Een configuratieobject voor de toepassingsgateway maken
4. Een toepassingsgatewayresource maken

## <a name="create-a-resource-group-for-resource-manager"></a>Een resourcegroep maken voor Resource Manager

Zorg ervoor dat u overschakelt naar de PowerShell-modus om de Azure Resource Manager-cmdlets te gebruiken. Zie [Using Windows PowerShell with Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

### <a name="step-1"></a>Stap 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Stap 2

Controleer de abonnementen voor het account.

```powershell
Get-AzSubscription
```

U wordt gevraagd om u te verifiëren met uw referenties.

### <a name="step-3"></a>Stap 3

Kies welk Azure-abonnement u wilt gebruiken.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Stap 4

Maak een nieuwe resourcegroep (u kunt deze stap overslaan als u een bestaande resourcegroep gebruikt).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Zorg ervoor dat bij alle opdrachten voor het maken van een toepassingsgateway dezelfde resourcegroep wordt gebruikt.

In het vorige voor beeld is er een resource groep gemaakt met de naam ' appgw-RG ' en de locatie ' West US '.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een virtueel netwerk en een subnet maken voor de toepassingsgateway

In het volgende voorbeeld ziet u hoe u een virtueel netwerk maakt met Resource Manager:

### <a name="step-1"></a>Stap 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Met deze stap wordt het adres bereik 10.0.0.0/24 toegewezen aan een subnetische variabele die moet worden gebruikt om een virtueel netwerk te maken.

### <a name="step-2"></a>Stap 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Met deze stap maakt u een virtueel netwerk met de naam ' appgwvnet ' in de resource groep ' appgw-RG ' voor de regio vs-West met het voor voegsel 10.0.0.0/16 met subnet 10.0.0.0/24.

### <a name="step-3"></a>Stap 3

```powershell
$subnet = $vnet.subnets[0]
```

Met deze stap wordt het subnetobject toegewezen aan de variabele $subnet voor de volgende stappen.

## <a name="create-an-application-gateway-configuration-object"></a>Een configuratieobject voor de toepassingsgateway maken

### <a name="step-1"></a>Stap 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Met deze stap maakt u een IP-configuratie voor de toepassings gateway met de naam ' gatewayIP01 '. Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Onthoud dat elk exemplaar één IP-adres gebruikt.

### <a name="step-2"></a>Stap 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Met deze stap configureert u de back-end-IP-adres groep met de naam "pool01" met IP-adressen "10.1.1.8, 10.1.1.9, 10.1.1.10". Dit zijn de IP-adressen waardoor het netwerkverkeer van het front-end-IP-eindpunt binnenkomt. U vervangt de bovenstaande IP-adressen met de IP-adreseindpunten van uw eigen toepassing.

### <a name="step-3"></a>Stap 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Met deze stap configureert u de instelling ' poolsetting01 ' voor de toepassings gateway voor het netwerk verkeer met gelijke taak verdeling in de back-end-pool.

### <a name="step-4"></a>Stap 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Met deze stap configureert u de front-end-IP-poort met de naam ' frontendport01 ' voor de ILB.

### <a name="step-5"></a>Stap 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Met deze stap maakt u de front-end-IP-configuratie met de naam ' fipconfig01 ' en koppelt u deze aan een persoonlijk IP-adres uit het huidige subnet van het virtuele netwerk.

### <a name="step-6"></a>Stap 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Met deze stap maakt u de listener ' listener01 ' en koppelt u de front-end-poort aan de front-end-IP-configuratie.

### <a name="step-7"></a>Stap 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Met deze stap maakt u de load balancer-routerings regel met de naam ' rule01 ' waarmee het load balancer gedrag wordt geconfigureerd.

### <a name="step-8"></a>Stap 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Met deze stap configureert u de exemplaar grootte van de toepassings gateway.

> [!NOTE]
> De standaard waarde voor capaciteit is 2. Voor SKU-naam kunt u kiezen tussen Standard_Small, Standard_Medium en Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Een toepassingsgateway maken met behulp van New-AzureApplicationGateway

Hiermee maakt u een toepassings gateway met alle configuratie-items uit de voor gaande stappen. In dit voorbeeld heeft de toepassingsgateway de naam appgwtest.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Met deze stap maakt u een toepassings gateway met alle configuratie-items uit de voor gaande stappen. In dit voorbeeld heeft de toepassingsgateway de naam appgwtest.

## <a name="delete-an-application-gateway"></a>Een toepassingsgateway verwijderen

Als u een toepassings gateway wilt verwijderen, moet u de volgende stappen in de aangegeven volg orde uitvoeren:

1. Gebruik de cmdlet `Stop-AzApplicationGateway` om de gateway te stoppen.
2. Gebruik de cmdlet `Remove-AzApplicationGateway` om de gateway te verwijderen.
3. Gebruik de cmdlet `Get-AzureApplicationGateway` om te controleren of de gateway is verwijderd.

### <a name="step-1"></a>Stap 1

Haal het toepassingsgatewayobject op en koppel dit aan de variabele $getgw.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Stap 2

Gebruik `Stop-AzApplicationGateway` om de toepassingsgateway te stoppen. In dit voor beeld wordt de `Stop-AzApplicationGateway` cmdlet op de eerste regel weer gegeven, gevolgd door de uitvoer.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Nadat de toepassingsgateway is gestopt, gebruikt u de cmdlet `Remove-AzApplicationGateway` om de service te verwijderen.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> U kunt de switch **-force** gebruiken om het bevestigingsbericht voor de verwijdering niet te laten weergeven.

Gebruik de cmdlet `Get-AzApplicationGateway` als u wilt controleren of de service is verwijderd. Deze stap is niet vereist.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Volgende stappen

Als u SSL-offload wilt configureren, raadpleegt u [Configure an application gateway for SSL offload](./tutorial-ssl-powershell.md) (Een toepassingsgateway voor SSL-offload configureren).

Als u meer informatie wilt over de algemene opties voor load balancing, raadpleegt u:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)