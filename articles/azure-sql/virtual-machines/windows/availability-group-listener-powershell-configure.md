---
title: Listeners en load balancer voor de beschikbaarheids groep configureren (Power shell)
description: Configureer listeners voor de beschikbaarheids groep op het Azure Resource Manager model met behulp van een interne load balancer met een of meer IP-adressen.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 147c507cde9abf2ef97098c6b41fbbd4d67f02d2
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324802"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Een of meer listeners voor Always on-beschikbaarheids groep configureren-Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit document wordt beschreven hoe u Power shell gebruikt om een van de volgende taken uit te voeren:
- een load balancer maken
- Voeg IP-adressen toe aan een bestaande load balancer voor SQL Server beschikbaarheids groepen.

Een beschikbaarheids groep-listener is een virtuele netwerk naam waarmee clients verbinding maken voor database toegang. In azure Virtual Machines bevat een load balancer het IP-adres voor de listener. Het load balancer stuurt verkeer naar het exemplaar van SQL Server dat luistert op de test poort. Normaal gesp roken gebruikt een beschikbaarheids groep een interne load balancer. Een interne load balancer van Azure kan een of meer IP-adressen hosten. Elk IP-adres gebruikt een specifieke test poort. 

De mogelijkheid om meerdere IP-adressen toe te wijzen aan een interne load balancer is nieuw in Azure en is alleen beschikbaar in het Resource Manager-model. Als u deze taak wilt volt ooien, moet u beschikken over een SQL Server-beschikbaarheids groep die is geïmplementeerd in azure Virtual Machines in het Resource Manager-model. Beide SQL Server virtuele machines moeten deel uitmaken van dezelfde beschikbaarheidsset. U kunt de [micro soft-sjabloon](./availability-group-quickstart-template-configure.md) gebruiken om automatisch de beschikbaarheids groep te maken in azure Resource Manager. Met deze sjabloon wordt automatisch de beschikbaarheids groep gemaakt, met inbegrip van de interne load balancer voor u. Als u wilt, kunt u [hand matig een always on-beschikbaarheids groep configureren](availability-group-manually-configure-tutorial.md).

Om de stappen in dit artikel te volt ooien, moeten uw beschikbaarheids groepen al zijn geconfigureerd.  

Verwante onderwerpen zijn onder andere:

* [AlwaysOn-beschikbaarheidsgroepen configureren in azure VM (GUI)](availability-group-manually-configure-tutorial.md)   
* [Een VNet-naar-VNet-verbinding configureren via Azure Resource Manager en PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell-versie bevestigen

De voor beelden in dit artikel worden getest met Azure PowerShell module versie 5.4.1.

Controleer of de Power shell-module 5.4.1 of hoger is.

Zie [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>De Windows Firewall configureren

Configureer de Windows Firewall om SQL Server toegang toe te staan. Met de firewall regels kunnen TCP-verbindingen met de poorten worden gebruikt door het SQL Server-exemplaar en de listener-test. Zie [Configure a Windows Firewall for data base engine Access](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1)(Engelstalig) voor gedetailleerde instructies. Maak een regel voor binnenkomende verbindingen voor de SQL Server poort en voor de test poort.

Als u de toegang met een Azure-netwerk beveiligings groep wilt beperken, moet u ervoor zorgen dat de regels voor toestaan de back-end-SQL Server VM-IP-adressen bevatten en de load balancer zwevende IP-adressen voor de AG-listener en het IP-adres van het cluster basis, indien van toepassing.

## <a name="determine-the-load-balancer-sku-required"></a>Bepalen welke load balancer SKU vereist is

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) is beschikbaar in twee Sku's: Basic & Standard. De standaard load balancer wordt aanbevolen. Als de virtuele machines zich in een beschikbaarheidsset bevinden, is basis load balancer toegestaan. Als de virtuele machines zich in een beschikbaarheids zone bevinden, is een standaard load balancer vereist. Standaard load balancer vereist dat alle IP-adressen van de virtuele machine standaard IP-adressen gebruiken.

De huidige [micro soft-sjabloon](./availability-group-quickstart-template-configure.md) voor een beschikbaarheids groep maakt gebruik van een basis Load Balancer met basis-IP-adressen.

   > [!NOTE]
   > U moet een [service-eind punt](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) configureren als u een standaard load balancer en Azure Storage voor de cloudwitness gebruikt. 
   > 

In de voor beelden in dit artikel wordt een standaard load balancer opgegeven. In de voor beelden bevat het script `-sku Standard` .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Als u een basis load balancer wilt maken, verwijdert u de `-sku Standard` regel waarmee de Load Balancer wordt gemaakt. Bijvoorbeeld:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Voorbeeld script: een interne load balancer maken met Power shell

> [!NOTE]
> Als u uw beschikbaarheids groep hebt gemaakt met de [micro soft-sjabloon](./availability-group-quickstart-template-configure.md), is de interne Load Balancer al gemaakt.

Met het volgende Power shell-script maakt u een interne load balancer, configureert u de regels voor taak verdeling en stelt u een IP-adres voor de load balancer. Als u het script wilt uitvoeren, opent u Windows PowerShell ISE en plakt u het script in het Script venster. Gebruik `Connect-AzAccount` om u aan te melden bij Power shell. Als u meerdere Azure-abonnementen hebt, gebruikt u `Select-AzSubscription` om het abonnement in te stellen. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Voorbeeld script: een IP-adres aan een bestaande load balancer toevoegen met Power shell

Als u meer dan één beschikbaarheids groep wilt gebruiken, voegt u een extra IP-adres toe aan de load balancer. Elk IP-adres vereist een eigen taakverdelings regel, test poort en front poort.

De front-end-poort is de poort die door toepassingen wordt gebruikt om verbinding te maken met het SQL Server-exemplaar. IP-adressen voor verschillende beschikbaarheids groepen kunnen dezelfde front-end-poort gebruiken.

> [!NOTE]
> Voor SQL Server-beschikbaarheids groepen is voor elk IP-adres een specifieke test poort vereist. Als bijvoorbeeld één IP-adres op een load balancer test poort 59999 gebruikt, kunnen er geen andere IP-adressen op die load balancer de test poort 59999 gebruiken.

* Zie voor meer informatie over load balancer limieten **persoonlijke front-end-IP per Load Balancer** onder [netwerk limieten-Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Zie voor meer informatie over limieten van de beschikbaarheids groep [beperkingen (beschikbaarheids groepen)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG).

Met het volgende script wordt een nieuw IP-adres toegevoegd aan een bestaand load balancer. De ILB maakt gebruik van de listener-poort voor de front-end-taakverdelings poort. Deze poort kan de poort zijn waarop SQL Server luistert. Voor de standaard exemplaren van SQL Server is de poort 1433. Voor de taakverdelings regel voor een beschikbaarheids groep is een zwevend IP-adres (Direct Server Return) vereist, zodat de back-end-poort hetzelfde is als de front-end-poort. Werk de variabelen voor uw omgeving bij. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>De listener configureren

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>De listener-poort instellen in SQL Server Management Studio

1. Start SQL Server Management Studio en maak verbinding met de primaire replica.

1. Ga naar **Hoge beschikbaarheid met AlwaysOn** > **Beschikbaarheidsgroepen** > **Listeners voor beschikbaarheidsgroep**. 

1. U ziet nu de naam van de listener die u hebt gemaakt in Failoverclusterbeheer. Klik met de rechtermuisknop op de naam en selecteer **Eigenschappen**.

1. Geef in het vak **poort** het poort nummer voor de beschikbaarheids groep-listener op met behulp van de $EndpointPort die u eerder hebt gebruikt (1433 was de standaard instelling) en selecteer **OK**.

## <a name="test-the-connection-to-the-listener"></a>De verbinding met de listener testen

Test als volgt de verbinding:

1. Gebruik Remote Desktop Protocol (RDP) om verbinding te maken met een SQL Server die zich in hetzelfde virtuele netwerk bevindt, maar geen eigenaar is van de replica. Dit kan de andere SQL Server in het cluster zijn.

1. Gebruik het **Sqlcmd** -hulp programma om de verbinding te testen. Met het volgende script wordt bijvoorbeeld met Windows-verificatie een **sqlcmd**-verbinding met de primaire replica tot stand gebracht via de listener:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Als de listener een andere poort dan de standaardpoort (1433) gebruikt, geeft u de poort op in de verbindingsreeks. De volgende Sqlcmd-opdracht maakt bijvoorbeeld verbinding met een listener op poort 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

De SQLCMD-verbinding maakt automatisch verbinding met het exemplaar van SQL Server dat als host voor de primaire replica fungeert. 

> [!NOTE]
> Zorg ervoor dat de poort die u opgeeft, geopend is op de firewall van beide SQL-servers. Voor beide servers is een regel voor binnenkomende verbindingen vereist voor de TCP-poort die u gebruikt. Zie [Add or Edit Firewall Rule](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)) (Firewallregel toevoegen of bewerken) voor meer informatie. 
> 

## <a name="guidelines-and-limitations"></a>Richt lijnen en beperkingen

Houd rekening met de volgende richt lijnen voor de beschikbaarheids groep-listener in azure met behulp van interne load balancer:

* Met een interne load balancer hebt u alleen toegang tot de listener vanuit hetzelfde virtuele netwerk.

* Als u de toegang wilt beperken met een Azure-netwerk beveiligings groep, moet u ervoor zorgen dat de regels voor toestaan bevatten:
  - De back-end-SQL Server VM-IP-adressen
  - Het load balancer zwevende IP-adressen voor de AG-listener
  - Het basis-IP-adres van het cluster, indien van toepassing.

* Maak een service-eind punt wanneer u een standaard load balancer met Azure Storage voor de cloudwitness gebruikt. Zie [toegang verlenen vanuit een virtueel netwerk](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)voor meer informatie.

## <a name="for-more-information"></a>Voor meer informatie

Zie AlwaysOn AlwaysOn- [beschikbaarheids groep in azure VM hand matig configureren](availability-group-manually-configure-tutorial.md)voor meer informatie.

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets

Gebruik de volgende Power shell-cmdlets om een interne load balancer voor Azure Virtual Machines te maken.

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) maakt een Load Balancer. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) maakt een front-end-IP-configuratie voor een Load Balancer. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) maakt een regel configuratie voor een Load Balancer. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) maakt een back-end-adres groep configuratie voor een Load Balancer. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) maakt een test configuratie voor een Load Balancer.
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) verwijdert een Load Balancer uit een Azure-resource groep.