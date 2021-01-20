---
title: 'Snelstartgids: een persoonlijke Azure-koppelings service maken met behulp van Azure PowerShell'
description: Meer informatie over het maken van een persoonlijke Azure-koppelings service met Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/20/2021
ms.author: allensu
ms.openlocfilehash: 66ad5aae9f8175d154bb07a8b112dada175a205a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610060"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Een persoonlijke koppelings service maken met behulp van Azure PowerShell

Aan de slag met een Private Link-service die naar uw service verwijst.  Geef Private Link toegang tot uw service of resource die achter een Azure Standard Load Balancer is geïmplementeerd.  Gebruikers van uw service hebben persoonlijke toegang via hun virtuele netwerk.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```
---
## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

In dit gedeelte maakt u een virtueel netwerken en een interne Azure Load Balancer.

### <a name="virtual-network"></a>Virtueel netwerk

In dit gedeelte maakt u een virtueel netwerk en een subnet om de load balancer te hosten die toegang heeft tot uw Private Link-service.

* Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Een standaard load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

* Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) voor de front-end-IP-pool. Dit IP-adres ontvangt het binnenkomende verkeer op de load balancer

* Maak een back-end-adrespool met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) voor verkeer dat vanaf de front-end van de load balancer wordt verzonden. Deze groep is de locatie waar uw virtuele machines met back-end worden geïmplementeerd.

* Maak een statustest met behulp van [add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) die de status van de back-end-VM-exemplaren bepaalt.

* Maak een load balancer-regel met [add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) die definieert hoe verkeer naar de virtuele machines wordt gedistribueerd.

* Maak een openbare load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

In deze sectie maakt u een persoonlijke koppelings service die gebruikmaakt van de standaard Azure Load Balancer die u in de vorige stap hebt gemaakt.

* De IP-configuratie van de private link service maken met [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig).

* Maak de service voor persoonlijke koppelingen met [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice).

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$fe = Get-AzLoadBalancer -Name 'myLoadBalancer' | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de load balancer en de resterende resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* Een virtueel netwerk gemaakt en een interne Azure Load Balancer.
* Een Private Link-service gemaakt

Voor meer informatie over Azure Privé-eindpunt gaat u naar:
> [!div class="nextstepaction"]
> [Snelstartgids: een persoonlijk eind punt maken met Azure Power shell](create-private-endpoint-powershell.md)

