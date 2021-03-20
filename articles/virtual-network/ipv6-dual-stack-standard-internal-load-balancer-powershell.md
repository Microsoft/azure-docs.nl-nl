---
title: Een IPv6-toepassing met dubbele stack implementeren met behulp van standaard interne Load Balancer in azure-Power shell
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u een IPv6-toepassing met dubbele stack implementeert met standaard interne Load Balancer in azure Virtual Network met behulp van Azure Power shell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220827"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Een IPv6-toepassing met dubbele stack implementeren met behulp van standaard interne Load Balancer in azure-Power shell (preview)

In dit artikel wordt beschreven hoe u een dual stack (IPv4 + IPv6)-toepassing implementeert in azure met een virtueel netwerk en subnet van twee stacks, een standaard interne Load Balancer met dubbele (IPv4 + IPv6) front-end configuraties, Vm's met Nic's met een dubbele IP-configuratie, netwerk beveiligings groep en open bare Ip's.

De procedure voor het maken van een intern Load Balancer die geschikt is voor IPv6, is bijna identiek aan het proces voor het maken van een Internet gerichte IPv6-Load Balancer die [hier](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)wordt beschreven. De enige verschillen bij het maken van een interne load balancer bevinden zich in de front-end-configuratie, zoals geïllustreerd in het onderstaande voor beeld van Power shell:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

De wijzigingen die het boven een interne configuratie van load balancer-front-end doen, zijn:
- De `PrivateIpAddressVersion` is opgegeven als ' IPv6 '
- Het `-PublicIpAddress` argument is wegge laten of vervangen door `-PrivateIpAddress` . Houd er rekening mee dat het privé adres zich binnen het bereik van de IP-ruimte van het subnet bevindt waarin de interne load balancer worden geïmplementeerd. Als er een statisch `-PrivateIpAddress` wordt wegge laten, wordt het volgende gratis IPv6-adres geselecteerd in het subnet waarin de interne Load Balancer is geïmplementeerd.
- Het dubbele stack subnet waarin de interne load balancer worden geïmplementeerd, wordt opgegeven met een of- `-Subnet` `-SubnetId` argument.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 6.9.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="prerequisites"></a>Vereisten
Voordat u een dual stack-toepassing in azure implementeert, moet u uw abonnement voor deze preview-functie configureren met behulp van de volgende Azure PowerShell:

Meld u als volgt aan:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. U kunt de registratie status controleren door de volgende Azure PowerShell opdracht uit te voeren: Controleer de registratie als volgt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Nadat de registratie is voltooid, voert u de volgende opdracht uit:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een virtueel netwerk met twee stacks kunt maken, moet u een resource groep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *dsStd_ILB_RG* gemaakt op de locatie *VS-Oost* :

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Open bare IPv4-en IPv6-adressen maken
Als u toegang wilt krijgen tot uw virtuele machines via internet, moet u de open bare IPv4-en IPv6-adressen voor de Vm's hebben. Open bare IP-adressen maken met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). In het volgende voor beeld worden het IPv4-en IPv6-open bare IP-adres gemaakt met de naam *RdpPublicIP_1* en *RdpPublicIP_2* in de *dsStd_ILB_RG* resource groep:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Het virtuele netwerk en het subnet maken

Maak een virtueel netwerk met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met dual stack een subnet-configuratie met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *dsVnet* met *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie configureert u dual front-end-IP (IPv4 en IPv6) en de back-end-adres groep voor de load balancer en maakt u vervolgens een Standard Load Balancer.

### <a name="create-front-end-ip"></a>Maak een front-end IP-adres

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). In het volgende voor beeld worden IPv4-en IPv6-front-end IP-configuraties met de naam *dsLbFrontEnd_v4* en *dsLbFrontEnd_v6* gemaakt:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-end-adres groep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld. In het volgende voor beeld worden back-end-adres groepen gemaakt met de naam *dsLbBackEndPool_v4* en *dsLbBackEndPool_v6* voor het toevoegen van Vm's met zowel IPv4-als IPv6-NIC-configuraties:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Om ervoor te zorgen dat alleen gezonde Vm's verkeer ontvangen, kunt u eventueel een status test definiëren. Basic load balancer gebruikt een IPv4-test om de status van IPv4-en IPv6-eind punten op de Vm's te beoordelen. Standard load balancer biedt ondersteuning voor expliciete IPv6-status controles.

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). In het volgende voor beeld worden load balancer-regels met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt het verkeer op *TCP* -poort *80* gebalanceerd naar de IPv4-en IPv6-front-end IP-configuraties:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Load balancer maken

Maak een Standard Load Balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). In het volgende voor beeld wordt een openbaar Standard Load Balancer gemaakt met de naam *myInternalLoadBalancer* met behulp van de IPv4-en IPv6-front-end-IP-configuraties, back-end-Pools en taakverdelings regels die u in de voor gaande stappen hebt gemaakt:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u enkele Vm's implementeert en uw Balancer kunt testen, moet u ondersteunende netwerk bronnen maken-beschikbaarheidsset, netwerk beveiligings groep en virtuele Nic's. 

### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Als u de hoge Beschik baarheid van uw toepassing wilt verbeteren, plaatst u uw virtuele machines in een beschikbaarheidsset.

Maak een beschikbaarheidsset met [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). In het volgende voor beeld wordt een beschikbaarheidsset gemaakt met de naam *dsAVset*:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep voor de regels die van toepassing zijn op binnenkomende en uitgaande communicatie in uw VNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 3389

Maak een netwerkbeveiligingsgroepsregel die RDP-verbindingen via poort 3389 toestaat met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80

Maak een regel voor de netwerk beveiligings groep waarmee Internet verbindingen via poort 80 met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)worden toegestaan.

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>NIC's maken

Virtuele Nic's maken met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). In het volgende voor beeld worden twee virtuele Nic's gemaakt met IPv4-en IPv6-configuraties. (Eén virtuele NIC voor elke VM die u in de volgende stappen voor uw app maakt).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Nu kunt u de VM’s maken met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack in Azure Portal weer geven
U kunt het virtuele IPv6-netwerk met dubbele stack als volgt weer geven in Azure Portal:
1. Voer in de zoek balk van de portal *dsVnet* in.
2. Wanneer **dsVnet** wordt weer gegeven in de zoek resultaten, selecteert u dit. Hiermee opent u de **overzichts** pagina van het virtuele netwerk met dubbele stack met de naam *dsVnet*. Het virtuele netwerk met dubbele stack toont de twee Nic's met zowel IPv4-als IPv6-configuraties die zich bevinden in het dubbele stack-subnet met de naam *dsSubnet*.

![IPv6 dubbele stack Virtual Network met standaard interne Load Balancer](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Het virtuele netwerk van IPv6 voor Azure is beschikbaar in het Azure Portal alleen-lezen voor deze preview-versie.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Standard Load Balancer met een dubbele frontend-IP-configuratie (IPv4 en IPv6) gemaakt. U hebt ook een twee virtuele machines gemaakt die Nic's bevatten met dubbele IP-configuraties (IPV4 + IPv6) die zijn toegevoegd aan de back-end-pool van de load balancer. Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md) voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.