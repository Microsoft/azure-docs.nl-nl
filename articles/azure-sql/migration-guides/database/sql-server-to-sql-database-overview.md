---
title: 'SQL Server SQL Database: migratie overzicht'
description: Meer informatie over de verschillende hulpprogram ma's en opties die beschikbaar zijn voor het migreren van uw SQL Server-data bases naar Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2d668775e8d11faa0b2913cebc41e5217b49c278
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357322"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Migratie overzicht: SQL Server naar SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Meer informatie over verschillende migratie opties en overwegingen voor het migreren van uw SQL Server naar Azure SQL Database. 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)  
- Cloud-SQL voor SQL Server (Google Cloud Platform – GCP) 

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's. 

## <a name="overview"></a>Overzicht

[Azure SQL database](../../database/sql-database-paas-overview.md) is een aanbevolen doel optie voor SQL Server workloads waarvoor een volledig beheerd platform als een service (PaaS) is vereist. SQL Database verwerkt de meeste database beheer functies, samen met een hoge Beschik baarheid, intelligente query verwerking, schaal baarheid en prestatie mogelijkheden die zijn ingebouwd voor een groot aantal verschillende toepassings typen. 

SQL Database biedt flexibiliteit met meerdere [implementatie modellen](../../database/sql-database-paas-overview.md#deployment-models) en [service lagen](../../database/service-tiers-vcore.md#service-tiers) die zijn voorzien van verschillende soorten toepassingen of workloads.

Een van de belangrijkste voor delen van het migreren naar SQL Database is dat u uw toepassing kunt moderniseren door gebruik te maken van de PaaS-mogelijkheden en alle afhankelijkheden te elimineren van de technische onderdelen die zijn afgestemd op het exemplaar niveau, zoals SQL-Agent taken.

U kunt ook besparen op kosten door uw SQL Server on-premises licenties te migreren naar Azure SQL Database met behulp van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) voor SQL Server moet u het [op vCore gebaseerde aankoop model](../../database/service-tiers-vcore.md)kiezen.

Deze hand leiding is gericht op het verduidelijken van de migratie opties en overwegingen bij het voorbereiden van de migratie van uw SQL Server-data bases naar Azure SQL Database.  

## <a name="considerations"></a>Overwegingen 

De belangrijkste factoren die u moet overwegen bij het evalueren van migratie opties zijn afhankelijk van: 

- Aantal servers en data bases
- Grootte van data bases
- Aanvaard bare bedrijfs downtime tijdens het migratie proces 

Bij de migratie opties die in deze hand leiding worden vermeld, worden de volgende factoren in rekening gebracht. Voor de migratie van logische gegevens naar Azure SQL Database kan de te migreren tijd afhankelijk zijn van het aantal objecten in een Data Base en de grootte van de data base. 

Er zijn verschillende hulpprogram ma's beschikbaar voor verschillende werk belastingen en gebruikers voorkeuren. Sommige hulpprogram ma's kunnen worden gebruikt voor het uitvoeren van een snelle migratie van één data base met behulp van een UI-hulp programma, terwijl andere hulpprogram ma's meerdere data bases kunnen migreren die kunnen worden geautomatiseerd voor het afhandelen van migraties op schaal. 

## <a name="choose-appropriate-target"></a>Het juiste doel kiezen

Bekijk algemene richt lijnen om u te helpen bij het kiezen van het juiste implementatie model en de servicelaag van Azure SQL Database. U kunt reken-en opslag Resources kiezen tijdens de implementatie en deze later wijzigen met behulp van de  [Azure Portal](../../database/scale-resources.md)  zonder uitval tijd voor uw toepassing.


**Implementatie modellen**: inzicht in de werk belasting van uw toepassing en het gebruiks patroon om te bepalen of een enkele data base of elastische pool. 

- [Eén data base](../../database/single-database-overview.md) vertegenwoordigt een volledig beheerde data base die geschikt is voor de meeste moderne Cloud toepassingen en micro Services.
- Een [elastische pool](../../database/elastic-pool-overview.md) is een verzameling afzonderlijke data bases met een gedeelde set resources, zoals CPU of geheugen, en is geschikt voor het combi neren van data bases in een pool met voorspel bare gebruiks patronen die effectief dezelfde set van bronnen kunnen delen.

**Inkoop modellen**: Kies tussen het VCORE, DTU of serverloos aankoop model. 

- Met het [vCore-model](../../database/service-tiers-vcore.md) kunt u het aantal vCores voor uw Azure SQL database kiezen, zodat het de eenvoudigste keuze is wanneer u van on-premises SQL server vertaalt. Dit is de enige optie die het opslaan van de licentie kosten met de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)ondersteunt. 
- Het [DTU-model](../../database/service-tiers-dtu.md) bevat een samen vatting van de onderliggende compute-, geheugen-en i/o-resources om een OVERVLOEI-DTU te bieden. 
- Het [model zonder server](../../database/serverless-tier-overview.md) is bedoeld voor werk belastingen waarvoor automatische schaling op aanvraag is vereist met reken resources die worden gefactureerd per seconde van het gebruik. De compute-laag zonder server onderbreekt automatisch data bases tijdens inactieve Peri Oden (waarbij alleen opslag wordt gefactureerd) en hervat automatisch data bases wanneer de activiteit wordt geretourneerd. 

**Service lagen**: Kies uit drie service lagen die zijn ontworpen voor verschillende soorten toepassingen.

- [Algemeen/Standard-servicelaag](../../database/service-tier-general-purpose.md) biedt een evenwichtige, budget gerichte optie met reken kracht en opslag die geschikt is voor het leveren van toepassingen uit de onderste laag, waarbij redundantie is ingebouwd in de opslaglaag om fouten op te lossen. Ontworpen voor de meeste data base-workloads. 
- [Bedrijfskritiek/Premium-servicelaag](../../database/service-tier-business-critical.md) is voor toepassingen met een hoge categorie waarvoor hoge transactie tarieven, io met lage latentie en een hoge mate van tolerantie met secundaire replica's beschikbaar zijn voor zowel de failover als voor het offloaden van Lees werk belastingen.
- [Grootschalige](../../database/service-tier-hyperscale.md) is voor data bases met groeiende gegevens volumes en moet automatisch worden geschaald naar een grootte van 100-TB data base. Ontworpen voor zeer grote data bases. 

> [!IMPORTANT]
> De frequentie van het [transactie logboek is in Azure SQL database onderhevig](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) aan het beperken van de hoge opname snelheid. Tijdens de migratie kan het nodig zijn om de bronnen van de doel database (vCores/Dtu's) te schalen om de belasting van de CPU of door voer te vereenvoudigen. Kies de juiste doel database, maar plan zo nodig het schalen van resources voor de migratie. 


### <a name="sql-server-on-azure-vm-alternative"></a>Alternatieve SQL Server op Azure VM

Uw bedrijf heeft mogelijk vereisten om SQL Server te maken [op Azure virtual machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) een meer geschikt doel dan Azure SQL database. 

Als het volgende van toepassing is op uw bedrijf, kunt u overwegen om over te stappen op een SQL Server in de Azure VM: 

- Als u directe toegang tot het besturings systeem of het bestands systeem nodig hebt, zoals het installeren van derden of aangepaste agents op dezelfde virtuele machine met SQL Server. 
- Als u strikte afhankelijkheid hebt voor functies die nog niet worden ondersteund, zoals FileStream/bestands tabel, poly base en trans acties met meerdere exemplaren. 
- Als u absoluut een specifieke versie van SQL Server moet blijven (2012, bijvoorbeeld). 
- Als uw reken vereisten veel lager zijn dan aanbiedingen voor beheerde instanties (een vCore, bijvoorbeeld), en de consolidatie van de data base is geen acceptabele optie. 


## <a name="migration-tools"></a>Hulpprogramma's voor migratie 

De aanbevolen hulpprogram ma's voor migratie zijn de Data Migration Assistant en de Azure Database Migration Service. Er zijn ook andere alternatieve migratie opties beschikbaar. 

### <a name="recommended-tools"></a>Aanbevolen hulpprogramma's

De volgende tabel bevat de aanbevolen migratie hulpprogramma's: 

|Technologie | Beschrijving|
|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|De Data Migration Assistant is een bureau blad dat zorgt voor naadloze evaluaties van SQL Server en migraties naar Azure SQL Database (schema en gegevens). Het hulp programma kan worden geïnstalleerd op een on-premises server of op uw lokale computer die verbinding heeft met uw bron databases. Het migratie proces is een logische gegevens verplaatsing tussen objecten in de bron-en doel database. </br> -Eén data base (schema en gegevens) migreren|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Een Azure-service voor de eerste partij waarmee u uw SQL Server-data bases kunt migreren naar Azure SQL Database met behulp van de Azure Portal of geautomatiseerde met Power shell. Voor Azure DMS moet u een voor Keurs-Azure-Virtual Network (VNet) selecteren tijdens de inrichting om ervoor te zorgen dat er verbinding is met uw bron SQL Server-data bases. </br> -Migreer afzonderlijke data bases of op een schaal. |
| | |


### <a name="alternative-tools"></a>Alternatieve hulpprogram ma's

De volgende tabel bevat alternatieve hulpprogram ma's voor migratie: 

|Technologie |Beschrijving  |
|---------|---------|
|[Transactionele replicatie](../../database/replication-to-sql-database.md)|Gerepliceerde gegevens van de bron SQL Server database tabel (s) naar SQL Database door een optie voor de migratie van het type Publisher-abonnee te bieden en de transactionele consistentie te hand haven. Incrementele gegevens wijzigingen worden door gegeven aan abonnees als deze zich op de uitgevers voordoen.|
|[Export service-BACPAC importeren](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) is een Windows-bestand met een `.bacpac` extensie waarmee het schema en de gegevens van een Data Base worden ingekapseld. BACPAC kan worden gebruikt voor het exporteren van gegevens uit een bron SQL Server en om de gegevens te importeren in Azure SQL Database. Het BACPAC-bestand kan worden geïmporteerd in een nieuwe Azure SQL Database met behulp van de Azure Portal. </br></br> Voor schaal baarheid en prestaties met grote data bases grootten of grote aantallen data bases kunt u het [SqlPackage](../../database/database-import.md#using-sqlpackage) -opdracht regel programma gebruiken om data bases te exporteren en te importeren.|
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|Het [hulp programma voor bulksgewijs kopiëren (BCP)](/sql/tools/bcp-utility) kopieert gegevens van een exemplaar van SQL Server naar een gegevens bestand. Gebruik het BCP-hulp programma voor het exporteren van de gegevens uit uw bron en het importeren van het gegevens bestand in het doel SQL Database. </br></br> Voor het verplaatsen van gegevens naar Azure SQL Database, kunt u met het [hulp programma](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) voor bulksgewijs kopiëren de overdrachts snelheid maximaliseren door gebruik te maken van parallelle Kopieer taken.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|De [Kopieer activiteit](../../../data-factory/copy-activity-overview.md) in azure Data Factory migreert gegevens van de bron-SQL Server Data Base (s) naar SQL database met ingebouwde connectors en een [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF ondersteunt een breed scala aan [connectors](../../../data-factory/connector-overview.md) voor het verplaatsen van gegevens van SQL Server bronnen naar SQL database.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u in twee richtingen selecteert, kunt synchroniseren in meerdere data bases, zowel on-premises als in de Cloud.</br>Gegevens synchronisatie is handig in gevallen waarin gegevens moeten worden bijgewerkt in meerdere data bases in Azure SQL Database of SQL Server.|
| | |

## <a name="compare-migration-options"></a>Migratie opties vergelijken

Vergelijk migratie opties om het pad te kiezen dat geschikt is voor uw bedrijfs behoeften. 

### <a name="recommended-options"></a>Aanbevolen opties

In de volgende tabel worden de aanbevolen migratie opties vergeleken: 

|Migratieoptie  |Wanneer gebruikt u dit?  |Overwegingen  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | -Migreer afzonderlijke data bases (schema en gegevens).  </br> -Kan downtime bevatten tijdens het gegevens migratie proces. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Migratie activiteit voert gegevens verplaatsing uit tussen database objecten (van bron naar doel) en wordt daarom aanbevolen om uit te voeren tijdens rustige tijden. </br> -DMA rapporteert de status van de migratie per database object, inclusief het aantal rijen dat is gemigreerd.  </br> -Voor grote migraties (aantal data bases/grootte van data base) gebruikt u de Azure Database Migration Service die hieronder worden weer gegeven.|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Migreer afzonderlijke data bases of op een schaal. </br> -Kan downtime tijdens het migratie proces inpassen. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Migraties op schaal kunnen worden geautomatiseerd via [Power shell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Het volt ooien van de migratie is afhankelijk van de grootte van de data base en het aantal objecten in de data base. </br> -De bron database moet worden ingesteld als alleen-lezen. |
| | | |

### <a name="alternative-options"></a>Alternatieve opties

De volgende tabel vergelijkt de alternatieve migratie opties: 

|Methode/technologie |Wanneer gebruikt u dit?    |Overwegingen  |
|---------|---------|---------|
|[Transactionele replicatie](../../database/replication-to-sql-database.md)| -Migreert voortdurend wijzigingen van de bron database tabellen naar doel SQL Database tabellen. </br> -Volledige of gedeeltelijke database migraties van geselecteerde tabellen (subset van data base).  </br> </br> Ondersteunde bronnen: </br> - [SQL Server (2016-2019) met enkele beperkingen](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP Compute SQL Server-VM  | -Setup is relatief complex ten opzichte van andere migratie opties.   </br> -Biedt een optie voor continue replicatie om gegevens te migreren (zonder de data bases offline te halen).  </br> -Transactionele replicatie heeft een aantal beperkingen om rekening mee te houden bij het instellen van de uitgever op de bron SQL Server. Zie de [beperkingen voor het publiceren van objecten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) voor meer informatie. </br>-Het is mogelijk om [replicatie activiteiten te bewaken](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Export service-BACPAC importeren](../../database/database-import.md)| -Afzonderlijke line-of-Business-toepassings databases migreren. </br>-Geschikt voor kleinere data bases.  </br>  -Vereist geen afzonderlijke migratie service of hulp programma. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM  |  -Vereist uitval tijd wanneer gegevens moeten worden geëxporteerd bij de bron en worden geïmporteerd op de doel locatie.   </br> -De bestands indelingen en gegevens typen die in de export/import worden gebruikt, moeten consistent zijn met de tabel schema's om te voor komen dat de gegevens niet overeenkomen met een Afbrekings fout.  </br> De tijd die nodig is om een Data Base met een groot aantal objecten te exporteren, kan aanzienlijk hoger zijn.       |
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Migreer volledige of gedeeltelijke gegevens migraties. </br> -Kan downtime bevatten. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM   | -Vereist uitval tijd voor het exporteren van gegevens uit de bron en het importeren naar het doel. </br> -De bestands indelingen en gegevens typen die worden gebruikt in de export/import moeten consistent zijn met tabel schema's. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| -Migreer en/of Transformeer gegevens van de bron SQL Server-data bases. </br> -Het samen voegen van gegevens uit meerdere gegevens bronnen naar Azure SQL Database meestal voor BI-workloads (Business Intelligence).  |  -Vereist het maken van pijp lijnen voor gegevens verplaatsing in ADF om gegevens van bron naar bestemming te verplaatsen.   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) zijn een belang rijke overweging en zijn gebaseerd op de pijplijn triggers, de uitvoering van de activiteit, de duur van de verplaatsing van gegevens, enzovoort. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Gegevens synchroniseren tussen de bron-en doel database.</br> -Geschikt om continue synchronisatie tussen Azure SQL Database en on-premises SQL Server in een bidirectionele stroom uit te voeren. | -Azure SQL Database moet de hub-Data Base zijn om te synchroniseren met on-premises SQL Server Data Base als lid-data base.</br> -Vergeleken met transactionele replicatie ondersteunt SQL Data Sync bidirectionele gegevens synchronisatie tussen on-premises en Azure SQL Database. </br> -Kan een hogere invloed hebben op de prestaties, afhankelijk van de werk belasting.|
| | | |

## <a name="feature-interoperability"></a>Functie compatibiliteit 

Er zijn aanvullende overwegingen bij het migreren van werk belastingen die afhankelijk zijn van andere SQL Server-functies.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
SQL Server Integration Services SSIS-pakketten migreren naar Azure door de pakketten opnieuw te implementeren naar Azure-SSIS runtime in [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [ondersteunt de migratie van SSIS-pakketten](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) door een runtime te bieden die is gebouwd om SSIS-pakketten uit te voeren in Azure. U kunt ook de SSIS ETL Logic systeem eigen herschrijven in ADF met behulp van [gegevens stromen](../../../data-factory/concepts-data-flow-overview.md).


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services (SSRS)-rapporten migreren naar gepagineerde rapporten in Power BI. Gebruik het [hulp programma voor RDL-migratie](https://github.com/microsoft/RdlMigration) om uw rapporten voor te bereiden en te migreren. Dit hulpprogramma is ontwikkeld door Microsoft om klanten te helpen bij het migreren van RDL-rapporten van hun SSRS-servers naar Power BI. Het is beschikbaar op GitHub en er wordt een end-to-end-scenario voor het migratie scenario gedocumenteerd. 

#### <a name="high-availability"></a>Hoge beschikbaarheid
Het hand matig instellen van SQL Server functies voor hoge Beschik baarheid, zoals always on failover cluster instances en AlwaysOn-beschikbaarheids groepen, worden verouderd op de doel Azure SQL Database omdat de architectuur voor hoge Beschik baarheid is al ingebouwd in [Algemeen (standaard beschikbaarheids model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) en [bedrijfskritiek (model voor Premium SQL database-Beschik baarheid)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . De Servicelaag van de Bedrijfskritiek/Premium biedt ook read scale-out waarmee verbinding kan worden gemaakt met een van de secundaire knoop punten voor alleen-lezen doeleinden. 

Naast de architectuur met hoge Beschik baarheid die deel uitmaakt van SQL Database, is er ook de functie [groepen voor automatische failover](../../database/auto-failover-group-overview.md) waarmee u de replicatie en failover van data bases in een beheerd exemplaar kunt beheren naar een andere regio. 

#### <a name="sql-agent-jobs"></a>SQL-Agent taken
SQL-Agent taken worden niet rechtstreeks ondersteund in Azure SQL Database en moeten worden geïmplementeerd op [Elastic database-taken (preview)](../../database/job-automation-overview.md#elastic-database-jobs-preview).

#### <a name="logins-and-groups"></a>Aanmeldingen en groepen
Verplaats SQL-aanmeldingen van de bron SQL Server naar Azure SQL Database met behulp van Database Migration Service (DMS) in de offline modus.  Gebruik de Blade **geselecteerde aanmeldingen** in de **migratie wizard** om aanmeldingen naar uw doel-SQL database te migreren. 

Windows-gebruikers en-groepen kunnen ook worden gemigreerd met behulp van DMS door de bijbehorende wissel knop in te scha kelen op de configuratie pagina van het DMS. 

U kunt ook het hulp [programma Power shell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) gebruiken dat speciaal is ontworpen voor micro soft Data Migration Architects. Het hulp programma maakt gebruik van Power shell om een Transact-SQL (T-SQL)-script te maken voor het opnieuw maken van aanmeldingen en het selecteren van database gebruikers van de bron naar het doel. Het hulp programma wijst automatisch Windows AD-accounts toe aan Azure AD-accounts en kan een UPN-lookup voor elke aanmelding uitvoeren op de bron Active Directory. Het hulp programma scripts aangepaste server-en database rollen, evenals rollidmaatschap, databaserol en gebruikers machtigingen. Inge sloten data bases worden nog niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. 


#### <a name="system-databases"></a>Systeem databases
Voor Azure SQL Database zijn de enige toepasselijke systeem databases [Master](/sql/relational-databases/databases/master-database) en Tempdb. Zie [tempdb in Azure SQL database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie.

## <a name="leverage-advanced-features"></a>Geavanceerde functies gebruiken 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die worden geboden door SQL Database. Zo hoeft u zich geen zorgen meer te maken over het beheer van back-ups omdat de service dit voor u doet. U kunt [binnen de Bewaar periode](../../database/recovery-using-backups.md#point-in-time-restore)herstellen naar elk gewenst moment. 

U kunt de beveiliging versterken door [Azure Active Directory verificatie](../../database/authentication-aad-overview.md), [controle](../../database/auditing-overview.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)te gebruiken.

Naast geavanceerde beheer-en beveiligings functies biedt SQL Database een aantal geavanceerde hulp middelen waarmee u [uw werk belasting kunt bewaken en afstemmen](../../database/monitor-tune-overview.md). [Azure SQL-analyse (preview)](../../../azure-monitor/insights/azure-sql.md) is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van al uw data bases in Azure SQL database op schaal en op meerdere abonnementen in één weer gave. Azure SQL-analyse verzamelt en visualiseert metrische prestatie gegevens met ingebouwde intelligentie voor het oplossen van prestaties.

[Automatisch afstemmen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   bewaakt voortdurend de prestaties van uw SQL Execution plan-statistieken en verhelpt automatisch de geïdentificeerde prestatie problemen. 


## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen die zijn ontwikkeld voor de echte wereld wijde migratie projecten voor meer hulp.

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren, waarmee u grote en ongeëvenaarde evaluaties versnelt door een geautomatiseerd en uniform platform besluitvormings proces te bieden.|
|[Hulp programma DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|De DBLoader kan worden gebruikt voor het laden van gegevens uit tekst bestanden met scheidings tekens naar SQL Server. Dit Windows-console hulpprogramma maakt gebruik van de SQL Server Native Client bulkload-interface, die werkt op alle versies van SQL Server, inclusief Azure SQL Database.|
|[Bulksgewijs data base maken met Power shell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Dit omvat een set van drie Power shell-scripts waarmee een resource groep (create_rg.ps1), de [logische server in azure](../../database/logical-servers.md) (create_sqlserver.ps1) en Azure SQL Database (create_sqldb.ps1) wordt gemaakt. De scripts bevatten lus-mogelijkheden, zodat u zoveel servers en data bases kunt herhalen en maken als dat nodig is.|
|[Implementatie met meerdere schema's met MSSQL-Scripter & Power shell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Met deze asset maakt u een resource groep, een of meer [logische servers in azure](../../database/logical-servers.md) voor het hosten van Azure SQL database, exporteert elk schema van een on-premises SQL Server (of meerdere SQL-servers (2005 +) en importeert deze in Azure SQL database.|
|[SQL Server Agent taken naar Elastic Database taken converteren](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Met dit script worden uw bron SQL Server Agent taken naar Elastic Database taken gemigreerd.|
|[E-mails verzenden vanuit Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Dit biedt een oplossing als alternatief voor de SendMail-functie die beschikbaar is in on-premises SQL Server. De oplossing maakt gebruik van Azure Functions en de Azure SendGrid-service voor het verzenden van e-mail berichten van Azure SQL Database.|
|[Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Een Power shell-script waarmee een T-SQL-opdracht script wordt gemaakt om aanmeldingen opnieuw te maken en database gebruikers te selecteren van on-premises SQL Server naar Azure SQL Database. Met het hulp programma kunt u automatische toewijzing van Windows AD-accounts aan Azure AD-accounts en optioneel migratie van SQL Server systeem eigen aanmeldingen.|
|[Automatisering van PerfMon Data Collection met logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Een hulp programma voor het verzamelen van prestatie-gegevens om inzicht te krijgen in de prestaties van de basis lijn en helpt bij het migreren van aanbevelingen. Dit hulp programma maakt gebruik van logman.exe om de opdracht te maken waarmee u prestatie meter items maakt, start, stopt en verwijdert die zijn ingesteld op een externe SQL Server|
|[White Paper-database migratie naar Azure SQL DB met behulp van BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Dit technisch document bevat richt lijnen en stappen voor het versnellen van de migratie van SQL Server naar Azure SQL Database met behulp van BACPAC-bestanden.|

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.


## <a name="next-steps"></a>Volgende stappen

Als u uw SQL Server wilt migreren naar Azure SQL Database, raadpleegt [u de SQL Server voor SQL database migratie handleiding](sql-server-to-sql-database-guide.md).

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Voor meer informatie over SQL Database raadpleegt u:
   - [Overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.