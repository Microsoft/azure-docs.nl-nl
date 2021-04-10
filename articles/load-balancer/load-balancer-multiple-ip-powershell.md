---
title: Taak verdeling op meerdere IP-configuraties-Azure CLI
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over de taak verdeling over de primaire en secundaire IP-configuraties met behulp van Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a2916f28be0b45eec6e9c1a85c0b8db3fb611381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417855"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Taak verdeling op meerdere IP-configuraties met behulp van Power shell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

In dit artikel wordt beschreven hoe u Azure Load Balancer gebruikt met meerdere IP-adressen op een secundaire netwerk interface (NIC). Voor dit scenario hebben we twee Vm's met Windows, elk met een primaire en een secundaire NIC. Elk van de secundaire Nic's heeft twee IP-configuraties. Elke virtuele machine host zowel websites contoso.com als fabrikam.com. Elke website is gebonden aan een van de IP-configuraties op de secundaire NIC. We gebruiken Azure Load Balancer om twee frontend-IP-adressen beschikbaar te maken, één voor elke website, om verkeer te distribueren naar de respectieve IP-configuratie voor de website. In dit scenario wordt hetzelfde poort nummer gebruikt voor zowel front-ends als voor beide IP-adressen van de back-end-pool.

![Afbeelding van LB-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Stappen voor taak verdeling op meerdere IP-configuraties

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Volg de onderstaande stappen om het scenario te verduidelijken dat in dit artikel wordt beschreven:

1. Installeer Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement en het aanmelden bij uw account.
2. Maak een resource groep met de volgende instellingen:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Zie voor meer informatie stap 2 van [een resource groep maken](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Maak een beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) die uw virtuele machines bevat. Voor dit scenario gebruikt u de volgende opdracht:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Volg de instructies stappen 3 tot en met 5 in [een Windows VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) -artikel maken om het maken van een virtuele machine met één NIC voor te bereiden. Voer stap 6,1 uit en gebruik het volgende in plaats van stap 6,2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Maak vervolgens [een Windows VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) -stap 6,3 tot en met 6,8.

5. Voeg een tweede IP-configuratie toe aan elke virtuele machine. Volg de instructies in het artikel [meerdere IP-adressen toewijzen aan virtuele machines](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) . Gebruik de volgende configuratie-instellingen:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    U hoeft de secundaire IP-configuraties niet te koppelen aan open bare Ip's voor het doel van deze zelf studie. Bewerk de opdracht om het open bare IP-koppelings onderdeel te verwijderen.

6. Voer de stappen 4 tot en met 6 van dit artikel opnieuw uit voor VM2. Zorg ervoor dat u de VM-naam vervangt door VM2 wanneer u dit doet. Houd er rekening mee dat u geen virtueel netwerk hoeft te maken voor de tweede virtuele machine. U kunt al dan niet een nieuw subnet maken op basis van uw use-case.

7. Maak twee open bare IP-adressen en sla deze op in de juiste variabelen, zoals wordt weer gegeven:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Twee frontend-IP-configuraties maken:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Maak uw back-end-adres groepen, een test en uw taakverdelings regels:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Nadat u deze resources hebt gemaakt, maakt u uw load balancer:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Voeg de tweede back-end-adres groep en de front-end-IP-configuratie toe aan de nieuw gemaakte load balancer:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. De onderstaande opdrachten krijgen de Nic's en voegen vervolgens beide IP-configuraties van elke secundaire NIC toe aan de back-end-adres groep van de load balancer:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Ten slotte moet u DNS-bron records configureren zodat deze verwijzen naar het respectieve IP-adres van de frontend van de Load Balancer. U kunt uw domeinen hosten in Azure DNS. Zie [Azure DNS gebruiken met andere Azure-Services](../dns/dns-for-azure-services.md)voor meer informatie over het gebruik van Azure DNS met Load Balancer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het combi neren van Load Balancing-Services in azure [met behulp van taakverdelings Services in azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Meer informatie over hoe u verschillende soorten logboeken in azure kunt gebruiken voor het beheren en oplossen van problemen met load balancer in [Azure monitor logboeken voor Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).