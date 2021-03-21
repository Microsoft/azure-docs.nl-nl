---
title: "Zelf studie: een load balancer voor meerdere regio's maken met behulp van Azure PowerShell"
titleSuffix: Azure Load Balancer
description: Aan de slag met deze zelf studie een Azure Load Balancer voor meerdere regio's implementeren met behulp van Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721341"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Zelf studie: een Azure Load Balancer voor meerdere regio's maken met behulp van Azure PowerShell

Een load balancer voor meerdere regio's zorgt ervoor dat een service wereldwijd beschikbaar is in meerdere Azure-regio's. Als de ene regio uitvalt, wordt het verkeer doorgestuurd naar de dichtstbijzijnde regionale load balancer.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een load balancer voor meerdere regio's maken.
> * Maak een load balancer-regel.
> * Een back-end-pool met twee regionale load balancers maken.
> * De load balancer testen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Twee Azure Load Balancers met een **standaard**-SKU met back-end-pools die in twee verschillende Azure-regio's zijn geïmplementeerd.
    - Voor informatie over het maken van een regionale standaard load balancer en virtuele machines voor back-endservers, Zie [Quick Start: een open bare Load Balancer maken om taken te verdelen over vm's met behulp van Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Voeg de naam van de load balancers en virtuele machines in elke regio toe met een **-R1** en **-R2**. 
- Azure PowerShell lokaal of Azure Cloud Shell geïnstalleerd.


Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-cross-region-load-balancer"></a>Een load balancer voor meerdere regio's maken


### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Load balancer resources voor meerdere regio's maken

In deze sectie maakt u de resources die nodig zijn voor de load balancer van de andere regio.

Er wordt een algemeen standaard SKU-IP-adres gebruikt voor de front-end load balancer.

* Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om het open bare IP-adres te maken.

* Maak een front-end-IP-configuratie met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Maak een back-end-adres groep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Maak een Load Balancer voor meerdere regio's met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Back-end-groep configureren

In deze sectie voegt u twee regionale standaard load balancers toe aan de back-end-pool van de load balancer voor meerdere regio's.

> [!IMPORTANT]
> Als u deze stappen wilt uitvoeren, moet u ervoor zorgen dat er twee regionale load balancers met back-end-pools zijn geïmplementeerd in uw abonnement.  Zie voor meer informatie Quick Start **[: een open bare Load Balancer maken om taken van vm's te verdelen met behulp van Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Gebruik [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) en [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) om de regionale Load Balancer informatie op te slaan in variabelen.

* Gebruik [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) om de configuratie van de back-endadresgroep te maken voor de Load Balancer.

* Gebruik [set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) om de regionale Load Balancer frontend toe te voegen aan de back-end van de andere regio.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Load balancer testen

In deze sectie gaat u de load balancer voor meerdere regio's testen. U maakt verbinding met het openbare IP-adres in een webbrowser.  U stopt de virtuele machines in een van de back-end-pools van de regionale load balancer en bekijkt de failover.

1. Gebruik [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de load balancer op te halen:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

3. Stop de virtuele machines in de back-end-pool van een van de regionale load balancers.

4. Vernieuw de webbrowser en bekijk de failover van de verbinding naar de andere regionale load balancer.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de load balancer en de resterende resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* Er is een globaal IP-adres gemaakt.
* Een load balancer voor meerdere regio's gemaakt.
* Een load balancer-regel toegevoegd.
* Regionale standaard load balancers toegevoegd aan de back-end-pool van de load balancer voor meerdere regio's.
* De load balancer getest.


Ga naar het volgende artikel om te lezen hoe u dit doet...
> [!div class="nextstepaction"]
> [Load Balancer-Vm's in verschillende beschikbaarheids zones](tutorial-load-balancer-standard-public-zone-redundant-portal.md)