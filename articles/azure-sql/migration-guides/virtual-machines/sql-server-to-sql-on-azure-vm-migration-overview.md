---
title: SQL Server SQL Server op Azure VM (migratie overzicht)
description: Meer informatie over de verschillende migratie strategieën wanneer u uw SQL Server wilt migreren naar SQL Server op virtuele machines van Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: a910edfbbe1ad07dca806026396c506f7e90e6e7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019429"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Migratie overzicht: SQL Server SQL Server op virtuele machines van Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Meer informatie over de verschillende migratie strategieën voor het migreren van uw SQL Server naar SQL Server op Azure Virtual Machines (Vm's). 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren:

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's. 

## <a name="overview"></a>Overzicht

Migreer naar [SQL Server op Azure-virtual machines (vm's)](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) wanneer u de vertrouwde SQL Server omgeving wilt gebruiken met besturings element besturings systeem en u wilt profiteren van Cloud functies zoals ingebouwde virtuele Beschik baarheid, [automatische back-ups](../../virtual-machines/windows/automated-backup.md)en [automatische patching](../../virtual-machines/windows/automated-patching.md). 

Bespaar op kosten door uw eigen licentie te nemen met het [Azure Hybrid Benefit-licentie model](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) of breid ondersteuning uit voor SQL Server 2008 en SQL Server 2008 R2 door [gratis beveiligings updates](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)te downloaden. 


## <a name="choosing-appropriate-target"></a>Geschikt doel kiezen

Azure Virtual Machines worden uitgevoerd in veel verschillende regio's van Azure en bieden ook diverse [machine grootten](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) en [opslag opties](https://docs.microsoft.com/azure/virtual-machines/disks-types). Raadpleeg de [richt lijnen voor prestaties voor SQL Server op Azure virtual machines](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance)voor het bepalen van de juiste grootte van de virtuele machine en opslag voor uw SQL Server-workload. Om de grootte van de virtuele machine en de opslag vereisten voor uw workload te bepalen. het is raadzaam om deze grootte te bepalen door een Performance-Based [Azure migrate beoordeling](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments). Als dit geen beschik bare optie is, raadpleegt u het volgende artikel over het maken van uw eigen [basis lijn voor prestaties](https://azure.microsoft.com/services/virtual-machines/sql-server/).

U moet ook rekening houden met de juiste installatie en configuratie van SQL Server op een virtuele machine. Het is raadzaam om de [Galerie met installatie kopieën voor virtuele Azure SQL-machines](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) te gebruiken, omdat hiermee kunt u een SQL Server VM maken met de juiste versie, editie en besturings systeem. Hiermee wordt de Azure-VM ook automatisch geregistreerd bij de [resource provider](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) van SQL Server, waarbij functies, zoals geautomatiseerde back-ups en automatische patching, worden ingeschakeld.

## <a name="migration-strategies"></a>Migratiestrategieën

Er zijn twee migratie strategieën voor het migreren van uw gebruikers databases naar een exemplaar van SQL Server op virtuele machines van Azure: **migrate**, en **til en Shift**. 

De juiste benadering voor uw bedrijf is doorgaans afhankelijk van de volgende factoren: 

- Grootte en schaal van migratie
- Snelheid van de migratie
- Toepassings ondersteuning voor code wijziging
- SQL Server versie, besturings systeem of beide moeten worden gewijzigd.
- Levens cyclus van de ondersteunings periode van uw bestaande producten
- Venster voor toepassings uitval tijdens de migratie

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="uitval tijd voor migratie van virtuele machines":::

In de volgende tabel worden de verschillen in de twee migratie strategieën beschreven:
<br />

| **Migratiestrategie** | **Beschrijving** | **Wanneer gebruiken** |
| --- | --- | --- |
| **& verschuiving optillen** | Gebruik de migratie strategie lift en Shift om de volledige fysieke of virtuele SQL Server te verplaatsen van de huidige locatie naar een exemplaar van SQL Server op Azure VM zonder wijzigingen in het besturings systeem of SQL Server versie. Zie [Azure migrate](../../../migrate/migrate-services-overview.md)voor het volt ooien van een lift-en Shift-migratie. <br /><br /> De bron server blijft online-en service aanvragen, terwijl de bron-en doel server gegevens synchroniseren die zorgen voor een vrijwel naadloze migratie. | Gebruiken voor een zeer grootschalige migratie, zelfs van toepassing op scenario's zoals het afsluiten van data centers. <br /><br /> Er zijn mini maal geen code wijzigingen vereist voor de SQL-data bases of-toepassingen van de gebruiker, zodat deze sneller kunnen worden gemigreerd. <br /><br />Er zijn geen extra stappen vereist voor het migreren van de business intelligence-services, zoals  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)en [SSAS](/analysis-services/analysis-services-overview). |
|**Migreren** | Gebruik een migratie strategie wanneer u de doel-SQL Server en/of de versie van het besturings systeem wilt bijwerken. <br /> <br /> Selecteer een Azure-VM in azure Marketplace of een voor bereide SQL Server installatie kopie die overeenkomt met de versie van de bron SQL Server. | Gebruik deze functie als er een vereiste is of als u functies wilt gebruiken die beschikbaar zijn in nieuwere versies van SQL Server, of als er een upgrade moet worden uitgevoerd voor verouderde SQL Server en/of besturingssysteem versies die niet meer worden ondersteund.  <br /> <br /> Mogelijk zijn er wijzigingen in de toepassings-of gebruikers database vereist ter ondersteuning van de SQL Server upgrade. <br /><br />Er zijn mogelijk aanvullende overwegingen voor het migreren van [Business Intelligence](#business-intelligence) -Services als dit binnen het bereik van de migratie ligt. |


## <a name="lift-and-shift"></a>Lift-and-shift  

De volgende tabel bevat informatie over de beschik bare methode voor de migratie strategie voor **lift en verschuiving** om uw SQL Server-Data Base te migreren naar SQL Server op Azure-vm's:
<br />

|**Methode** | **Minimale bron versie** | **Minimale doel versie** | **Beperking van bron back-upgrootte** |  **Opmerkingen** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Bestaande SQL Server worden verplaatst naar een exemplaar van SQL Server op een virtuele Azure-machine. Kan werk belastingen voor de migratie van Maxi maal 35.000 Vm's schalen. <br /><br /> Bron server (s) blijven online-en onderhouds aanvragen tijdens de synchronisatie van Server gegevens en beperken de downtime. <br /><br /> **Automatisering & scripting**: [Azure site Recovery scripts](../../../migrate/how-to-migrate-at-scale.md) en [voor beeld van geschaalde migratie en planning voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

Vanwege het gemak van de installatie is de aanbevolen migratie benadering om lokaal een lokale SQL Server [back-up](/sql/t-sql/statements/backup-transact-sql) te maken en het bestand vervolgens naar Azure te kopiëren. Deze methode ondersteunt grotere data bases (>1 TB) voor alle versies van SQL Server vanaf 2008 en grotere database back-ups (>1 TB). Voor data bases die beginnen met SQL Server 2014, die kleiner zijn dan 1 TB en die goede connectiviteit met Azure hebben, is [SQL Server back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) de betere benadering. 

Bij het migreren van SQL Server-data bases naar een exemplaar van SQL Server op virtuele Azure-machines, is het belang rijk om een aanpak te kiezen die aansluit bij de cutover van de doel server, aangezien dit van invloed is op het downtime-toepassings venster.

De volgende tabel bevat een overzicht van alle beschik bare methoden voor het migreren van uw SQL Server-Data Base naar SQL Server op Azure Vm's:
<br />

|**Methode** | **Minimale bron versie** | **Minimale doel versie** | **Beperking van bron back-upgrootte** | **Opmerkingen** |
| --- | --- | --- | --- | --- |
| **[Back-up naar een bestand](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Dit is een eenvoudige en goed geteste techniek voor het verplaatsen van data bases op verschillende computers. Gebruik compressie om de back-upgrootte voor overdracht te minimaliseren. <br /><br /> **Automation & scripting**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) en [AzCopy naar Blob Storage](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB voor SQL Server 2016, anders 1 TB | Een alternatieve manier om het back-upbestand naar de virtuele machine te verplaatsen met behulp van Azure Storage. Gebruik compressie om de back-upgrootte voor overdracht te minimaliseren. <br /><br /> **Automatisering & scripting**:  [T-SQL of onderhouds plan](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Data base-Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  De [DMA](/sql/dma/dma-overview) evalueert SQL Server on-premises en voert vervolgens probleemloos een upgrade uit naar latere versies van SQL Server of migreert naar SQL Server op virtuele machines van Azure, Azure SQL database of Azure SQL Managed instance. <br /><br /> Mag niet worden gebruikt voor gebruikers databases met FileStream-functionaliteit.<br /><br /> DMA bevat ook mogelijkheden voor het migreren van [SQL-en Windows-aanmeldingen](/sql/dma/dma-migrateserverlogins) en het beoordelen van [SSIS-pakketten](/sql/dma/dma-assess-ssis). <br /><br /> **Automatisering & scripting**: [opdracht regel interface](/sql/dma/dma-commandline) |
| **[Ontkoppelen en koppelen](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Gebruik deze methode wanneer u van plan bent om [deze bestanden op te slaan met behulp van de Azure Blob Storage-service](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) en deze te koppelen aan een exemplaar van SQL Server op een virtuele Azure-machine, met name nuttig voor zeer grote data bases of wanneer de tijd voor back-up en herstel te lang is. <br /><br /> **Automatisering & scripting**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) en [AzCopy naar Blob Storage](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Logboek verzending](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (alleen Windows) | SQL Server 2008 SP4 (alleen Windows) | [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Met logboek verzending worden transactionele logboek bestanden gerepliceerd van on-premises naar een exemplaar van SQL Server op een virtuele Azure-machine. <br /><br /> Dit biedt minimale downtime tijdens de failover en heeft minder configuratie overhead dan het instellen van een AlwaysOn-beschikbaarheids groep. <br /><br /> **Automatisering & scripting**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Gedistribueerde beschikbaarheids groep](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Een [gedistribueerde beschikbaarheids groep](/sql/database-engine/availability-groups/windows/distributed-availability-groups) is een speciaal type beschikbaarheids groep dat twee afzonderlijke beschikbaarheids groepen omvat. De beschikbaarheids groepen die deel uitmaken van een gedistribueerde beschikbaarheids groep hoeven zich niet op dezelfde locatie te bevinden en ondersteuning voor meerdere domeinen te omvatten. <br /><br /> Met deze methode minimaliseert u uitval tijd, gebruikt u wanneer u een beschikbaarheids groep hebt geconfigureerd voor on-premises. <br /><br /> **Automatisering & scripting**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Zie [grote gegevens overdrachten met beperkte connectiviteit](../../../storage/common/storage-solution-large-dataset-low-network.md)voor grote gegevens overdrachten met beperkte toegang tot geen netwerk opties.
> 

### <a name="considerations"></a>Overwegingen

Hier volgt een lijst met belang rijke punten waarmee u rekening moet houden bij het controleren van migratie methoden:

- Voor optimale prestaties van de gegevens overdracht migreert u data bases en bestanden naar een exemplaar van SQL Server op Azure VM met behulp van een gecomprimeerd back-upbestand. Voor grotere data bases, naast compressie, [splitst u het back-upbestand in kleinere bestanden](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) voor betere prestaties tijdens het maken van back-ups en de overdracht. 
- Als u migreert van SQL Server 2014 of hoger, kunt u overwegen [de back-ups te versleutelen](/sql/relational-databases/backup-restore/backup-encryption) om gegevens tijdens de netwerk overdracht te beveiligen.
- Als u de downtime tijdens de database migratie wilt minimaliseren, gebruikt u de optie AlwaysOn-beschikbaarheids groep. 
- Gebruik de optie voor het vastleggen van Logboeken om de downtime te minimaliseren zonder de overhead van het configureren van een beschikbaarheids groep. 
- Gebruik offline migratie methoden zoals back-up-en herstel bewerkingen of [Services voor schijf overdracht](../../../storage/common/storage-solution-large-dataset-low-network.md) die beschikbaar zijn in azure voor een beperkt aantal of geen netwerk opties.
- Zie [SQL Server editie wijzigen](../../virtual-machines/windows/change-sql-server-edition.md)als u ook de versie van SQL Server wilt wijzigen op een SQL Server op Azure VM.

## <a name="business-intelligence"></a>Business Intelligence 

Er kunnen aanvullende overwegingen zijn bij het migreren van SQL Server Business intelligence-services buiten het bereik van de migratie van gebruikers databases. 

Deze services omvatten:

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Ondersteunde versies

Bij de voor bereiding voor het migreren van SQL Server-data bases naar SQL Server op virtuele machines van Azure, moet u rekening houden met de versies van SQL Server die worden ondersteund. Zie [SQL Server op virtuele machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)van Azure voor een lijst met momenteel ondersteunde SQL Server versies op Azure-vm's.


## <a name="next-steps"></a>Volgende stappen

Zie de [hand leiding voor de migratie van afzonderlijke data bases](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)om te beginnen met het migreren van uw SQL server-data bases naar SQL Server op Azure vm's. 

- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md) voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL:
   - [Implementatieopties](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server in Azure VM's](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie voor meer informatie over licentie verlening
   - [Bring Your Own License met de Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Krijg gratis uitgebreide ondersteuning voor SQL Server 2008 en SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.
