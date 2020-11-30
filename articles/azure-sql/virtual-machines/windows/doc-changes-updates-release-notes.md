---
title: Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines
description: Meer informatie over de nieuwe functies en verbeteringen voor verschillende versies van SQL Server op Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325720"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Met Azure kunt u een virtuele machine (VM) implementeren met een installatie kopie van SQL Server ingebouwde. In dit artikel vindt u een overzicht van de wijzigingen in de documentatie die zijn gekoppeld aan nieuwe functies en verbeteringen in de recente releases van [SQL Server op Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="october-2020"></a>Oktober 2020

| Wijzigingen | Details |
| --- | --- |
| **DNN voor AG** | U kunt nu een [DNN-listener (Distributed Network name)](availability-group-distributed-network-name-dnn-listener-configure.md) configureren voor SQL Server 2019 CU8 en hoger om de traditionele [VNN-listener](availability-group-overview.md#connectivity)te vervangen, zodat er geen Azure Load Balancer hoeft te worden.   | 

## <a name="september-2020"></a>September 2020

| Wijzigingen | Details |
| --- | --- |
| **Automatische extensie registratie** | U kunt nu de functie [automatische registratie](sql-agent-extension-automatic-registration-all-vms.md) inschakelen om automatisch alle SQL Server vm's te registreren die al zijn geïmplementeerd in uw abonnement met de [SQL IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md). Dit geldt voor alle bestaande Vm's en registreert ook automatisch alle SQL Server Vm's die in de toekomst worden toegevoegd.   | 


## <a name="august-2020"></a>Augustus 2020

| Wijzigingen | Details |
| --- | --- |
| **AG in portal configureren** | Het is nu mogelijk om [uw beschikbaarheids groep te configureren via de Azure Portal](availability-group-azure-portal-configure.md). Deze functie is momenteel beschikbaar als preview-versie en wordt geïmplementeerd, zodat de gewenste regio niet meer wordt weer gegeven. | 


## <a name="july-2020"></a>Juli 2020


| Wijzigingen | Details |
| --- | --- |
| **Logboek naar ultra schijf migreren** | Meer informatie over hoe u [het logboek bestand naar een ultra schijf kunt migreren](storage-migrate-to-ultradisk.md) om hoge prestaties en lage latentie te benutten. | 
| **AG maken met behulp van Azure PowerShell** | Het is nu mogelijk om het maken van een beschikbaarheids groep te vereenvoudigen met behulp van [Azure PowerShell](availability-group-az-commandline-configure.md) en de Azure cli. | 


## <a name="june-2020"></a>Juni 2020

| Wijzigingen | Details |
| --- | --- |
| **Gedistribueerde netwerknaam (DNN)** | SQL Server 2019 op Windows Server 2016 + is nu een voor beeld van ondersteuning voor het routeren van verkeer naar uw FCI (failover cluster instance) door gebruik te maken van een [gedistribueerde netwerk naam](./failover-cluster-instance-distributed-network-name-dnn-configure.md) in plaats van Azure Load Balancer te gebruiken. Deze ondersteuning vereenvoudigt en stroomlijnt het maken van verbinding met uw oplossing voor hoge Beschik baarheid (HA) in Azure. | 
| **FCI met gedeelde Azure-schijven** | Het is nu mogelijk om uw [FCI (failover cluster instance)](failover-cluster-instance-overview.md) te implementeren met behulp van [gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Opnieuw georganiseerde FCI docs** | De documentatie voor [failover-cluster exemplaren met SQL Server op Azure vm's](failover-cluster-instance-overview.md) is herschreven en opnieuw georganiseerd voor duidelijkheid. We hebben een deel van de configuratie-inhoud gescheiden, zoals de [Aanbevolen procedures voor cluster configuratie](hadr-cluster-best-practices.md), het voorbereiden [van een virtuele machine voor een SQL Server FCI](failover-cluster-instance-prepare-vm.md)en het configureren van [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Mei 2020 

| Wijzigingen | Details |
| --- | --- |
| **Azure SQL-familie** | SQL Server op Azure Virtual Machines maakt nu deel uit van de [Azure SQL-familie van producten](../../azure-sql-iaas-vs-paas-what-is-overview.md). Bekijk ons [nieuwe uiterlijk](../index.yml). Er is niets gewijzigd in het product, maar in de documentatie wordt beoogd de Azure SQL-product beslissing gemakkelijker te maken. | 


## <a name="january-2020"></a>Januari 2020

| Wijzigingen | Details |
| --- | --- |
| **Ondersteuning voor Azure Government** | Het is nu mogelijk om SQL Server virtuele machines te registreren met de SQL IaaS agent-extensie voor virtuele machines die in de [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) Cloud worden gehost. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Wijzigingen | Details |
 --- | --- |
| **Gratis DR-replica in azure** | Als u [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)hebt, kunt u een [gratis passief exemplaar](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) hosten voor herstel na nood gevallen in azure voor uw on-premises SQL Server exemplaar. | 
| **Registratie voor bulksgewijze SQL IaaS-extensie** | U kunt SQL Server virtuele machines nu [bulksgewijs registreren](sql-agent-extension-manually-register-vms-bulk.md) met de [SQL IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md). | 
|**Voor prestaties geoptimaliseerde opslag configuratie** | U kunt nu [uw opslag configuratie volledig aanpassen](storage-configuration.md#new-vms) bij het maken van een nieuwe SQL Server VM. |
|**Premium-bestands share voor FCI** | U kunt nu een failover-cluster exemplaar maken met behulp van een [Premium-bestands share](failover-cluster-instance-premium-file-share-manually-configure.md) in plaats van de oorspronkelijke methode van [opslagruimten direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Voor Azure toegewezen host** | U kunt uw SQL Server-VM uitvoeren op een [toegewezen Azure-host](dedicated-host.md). | 
| **VM-migratie SQL Server naar een andere regio** | Gebruik Azure Site Recovery om [uw SQL Server-VM van de ene regio naar de andere te migreren](move-sql-vm-different-region.md). |
|  **Nieuwe SQL IaaS-installatie modi** | Het is nu mogelijk om de SQL Server IaaS-uitbrei ding in de [Lightweight-modus](sql-server-iaas-agent-extension-automate-management.md) te installeren om te voor komen dat de SQL Server-service opnieuw wordt gestart.  |
| **Aanpassing van SQL Server-editie** | U kunt nu de [eigenschap Edition](change-sql-server-edition.md) voor uw SQL Server virtuele machine wijzigen. |
| **Wijzigingen in de SQL IaaS agent-extensie** | U kunt [uw SQL Server-VM registreren met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) met behulp van de nieuwe SQL IaaS-modi. Deze mogelijkheid omvat [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) -installatie kopieën.|
| **Gebruik Azure Hybrid Benefit om uw eigen licentie kopieën te maken** | Met uw eigen licentie-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace, kunnen nu het [licentie type overschakelen naar betalen per](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)gebruik.| 
| **Nieuw SQL Server VM-beheer in de Azure Portal** | Er is nu een manier om uw SQL Server-VM te beheren in de Azure Portal. Zie [SQL Server Vm's beheren in de Azure Portal](manage-sql-vm-portal.md)voor meer informatie.  | 
| **Uitgebreide ondersteuning voor SQL Server 2008 en 2008 R2** | [Uitgebreide ondersteuning](sql-server-2008-extend-end-of-support.md) voor SQL Server 2008 en SQL Server 2008 R2 door te migreren *naar een* virtuele machine van Azure. | 
| **Ondersteuning voor aangepaste installatie kopieën** | U kunt nu de [SQL Server IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md#installation) installeren op aangepaste besturings systemen en SQL Server installatie kopieën, die de beperkte functionaliteit van [flexibele licenties](licensing-model-azure-hybrid-benefit-ahb-change.md)bieden. Wanneer u uw aangepaste installatie kopie registreert met de SQL IaaS agent-extensie, geeft u het licentie type op als ' AHUB '. Anders mislukt de registratie. | 
| **Ondersteunings ondersteuning voor benoemde instanties** | U kunt nu de [SQL Server IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md#installation) gebruiken met een benoemd exemplaar als het standaard exemplaar op de juiste wijze is verwijderd. | 
| **Portal verbetering** | De Azure Portal ervaring voor het implementeren van een SQL Server VM is vernieuwd om de bruikbaarheid te verbeteren. Meer informatie vindt u in de korte [Snelstartgids](sql-vm-create-portal-quickstart.md) en uitgebreidere [hand leiding](create-sql-vm-portal.md) voor het implementeren van een SQL Server-VM.|
| **Portal verbetering** | Het is nu mogelijk om het licentie model voor een SQL Server-VM te wijzigen van betalen per gebruik naar uw eigen licentie met behulp van de [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model).|
| **Vereenvoudiging van de implementatie van de beschikbaarheids groep naar een SQL Server virtuele machine via de Azure CLI** | Het is nu eenvoudiger dan ooit om een beschikbaarheids groep te implementeren op een SQL Server VM in Azure. U kunt de [Azure cli](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) gebruiken om de Windows-failovercluster, interne Load Balancer en beschikbaarheids groep-listeners te maken vanaf de opdracht regel. Zie [de Azure CLI gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](./availability-group-az-commandline-configure.md)voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Wijzigingen | Details |
| --- | --- |
|  **Nieuwe resource provider voor een SQL Server cluster** | Met een nieuwe resource provider (micro soft. SqlVirtualMachine/SqlVirtualMachineGroups) worden de meta gegevens van het Windows-failovercluster gedefinieerd. Het toevoegen van een SQL Server VM aan *SqlVirtualMachineGroups* Boots traps de WSFC-service (Windows Server failover cluster) en koppelt de virtuele machine aan het cluster.  |
| **Automatische installatie van een implementatie van een beschikbaarheids groep met Azure Quick Start-sjablonen** |Het is nu mogelijk om het Windows-failovercluster te maken, SQL Server Vm's aan toe te voegen, de listener te maken en de interne load balancer te configureren met behulp van twee Azure Quick Start-sjablonen. Zie Azure Quick Start- [sjablonen gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](availability-group-quickstart-template-configure.md)voor meer informatie. | 
| **Automatische registratie bij de SQL IaaS agent-extensie** | SQL Server Vm's die na deze maand zijn geïmplementeerd, worden automatisch geregistreerd met de nieuwe SQL IaaS agent-extensie. SQL Server Vm's die vóór deze maand zijn geïmplementeerd, moeten nog steeds hand matig worden geregistreerd. Zie voor meer informatie [registreren van een SQL Server virtuele machine in azure met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md).|
|**Nieuwe SQL IaaS agent-extensie** |  Een nieuwe resource provider (micro soft. SqlVirtualMachine) biedt een beter beheer van uw SQL Server-Vm's. Zie voor meer informatie over het registreren van uw Vm's [een SQL Server virtuele machine registreren in azure met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md). |
|**Licentie model scha kelen** | U kunt nu scha kelen tussen de modellen van betalen per gebruik en uw eigen licentie voor uw SQL Server-VM met behulp van Azure CLI of Power shell. Zie [How to Change the License model for a SQL Server Virtual Machine in azure](licensing-model-azure-hybrid-benefit-ahb-change.md)(Engelstalig) voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Aanvullende resources

**Windows-vm's**:

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server inrichten op een Windows-VM](create-sql-vm-portal.md)
* [Een database migreren naar SQL Server op een virtuele machine van Azure](migrate-to-vm-from-sql-server.md)
* [Hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Aanbevolen procedures voor het uitvoeren van SQL Server op Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Toepassings patronen en ontwikkelings strategieën voor het SQL Server op Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuele Linux-machines**:

* [Overzicht van SQL Server op een Linux-VM](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server inrichten op een virtuele Linux-machine](../linux/sql-vm-create-portal-quickstart.md)
* [Veelgestelde vragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentatie voor SQL Server op Linux](/sql/linux/sql-server-linux-overview)