---
title: 'SQL Server SQL Server op virtuele machines van Azure: migratie handleiding'
description: Deze hand leiding leert u uw afzonderlijke SQL Server-data bases te migreren naar SQL Server op virtuele machines van Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0e1b44667a5ff42978b22ab9450d6a8e9870960b
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563212"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Migratie handleiding: SQL Server SQL Server op virtuele machines van Azure 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Deze migratie handleiding leert u uw gebruikers databases te **ontdekken**, **evalueren** en **migreren** van SQL Server naar een exemplaar van SQL Server op Azure virtual machines (vm's) met behulp van de back-up-en herstel-en logboek verzending, waarbij de [Data Base migration assistant (DMA)](/sql/dma/dma-overview) wordt gebruikt voor evaluatie. 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren:

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)

Voor informatie over aanvullende migratie strategieën raadpleegt u het [overzicht van SQL Server VM-migratie](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Migratie proces stroom":::

## <a name="prerequisites"></a>Vereisten

Voor de migratie naar SQL Server op Azure Vm's is het volgende vereist: 

- [Data Base-migration assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- Een [Azure migrate-project](../../../migrate/create-manage-projects.md).
- Een voor bereide doel [SQL Server op de Azure-VM](../../virtual-machines/windows/create-sql-vm-portal.md) die dezelfde of een grotere versie heeft dan de bron SQL Server.
- [Connectiviteit tussen Azure en on-premises](/azure/architecture/reference-architectures/hybrid-networking).
- [Een geschikte migratie strategie kiezen](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Premigratie

Voordat u met de migratie begint, moet u de topologie van uw SQL-omgeving ontdekken en de uitvoer baarheid van uw beoogde migratie beoordelen. 

### <a name="discover"></a>Ontdekken

Azure Migrate beoordeling van de geschiktheid van de migratie van on-premises computers, voert de prestaties op basis van een grootte en biedt kosten ramingen voor on-premises uitvoering. Als u de migratie wilt plannen, gebruikt u Azure Migrate om [bestaande gegevens bronnen te identificeren en Details over de functies](../../../migrate/concepts-assessment-calculation.md) die uw SQL Server-instanties gebruiken. Dit proces omvat het scannen van het netwerk om al uw SQL Server exemplaren in uw organisatie te identificeren met de versie en de functies die in gebruik zijn. 

> [!IMPORTANT]
> Bij het kiezen van een virtuele Azure-doel machine voor uw SQL Server-exemplaar, moet u rekening houden met de [richt lijnen voor prestaties voor SQL Server op Azure-vm's](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Zie [Services en hulpprogram ma's](../../../dms/dms-tools-matrix.md#business-justification-phase) die beschikbaar zijn voor scenario's voor gegevens migratie voor aanvullende hulpprogram ma's voor detectie.


### <a name="assess"></a>Evalueren

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Nadat u alle gegevens bronnen hebt gedetecteerd, gebruikt u de [Data Migration Assistant (DMA)](/sql/dma/dma-overview) voor het beoordelen van on-premises SQL Server instantie (s) die worden gemigreerd naar een exemplaar van SQL Server op Azure VM om inzicht te krijgen in de hiaten tussen de bron-en doel exemplaren. 


> [!NOTE]
> Als u de versie van SQL Server _niet_ bijwerkt, slaat u deze stap over en gaat u naar de sectie [migrate](#migrate) . 


#### <a name="assess-user-databases"></a>Gebruikers databases beoordelen 

De Data Migration Assistant (DMA) helpt u bij de migratie naar een modern gegevens platform door compatibiliteits problemen te detecteren die van invloed kunnen zijn op de database functionaliteit in uw nieuwe versie van SQL Server. DMA adviseert verbeteringen in prestaties en betrouw baarheid voor uw doel omgeving en biedt u ook de mogelijkheid om uw schema, gegevens en aanmeldings objecten van de bron server naar de doel server te verplaatsen.

Bekijk de [evaluatie](/sql/dma/dma-migrateonpremsql) voor meer informatie. 


> [!IMPORTANT]
>Op basis van het type evaluatie kunnen de vereiste machtigingen op de bron SQL Server verschillen. 
   > - Voor de **functie parity** Advisor moeten de referenties die zijn gegeven om verbinding te maken met de bron-SQL Server Data Base lid zijn van de serverrol *sysadmin* .
   > - Voor de compatibiliteits problemen Advisor moeten de verstrekte referenties ten minste `CONNECT SQL` `VIEW SERVER STATE` en de `VIEW ANY DEFINITION` juiste machtigingen hebben.
   > - DMA markeert de machtigingen die vereist zijn voor de gekozen Advisor voordat de evaluatie wordt uitgevoerd.


#### <a name="assess-applications"></a>Toepassingen beoordelen

Normaal gesp roken opent een toepassings laag gebruikers databases om gegevens te behouden en te wijzigen.  Met DMA kan de Gegevenstoegangslaag van een toepassing op twee manieren worden geëvalueerd: 

- Vastgelegde [uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/extended-events) of [SQL Server Profiler traceringen ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) van uw gebruikers databases gebruiken. U kunt ook de [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) gebruiken om een tracerings logboek te maken dat ook kan worden gebruikt voor een/B-test.

- Met de [Data Access Migration Toolkit (preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), die een detectie en evaluatie van SQL-query's in de code biedt, en wordt gebruikt om de bron code van de toepassing te migreren van het ene database platform naar het andere. Dit hulp programma ondersteunt diverse populaire bestands typen, waaronder C#-en Java-, XML-en tekst zonder opmaak. Zie de blog van [DMAT gebruiken](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) voor een hand leiding voor het uitvoeren van een DAMT-beoordeling.

Gebruik DMA om vastgelegde tracerings bestanden of DAMT-bestanden te [importeren](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) tijdens de evaluatie van gebruikers databases. 


#### <a name="scale-assessments"></a>Scale-analyses

Als u meerdere servers hebt waarvoor een DMA-analyse is vereist, kunt u het proces automatiseren via de [opdracht regel interface](/sql/dma/dma-commandline). Met de-interface kunt u evaluatie opdrachten vooraf voorbereiden voor elk SQL Server exemplaar in het bereik voor migratie. 

Data Migration Assistant (DMA)-evaluaties kunnen nu samen worden [geconsolideerd in azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Refactory-data bases met DMA

Op basis van de resultaten van de DMA-evaluatie hebt u mogelijk een aantal aanbevelingen om ervoor te zorgen dat uw gebruikers database (s) goed worden uitgevoerd en werken na de migratie. DMA bevat details over de betrokken objecten en bronnen voor het oplossen van elk probleem. Het is raadzaam alle breuk wijzigingen te verhelpen en wijzigingen in het gedrag worden opgelost vóór de productie migratie.

Voor afgeschafte functies kunt u ervoor kiezen om uw gebruikers database (s) uit te voeren in de oorspronkelijke [compatibiliteits](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) modus als u wilt voor komen dat u deze wijzigingen aanbrengt en de migratie wilt versnellen. Hiermee wordt echter voor komen dat [uw database compatibiliteit wordt bijgewerkt](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) totdat de afgeschafte items zijn opgelost.

Het wordt ten zeerste aangeraden alle DMA-oplossingen tijdens het [na de migratie](#post-migration)toe te passen op de doel-SQL Server Data Base.

> [!CAUTION]
> Niet alle versies van SQL Server ondersteunen alle compatibiliteits modi. Controleer of uw [doel-SQL Server-versie](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) de gekozen database compatibiliteit ondersteunt. SQL Server 2019 biedt bijvoorbeeld geen ondersteuning voor data bases met niveau-90-compatibiliteit (SQL Server 2005). Voor deze data bases is ten minste een upgrade van het compatibiliteits niveau 100 vereist.
>

## <a name="migrate"></a>Migrate

Nadat u de stappen voorafgaand aan de migratie hebt voltooid, bent u klaar om de gebruikers databases en-onderdelen te migreren. Migreer uw data bases met uw voorkeurs [migratie methode](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).  

Hieronder vindt u de stappen voor het uitvoeren van een migratie met behulp van back-ups maken en herstellen, of een minimale downtime-migratie met back-up en herstel samen met logboek verzending. 

### <a name="backup-and-restore"></a>Back-ups en herstellen

Voer de volgende stappen uit om een standaard migratie uit te voeren met back-up en herstel: 

1. Stel de verbinding met de doel-SQL Server op de Azure-VM in op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Toepassingen onderbreken/stoppen die gebruikmaken van data bases die bedoeld zijn voor migratie. 
1. Zorg ervoor dat de gebruikers database (s) inactief zijn door gebruik te maken van de [modus voor één gebruiker](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Voer een volledige back-up van de data base uit naar een on-premises locatie.
1. Kopieer uw lokale back-upbestand (en) naar uw VM met behulp van extern bureau blad, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)of het [AZCopy-opdracht regel programma](../../../storage/common/storage-use-azcopy-v10.md) (> 2-TB back-ups aanbevolen).
1. Back-ups van de volledige data base herstellen naar de SQL Server op de virtuele machine van Azure.

### <a name="log-shipping--minimize-downtime"></a>Logboek verzending (downtime minimaliseren)

Voer de volgende stappen uit om een minimale downtime-migratie uit te voeren met back-up, herstel en logboek verzending: 

1. Stel de connectiviteit in voor de doel SQL Server op Azure VM op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Zorg ervoor dat de lokale gebruikers database (s) die moeten worden gemigreerd, zich in het volledige of bulksgewijs vastgelegde herstel model bevinden.
1. Voer een volledige back-up van de Data Base naar een on-premises locatie en wijzig alle bestaande back-uptaken van de volledige data base om [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) tref woord te gebruiken om de logboek keten te bewaren.
1. Kopieer uw lokale back-upbestand (en) naar uw VM met behulp van extern bureau blad, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)of het [AZCopy-opdracht regel programma](../../../storage/common/storage-use-azcopy-v10.md) (>1-TB back-ups aanbevolen).
1. Herstel de volledige back-ups van de Data Base op de SQL Server op de virtuele machine van Azure.
1. Stel de [back-upfunctie voor logboeken](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) in tussen de on-premises data base en het doel SQL Server op de Azure VM. Zorg ervoor dat u de data base (s) niet opnieuw initialiseert omdat deze al is voltooid in de vorige stappen.
1. **Knippen** naar de doel server. 
   1. Toepassingen met data bases die moeten worden gemigreerd, onderbreken/stoppen. 
   1. Zorg ervoor dat de gebruikers database (s) inactief zijn door gebruik te maken van de [modus voor één gebruiker](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Als u klaar bent, voert u een [failover](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) van een overmatige logboek registratie van de lokale data base (s) uit om SQL Server te richten op de virtuele machine van Azure.



### <a name="migrating-objects-outside-user-databases"></a>Objecten buiten de gebruikers database (s) migreren

Er kunnen aanvullende SQL Server objecten zijn die vereist zijn voor de naadloze werking van uw gebruikers databases na migratie. 

De volgende tabel bevat een lijst met onderdelen en aanbevolen migratie methoden die kunnen worden uitgevoerd vóór of na de migratie van uw gebruikers databases: 


| **Functie** | **Onderdeel** | **Migratie methode (n)** |
| --- | --- | --- |
| **Databases** | Model  | Script met SQL Server Management Studio |
|| TempDB | Plan TempDB te verplaatsen naar de [tijdelijke schijf van de virtuele machine van Azure (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) voor de beste prestaties. Zorg ervoor dat u een VM-grootte kiest die een voldoende lokale SSD heeft om uw TempDB te kunnen verwerken. |
|| Gebruikers databases met FileStream |  Gebruik de [back-up-en herstel](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) methoden voor migratie. DMA biedt geen ondersteuning voor data bases met FileStream. |
| **Beveiliging** | SQL Server-en Windows-aanmeldingen | Gebruik DMA om [Gebruikers aanmeldingen te migreren](/sql/dma/dma-migrateserverlogins). |
|| SQL Server rollen | Script met SQL Server Management Studio |
|| Cryptografische providers | [U kunt het beste converteren om Azure Key Vault-service te gebruiken](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Deze procedure maakt gebruik van de [resource provider](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)van de SQL-VM. |
| **Server objecten** | Back-upapparaten | Vervang door back-up van de data base met [Azure backup-service](../../../backup/backup-sql-server-database-azure-vms.md) of schrijf back-ups naar [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 Cu2 +). Deze procedure maakt gebruik van de [resource provider](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)van de SQL-VM.|
|| Gekoppelde servers | Script met SQL Server Management Studio. |
|| Server triggers | Script met SQL Server Management Studio. |
| **Replicatie** | Lokale publicaties | Script met SQL Server Management Studio. |
|| Lokale abonnees | Script met SQL Server Management Studio. |
| **Polybase** | Polybase | Script met SQL Server Management Studio. |
| **Beheer** | Database Mail | Script met SQL Server Management Studio. |
| **SQL Server Agent** | Taken | Script met SQL Server Management Studio. |
|| Waarschuwingen | Script met SQL Server Management Studio. |
|| Operators | Script met SQL Server Management Studio. |
|| Proxy's | Script met SQL Server Management Studio. |
| **Besturingssysteem** | Bestanden, bestands shares | Noteer alle extra bestanden of bestands shares die worden gebruikt door uw SQL-servers en repliceer op het Azure VM-doel. |


## <a name="post-migration"></a>Postmigratie

Nadat u de migratie fase hebt voltooid, gaat u door een reeks taken na migratie om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. Dit kan in sommige gevallen wijzigingen in de toepassingen vereisen.

Pas een Data Base toe Migration Assistant aanbevolen oplossingen voor gebruikers database (s). Deze worden aanbevolen voor een script om consistentie te garanderen en om automatisering mogelijk te maken.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit het uitvoeren van de volgende activiteiten:

1. **Validatie tests ontwikkelen.**  Gebruik SQL-query's om database migraties te testen. Validatie query's maken om uit te voeren op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
2. **Test omgeving instellen.**  De test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
3. **Validatie tests uitvoeren.**  Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
4. **Prestatie tests uitvoeren.**  Voer prestatie tests uit op basis van de bron en het doel, en analyseer en vergelijk de resultaten.

> [!TIP]
> Gebruik de [database Experimentation Assistant (DEA)](/sql/dea/database-experimentation-assistant-overview) om te helpen bij het evalueren van de doel-SQL Server prestaties.


### <a name="optimize"></a>Optimaliseren

De post migratie fase is van cruciaal belang voor het afstemmen van problemen met gegevens nauwkeurigheid en volledigheid, en voor het adresseren van potentiële prestatie problemen met de werk belasting.

Raadpleeg de volgende bronnen voor meer informatie over deze problemen en specifieke stappen om ze te beperken:

- [Hand leiding voor validatie na migratie en optimalisatie.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Prestaties afstemmen in Azure SQL virtual machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Azure cost Optimization Center](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Volgende stappen

- Als u de beschik baarheid van services wilt controleren die van toepassing zijn op SQL Server raadpleegt u het [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

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