---
title: Een Internet gerichte load balancer maken met IPv6-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Meer informatie over het maken van een Internet gerichte load balancer met IPv6 met behulp van Power shell voor Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure load balancer, dual stack, openbaar IP, systeem eigen IPv6, mobiel, IOT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 43203a756bcb42c7d00de9c11e9223f1d8b9e2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001584"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Aan de slag met het maken van een Internet gerichte load balancer met IPv6 met behulp van Power shell voor Resource Manager

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure-CLI](load-balancer-ipv6-internet-cli.md)
> * [Sjabloon](load-balancer-ipv6-internet-template.md)

>[!NOTE] 
>In dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic load balancers zowel IPv4-als IPv6-connectiviteit kunnen bieden. Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) , die IPv6-connectiviteit integreert met uw virtuele netwerken en die belang rijke functies bevat zoals regels voor IPv6-netwerk beveiligings groepen, door IPv6 door de gebruiker gedefinieerde route ring, IPv6-basis en standaard taak verdeling en meer.  IPv6 voor Azure VNETs is de aanbevolen standaard voor IPv6-toepassingen in Azure. Zie [IPv6 voor Azure VNET Power shell-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. De load balancer biedt hoge beschikbaarheid bij het verdelen van inkomend verkeer over gezonde service-exemplaren in cloudservices of virtuele machines in een load balancer-set. Azure Load Balancer kan deze services ook toepassen op meerdere poorten, meerdere IP-adressen of allebei.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Voor beeld van implementatie scenario

In het volgende diagram ziet u de oplossing voor taak verdeling die in dit artikel wordt geïmplementeerd.

![Load balancer-scenario](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

In dit scenario maakt u de volgende Azure-resources:

* een Internet gerichte Load Balancer met een IPv4-en een openbaar IP-adres voor IPv6
* twee taakverdelings regels om de open bare Vip's toe te wijzen aan de privé-eind punten
* een Beschikbaarheidsset met de twee virtuele machines
* twee virtuele machines (Vm's)
* een virtuele netwerk interface voor elke virtuele machine waaraan IPv4-en IPv6-adressen zijn toegewezen

## <a name="deploying-the-solution-using-the-azure-powershell"></a>De oplossing implementeren met behulp van de Azure PowerShell

De volgende stappen laten zien hoe u een Internet gerichte load balancer maakt met behulp van Azure Resource Manager met Power shell. Met Azure Resource Manager wordt elke resource afzonderlijk gemaakt en geconfigureerd, en vervolgens samengesteld om een resource te maken.

Als u een load balancer wilt implementeren, maakt en configureert u de volgende objecten:

* Front-end-IP-configuratie: bevat open bare IP-adressen voor binnenkomend netwerk verkeer.
* Back-endadresgroep: bevat netwerk interfaces (Nic's) voor de virtuele machines om netwerk verkeer van de load balancer te ontvangen.
* Regels voor taakverdeling: bevat regels die een openbare poort op de load balancer toewijzen aan een poort in de back-endadresgroep.
* NAT-regels voor binnenkomende verbindingen: bevat regels die een openbare poort op de load balancer toewijzen aan een poort voor een specifieke virtuele machine in de back-endadresgroep.
* Tests: bevat statustests die worden gebruikt om de beschikbaarheid van exemplaren van virtuele machines in de back-endadresgroep te controleren.

Zie [Azure Load Balancer-onderdelen](./components.md)voor meer informatie.

## <a name="set-up-powershell-to-use-resource-manager"></a>PowerShell instellen voor het gebruik van Resource Manager

Zorg ervoor dat u beschikt over de nieuwste productie versie van de module Azure Resource Manager voor Power shell.

1. Aanmelden bij Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Voer uw referenties in wanneer dit wordt gevraagd.

2. De abonnementen voor het account controleren

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Kies welk Azure-abonnement u wilt gebruiken.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Een resource groep maken (Sla deze stap over als u een bestaande resource groep gebruikt)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Een virtueel netwerk en een openbaar IP-adres voor de front-end-IP-adresgroep maken

1. Maak een virtueel netwerk met een subnet.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Maak Azure open bare IP-adres bronnen (PIP) voor de front-end-IP-adres groep. Zorg ervoor dat u de waarde voor wijzigt `-DomainNameLabel` voordat u de volgende opdrachten uitvoert. De waarde moet uniek zijn binnen de Azure-regio.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Het load balancer gebruikt het domein label van het open bare IP als voor voegsel voor de FQDN. In dit voor beeld zijn de FQDN- *lbnrpipv4.westus.cloudapp.Azure.com* en *lbnrpipv6.westus.cloudapp.Azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Een Front-End IP-configuraties en een Back-End-adres groep maken

1. Een front-end-adres configuratie maken die gebruikmaakt van de open bare IP-adressen die u hebt gemaakt.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Maak back-end-adres groepen.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>LB-regels, NAT-regels, een test en een load balancer maken

In dit voorbeeld worden de volgende items gemaakt:

* een NAT-regel om al het binnenkomende verkeer op poort 443 te vertalen naar poort 4443
* een load balancer-regel om al het verkeer dat binnenkomt op poort 80, gelijk te verdelen naar poort 80 op de adressen in de back-endgroep.
* een load balancer regel voor het toestaan van RDP-verbinding met de virtuele machines op poort 3389.
* een test regel om de integriteits status te controleren op een pagina met de naam *HealthProbe. aspx* of een service op poort 8080
* een load balancer waarbij al deze objecten worden gebruikt

1. Maak de NAT-regels.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Maak een statustest. Er zijn twee manieren om een steekproef te configureren:

    HTTP-test

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    of TCP-test

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    In dit voor beeld gaan we de TCP-tests gebruiken.

3. Maak een load balancer-regel.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Maak de load balancer met behulp van de eerder gemaakte objecten.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Nic's maken voor de back-end-Vm's

1. Haal het Virtual Network-en Virtual Network-subnet op, waar de Nic's moeten worden gemaakt.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. IP-configuraties en Nic's voor de virtuele machines maken.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Virtuele machines maken en de zojuist gemaakte Nic's toewijzen

Zie [een virtuele Windows-machine maken en vooraf configureren met Resource Manager en Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json) voor meer informatie over het maken van een VM.

1. Een Beschikbaarheidsset en een opslag account maken

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Maak elke VM en wijs de eerder gemaakte Nic's toe

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


