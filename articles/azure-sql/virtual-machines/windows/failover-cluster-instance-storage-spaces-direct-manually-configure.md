---
title: Een FCI maken met Opslagruimten Direct
description: Gebruik Opslagruimten Direct voor het maken van een FCI (failover cluster instance) met SQL Server op virtuele machines van Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 6ed5e11a8492314e99b9f105d259fa910dcdb77d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357803"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Een FCI maken met Opslagruimten Direct (SQL Server op Azure Vm's)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een FCI (failover cluster instance) maakt met behulp van [opslagruimten direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) met SQL Server op Azure virtual machines (vm's). Opslagruimten Direct fungeert als een op software gebaseerde virtuele Storage Area Network (VSAN) die de opslag (gegevens schijven) synchroniseert tussen de knoop punten (Azure Vm's) in een Windows-cluster. 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor clusters](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Overzicht 

[Opslagruimten direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) ondersteunt twee typen architecturen: geconvergeerde en hypergeconvergeerd. Een hypergeconvergeerd-infra structuur plaatst de opslag op de servers die als host fungeren voor de geclusterde toepassing, zodat opslag zich op elk SQL Server FCI-knoop punt bevindt. 

In het volgende diagram ziet u de volledige oplossing, die gebruikmaakt van hypergeconvergeerd Opslagruimten Direct met SQL Server op Azure-Vm's: 

![Diagram van de volledige oplossing met behulp van hypergeconvergeerd Opslagruimten Direct](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

In het voor gaande diagram worden de volgende resources in dezelfde resource groep weer gegeven:

- Twee virtuele machines in een Windows Server-failovercluster. Wanneer een virtuele machine zich in een failovercluster bevindt, wordt deze ook wel een *cluster knooppunt* of *knoop punt* genoemd.
- Elke virtuele machine heeft twee of meer gegevens schijven.
- Opslagruimten Direct synchroniseert de gegevens op de gegevens schijven en geeft de gesynchroniseerde opslag weer als een opslag groep.
- De opslag groep presenteert een Cluster Shared Volume (CSV) voor het failovercluster.
- De SQL Server cluster functie FCI gebruikt het CSV voor de gegevens stations.
- Een Azure-load balancer om het IP-adres voor de SQL Server FCI te bewaren.
- Een Azure Availability set bevat alle resources.

   > [!NOTE]
   > U kunt deze volledige oplossing in azure maken op basis van een sjabloon. Een voor beeld van een sjabloon is beschikbaar op de pagina GitHub [Azure Quick](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) start-sjablonen. Dit voor beeld is niet ontworpen of getest voor een specifieke werk belasting. U kunt de sjabloon uitvoeren om een SQL Server FCI te maken met Opslagruimten Direct opslag die is verbonden met uw domein. U kunt de sjabloon evalueren en aanpassen voor uw doel einden.


## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel hebt voltooid, hebt u het volgende nodig:

- Een Azure-abonnement. Ga [gratis](https://azure.microsoft.com/free/)aan de slag. 
- [Twee of meer voor bereide virtuele Windows Azure-machines](failover-cluster-instance-prepare-vm.md) in een [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Een account met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- De meest recente versie van [Power shell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>De Windows-cluster functie toevoegen

1. Maak verbinding met de eerste virtuele machine met behulp van Remote Desktop Protocol (RDP) met een domein account dat lid is van de lokale beheerders en dat gemachtigd is om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. Voeg Failover Clustering toe aan elke virtuele machine.

   Ga als volgt te werk om failover clustering te installeren vanuit de gebruikers interface:

   1. Selecteer in **Serverbeheer** **beheren** en selecteer vervolgens **functies en onderdelen toevoegen**.
   1. Selecteer in de wizard **functies en onderdelen toevoegen** de optie **volgende** totdat u **onderdelen selecteert**.
   1. Selecteer in **functies selecteren** de optie **failover clustering**. Neem alle vereiste functies en de beheer hulpprogramma's op. 
   1. Selecteer **onderdelen toevoegen**.
   1. Selecteer **volgende** en selecteer vervolgens **volt ooien** om de functies te installeren.

   Als u Failover Clustering wilt installeren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Voor meer informatie over de volgende stappen raadpleegt u de instructies in de sectie ' stap 3: Configure Opslagruimten Direct ' van de [hypergeconvergeerd-oplossing met behulp van opslagruimten direct in Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Het cluster valideren

Valideer het cluster in de gebruikers interface of met behulp van Power shell.

Als u het cluster wilt valideren met behulp van de gebruikers interface, gaat u als volgt te werk op een van de virtuele machines:

1. Klik onder **Serverbeheer** op **extra** en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer onder **Failoverclusterbeheer** **actie** en selecteer vervolgens **configuratie valideren**.
1. Selecteer **Next**.
1. Voer onder **servers of een cluster selecteren** de namen van beide virtuele machines in.
1. Onder **test opties** selecteert u **alleen geselecteerde tests uitvoeren**. 
1. Selecteer **Next**.
1. Selecteer onder **selectie testen** alle tests, met uitzonde ring van **opslag**, zoals hier wordt weer gegeven:

   ![Cluster validatie tests selecteren](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecteer **Next**.
1. Klik onder **bevestiging** op **volgende**.

    Met de wizard **een configuratie valideren** worden de validatie tests uitgevoerd.

Als u het cluster wilt valideren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.


## <a name="create-failover-cluster"></a>Failovercluster maken

Als u het failovercluster wilt maken, hebt u het volgende nodig:

- De namen van de virtuele machines die worden de cluster knooppunten.
- Een naam voor het failovercluster.
- Een IP-adres voor het failovercluster. U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de cluster knooppunten.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2012 via Windows Server 2016. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2019.  Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Zie voor meer informatie [failover cluster: Cluster Network object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Quorum configureren

Configureer de quorum oplossing die het beste past bij uw bedrijfs behoeften. U kunt een schijfwitness [, een](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) [Cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)of een [Bestands share-Witness](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)configureren. Zie voor meer informatie [quorum met SQL Server vm's](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Opslag toevoegen

De schijven voor Opslagruimten Direct moeten leeg zijn. Ze kunnen geen partities of andere gegevens bevatten. Volg de instructies in [Deploy opslagruimten direct](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives)om de schijven op te schonen.

1. [Schakel opslagruimten direct in](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Met het volgende Power shell-script kunt Opslagruimten Direct:  

   ```powershell
   Enable-ClusterS2D
   ```

   In **Failoverclusterbeheer** kunt u nu de opslag groep zien.

1. [Maak een volume](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Opslagruimten Direct maakt automatisch een opslag groep wanneer u deze inschakelt. U bent nu klaar om een volume te maken. De Power shell-cmdlet `New-Volume` automatiseert het proces voor het maken van een volume. Dit proces omvat het format teren, het toevoegen van het volume aan het cluster en het maken van een CSV. In dit voor beeld wordt een CSV-bestand van 800 GB gemaakt:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Nadat u de voor gaande opdracht hebt uitgevoerd, wordt een volume van 800 GB gekoppeld als een cluster bron. Het volume bevindt zich op `C:\ClusterStorage\Volume1\` .

   Deze scherm afbeelding toont een CSV met Opslagruimten Direct:

   ![Scherm afbeelding van een Cluster Shared Volume met Opslagruimten Direct](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Cluster-Failover testen

Test de failover van het cluster. Klik in **Failoverclusterbeheer** met de rechter muisknop op uw cluster, selecteer **meer acties**  >  **basis cluster resource verplaatsen**  >  **knoop punt selecteren** en selecteer vervolgens het andere knoop punt van het cluster. Verplaats de kern cluster bron naar elk knoop punt van het cluster en verplaats deze vervolgens terug naar het primaire knoop punt. Als u het cluster kunt verplaatsen naar elk knoop punt, bent u klaar om SQL Server te installeren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Cluster-Failover testen door de kern bron te verplaatsen naar de andere knoop punten":::

## <a name="create-sql-server-fci"></a>SQL Server FCI maken

Nadat u het failovercluster en alle cluster onderdelen, inclusief opslag, hebt geconfigureerd, kunt u de SQL Server-FCI maken.

1. Maak verbinding met de eerste virtuele machine met behulp van RDP.

1. Zorg er in **Failoverclusterbeheer** voor dat alle kern cluster bronnen zich op de eerste virtuele machine bevinden. Verplaats, indien nodig, alle resources naar die virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full` . Selecteer **Setup**.

1. In **SQL Server Installation Center** selecteert u **installatie**.

1. Selecteer **nieuwe SQL Server failover-cluster installatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevens directory's moeten zich op geclusterde opslag beslaan. Met Opslagruimten Direct is het geen gedeelde schijf, maar een koppel punt naar een volume op elke server. Opslagruimten Direct synchroniseert het volume tussen beide knoop punten. Het volume wordt door gegeven aan het cluster als een CSV-bestand. Gebruik het CSV-koppel punt voor de gegevens directory's.

   ![Gegevens directory's](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Nadat u de instructies in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met behulp van RDP.

1. Open het **SQL Server-installatie centrum**. Selecteer **installatie**.

1. Selecteer **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en de server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt die SQL Server bevat, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u een van deze installatie kopieën niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS) (Engelstalig)](/sql/ssms/download-sql-server-management-studio-ssms)voor meer informatie.
   >


## <a name="register-with-the-sql-vm-rp"></a>Registreren bij de SQL-VM RP

Als u uw SQL Server-VM wilt beheren vanuit de portal, registreert u deze bij de SQL IaaS agent-extensie (RP) in de [modus voor licht gewicht beheer](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), momenteel de enige modus die wordt ondersteund door FCI en SQL Server op Azure-vm's. 


Een SQL Server VM registreren in de licht gewicht modus met Power shell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Connectiviteit configureren 

Als u verkeer op de juiste manier wilt door sturen naar het huidige primaire knoop punt, configureert u de connectiviteits optie die geschikt is voor uw omgeving. U kunt een [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) maken of als u SQL Server 2019 Cu2 (of hoger) en Windows Server 2016 (of hoger) gebruikt, kunt u in plaats daarvan de functie [gedistribueerde netwerk naam](failover-cluster-instance-distributed-network-name-dnn-configure.md) gebruiken. 

## <a name="limitations"></a>Beperkingen

- Azure virtual machines ondersteunen micro soft Distributed Transaction Coordinator (MSDTC) op Windows Server 2019 met opslag op Csv's en een [standaard Load Balancer](../../../load-balancer/load-balancer-overview.md).
- Schijven die zijn gekoppeld als schijven met NTFS-indeling kunnen alleen worden gebruikt met Opslagruimten Direct als de optie voor de geschiktheid van de schijf is uitgeschakeld, of als deze is gewist, wanneer opslag wordt toegevoegd aan het cluster. 
- Alleen registreren met de SQL IaaS agent-extensie in de [Lightweight-beheer modus](sql-server-iaas-agent-extension-automate-management.md#management-modes) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, configureert u de connectiviteit met uw FCI met de naam van een [virtueel netwerk en een Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) of [gedistribueerde netwerk naam (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Als Opslagruimten Direct niet de juiste FCI-opslag oplossing voor u is, kunt u overwegen om uw FCI te maken met behulp van [gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md) of [Premium-bestands shares](failover-cluster-instance-premium-file-share-manually-configure.md) . 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor cluster configuratie](hadr-cluster-best-practices.md). 

Zie voor meer informatie: 
- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)