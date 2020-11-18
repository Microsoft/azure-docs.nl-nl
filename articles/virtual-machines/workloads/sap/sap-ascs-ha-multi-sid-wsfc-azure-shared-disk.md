---
title: SAP ASCS/SCS multi-SID HA met WSFC-en Azure gedeelde schijf | Microsoft Docs
description: Multi-SID hoge Beschik baarheid voor een SAP ASCS/SCS-exemplaar met WSFC-en Azure gedeelde schijf
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df611e01fefacd22f4dc026a819d4c71ede6e7e3
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686086"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en Azure Shared Disk

> ![Windows OS][Logo_Windows] Windows
>

In dit artikel wordt uitgelegd hoe u vanuit één ASCS/SCS-installatie overstapt naar een SAP multi-SID-configuratie door extra geclusterde SAP ASCS/SCS-exemplaren te installeren in een bestaand WSFC-cluster (Windows Server failover clustering) met een gedeelde Azure-schijf. Wanneer dit proces is voltooid, hebt u een SAP multi-SID-cluster geconfigureerd.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

Momenteel kunt u Azure Premium-SSD-schijven gebruiken als een gedeelde Azure-schijf voor het SAP ASCS/SCS-exemplaar. De volgende beperkingen zijn van kracht:

-  [Azure Ultra Disk](../../disks-types.md#ultra-disk) wordt niet ondersteund als een gedeelde Azure-schijf voor SAP-workloads. Het is momenteel niet mogelijk om virtuele Azure-machines te plaatsen met behulp van Azure Ultra disk in de Beschikbaarheidsset
-  Een [gedeelde Azure-schijf](../../disks-shared.md) met Premium-SSD schijven wordt alleen ondersteund met vm's in de beschikbaarheidsset. Het wordt niet ondersteund in Beschikbaarheidszones-implementatie. 
-  De waarde [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) voor de gedeelde Azure-schijf bepaalt hoeveel cluster knooppunten de gedeelde schijf kunnen gebruiken. Normaal gesp roken kunt u met het SAP ASCS/SCS-exemplaar twee knoop punten in een Windows-failovercluster configureren. Daarom moet de waarde voor `maxShares` zijn ingesteld op twee.
-  Alle SAP-ASCS/SCS-cluster-Vm's moeten worden geïmplementeerd in dezelfde [plaatsings groep voor Azure nabijheid](../../windows/proximity-placement-groups.md).   
   Hoewel u Windows-cluster-Vm's kunt implementeren in de Beschikbaarheidsset met een gedeelde Azure-schijf zonder PPG, zorgt PPG ervoor dat de fysieke nabijheid van Azure gedeelde schijven en de cluster-Vm's worden gesloten, waardoor er minder latentie tussen de virtuele machines en de opslaglaag kan worden bereikt.    

Raadpleeg het gedeelte over [beperkingen](../../disks-shared.md#limitations) van de documentatie van de gedeelde schijf van Azure voor meer informatie over de beperkingen voor een gedeelde Azure-schijf.  

> [!IMPORTANT]
> Wanneer u een Windows-failovercluster met SAP ASCS/SCS implementeert met een gedeelde Azure-schijf, moet u er rekening mee houden dat uw implementatie met één gedeelde schijf in één opslag cluster wordt uitgevoerd. Uw SAP ASCS/SCS-exemplaar wordt beïnvloed, in het geval van problemen met het opslag cluster, waar de gedeelde Azure-schijf wordt geïmplementeerd.  

> [!IMPORTANT]
> De installatie moet voldoen aan de volgende voor waarden:
> * Elke Database Management System (DBMS) SID moet een eigen toegewezen WSFC-cluster hebben.  
> * SAP-toepassings servers die deel uitmaken van één SAP-systeem-SID moeten hun eigen toegewezen Vm's hebben.  
> * Een combi natie van replicatie server in de wachtrij 1 en de in plaats van gerepliceerde replicatie Server 2 in hetzelfde cluster wordt niet ondersteund.  


## <a name="supported-os-versions"></a>Ondersteunde versies van besturings systemen

Zowel Windows Server 2016 als Windows Server 2019 worden ondersteund (gebruik de meest recente Data Center-installatie kopieën).

We raden u ten zeerste aan **Windows Server 2019 Data Center** te gebruiken als:
- Windows 2019 failover cluster-service is Azure-compatibel
- Er is een integratie en bewustzijn van Azure host-onderhoud toegevoegd en de ervaring is verbeterd door bewaking voor Azure Schedule-gebeurtenissen.
- U kunt de naam van een gedistribueerde netwerk gebruiken (dit is de standaard optie). Daarom is er geen specifiek IP-adres nodig voor de naam van het cluster netwerk. Bovendien hoeft u dit IP-adres niet te configureren op de interne Azure-Load Balancer. 

## <a name="architecture"></a>Architectuur

Zowel de in wachtrij plaatsen van replicatie server 1 (ERS1) als de in plaats van replicatie Server 2 (ERS2) worden ondersteund in een configuratie met meerdere SID'S.  Een combi natie van ERS1 en ERS2 wordt niet ondersteund in hetzelfde cluster. 

1. In het eerste voor beeld ziet u twee SAP-Sid's, beide met ERS1-architectuur, waarbij:

   - SAP SID1 wordt geïmplementeerd op een gedeelde schijf, met ERS1. Het ERS-exemplaar is geïnstalleerd op de lokale host en op de lokale schijf.
     SAP SID1 heeft een eigen (virtueel) IP-adres (SID1 (A) SCS IP1), dat is geconfigureerd op de interne Load Balancer van Azure.

   - SAP SID2 wordt geïmplementeerd op een gedeelde schijf, met ERS1. Het ERS-exemplaar is geïnstalleerd op de lokale host en op de lokale schijf.
     SAP SID2 heeft eigen (virtueel) IP-adres (SID2 (A) SCS IP2), dat ook is geconfigureerd op de interne Load Balancer van Azure.

![SAP ASCS/SCS-instantie met hoge Beschik baarheid-twee exemplaren met ERS1-configuratie][sap-ha-guide-figure-6007]

2. In het tweede voor beeld ziet u twee SAP-Sid's, beide met ERS2-architectuur, waarbij: 

   - SAP SID1 met ERS2, is die ook geclusterd en geïmplementeerd op een lokale schijf.  
     SAP SID1 heeft eigen (virtueel) IP-adres (SID1 (A) SCS IP1), dat is geconfigureerd op de interne Load Balancer van Azure.
     SAP ERS2, dat wordt gebruikt door het SAP SID1-systeem heeft een eigen (virtueel) IP-adres (SID1 ERS2 IP2), dat is geconfigureerd op de interne Load Balancer van Azure.

   - SAP SID2 met ERS2, is die ook geclusterd en geïmplementeerd op een lokale schijf.  
     SAP SID2 heeft eigen (virtueel) IP-adres (SID2 (A) SCS IP3), dat is geconfigureerd op de interne Load Balancer van Azure.
     SAP ERS2, dat wordt gebruikt door het SAP SID2-systeem heeft een eigen (virtueel) IP-adres (SID2 ERS2 IP4), dat is geconfigureerd op de interne Load Balancer van Azure.

   Hier hebben we een totaal van vier virtuele IP-adressen:  
   - SID1 (A) SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A) SCS IP3
   - SID2 ERS2 IP4

![SAP ASCS/SCS-instantie met hoge Beschik baarheid-twee exemplaren met ERS1-en ERS2-configuratie][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Infrastructuur voorbereiding

Er worden naast het **bestaande geclusterde** SAP **PR1** ASCS/SCS-exemplaar een nieuwe SAP-sid- **PR2** geïnstalleerd.  

### <a name="host-names-and-ip-addresses"></a>Hostnamen en IP-adressen

| Host-naam functie | Hostnaam | Statisch IP-adres | Beschikbaarheidsset | Proximity-plaatsings groep |
| --- | --- | --- |---| ---|
| ASCS/SCS-cluster van 1e cluster node |PR1-ascs-10 |10.0.0.4 |PR1-ascs-avset |PR1PPG |
| 2e cluster knooppunt ASCS/SCS-cluster |PR1-ascs-11 |10.0.0.5 |PR1-ascs-avset |PR1PPG |
| Naam van het clusternetwerk | pr1clust |10.0.0.42 (**alleen** voor het Win 2016-cluster) | n.v.t. | n.v.t. |
| **SID1** ASCS-cluster netwerk naam | pr1-ascscl |10.0.0.43 | n.v.t. | n.v.t. |
| **SID1** ERS-cluster netwerk naam (**alleen** voor ERS2) | pr1-erscl |10.0.0.44 | n.v.t. | n.v.t. |
| **SID2** ASCS-cluster netwerk naam | pr2-ascscl |10.0.0.45 | n.v.t. | n.v.t. |
| **SID2** ERS-cluster netwerk naam (**alleen** voor ERS2) | pr1-erscl |10.0.0.46 | n.v.t. | n.v.t. |

### <a name="create-azure-internal-load-balancer"></a>Interne Azure-load balancer maken

SAP ASCS, SAP SCS en de nieuwe SAP ERS2, gebruiken virtuele hostnamen en virtuele IP-adressen. In Azure is een [Load Balancer](../../../load-balancer/load-balancer-overview.md) vereist voor het gebruik van een virtueel IP-adres. Het is raadzaam om [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)te gebruiken. 

U moet een configuratie toevoegen aan de bestaande load balancer voor de tweede SAP SID ASCS/SCS/ERS-instantie **PR2**. De configuratie voor de eerste SAP-SID **PR1** moet al aanwezig zijn.  

**Één SCS PR2 [exemplaar nummer 02]**
- Front-end configuratie
    - Statisch ASCS/SCS IP-adres **10.0.0.45**
- Back-end-configuratie  
    Is al aanwezig-de Vm's zijn al toegevoegd aan de back-end-groep, terwijl de configuratie voor SAP-SID **PR1**
- Test poort
    - Poort 620 **Nr** . [**62002**] de standaard optie voor protocol (TCP), interval (5), beschadigde drempel waarde (2) behouden
- Taakverdelings regels
    - Als u Standard Load Balancer gebruikt, selecteert u HA-poorten
    - Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
        - 32 **Nr** TCP [**3202**]
        - 36 **Nr** TCP [**3602**]
        - 39 **Nr** TCP [**3902**]
        - 81 **Nr** TCP [**8102**]
        - 5 **Nr**. 13 TCP [**50213**]
        - 5 **Nr**. 14 TCP [**50214**]
        - 5 **Nr**. 16 TCP [**50216**]
        - Koppel het **PR2** ASCS-frontend-IP, de status test en de bestaande back-end-pool.  

    - Zorg ervoor dat time-out voor inactiviteit (minuten) is ingesteld op de maximum waarde 30 en dat zwevend IP-adres (Direct Server Return) is ingeschakeld.

**ERS2 PR2 [exemplaar nummer 12]** 

Als de wachtrij voor het plaatsen van replicatie Server 2 (ERS2) ook is geclusterd, moet het virtuele IP-adres van ERS2 ook worden geconfigureerd op Azure ILB naast het bovenstaande SAP ASCS/SCS IP. Deze sectie is alleen van toepassing als u de architectuur voor het plaatsen van replicatie Server 2 gebruikt voor **PR2**.  
- Nieuwe frontend-configuratie
    - Statisch SAP ERS2 IP-adres **10.0.0.46**

- Back-end-configuratie  
  De virtuele machines zijn al toegevoegd aan de back-ILB.  

- Nieuwe test poort
    - Poort 621 **Nr**  . [**62112**] de standaard optie voor protocol (TCP), interval (5), beschadigde drempel waarde (2) behouden

- Nieuwe regels voor taak verdeling
    - Als u Standard Load Balancer gebruikt, selecteert u HA-poorten
    - Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
        - 32 **Nr** TCP [**3212**]
        - 33 **Nr** TCP [**3312**]
        - 5 **Nr**. 13 TCP [**51212**]
        - 5 **Nr**. 14 TCP [**51212**]
        - 5 **Nr**. 16 TCP [**51212**]
        - Koppel het **PR2** ERS2-frontend-IP, de status test en de bestaande back-end-pool.  

    - Zorg ervoor dat time-out voor inactiviteit (minuten) is ingesteld op een maximum waarde, bijvoorbeeld 30, en dat zwevend IP-adres (Direct Server Return) is ingeschakeld.


### <a name="create-and-attach-second-azure-shared-disk"></a>Een tweede gedeelde Azure-schijf maken en koppelen

Voer deze opdracht uit op een van de cluster knooppunten. U moet de waarden aanpassen voor uw resource groep, Azure-regio, SAPSID, enzovoort.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>De gedeelde schijf Format teren met Power shell
1. Haal het schijf nummer op. Voer de Power shell-opdrachten uit op een van de cluster knooppunten:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Format teer de schijf. In dit voor beeld is schijf nummer 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Controleer of de schijf nu als een cluster schijf wordt weer gegeven.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Registreer de schijf in het cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Een virtuele-hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar

1. Maak een DNS-vermelding voor de naam van de virtuele host voor het nieuwe SAP ASCS/SCS-exemplaar in het Windows DNS-beheer.  
   Het IP-adres dat u toewijst aan de naam van de virtuele host in DNS moet hetzelfde zijn als het IP-adres dat u in Azure Load Balancer hebt toegewezen.  

   ![De DNS-vermelding voor de virtuele naam en het IP-adres van het SAP ASCS/SCS-cluster _Define][sap-ha-guide-figure-6009]
 
   _Definieer de DNS-vermelding voor de virtuele naam en het IP-adres van het SAP ASCS/SCS-cluster_

2. Als u SAP-server voor in wachtrij plaatsen gebruikt, dat ook een geclusterd exemplaar is, moet u de naam van de virtuele host voor ERS2 ook reserveren in DNS. 
   Het IP-adres dat u toewijst aan de naam van de virtuele host voor ERS2 in DNS moet hetzelfde zijn als het IP-adres dat u in Azure Load Balancer hebt toegewezen.  

   ![De DNS-vermelding voor de virtuele naam en het IP-adres van het SAP ERS2-cluster _Define][sap-ha-guide-figure-6010]
 
   _Definieer de DNS-vermelding voor de virtuele naam en het IP-adres van het SAP ERS2-cluster_

3. Als u het IP-adres wilt definiëren dat is toegewezen aan de naam van de virtuele host, selecteert u **DNS-beheer**  >  **domein**.

   ![Nieuwe virtuele naam en IP-adres voor SAP ASCS/SCS en ERS2-cluster configuratie][sap-ha-guide-figure-6011]

   _Nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS en ERS2-cluster configuratie_

## <a name="sap-installation"></a>SAP-installatie 

### <a name="install-the-sap-first-cluster-node"></a>Het SAP eerste cluster knooppunt installeren

Volg de door SAP beschreven installatie procedure. Zorg ervoor dat u de installatie optie eerste cluster knooppunt start en kies cluster gedeelde schijf als configuratie optie.  
Kies de nieuwe gedeelde schijf maken.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Het SAP-Profiel van het ASCS/SCS-exemplaar wijzigen

Als u replicatie server voor plaatsen uitvoert, voegt u de para meter SAP-profiel toe, `enque/encni/set_so_keepalive` zoals hieronder wordt beschreven. De profiel parameter voor komt dat verbindingen tussen SAP-werk processen en de bewerkings server worden afgesloten wanneer ze te lang niet actief zijn. De SAP-para meter is niet vereist voor ERS2. 

1. Voeg deze profiel parameter toe aan het SAP-exemplaar profiel voor ASCS/SCS als u gebruikmaakt van ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Voor zowel ERS1 als ERS2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Als u de SAP-profiel parameter wijzigingen wilt Toep assen, start u het SAP ASCS/SCS-exemplaar opnieuw.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Test poort op de cluster bron configureren

Gebruik de test functionaliteit van de interne load balancer om ervoor te zorgen dat de volledige cluster configuratie werkt met Azure Load Balancer. De interne load balancer van Azure distribueert doorgaans de inkomende werk belasting gelijkmatig tussen de deelnemende virtuele machines.

Dit werkt echter niet in sommige cluster configuraties, omdat er slechts één exemplaar actief is. De andere instantie is passief en kan geen enkele werk belasting accepteren. Een test functionaliteit helpt wanneer de interne load balancer van Azure detecteert welk exemplaar actief is en alleen gericht is op het actieve exemplaar.  

> [!IMPORTANT]
> In dit voor beeld is de **ProbePort** ingesteld op 620 **Nr**. Voor het SAP ASCS-exemplaar met het nummer **02** is 620 **02**.
> U moet de configuratie aanpassen zodat deze overeenkomt met uw SAP-instantie nummers en uw SAP-SID.

Als u een test poort wilt toevoegen, voert u deze Power shell-module uit op een van de cluster-Vm's:

- In het geval van een SAP ASC/SCS-exemplaar met het exemplaar nummer **02** 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Bij gebruik van ERS2, met instantie nummer **12**, dat is geclusterd. Het is niet nodig om de test poort voor ERS1 te configureren, omdat deze niet is geclusterd.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 De functie code voor `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` zou er als volgt uitzien:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Door gaan met de SAP-installatie

1. Installeer het data base-exemplaar door het proces te volgen dat wordt beschreven in de SAP-installatie handleiding.  
2. Installeer SAP op het tweede cluster knooppunt door de stappen te volgen die worden beschreven in de SAP-installatie handleiding.  
3. Installeer de SAP primaire Application Server-instantie (PAS) op de virtuele machine die u hebt aangewezen voor het hosten van de PAS.   
   Volg de procedure die wordt beschreven in de SAP-installatie handleiding. Er zijn geen afhankelijkheden voor Azure.
4. Installeer extra SAP-toepassings servers op de virtuele machines die zijn aangewezen voor het hosten van SAP Application Server-exemplaren.  
   Volg de procedure die wordt beschreven in de SAP-installatie handleiding. Er zijn geen afhankelijkheden voor Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>De ASCS/SCS-failover van het SAP-exemplaar testen
We gaan ervan uit dat SAP ASCS actief is op knoop punt A.  

1. Controleer of het SAP-systeem een failover van knoop punt A naar knoop punt B kan worden uitgevoerd. In dit voor beeld wordt de test uitgevoerd voor SAPSID **PR2**.  
   Zorg ervoor dat elk van de SAPSID naar het andere cluster knooppunt kan worden verplaatst.   
   Kies een van deze opties voor het initiëren van een failover van de SAP- \<SID\> cluster groep van cluster knooppunt a naar cluster knooppunt B:
    - Failoverclusterbeheer  
    - Failover cluster Power shell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Start cluster knooppunt A opnieuw op in het Windows-gast besturingssysteem. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.  
3. Start het cluster knooppunt A opnieuw vanaf het Azure Portal. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.  
4. Start cluster knooppunt A opnieuw met behulp van Azure PowerShell. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.

## <a name="next-steps"></a>Volgende stappen

* [De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md