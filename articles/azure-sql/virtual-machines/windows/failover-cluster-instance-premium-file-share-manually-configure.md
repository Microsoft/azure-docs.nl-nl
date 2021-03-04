---
title: Een FCI maken met een Premium-bestands share
description: Gebruik een Premium-bestands share (PFS) om een FCI (failover cluster instance) te maken met SQL Server op virtuele machines van Azure.
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
ms.openlocfilehash: 80fe9f03f2c57eab8527e553153f3e65315a54bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034843"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Een FCI maken met een Premium-bestands share (SQL Server op Azure Vm's)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een FCI (failover cluster instance) maakt met SQL Server op Azure Virtual Machines (Vm's) met behulp van een [Premium-bestands share](../../../storage/files/storage-how-to-create-file-share.md).

Premium-bestands shares zijn Opslagruimten Direct (SSD), consistente bestands shares met een lage latentie die volledig worden ondersteund voor gebruik met failoverclusters voor SQL Server 2012 of hoger op Windows Server 2012 of hoger. Premium-bestands shares bieden meer flexibiliteit, waardoor u een bestands share zonder uitval tijd kunt verg Roten of verkleinen en schalen.

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor clusters](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel hebt voltooid, hebt u het volgende nodig:

- Een Azure-abonnement.
- Een account met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- [Twee of meer voor bereide virtuele Windows Azure-machines](failover-cluster-instance-prepare-vm.md) in een [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) of verschillende [beschikbaarheids zones](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Een [Premium-bestands share](../../../storage/files/storage-how-to-create-file-share.md) die moet worden gebruikt als het geclusterde station, op basis van de opslag quota van uw Data Base voor uw gegevens bestanden.
- De meest recente versie van [Power shell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Premium-bestands share koppelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). en ga naar uw opslag account.
1. Ga naar **Bestands shares** onder **File Service** en selecteer vervolgens de Premium-bestands share die u wilt gebruiken voor uw SQL-opslag.
1. Selecteer **verbinding** om de Connection String voor uw bestands share weer te geven.
1. Selecteer in de vervolg keuzelijst de stationsletter die u wilt gebruiken en Kopieer beide code blokken naar Klad blok.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Beide Power shell-opdrachten kopiëren van de bestands share Connect Portal":::

1. Gebruik Remote Desktop Protocol (RDP) om verbinding te maken met de SQL Server virtuele machine met het account dat door uw SQL Server FCI wordt gebruikt voor het service account.
1. Open een Power shell-opdracht console voor beheer.
1. Voer de opdrachten uit die u eerder hebt opgeslagen tijdens het werken in de portal.
1. Ga naar de share via Verkenner of het dialoog venster **uitvoeren** (Selecteer Windows + R). Gebruik het netwerkpad `\\storageaccountname.file.core.windows.net\filesharename` . Bijvoorbeeld: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Maak ten minste één map op de zojuist verbonden bestands share om uw SQL-gegevens bestanden in te brengen.
1. Herhaal deze stappen op elke SQL Server virtuele machine die u aan het cluster wilt deel nemen.

  > [!IMPORTANT]
  > - Overweeg het gebruik van een afzonderlijke bestands share voor back-upbestanden om de invoer/uitvoer-bewerkingen per seconde (IOPS) en de ruimte capaciteit van deze share voor gegevens en logboek bestanden op te slaan. U kunt een Premium-of standaard bestands share gebruiken voor back-upbestanden.
  > - Als u zich in Windows 2012 R2 of eerder bevindt, volgt u dezelfde stappen voor het koppelen van de bestands share die u als de bestandssharewitness gaat gebruiken. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows-cluster functie toevoegen

1. Maak verbinding met de eerste virtuele machine met RDP met behulp van een domein account dat lid is van de lokale beheerders en dat gemachtigd is om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Voeg Failover Clustering toe aan elke virtuele machine](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

## <a name="validate-cluster"></a>Cluster valideren

Valideer het cluster in de gebruikers interface of met behulp van Power shell.

Als u het cluster wilt valideren met behulp van de gebruikers interface, gaat u als volgt te werk op een van de virtuele machines:

1. Klik onder **Serverbeheer** op **extra** en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer onder **Failoverclusterbeheer** **actie** en selecteer vervolgens **configuratie valideren**.
1. Selecteer **Next**.
1. Voer onder **servers of een cluster selecteren** de namen van beide virtuele machines in.
1. Onder **test opties** selecteert u **alleen geselecteerde tests uitvoeren**. 
1. Selecteer **Next**.
1. Selecteer onder **selectie testen** alle tests, met uitzonde ring van **opslag** en **opslagruimten direct**, zoals hier wordt weer gegeven:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Cluster validatie tests selecteren":::

1. Selecteer **Next**.
1. Klik onder **bevestiging** op **volgende**.

Met de wizard **een configuratie valideren** worden de validatie tests uitgevoerd.

Als u het cluster wilt valideren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>Cluster-Failover testen

Test de failover van het cluster. Klik in **Failoverclusterbeheer** met de rechter muisknop op uw cluster, selecteer **meer acties**  >  **basis cluster resource verplaatsen**  >  **knoop punt selecteren** en selecteer vervolgens het andere knoop punt van het cluster. Verplaats de kern cluster bron naar elk knoop punt van het cluster en verplaats deze vervolgens terug naar het primaire knoop punt. Als u het cluster kunt verplaatsen naar elk knoop punt, bent u klaar om SQL Server te installeren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Cluster-Failover testen door de kern bron te verplaatsen naar de andere knoop punten":::


## <a name="create-sql-server-fci"></a>SQL Server FCI maken

Nadat u het failovercluster hebt geconfigureerd, kunt u de SQL Server FCI maken.

1. Maak verbinding met de eerste virtuele machine met behulp van RDP.

1. In **Failoverclusterbeheer** moet u ervoor zorgen dat alle kern cluster bronnen zich op de eerste virtuele machine bevinden. Verplaats, indien nodig, alle resources naar deze virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full` . 

1. Selecteer **Setup**.

1. Selecteer in het **SQL Server-installatie centrum** de optie **installatie**.

1. Selecteer **nieuwe SQL Server failover-cluster installatie** en volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevens directory's moeten zich op de Premium-bestands share bestaan. Voer het volledige pad van de share in, in deze indeling: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Er wordt een waarschuwing weer gegeven met de mede deling dat u een bestands server hebt opgegeven als de data directory. Deze waarschuwing wordt verwacht. Zorg ervoor dat het gebruikers account dat u hebt gebruikt voor toegang tot de virtuele machine via RDP wanneer u de bestands share persistent maakt, hetzelfde account is dat door de SQL Server-service wordt gebruikt om mogelijke storingen te voor komen.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Bestands share gebruiken als SQL-gegevens mappen":::

1. Nadat u de stappen in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met behulp van RDP.

1. Open het **SQL Server-installatie centrum** en selecteer vervolgens **installatie**.

1. Selecteer **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en de server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt met SQL Server, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u een van deze installatie kopieën niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS) (Engelstalig)](/sql/ssms/download-sql-server-management-studio-ssms)voor meer informatie.

1. Herhaal deze stappen op alle andere knoop punten die u wilt toevoegen aan het SQL Server failover-cluster exemplaar. 

## <a name="register-with-the-sql-vm-rp"></a>Registreren bij de SQL-VM RP

Als u uw SQL Server-VM wilt beheren vanuit de portal, registreert u deze bij de SQL IaaS agent-extensie (RP) in de [modus voor licht gewicht beheer](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), momenteel de enige modus die wordt ondersteund door FCI en SQL Server op Azure-vm's. 

Een SQL Server VM registreren in de licht gewicht modus met Power shell (-License type kan zijn `PAYG` of `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Connectiviteit configureren 

Als u verkeer op de juiste manier wilt door sturen naar het huidige primaire knoop punt, configureert u de connectiviteits optie die geschikt is voor uw omgeving. U kunt een [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) maken of als u SQL Server 2019 Cu2 (of hoger) en Windows Server 2016 (of hoger) gebruikt, kunt u in plaats daarvan de functie [gedistribueerde netwerk naam](failover-cluster-instance-distributed-network-name-dnn-configure.md) gebruiken. 

Raadpleeg [HADR-verbindingen routeren naar SQL Server in Azure-VM's](hadr-cluster-best-practices.md#connectivity) voor meer informatie over opties voor clusterconnectiviteit. 

## <a name="limitations"></a>Beperkingen

- Micro soft gedistribueerde transactie (MSDTC) wordt niet ondersteund in Windows Server 2016 en lager. 
- FileStream wordt niet ondersteund voor een failovercluster met een Premium-bestands share. Als u FileStream wilt gebruiken, implementeert u het cluster met behulp van [opslagruimten direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) of [gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md) .
- Alleen registreren met de SQL IaaS agent-extensie in de [Lightweight-beheer modus](sql-server-iaas-agent-extension-automate-management.md#management-modes) wordt ondersteund. 

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, configureert u de connectiviteit met uw FCI met de naam van een [virtueel netwerk en een Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) of [gedistribueerde netwerk naam (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Als Premium-bestands shares niet de juiste FCI-opslag oplossing voor u zijn, kunt u overwegen om uw FCI te maken met behulp van [gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md) of [opslagruimten direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) in plaats daarvan. 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor cluster configuratie](hadr-cluster-best-practices.md). 

Zie voor meer informatie: 
- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)