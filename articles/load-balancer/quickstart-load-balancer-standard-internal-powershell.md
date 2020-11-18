---
title: 'Quickstart: Een interne load balancer maken - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In deze snelstartgids vindt u informatie over het maken van een interne load balancer via Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696775"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Quickstart: Een interne load balancer maken met Azure PowerShell om taken te verdelen over VM's

Ga aan de slag met Azure Load Balancer via Azure PowerShell om een interne load balancer en twee virtuele machines te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* Met de naam **CreateIntLBQS-rg**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Een virtueel netwerk en een Azure Bastion-host maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Met de naam **myVNet**.
* In resourcegroep **CreateIntLBQS-rg**.
* Subnet met de naam **myBackendSubnet**.
* Virtueel netwerk **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.
* Subnet met de naam **AzureBastionSubnet**.
* Subnet **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Openbaar IP-adres maken voor Azure Bastion-host

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om een openbaar IP-adres voor de Bastion-host te maken:

* Met de naam **myPublicIPBastion**
* In resourcegroep **CreateIntLBQS-rg**.
* Op de locatie **eastus**.
* Toewijzingsmethode **statisch**.
* **Standaard** SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [New-AzBastion](/powershell/module/az.network/new-azbastion) om een bastion-host te maken:

* Met de naam **myBastion**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* Gekoppeld aan openbaar IP-adres **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep om binnenkomende verbindingen met uw virtuele netwerk te definiëren.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80
Maak een netwerkbeveiligingsgroepregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleHTTP**.
* Beschrijving: **HTTP toestaan**.
* Toegang: **Toestaan**.
* Protocol **(*)** .
* Richting: **Inkomend**.
* Prioriteit: **2000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel **poort 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Met de naam **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* Met beveiligingsregels die in de vorige stappen zijn gemaakt en zijn opgeslagen in een variabele.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Een standaard load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-frontend-ip"></a>Front-end-IP maken

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Met de naam **myFrontEnd**.
* Privé ip-adres van **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-endadresgroep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Met de naam **myBackEndPool**.
* In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Voer een statustest uit met [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Controleert de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Met de naam **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Time-out voor inactiviteit: **15 minuten**.
* Schakel TCP opnieuw instellen in.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>De virtuele machines in de back-end-pool hebben geen uitgaande internetverbinding met deze configuratie. </br> Voor meer informatie over het bieden van uitgaande connectiviteit raadpleegt u: </br> **[Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)**</br> Opties voor het bieden van connectiviteit: </br> **[Load balancer-configuratie voor alleen uitgaand verkeer](egress-only.md)** </br> **[Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Resource voor load balancer maken

Maak een interne load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Met de naam **myLoadBalancer**
* In **eastus**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Netwerkinterfaces maken

Maak drie netwerkinterfaces met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Met de naam **myNicVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Met de naam **myNicVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Maak de virtuele machines met:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Met de naam **myVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* In **Zone 1**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Met de naam **myVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* In **Zone 2**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Een virtueel netwerk en een Azure Bastion-host maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Met de naam **myVNet**.
* In resourcegroep **CreateIntLBQS-rg**.
* Subnet met de naam **myBackendSubnet**.
* Virtueel netwerk **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.
* Subnet met de naam **AzureBastionSubnet**.
* Subnet **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Openbaar IP-adres maken voor Azure Bastion-host

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om een openbaar IP-adres voor de Bastion-host te maken:

* Met de naam **myPublicIPBastion**
* In resourcegroep **CreateIntLBQS-rg**.
* Op de locatie **eastus**.
* Toewijzingsmethode **statisch**.
* **Standaard** SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [New-AzBastion](/powershell/module/az.network/new-azbastion) om een bastion-host te maken:

* Met de naam **myBastion**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* Gekoppeld aan openbaar IP-adres **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.


### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep om binnenkomende verbindingen met uw virtuele netwerk te definiëren.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80
Maak een netwerkbeveiligingsgroepregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleHTTP**.
* Beschrijving: **HTTP toestaan**.
* Toegang: **Toestaan**.
* Protocol **(*)** .
* Richting: **Inkomend**.
* Prioriteit: **2000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel **poort 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Met de naam **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* Met beveiligingsregels die in de vorige stappen zijn gemaakt en zijn opgeslagen in een variabele.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Een eenvoudige load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-frontend-ip"></a>Front-end-IP maken

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Met de naam **myFrontEnd**.
* Privé ip-adres van **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-endadresgroep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Met de naam **myBackEndPool**.
* In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Voer een statustest uit met [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Controleert de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Met de naam **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Time-out voor inactiviteit: **15 minuten**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Resource voor load balancer maken

Maak een openbare load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Met de naam **myLoadBalancer**
* In **eastus**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Netwerkinterfaces maken

Maak drie netwerkinterfaces met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Met de naam **myNicVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Met de naam **myNicVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Een beschikbaarheidsset voor virtuele machines maken

Gebruik [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) om een beschikbaarheidsset voor de virtuele machines te maken:

* Met de naam **myAvSet**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Maak de virtuele machines met:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Met de naam **myVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Op de locatie **eastus**.
* In de beschikbaarheidsset **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Met de naam **myVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Op de locatie **eastus**.
* In de beschikbaarheidsset **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Het duurt enkele minuten voordat de drie VM's zijn gemaakt en geconfigureerd.

---

## <a name="install-iis"></a>IIS installeren

Gebruik [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest) om de aangepaste scriptextensie te installeren. 

De extensie voert PowerShell Add-WindowsFeature Web-Server uit om de IIS-webserver te installeren en werkt vervolgens de pagina Default.htm bij om de hostnaam van de VM weer te geven:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Load balancer testen

### <a name="create-network-interface"></a>Een netwerkinterface maken

Maak een netwerkinterface met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* Met de naam **myNicTestVM**.
* In resourcegroep **CreateIntLBQS-rg**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Virtuele machine maken

Stel een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Maak de virtuele machine met:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Met de naam **myTestVM**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicTestVM**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Testen

1. [Meld u aan](https://portal.azure.com) bij Azure Portal.

1. Zoek op het scherm **Overzicht** het privé-IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer**.

2. Noteer of kopieer het adres naast **Privé IP-adres** in het **Overzicht** van **myLoadBalancer**.

3. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myTestVM**, die zich in de resourcegroep **CreateIntLBQS-rg** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Een interne load balancer van het type Standard maken" border="true":::
   
U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de load balancer en de resterende resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u:

* U hebt een interne load balancer van het type standaard of basis gemaakt
* Virtuele machines gekoppeld. 
* De load balancer-verkeersregel en de statustest geconfigureerd.
* De load balancer getest.

Als u meer informatie wilt over Azure Load Balancer, gaat u naar
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)