---
title: Een FCI maken met een Premium-bestands share
description: Gebruik een Premium-bestands share (PFS) om een failovercluster-exemplaar (FCI) te maken met SQL Server op virtuele Azure-machines.
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
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482191"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Een FCI maken met een Premium-bestands share (SQL Server azure-VM's)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een exemplaar van een failovercluster (FCI) maakt met SQL Server op Azure Virtual Machines (VM's) met behulp van een [Premium-bestands share.](../../../storage/files/storage-how-to-create-file-share.md)

Premium-bestands shares worden Opslagruimten Direct (SSD) ondersteund, consistent bestands shares met lage latentie die volledig worden ondersteund voor gebruik met exemplaren van failovercluster voor SQL Server 2012 of hoger op Windows Server 2012 of hoger. Premium-bestands shares bieden u meer flexibiliteit, zodat u de omvang en schaal van een bestands share zonder uitvaltijd kunt schalen.

Zie voor meer informatie een overzicht van FCI met [SQL Server over Azure-VM's](failover-cluster-instance-overview.md) en [best practices voor clusters.](hadr-cluster-best-practices.md) 

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel voltooit, moet u al het volgende hebben:

- Een Azure-abonnement.
- Een account met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- [Twee of meer voorbereide virtuele Windows Azure-machines](failover-cluster-instance-prepare-vm.md) in een [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) of verschillende [beschikbaarheidszones](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Een [Premium-bestands share](../../../storage/files/storage-how-to-create-file-share.md) die moet worden gebruikt als het geclusterde station, op basis van het opslagquotum van uw database voor uw gegevensbestanden.
- De nieuwste versie van [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Premium-bestands share toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). en ga naar uw opslagaccount.
1. Ga naar **Bestands shares** onder **Bestandsservice** en selecteer vervolgens de Premium-bestands share die u wilt gebruiken voor uw SQL-opslag.
1. Selecteer **Verbinding maken** om de connection string voor uw bestands share weer te geven.
1. Selecteer in de vervolgkeuzelijst de stationletter die u wilt gebruiken en kopieer beide codeblokken naar Kladblok.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopieer beide PowerShell-opdrachten vanuit de connect-portal voor bestands delen":::

1. Gebruik Remote Desktop Protocol (RDP) om verbinding te maken met de SQL Server-VM met het account dat uw SQL Server FCI gebruikt voor het serviceaccount.
1. Open een PowerShell-opdrachtconsole voor beheer.
1. Voer de opdrachten uit die u eerder hebt opgeslagen toen u in de portal werkte.
1. Ga naar de share met behulp van Verkenner of **het** dialoogvenster Uitvoeren (selecteer Windows + R). Gebruik het netwerkpad `\\storageaccountname.file.core.windows.net\filesharename` . Bijvoorbeeld: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Maak ten minste één map op de zojuist verbonden bestands share om uw SQL-gegevensbestanden in te plaatsen.
1. Herhaal deze stappen op elke SQL Server VM die deelneemt aan het cluster.

  > [!IMPORTANT]
  > - Overweeg het gebruik van een afzonderlijke bestands share voor back-upbestanden om de invoer-/uitvoerbewerkingen per seconde (IOPS) en de ruimtecapaciteit van deze share op te slaan voor gegevens en logboekbestanden. U kunt een Premium- of Standard-bestands share gebruiken voor back-upbestanden.
  > - Als u windows 2012 R2 of eerder gebruikt, volgt u dezelfde stappen om de bestands share te installeren die u gaat gebruiken als de bestandsdeel witness. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows-clusterfunctie toevoegen

1. Maak verbinding met de eerste virtuele machine met RDP met behulp van een domeinaccount dat lid is van de lokale beheerders en dat is machtigingen heeft om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Voeg failoverclustering toe aan elke virtuele machine.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

   Als u failoverclustering wilt installeren vanuit de gebruikersinterface, doet u het volgende op beide virtuele machines:
   1. Selecteer **Serverbeheer** beheren **en** selecteer vervolgens **Functies en onderdelen toevoegen.**
   1. Selecteer in de wizard **Functies en onderdelen** toevoegen de optie **Volgende** totdat u Functies **selecteren krijgt.**
   1. Selecteer **failoverclustering in Functies selecteren.**  Neem alle vereiste functies en de beheerhulpprogramma's op. 
   1. Selecteer **Onderdelen toevoegen.**
   1. Selecteer **Volgende** en selecteer vervolgens **Voltooien om** de functies te installeren.

   Als u failoverclustering wilt installeren met behulp van PowerShell, moet u het volgende script uitvoeren vanuit een PowerShell-sessie van een beheerder op een van de virtuele machines:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Cluster valideren

Valideer het cluster in de gebruikersinterface of met behulp van PowerShell.

Als u het cluster wilt valideren met behulp van de gebruikersinterface, doet u het volgende op een van de virtuele machines:

1. Selecteer **Serverbeheer** **hulpprogramma's** en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer **onder Failoverclusterbeheer** actie en selecteer vervolgens Configuratie **valideren.**
1. Selecteer **Next**.
1. Voer **onder Servers of een cluster selecteren** de namen van beide virtuele machines in.
1. Selecteer **onder Testopties** de optie **Alleen tests uitvoeren ik selecteer**. 
1. Selecteer **Next**.
1. Selecteer **onder Testselectie** alle tests, met uitzondering van **Storage** **en Opslagruimten Direct,** zoals hier wordt weergegeven:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Clustervalidatietests selecteren":::

1. Selecteer **Next**.
1. Selecteer **onder Bevestiging** de optie **Volgende.**

De **wizard Een configuratie valideren** voert de validatietests uit.

Als u het cluster wilt valideren met behulp van PowerShell, moet u het volgende script uitvoeren vanuit een PowerShell-beheerderssessie op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.


## <a name="create-failover-cluster"></a>Failovercluster maken

Voor het maken van het failovercluster hebt u het volgende nodig:

- De namen van de virtuele machines die de clusterknooppunten worden.
- Een naam voor het failovercluster.
- Een IP-adres voor het failovercluster. U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de clusterknooppunten.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

Met het volgende PowerShell-script maakt u een failovercluster voor Windows Server 2012 tot en met Windows Server 2016. Werk het script bij met de namen van de knooppunten (de namen van de virtuele machines) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Met het volgende PowerShell-script maakt u een failovercluster voor Windows Server 2019.  Werk het script bij met de namen van de knooppunten (de namen van de virtuele machines) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Zie [Failovercluster: Cluster Network Object voor meer informatie.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)

---


## <a name="configure-quorum"></a>Quorum configureren

Configureer de quorumoplossing die het beste past bij de behoeften van uw bedrijf. U kunt een [schijf-witness,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)een [cloud-witness](/windows-server/failover-clustering/deploy-cloud-witness)of een [bestandsdeel witness configureren.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) Zie Quorum met virtuele SQL Server [voor meer informatie.](hadr-cluster-best-practices.md#quorum) 


## <a name="test-cluster-failover"></a>Failover van cluster testen

Test de failover van uw cluster. Klik **Failoverclusterbeheer** met de rechtermuisknop op uw cluster, selecteer Meer acties Knooppunt Kerncluster selecteren verplaatsen en selecteer vervolgens het  >    >  andere knooppunt van het cluster. Verplaats de kernclusterresource naar elk knooppunt van het cluster en verplaats deze vervolgens terug naar het primaire knooppunt. Als u het cluster naar elk knooppunt kunt verplaatsen, kunt u het cluster SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Failover van cluster testen door de kernresource naar de andere knooppunten te verplaatsen":::


## <a name="create-sql-server-fci"></a>Een SQL Server-FCI maken

Nadat u het failovercluster hebt geconfigureerd, kunt u de FCI SQL Server maken.

1. Maak verbinding met de eerste virtuele machine met behulp van RDP.

1. Zorg **Failoverclusterbeheer** ervoor dat alle kernclusterbronnen zich op de eerste virtuele machine hebben. Verplaats indien nodig alle resources naar deze virtuele machine.

1. Zoek het installatiemedia. Als de virtuele machine een van de Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full` . 

1. Selecteer **Setup.**

1. Selecteer in **SQL Server Installatiecentrum** de **optie Installatie.**

1. Selecteer **Nieuwe SQL Server failoverclusterinstallatie** en volg de instructies in de wizard om de FCI SQL Server installeren.

   De FCI-gegevensdirecties moeten zich op de Premium-bestands share. Voer het volledige pad van de share in, in deze indeling: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Er wordt een waarschuwing weergegeven met de melding dat u een bestandsserver hebt opgegeven als de gegevensmap. Deze waarschuwing wordt verwacht. Zorg ervoor dat het gebruikersaccount dat u hebt gebruikt voor toegang tot de VM via RDP wanneer u de bestands share persistent hebt gemaakt, hetzelfde account is dat de SQL Server-service gebruikt om mogelijke fouten te voorkomen.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Bestands share gebruiken als SQL-gegevensdirecties":::

1. Nadat u de stappen in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knooppunt.

1. Nadat Setup de FCI op het eerste knooppunt heeft geïnstalleerd, maakt u via RDP verbinding met het tweede knooppunt.

1. Open het **SQL Server Installation Center** en selecteer vervolgens **Installatie**.

1. Selecteer **Knooppunt toevoegen aan een SQL Server failovercluster.** Volg de instructies in de wizard om de SQL Server server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een galerie-Azure Marketplace gebruikt met SQL Server, SQL Server hulpprogramma's in de afbeelding opgenomen. Als u niet een van deze installatieprogramma's hebt gebruikt, installeert SQL Server hulpprogramma's afzonderlijk. Zie Download SQL Server Management Studio [(SSMS) voor meer informatie.](/sql/ssms/download-sql-server-management-studio-ssms)

1. Herhaal deze stappen op alle andere knooppunten die u wilt toevoegen aan het SQL Server failovercluster-exemplaar. 

## <a name="register-with-the-sql-vm-rp"></a>Registreren bij de SQL VM RP

Als u uw SQL Server-VM wilt beheren vanuit de portal, registreert u deze bij de SQL IaaS Agent-extensie (RP) [in](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)de lichtgewicht beheermodus, momenteel de enige modus die wordt ondersteund met FCI en SQL Server op Virtuele Azure-VM's. 

Registreer een SQL Server-VM in de lichtgewicht modus met PowerShell (-LicenseType kan `PAYG` of `AHUB` zijn):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Connectiviteit configureren 

Configureer de connectiviteitsoptie die geschikt is voor uw omgeving om verkeer op de juiste manier naar het huidige primaire knooppunt te laten doorstromen. U kunt een [Azure-load balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) maken. Als u SQL Server 2019 CU2 (of hoger) en Windows Server 2016 (of hoger) gebruikt, kunt u in plaats daarvan de functie gedistribueerde netwerknaam gebruiken. [](failover-cluster-instance-distributed-network-name-dnn-configure.md) 

Raadpleeg [HADR-verbindingen routeren naar SQL Server in Azure-VM's](hadr-cluster-best-practices.md#connectivity) voor meer informatie over opties voor clusterconnectiviteit. 

## <a name="limitations"></a>Beperkingen

- Microsoft Distributed Transaction Coordinator (MSDTC) wordt niet ondersteund in Windows Server 2016 en eerder. 
- Filestream wordt niet ondersteund voor een failovercluster met een Premium-bestands share. Als u filestream wilt gebruiken, implementeert u uw cluster met behulp [van Opslagruimten Direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) [of gedeelde Azure-schijven.](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Alleen registratie bij de SQL IaaS Agent-extensie in de [lichtgewicht beheermodus](sql-server-iaas-agent-extension-automate-management.md#management-modes) wordt ondersteund. 
- Databasemomentopnamen worden momenteel niet ondersteund met [Azure Files vanwege sparse bestandsbeperkingen.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)  

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, configureert u connectiviteit met uw FCI met een naam voor een virtueel netwerk en een [Azure load balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) of gedistribueerde netwerknaam [(DNN).](failover-cluster-instance-distributed-network-name-dnn-configure.md) 


Als Premium-bestands shares niet de juiste FCI-opslagoplossing [voor](failover-cluster-instance-storage-spaces-direct-manually-configure.md) u zijn, kunt u overwegen om uw FCI te maken met gedeelde [Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md) of Opslagruimten Direct. 

Zie voor meer informatie een overzicht van FCI met [SQL Server azure-VM's](failover-cluster-instance-overview.md) en [best practices voor clusterconfiguratie.](hadr-cluster-best-practices.md) 

Zie voor meer informatie: 
- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
