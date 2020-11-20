---
title: Multi-SID-configuratie met hoge Beschik baarheid maken in azure
description: Meer informatie over het maken van een multi-SID-configuratie met een hoge Beschik baarheid van SAP NetWeaver op virtuele Windows-machines.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d9e611ea3bd1243ef93bd5731199c549ed7e431
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951295"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Een multi-SID-configuratie met een hoge Beschik baarheid maken

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

In september 2016 heeft micro soft een functie uitgebracht waarmee u meerdere virtuele IP-adressen kunt beheren door gebruik te maken van een interne load balancer van Azure. Deze functionaliteit bestaat al in de externe Azure-load balancer.

Als u een SAP-implementatie hebt, kunt u een interne load balancer gebruiken om een Windows-cluster configuratie te maken voor SAP ASCS/SCS, zoals beschreven in de [hand leiding voor High-AVAILABILITY SAP NetWeaver op Windows-vm's][sap-ha-guide].

In dit artikel wordt uitgelegd hoe u vanuit één ASCS/SCS-installatie overstapt naar een SAP multi-SID-configuratie door extra geclusterde SAP ASCS/SCS-exemplaren in een bestaand WSFC-cluster (Windows Server failover clustering) te installeren. Wanneer dit proces is voltooid, hebt u een SAP multi-SID-cluster geconfigureerd.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
U hebt al een WSFC-cluster geconfigureerd dat wordt gebruikt voor één SAP ASCS/SCS-exemplaar, zoals beschreven in de [hand leiding voor een hoge beschik BAARHEID SAP net-Weaver op Windows-vm's][sap-ha-guide] , zoals wordt weer gegeven in dit diagram.

![SAP ASCS/SCS-instantie met hoge Beschik baarheid][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Doel architectuur

Het doel is om meerdere SAP ABAP ASCS of SAP Java SCS geclusterde instanties in hetzelfde WSFC-cluster te installeren, zoals hier wordt geïllustreerd:

![Meerdere op SAP ASCS/SCS geclusterde instanties in azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Er geldt een limiet voor het aantal privé-front-end Ip's voor elke interne Azure-load balancer.
>
>Het maximum aantal SAP-ASCS/SCS-exemplaren in één WSFC-cluster is gelijk aan het maximum aantal privé-front-end Ip's voor elke interne Azure-load balancer.
>

Zie ' persoonlijk IP-adres voor front-end per load balancer ' in [netwerk limieten: Azure Resource Manager][networking-limits-azure-resource-manager]voor meer informatie over limieten voor load balancers.

De volledige liggende periode met twee SAP-systemen met hoge Beschik baarheid ziet er als volgt uit:

![Multi-SID-installatie van SAP met hoge Beschik baarheid met twee SAP systeem-Sid's][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> De installatie moet voldoen aan de volgende voor waarden:
> - De SAP ASCS/SCS-instanties moeten hetzelfde WSFC-cluster delen.
> - Elke DBMS-SID moet een eigen toegewezen WSFC-cluster hebben.
> - SAP-toepassings servers die deel uitmaken van één SAP-systeem-SID moeten hun eigen toegewezen Vm's hebben.


## <a name="prepare-the-infrastructure"></a>De infrastructuur voorbereiden
Als u uw infra structuur wilt voorbereiden, kunt u een extra SAP ASCS/SCS-exemplaar installeren met de volgende para meters:

| Parameternaam | Waarde |
| --- | --- |
| SAP ASCS/SCS SID |PR1-lb-ascs |
| SAP DBMS interne load balancer | PR5 |
| Naam van SAP-virtuele host | PR5-SAP-cl |
| IP-adres van de virtuele host van SAP ASCS/SCS (extra Azure load balancer IP-adres) | 10.0.0.50 |
| SAP-ASCS/SCS-instantie nummer | 50 |
| ILB-test poort voor extra SAP ASCS/SCS-exemplaar | 62350 |

> [!NOTE]
> Voor SAP-ASCS/SCS-cluster instanties is voor elk IP-adres een unieke test poort vereist. Als bijvoorbeeld één IP-adres op een interne Azure-load balancer test poort 62300 gebruikt, kan er geen ander IP-adres op dat load balancer gebruik van test poort 62300.
>
>Voor onze doel einden, omdat test poort 62300 al is gereserveerd, gebruiken we test poort 62350.

U kunt extra SAP ASCS/SCS-exemplaren in het bestaande WSFC-cluster met twee knoop punten installeren:

| Virtuele-machine functie | Hostnaam van virtuele machine | Statisch IP-adres |
| --- | --- | --- |
| 1e cluster knooppunt voor ASCS/SCS-instantie |PR1-ascs-0 |10.0.0.10 |
| 2e cluster knooppunt voor ASCS/SCS-instantie |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>De naam van een virtuele host voor het geclusterde SAP ASCS/SCS-exemplaar op de DNS-server maken

U kunt een DNS-vermelding maken voor de naam van de virtuele host van het ASCS/SCS-exemplaar met behulp van de volgende para meters:

| Nieuwe SAP ASCS/SCS virtual host name | Gekoppeld IP-adres |
| --- | --- |
|PR5-SAP-cl |10.0.0.50 |

De nieuwe hostnaam en het IP-adres worden weer gegeven in DNS-beheer, zoals wordt weer gegeven in de volgende scherm afbeelding:

![DNS-beheer lijst met de gedefinieerde DNS-vermelding voor de nieuwe SAP ASCS/SCS-cluster naam en het TCP/IP-adres][sap-ha-guide-figure-6004]

De procedure voor het maken van een DNS-vermelding wordt ook uitvoerig beschreven in de belangrijkste [hand leiding voor SAP NetWeaver met hoge Beschik baarheid op Windows-vm's][sap-ha-guide-9.1.1].

> [!NOTE]
> Het nieuwe IP-adres dat u toewijst aan de naam van de virtuele host van het extra ASCS/SCS-exemplaar moet hetzelfde zijn als het nieuwe IP-adres dat u hebt toegewezen aan de SAP Azure-load balancer.
>
>In ons scenario is het IP-adres 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Een IP-adres aan een bestaande Azure interne load balancer toevoegen met behulp van Power shell

Als u meer dan één SAP ASCS/SCS-exemplaar in hetzelfde WSFC-cluster wilt maken, gebruikt u Power shell om een IP-adres toe te voegen aan een bestaande Azure interne load balancer. Elk IP-adres vereist een eigen taakverdelings regel, test poort, front-end-IP-adres groep en back-end-pool.

Met het volgende script wordt een nieuw IP-adres toegevoegd aan een bestaand load balancer. Werk de Power shell-variabelen voor uw omgeving bij. Met het script worden alle benodigde taakverdelings regels gemaakt voor alle SAP-ASCS/SCS-poorten.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Nadat het script is uitgevoerd, worden de resultaten weer gegeven in de Azure Portal, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Nieuwe front-end-IP-adres groep in de Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Schijven toevoegen aan cluster machines en de schijf met de SIOS-cluster share configureren

U moet een nieuwe cluster share schijf toevoegen voor elk extra SAP-ASCS/SCS-exemplaar. Voor Windows Server 2012 R2 is de schijf met het WSFC-cluster share die momenteel in gebruik is de data Keeper-software oplossing van SIOS.

Ga als volgt te werk:
1. Voeg een extra schijf of schijven van dezelfde grootte toe (die u moet verwijderen) naar elk van de cluster knooppunten en Format teer deze.
2. Configureer de opslag replicatie met SIOS data keeper.

Bij deze procedure wordt ervan uitgegaan dat u op de WSFC-cluster machines al SIOS data keeper hebt geïnstalleerd. Als u deze hebt geïnstalleerd, moet u nu replicatie tussen de computers configureren. Het proces wordt uitvoerig beschreven in de belangrijkste [hand leiding voor SAP NetWeaver met hoge Beschik baarheid op Windows-vm's][sap-ha-guide-8.12.3.3].  

![Data keeper synchrone mirroring voor de nieuwe SAP-schijf voor ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Vm's voor SAP-toepassings servers en DBMS-cluster implementeren

Ga als volgt te werk om de voor bereiding van de infra structuur voor het tweede SAP-systeem te volt ooien:

1. Implementeer specifieke Vm's voor SAP-toepassings servers en plaats ze in hun eigen toegewezen beschikbaarheids groep.
2. Implementeer specifieke Vm's voor het DBMS-cluster en plaats ze in hun eigen toegewezen beschikbaarheids groep.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Installeer het tweede SAP SID2 NetWeaver-systeem

Het volledige proces voor het installeren van een tweede SAP SID2-systeem wordt beschreven in de belangrijkste [hand leiding voor SAP NetWeaver met hoge Beschik baarheid op Windows-vm's][sap-ha-guide-9].

De high-level-procedure is als volgt:

1. [Installeer het eerst het SAP-cluster knooppunt][sap-ha-guide-9.1.2].  
 In deze stap installeert u SAP met een ASCS/SCS-exemplaar met hoge Beschik baarheid op het **bestaande WSFC-cluster knooppunt 1**.

2. [Wijzig het SAP-Profiel van het ASCS/SCS-exemplaar][sap-ha-guide-9.1.3].

3. [Een test poort configureren][sap-ha-guide-9.1.4].  
 In deze stap configureert u een SAP cluster resource SAP-SID2-IP-test poort met behulp van Power shell. Voer deze configuratie uit op een van de SAP ASCS/SCS-cluster knooppunten.

4. [Het data base-exemplaar installeren] [SAP-ha-Guide-9,2].  
 In deze stap installeert u DBMS op een toegewezen WSFC-cluster.

5. [Het tweede cluster knooppunt installeren] [SAP-ha-Guide-9,3].  
 In deze stap installeert u SAP met een ASCS/SCS-exemplaar met hoge Beschik baarheid op het bestaande WSFC-cluster knooppunt 2.

6. Open Windows Firewall poorten voor het SAP ASCS/SCS-exemplaar en ProbePort.  
 Op cluster knooppunten die worden gebruikt voor SAP ASCS/SCS-instanties, opent u alle Windows Firewall poorten die worden gebruikt door SAP ASCS/SCS. Deze poorten worden weer gegeven in de [hand leiding voor SAP NetWeaver met hoge Beschik baarheid op virtuele Windows-machines][sap-ha-guide-8.8].  
 Open ook de test poort voor interne load balancer van Azure, die 62350 in ons scenario.

7. [Wijzig het start type van het SAP ers Windows-service-exemplaar][sap-ha-guide-9.4].

8. [Installeer de toepassing SAP Primary Application Server][sap-ha-guide-9.5] op de nieuwe specifieke VM.

9. [Installeer de extra SAP-toepassings server][sap-ha-guide-9.6] op de nieuwe specifieke VM.

10. [Test de SAP-ASCS/SCS-exemplaar-failover en SIOS-replicatie][sap-ha-guide-10].

## <a name="next-steps"></a>Volgende stappen

- [Netwerk limieten: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Meerdere Vip's voor Azure Load Balancer][load-balancer-multivip-overview]
- [Hand leiding voor SAP NetWeaver met hoge Beschik baarheid op Windows-Vm's][sap-ha-guide]
