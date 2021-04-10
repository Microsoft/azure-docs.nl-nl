---
title: 'SQL Server Azure SQL Database: migratie overzicht'
description: Meer informatie over de hulpprogram ma's en opties die beschikbaar zijn voor het migreren van uw SQL Server-data bases naar Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065154"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Migratie overzicht: SQL Server naar Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Meer informatie over de opties en overwegingen voor het migreren van uw SQL Server-data bases naar Azure SQL Database. 

U kunt SQL Server data bases die on-premises of op worden uitgevoerd, migreren: 

- SQL Server op Azure Virtual Machines.  
- Amazon Web Services (AWS) elastische Compute-Cloud (EC2).
- AWS relationele database service (RDS).
- Compute-engine in Google Cloud Platform (GCP).  
- Cloud-SQL voor SQL Server in GCP. 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

## <a name="overview"></a>Overzicht

[Azure SQL database](../../database/sql-database-paas-overview.md) is een aanbevolen doel optie voor SQL Server workloads waarvoor een volledig beheerd platform als een service (PaaS) is vereist. SQL Database verwerkt de meeste database beheer functies. Het heeft ook ingebouwde hoge Beschik baarheid, intelligente query verwerking, schaal baarheid en prestatie mogelijkheden voor veel toepassings typen. 

SQL Database biedt flexibiliteit met meerdere [implementatie modellen](../../database/sql-database-paas-overview.md#deployment-models) en [service lagen](../../database/service-tiers-vcore.md#service-tiers) die zijn voorzien van verschillende soorten toepassingen of workloads.

Een van de belangrijkste voor delen van het migreren naar SQL Database is dat u uw toepassing kunt moderniseren door gebruik te maken van de PaaS-mogelijkheden. U kunt vervolgens alle afhankelijkheden elimineren van technische onderdelen die zijn afgestemd op het niveau van de instantie, zoals SQL-Agent taken.

U kunt ook kosten besparen met behulp van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) voor SQL Server om uw SQL Server on-premises licenties te migreren naar Azure SQL database. Deze optie is beschikbaar als u het [op vCore gebaseerde inkoop model](../../database/service-tiers-vcore.md)kiest.

## <a name="considerations"></a>Overwegingen 

De belangrijkste factoren waarmee u rekening moet houden wanneer u migratie opties evalueert zijn: 

- Aantal servers en data bases
- Grootte van data bases
- Aanvaard bare bedrijfs downtime tijdens het migratie proces 

Bij de migratie opties die in deze hand leiding worden vermeld, worden de volgende factoren in rekening gebracht. Voor de migratie van logische gegevens naar Azure SQL Database kan de migratie tijd afhankelijk zijn van het aantal objecten in een Data Base en de grootte van de data base. 

Er zijn hulpprogram ma's beschikbaar voor verschillende werk belastingen en gebruikers voorkeuren. Sommige hulpprogram ma's kunnen worden gebruikt voor het uitvoeren van een snelle migratie van één data base via een UI-hulp programma. Andere hulpprogram ma's kunnen de migratie van meerdere data bases automatiseren voor het afhandelen van migraties op schaal. 

## <a name="choose-an-appropriate-target"></a>Kies een geschikt doel

Bekijk algemene richt lijnen om u te helpen bij het kiezen van het juiste implementatie model en de servicelaag van Azure SQL Database. U kunt reken-en opslag Resources kiezen tijdens de implementatie en [deze later wijzigen met behulp van de Azure Portal](../../database/scale-resources.md) zonder uitval tijd voor uw toepassing.

**Implementatie modellen**: inzicht in de workload van uw toepassing en het gebruiks patroon om te bepalen of een enkele data base of een elastische pool. 

- [Eén data base](../../database/single-database-overview.md) vertegenwoordigt een volledig beheerde data base die geschikt is voor de meeste moderne Cloud toepassingen en micro Services.
- Een [elastische pool](../../database/elastic-pool-overview.md) is een verzameling van afzonderlijke data bases met een gedeelde set resources, zoals CPU of geheugen. Het is geschikt voor het combi neren van data bases in een pool met voorspel bare gebruiks patronen die effectief dezelfde set resources kunnen delen.

**Inkoop modellen**: Kies tussen de vCore, de data base Trans Action Unit (DTU) of serverloze aankoop modellen. 

- Met het [vCore-model](../../database/service-tiers-vcore.md) kunt u het aantal vCores voor Azure SQL database kiezen. Dit is de eenvoudigste keuze bij het vertalen van on-premises SQL Server. Dit is de enige optie die het opslaan van licentie kosten met de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)ondersteunt. 
- Het [DTU-model](../../database/service-tiers-dtu.md) bevat een samen vatting van de onderliggende compute, het geheugen en I/O-resources om een OVERVLOEI-DTU te bieden. 
- Het [model zonder server](../../database/serverless-tier-overview.md) is voor werk belastingen waarvoor automatische schaling op aanvraag is vereist met reken resources die worden gefactureerd per seconde van het gebruik. De compute-laag zonder server onderbreekt automatisch data bases tijdens inactieve Peri Oden (waarbij alleen opslag wordt gefactureerd). Data bases worden automatisch hervat wanneer de activiteit wordt geretourneerd. 

**Service lagen**: Kies uit drie service lagen die zijn ontworpen voor verschillende soorten toepassingen.

- [Algemeen/Standard-servicelaag](../../database/service-tier-general-purpose.md) biedt een evenwichtige, budget gerichte optie met reken kracht en opslag die geschikt is voor het leveren van toepassingen in de middelste en lagere laag. Redundantie is ingebouwd in de opslaglaag om fouten op te lossen. Het is ontworpen voor de meeste data base-workloads. 
- [Bedrijfskritiek/Premium-servicelaag](../../database/service-tier-business-critical.md) is voor toepassingen met een hoge laag waarvoor hoge transactie tarieven, I/O met lage latentie en een hoge mate van tolerantie nodig zijn. Secundaire replica's zijn beschikbaar voor failover en om Lees workloads te offloaden.
- [Grootschalige](../../database/service-tier-hyperscale.md) is voor data bases met groeiende gegevens volumes en moet automatisch worden geschaald naar 100 TB in de grootte van de data base. Het is ontworpen voor zeer grote data bases. 

> [!IMPORTANT]
> De frequentie van het [transactie logboek is in Azure SQL database onderhevig](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) aan het beperken van de hoge opname snelheid. Tijdens de migratie moet u mogelijk de doel database resources (vCores of Dtu's) schalen om de belasting van de CPU of door voer te vereenvoudigen. Kies de juiste grootte van de doel database, maar plan zo nodig het schalen van resources voor de migratie. 


### <a name="sql-server-vm-alternative"></a>SQL Server VM-alternatief

Uw bedrijf heeft mogelijk vereisten om SQL Server te maken [op Azure virtual machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) een meer geschikt doel dan Azure SQL database. 

Als een van de volgende voor waarden van toepassing is op uw bedrijf, overweeg dan om over te stappen op een SQL Server virtuele machine (VM): 

- U hebt rechtstreeks toegang tot het besturings systeem of het bestands systeem nodig, zoals bij het installeren van derden of aangepaste agents op dezelfde virtuele machine met SQL Server. 
- U hebt strikte afhankelijkheden voor functies die nog niet worden ondersteund, zoals FileStream/bestands tabel, poly base en trans acties met meerdere exemplaren. 
- U moet een specifieke versie van SQL Server (bijvoorbeeld 2012) blijven. 
- Uw reken vereisten zijn veel lager dan een Managed instance-aanbieding (bijvoorbeeld een vCore) en de database consolidatie is geen acceptabele optie. 


## <a name="migration-tools"></a>Hulpprogramma's voor migratie 

We raden u aan de volgende migratie hulpprogramma's te volgen: 

|Technologie | Beschrijving|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Deze Azure-service helpt u bij het detecteren en evalueren van uw SQL-gegevens op schaal in VMware. Het biedt aanbevelingen voor Azure SQL-implementatie, doel grootte en maandelijkse schattingen. | 
|[Gegevensmigratieassistent](/sql/dma/dma-migrateonpremsqltosqldb)|Dit bureau blad van micro soft biedt naadloze evaluaties van SQL Server en migraties met één Data Base naar Azure SQL Database (schema en gegevens). </br></br>Het hulp programma kan worden geïnstalleerd op een on-premises server of op uw lokale computer die verbinding heeft met uw bron databases. Het migratie proces is een logische gegevens verplaatsing tussen objecten in de bron-en doel database.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Met deze Azure-service kunnen SQL Server-data bases worden gemigreerd naar Azure SQL Database via de Azure Portal of automatisch via Power shell. Database Migration Service moet u een voor Keurs-virtueel Azure-netwerk selecteren tijdens het inrichten om ervoor te zorgen dat de verbinding met uw bron SQL Server data bases wordt gemaakt. U kunt afzonderlijke data bases of op schaal migreren. |
| | |


De volgende tabel bevat alternatieve hulpprogram ma's voor migratie: 

|Technologie |Beschrijving  |
|---------|---------|
|[Transactionele replicatie](../../database/replication-to-sql-database.md)|Gegevens repliceren van bron-SQL Server database tabellen naar Azure SQL Database door een optie te bieden voor de migratie van het type Publisher-abonnee terwijl transactionele consistentie wordt gehandhaafd. Incrementele gegevens wijzigingen worden door gegeven aan abonnees als deze zich op de uitgevers voordoen.|
|[Export service-BACPAC importeren](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) is een Windows-bestand met de extensie. BACPAC waarmee het schema en de gegevens van een Data Base worden ingekapseld. U kunt BACPAC gebruiken om gegevens uit een SQL Server bron te exporteren en de gegevens te importeren in Azure SQL Database. Een BACPAC-bestand kan via de Azure Portal worden geïmporteerd in een nieuwe SQL database. </br></br> Voor schaal en prestaties met grote data bases grootten of een groot aantal data bases kunt u het opdracht regel programma [SqlPackage](../../database/database-import.md#using-sqlpackage) gebruiken om data bases te exporteren en importeren.|
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|Het [hulp programma voor bulksgewijs kopiëren (BCP)](/sql/tools/bcp-utility) kopieert gegevens van een exemplaar van SQL Server naar een gegevens bestand. Gebruik het hulp programma om de gegevens uit uw bron te exporteren en het gegevens bestand te importeren in het doel SQL database. </br></br> Voor het verplaatsen van gegevens naar Azure SQL Database, kunt u het [hulp programma](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) voor bulksgewijs kopiëren gebruiken om de overdrachts snelheid te maximaliseren door gebruik te maken van parallelle Kopieer taken.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|De [Kopieer activiteit](../../../data-factory/copy-activity-overview.md) in azure Data Factory migreert gegevens van de bron-SQL server data base naar Azure SQL database met behulp van ingebouwde connectors en een [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory ondersteunt een breed scala aan [connectors](../../../data-factory/connector-overview.md) om gegevens van SQL Server bronnen naar Azure SQL database te verplaatsen.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u geselecteerde gegevens twee maal kunt synchroniseren in meerdere data bases, zowel on-premises als in de Cloud.</br>Gegevens synchronisatie is handig in gevallen waarin gegevens moeten worden bijgewerkt in meerdere data bases in Azure SQL Database of SQL Server.|
| | |

## <a name="compare-migration-options"></a>Migratie opties vergelijken

Vergelijk migratie opties om het pad te kiezen dat geschikt is voor uw bedrijfs behoeften. 

De volgende tabel vergelijkt de migratie opties die wij aanraden: 

|Migratieoptie  |Wanneer gebruikt u dit?  |Overwegingen  |
|---------|---------|---------|
|[Gegevensmigratieassistent](/sql/dma/dma-migrateonpremsqltosqldb) | -Migreer afzonderlijke data bases (schema en gegevens).  </br> -Kan downtime bevatten tijdens het gegevens migratie proces. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Migratie activiteit voert gegevens verplaatsing uit tussen database objecten (van bron naar doel), daarom raden we u aan deze uit te voeren tijdens daluren. </br> -Data Migration Assistant rapporteert de status van de migratie per database object, inclusief het aantal rijen dat is gemigreerd.  </br> -Gebruik Azure Database Migration Service voor grote migraties (het aantal data bases of de grootte van de data base).|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Migreer afzonderlijke data bases of op een schaal. </br> -Kan downtime bevatten tijdens het migratie proces. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Migraties op schaal kunnen worden geautomatiseerd via [Power shell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Tijd voor het volt ooien van de migratie is afhankelijk van de grootte van de data base en het aantal objecten in de data base. </br> -Vereist dat de bron database is ingesteld als alleen-lezen. |
| | | |

De volgende tabel vergelijkt de alternatieve migratie opties: 

|Methode of technologie |Wanneer gebruikt u dit?    |Overwegingen  |
|---------|---------|---------|
|[Transactionele replicatie](../../database/replication-to-sql-database.md)| -Migreert voortdurend wijzigingen van de bron database tabellen naar doel SQL Database tabellen. </br> -Volledige of gedeeltelijke database migraties van geselecteerde tabellen (subset van een Data Base).  </br> </br> Ondersteunde bronnen: </br> - [SQL Server (2016 tot 2019) met enkele beperkingen](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP Compute SQL Server-VM  | -Setup is relatief complex ten opzichte van andere migratie opties.   </br> -Biedt een optie voor continue replicatie om gegevens te migreren (zonder de data bases offline te halen).  </br> -Transactionele replicatie heeft beperkingen om rekening mee te houden wanneer u de uitgever instelt op het bron SQL Server exemplaar. Zie de [beperkingen voor het publiceren van objecten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) voor meer informatie. </br>-Het is mogelijk om [replicatie activiteiten te bewaken](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Export service-BACPAC importeren](../../database/database-import.md)| -Afzonderlijke line-of-Business-toepassings databases migreren. </br>-Geschikt voor kleinere data bases.  </br>  -Vereist geen afzonderlijke migratie service of hulp programma. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM  |  -Vereist downtime omdat gegevens moeten worden geëxporteerd bij de bron en moeten worden geïmporteerd op de bestemming.   </br> -De bestands indelingen en gegevens typen die in de export-of import bewerking worden gebruikt, moeten consistent zijn met tabel schema's om te voor komen dat er fouten worden afgekapt of gegevens typen die niet overeenkomen.  </br> De tijd die nodig is om een Data Base met een groot aantal objecten te exporteren, kan aanzienlijk hoger zijn.       |
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Voer volledige of gedeeltelijke gegevens migraties uit. </br> -Kan downtime bevatten. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM   | -Vereist uitval tijd voor het exporteren van gegevens uit de bron en het importeren naar het doel. </br> -De bestands indelingen en gegevens typen die in de export-of import bewerking worden gebruikt, moeten consistent zijn met tabel schema's. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Migreer en/of Transformeer gegevens uit de bron-SQL Server-data bases. </br> -Het samen voegen van gegevens uit meerdere gegevens bronnen naar Azure SQL Database is doorgaans voor de werk belasting van business intelligence (BI).  |  -Vereist het maken van pijp lijnen voor gegevens verplaatsing in Data Factory om gegevens van bron naar bestemming te verplaatsen.   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) zijn een belang rijke overweging en zijn gebaseerd op factoren zoals pijplijn triggers, uitvoeringen van activiteiten en duur van de verplaatsing van gegevens. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Gegevens synchroniseren tussen de bron-en doel database.</br> -Geschikt om doorlopende synchronisatie tussen Azure SQL Database en on-premises SQL Server in een bidirectionele stroom uit te voeren. | -Azure SQL Database moet de hub-Data Base zijn voor synchronisatie met een on-premises SQL Server-Data Base als een lid-data base.</br> -Vergeleken met transactionele replicatie, ondersteunt SQL Data Sync bidirectionele gegevens synchronisatie tussen on-premises en Azure SQL Database. </br> -Kan een hogere invloed hebben op de prestaties, afhankelijk van de werk belasting.|
| | | |

## <a name="feature-interoperability"></a>Functie compatibiliteit 

Er zijn meer overwegingen bij het migreren van werk belastingen die afhankelijk zijn van andere SQL Server-functies.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
SQL Server Integration Services SSIS-pakketten migreren naar Azure door de pakketten opnieuw te implementeren naar de Azure-SSIS-runtime in [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [ondersteunt de migratie van SSIS-pakketten](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) door een runtime te bieden die is gebouwd om SSIS-pakketten uit te voeren in Azure. U kunt ook de SSIS ETL (extra heren, transformeren, laden) in Azure Data Factory met behulp van [gegevens stromen](../../../data-factory/concepts-data-flow-overview.md)herschrijven.


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services (SSRS)-rapporten migreren naar gepagineerde rapporten in Power BI. Gebruik het [hulp programma voor RDL-migratie](https://github.com/microsoft/RdlMigration) om uw rapporten voor te bereiden en te migreren. Micro soft heeft dit hulp programma ontwikkeld om klanten te helpen bij het migreren van Report Definition Language rapporten (RDL) van hun SSRS-servers naar Power BI. Het is beschikbaar op GitHub en bevat een end-to-end overzicht van het migratiescenario. 

### <a name="high-availability"></a>Hoge beschikbaarheid
Het hand matig instellen van SQL Server functies met hoge Beschik baarheid, zoals always on failover cluster instances en AlwaysOn-beschikbaarheids groepen, worden verouderd op de doel SQL database. Architectuur met hoge Beschik baarheid is al ingebouwd in de service lagen [Algemeen (standaard beschikbaarheids model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) en [bedrijfskritiek (Premium Availability model)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) voor Azure SQL database. De servicelaag van de Bedrijfskritiek/Premium biedt ook read scale-out waarmee verbinding kan worden gemaakt met een van de secundaire knoop punten voor alleen-lezen doeleinden. 

Naast de architectuur met hoge Beschik baarheid die is opgenomen in Azure SQL Database, kunt u met de functie [groepen voor automatische failover](../../database/auto-failover-group-overview.md) de replicatie en failover van data bases in een beheerd exemplaar naar een andere regio beheren. 

### <a name="sql-agent-jobs"></a>SQL-Agent taken
SQL-Agent taken worden niet rechtstreeks ondersteund in Azure SQL Database en moeten worden geïmplementeerd voor [Elastic data base-taken (preview)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Aanmeldingen en groepen
Verplaats SQL-aanmeldingen van de SQL Server bron naar Azure SQL Database met behulp van Database Migration Service in de offline modus. Gebruik het deel venster **geselecteerde aanmeldingen** in de migratie wizard om aanmeldingen naar uw doel-SQL database te migreren. 

U kunt Windows-gebruikers en-groepen ook migreren via Database Migration Service door de bijbehorende wissel knop in te scha kelen op de Database Migration Service **configuratie** pagina. 

U kunt ook het [Power shell-hulp programma](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) gebruiken dat speciaal is ontworpen voor micro soft Data Migration Architects. Het hulp programma maakt gebruik van Power shell om een Transact-SQL (T-SQL)-script te maken voor het opnieuw maken van aanmeldingen en het selecteren van database gebruikers van de bron naar het doel. 

Het hulp programma Power shell wijst automatisch Windows Server-Active Directory accounts toe aan Azure Active Directory Azure AD-accounts, en kan een UPN-zoek opdracht voor elke aanmelding uitvoeren op basis van het bron Active Directory exemplaar. De hulp programma scripts aangepaste server-en database rollen, samen met het lidmaatschap van de rol en gebruikers machtigingen. Inge sloten data bases worden nog niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. 

### <a name="system-databases"></a>Systeem databases
Voor Azure SQL Database zijn de enige toepasselijke systeem databases [Master](/sql/relational-databases/databases/master-database) en Tempdb. Zie [tempdb in Azure SQL database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie.

## <a name="advanced-features"></a>Geavanceerde functies 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies in SQL Database. Zo hoeft u zich geen zorgen te maken over het beheer van back-ups omdat de service dit voor u doet. U kunt [binnen de Bewaar periode](../../database/recovery-using-backups.md#point-in-time-restore)herstellen naar elk gewenst moment. 

U kunt de beveiliging versterken door gebruik te maken van [Azure AD-verificatie](../../database/authentication-aad-overview.md), [controle](../../database/auditing-overview.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking).

Naast geavanceerde beheer-en beveiligings functies biedt SQL Database hulpprogram ma's waarmee u [uw werk belasting kunt bewaken en afstemmen](../../database/monitor-tune-overview.md). [Azure SQL-analyse (preview)](../../../azure-monitor/insights/azure-sql.md) is een geavanceerde oplossing voor het bewaken van de prestaties van al uw data bases in Azure SQL database op schaal en op meerdere abonnementen in één weer gave. Azure SQL-analyse verzamelt en visualiseert metrische prestatie gegevens met ingebouwde intelligentie voor het oplossen van prestaties.

[Automatisch afstemmen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   bewaakt voortdurend de prestaties van uw SQL-uitvoerings plan en verhelpt automatisch de geïdentificeerde prestatie problemen. 


## <a name="migration-assets"></a>Migratie-assets 

Zie voor meer informatie de volgende resources die zijn ontwikkeld voor migratie projecten met een werkelijke wereld.

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt de aanbevolen doel platformen ' Best passend ', Cloud voorbereiding en een toepassings-en database herstel niveau voor een werk belasting. U kunt met één klik berekeningen en rapporten genereren, waarmee u grote en ongeëvenaarde evaluaties versnelt door een geautomatiseerd en uniform besluitvormings proces te bieden voor doel platforms.|
|[Hulp programma DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|U kunt DBLoader gebruiken om gegevens uit tekst bestanden met scheidings tekens te laden in SQL Server. Dit Windows-console hulpprogramma maakt gebruik van de SQL Server Native-laad interface van de client. De interface werkt in alle versies van SQL Server, samen met Azure SQL Database.|
|[Bulksgewijs data base maken met Power shell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|U kunt een set van drie Power shell-scripts gebruiken voor het maken van een resource groep (create_rg.ps1), de [logische server in azure](../../database/logical-servers.md) (create_sqlserver.ps1) en een SQL database (create_sqldb.ps1). De scripts bevatten lus-mogelijkheden, zodat u zoveel servers en data bases kunt herhalen en maken als dat nodig is.|
|[Implementatie met meerdere schema's met MSSQL-Scripter en Power shell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Met deze asset maakt u een resource groep, maakt u een of meer [logische servers in azure](../../database/logical-servers.md) om Azure SQL database te hosten, exporteert elk schema van een on-premises SQL Server-exemplaar (of meerdere SQL Server 2005 + instanties) en importeert u de schema's in Azure SQL database.|
|[SQL Server Agent taken converteren naar taken voor elastische data bases](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Met dit script worden uw bron SQL Server Agent taken gemigreerd naar Elastic data base-taken.|
|[E-mail berichten verzenden van Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Deze oplossing is een alternatief voor de SendMail-mogelijkheid en is beschikbaar voor on-premises SQL Server. Er worden Azure Functions en de SendGrid-service gebruikt voor het verzenden van e-mail berichten van Azure SQL Database.|
|[Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Een Power shell-script kan een T-SQL-opdracht script maken om aanmeldingen opnieuw te maken en database gebruikers te selecteren van on-premises SQL Server naar Azure SQL Database. Met het hulp programma kunt u automatische toewijzing van Windows Server Active Directory-accounts aan Azure AD-accounts, naast eventueel migratie van SQL Server systeem eigen aanmeldingen.|
|[Automatisering van perfmon Data-verzameling met behulp van logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|U kunt het hulp programma logman gebruiken om perfmon-gegevens te verzamelen (om inzicht te krijgen in de prestaties van de basis lijn) en aanbevelingen voor migratie doelen te verkrijgen. Dit hulp programma maakt gebruik van logman.exe om de opdracht te maken waarmee u prestatie meter items maakt, start, stopt en verwijdert die zijn ingesteld voor een extern SQL Server exemplaar.|
|[Database migratie naar Azure SQL Database met behulp van BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Dit technisch document bevat richt lijnen en stappen om migraties te helpen versnellen van SQL Server naar Azure SQL Database met behulp van BACPAC-bestanden.|

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.


## <a name="next-steps"></a>Volgende stappen

- Als u uw SQL Server-data bases naar Azure SQL Database wilt migreren, raadpleegt [u de SQL Server voor Azure SQL database migratie handleiding](sql-server-to-sql-database-guide.md).

- Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een overzicht van services en hulpprogram ma's die u kunnen helpen bij data base-en gegevens migratie scenario's en speciale taken.

- Voor meer informatie over SQL Database raadpleegt u:
   - [Overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het bepalen van de kosten en grootte van workloads die naar Azure zijn gemigreerd](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)over het beoordelen van de Application Access-laag.

- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een/B-test voor de gegevenslaag van gegevens toegang.
