---
title: Algemene Power shell-opdrachten voor virtuele Azure-netwerken
description: Algemene Power shell-opdrachten waarmee u aan de slag kunt gaan met het maken van een virtueel netwerk en de bijbehorende resources voor Vm's.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088373"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Algemene Power shell-opdrachten voor virtuele Azure-netwerken

Als u een virtuele machine wilt maken, moet u een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) maken of een bestaand virtueel netwerk weten waarin de VM kan worden toegevoegd. Wanneer u een virtuele machine maakt, moet u meestal ook overwegen om de resources te maken die in dit artikel worden beschreven.

Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement en het aanmelden bij uw account.

Sommige variabelen kunnen nuttig zijn wanneer u meer dan een van de opdrachten in dit artikel uitvoert:

- $location: de locatie van de netwerk bronnen. U kunt [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken om een [geografische regio](https://azure.microsoft.com/regions/) te vinden die geschikt is voor u.
- $myResourceGroup: de naam van de resource groep waarin de netwerk bronnen zich bevinden.

## <a name="create-network-resources"></a>Netwerkbronnen maken

| Taak | Opdracht |
| ---- | ------- |
| Subnetconfiguraties maken |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -name "mySubnet1"-AddressPrefix xx. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-name "mySubnet2"-AddressPrefix XX. X. X. X/XX<BR><BR>Een typisch netwerk heeft mogelijk een subnet voor een [Internet gerichte Load Balancer](../../load-balancer/load-balancer-overview.md) en een afzonderlijk subnet voor een [interne Load Balancer](../../load-balancer/load-balancer-overview.md). |
| Een virtueel netwerk maken |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -name "myVNet"-ResourceGroupName $MyResourceGroup-locatie $location-AddressPrefix xx. X. X. x/xx-subnet $subnet 1, $subnet 2 |
| Testen op een unieke domein naam |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -domeinnaam label "myDNS"-locatie $location<BR><BR>U kunt een DNS-domein naam opgeven voor een [open bare IP-resource](../../virtual-network/public-ip-addresses.md), waarmee een toewijzing voor domainname.location.cloudapp.Azure.com wordt gemaakt aan het open bare IP-adres op de door Azure beheerde DNS-servers. De naam mag alleen letters, cijfers en afbreekstreepjes bevatten. Het eerste en laatste teken moeten een letter of cijfer zijn en de domein naam moet uniek zijn binnen de Azure-locatie. Als **waar** wordt geretourneerd, is de voorgestelde naam wereld wijd uniek. |
| Een openbaar IP-adres maken |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -name "myPublicIp"-ResourceGroupName $MyResourceGroup-domeinnaam label "myDNS"-location $location-AllocationMethod Dynamic<BR><BR>Het open bare IP-adres gebruikt de domein naam die u eerder hebt getest en wordt gebruikt door de front-end-configuratie van de load balancer. |
| Een front-end-IP-configuratie maken |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -name "myFrontendIP"-PublicIpAddress $PIP<BR><BR>De front-end-configuratie bevat het open bare IP-adres dat u eerder hebt gemaakt voor binnenkomend netwerk verkeer. |
| Een back-endadresgroep maken |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -name "myBackendAddressPool"<BR><BR>Biedt interne adressen voor de back-end van de load balancer die toegankelijk zijn via een netwerk interface. |
| Een test maken |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -name "myProbe"-RequestPath ' healthProbe. aspx '-protocol http-poort 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Bevat status tests die worden gebruikt om de beschik baarheid van exemplaren van virtuele machines in de back-end-adres groep te controleren. |
| Een taakverdelings regel maken |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -name http-FrontendIpConfiguration $FrontendIP-BackendAddressPool $BeAddressPool-probe $HealthProbe-protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Bevat regels die een open bare poort op de load balancer toewijzen aan een poort in de back-end-adres groep. |
| Een binnenkomende NAT-regel maken |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -name "myInboundRule1"-FrontendIpConfiguration $FrontendIP-protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Bevat regels die een open bare poort op de load balancer toewijzen aan een poort voor een specifieke virtuele machine in de back-end-adres groep. |
| Een load balancer maken |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-name "myLoadBalancer"-location $location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-probe $healthProbe |
| Een netwerk interface maken |$nic 1 = [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-name "myNIC"-location $location-PrivateIpAddress xx. X. X. x-subnet $subnet 2-LoadBalancerBackendAddressPool $LoadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $LoadBalancer. InboundNatRules [0]<BR><BR>Maak een netwerk interface met behulp van het open bare IP-adres en het subnet van het virtuele netwerk dat u eerder hebt gemaakt. |

## <a name="get-information-about-network-resources"></a>Informatie over netwerk bronnen ophalen

| Taak | Opdracht |
| ---- | ------- |
| Virtuele netwerken weer geven |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle virtuele netwerken in de resource groep. |
| Informatie over een virtueel netwerk ophalen |Get-AzVirtualNetwork naam myVNet-ResourceGroupName $myResourceGroup |
| Subnetten in een virtueel netwerk weer geven |Get-AzVirtualNetwork naam ' myVNet '-ResourceGroupName $myResourceGroup &#124; Selecteer subnetten |
| Informatie over een subnet ophalen |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Hiermee haalt u informatie op over het subnet in het opgegeven virtuele netwerk. De $vnet waarde vertegenwoordigt het object dat door Get-AzVirtualNetwork wordt geretourneerd. |
| IP-adressen weer geven |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met de open bare IP-adressen in de resource groep. |
| Load balancers weer geven |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle load balancers in de resource groep. |
| Netwerk interfaces weer geven |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle netwerk interfaces in de resource groep. |
| Informatie over een netwerk interface ophalen |Get-AzNetworkInterface naam myNIC-ResourceGroupName $myResourceGroup<BR><BR>Hiermee haalt u informatie op over een specifieke netwerk interface. |
| De IP-configuratie van een netwerk interface ophalen |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -name "myNICIP"-Network Interface $NIC<BR><BR>Hiermee haalt u informatie op over de IP-configuratie van de opgegeven netwerk interface. De $nic waarde vertegenwoordigt het object dat door Get-AzNetworkInterface wordt geretourneerd. |

## <a name="manage-network-resources"></a>Netwerkresources beheren

| Taak | Opdracht |
| ---- | ------- |
| Een subnet toevoegen aan een virtueel netwerk |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix xx. X. X. X/XX-name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Hiermee voegt u een subnet toe aan een bestaand virtueel netwerk. De $vnet waarde vertegenwoordigt het object dat door Get-AzVirtualNetwork wordt geretourneerd. |
| Een virtueel netwerk verwijderen |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -name "myVNet"-ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven virtuele netwerk uit de resource groep. |
| Een netwerkinterface verwijderen |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven netwerk interface uit de resource groep. |
| Een load balancer verwijderen |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -name "myLoadBalancer"-ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven load balancer uit de resource groep. |
| Een openbaar IP-adres verwijderen |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-name "myIPAddress"-ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven open bare IP-adres uit de resource groep. |

## <a name="next-steps"></a>Volgende stappen
Gebruik de netwerk interface die u zojuist hebt gemaakt tijdens [het maken van een virtuele machine](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
