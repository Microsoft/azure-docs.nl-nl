---
title: 'SQL Server naar SQL Managed instance: migratie overzicht'
description: Meer informatie over de hulpprogram ma's en opties die beschikbaar zijn voor het migreren van uw SQL Server-data bases naar Azure SQL Managed instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078975"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Migratie overzicht: SQL Server naar Azure SQL Managed instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Meer informatie over de opties en overwegingen voor het migreren van uw SQL Server-data bases naar Azure SQL Managed instance. 

U kunt SQL Server data bases die on-premises of op worden uitgevoerd, migreren: 

- SQL Server op Azure Virtual Machines.  
- Amazon Web Services (AWS) elastische Compute-Cloud (EC2). 
- AWS relationele database service (RDS). 
- Compute-engine in Google Cloud Platform (GCP).  
- Cloud-SQL voor SQL Server in GCP. 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

## <a name="overview"></a>Overzicht

[Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md) is een aanbevolen doel optie voor SQL Server werk belastingen waarvoor een volledig beheerde service is vereist zonder dat u virtuele machines of hun besturings systemen hoeft te beheren. Met SQL Managed Instance kunt u uw on-premises toepassingen naar Azure verplaatsen met minimale toepassings-of database wijzigingen. Het biedt volledige isolatie van uw instanties met ondersteuning voor systeem eigen virtuele netwerken. 

## <a name="considerations"></a>Overwegingen 

De belangrijkste factoren waarmee u rekening moet houden wanneer u migratie opties evalueert zijn: 
- Aantal servers en data bases
- Grootte van data bases
- Aanvaard bare bedrijfs downtime tijdens het migratie proces 

Een van de belangrijkste voor delen van het migreren van uw SQL Server-data bases naar een SQL Managed instance is dat u ervoor kunt kiezen om het hele exemplaar of alleen een subset van afzonderlijke data bases te migreren. Plan het volgende in uw migratie proces zorgvuldig: 
- Alle data bases die moeten worden opgeslagen in hetzelfde exemplaar 
- Objecten op exemplaar niveau die vereist zijn voor uw toepassing, waaronder aanmeldingen, referenties, SQL-Agent taken en-Opera tors en triggers op server niveau 

> [!NOTE]
> Azure SQL Managed instance garandeert een Beschik baarheid van 99,99 procent, zelfs in kritieke scenario's. Overhead veroorzaakt door sommige functies in het SQL-beheerde exemplaar, kan niet worden uitgeschakeld. Zie de [belangrijkste oorzaken van prestatie verschillen tussen SQL Managed instance en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) blog item voor meer informatie. 


## <a name="choose-an-appropriate-target"></a>Kies een geschikt doel

De volgende algemene richt lijnen kunnen u helpen bij het kiezen van de juiste servicelaag en kenmerken van SQL Managed instance om u te helpen bij de prestaties van uw [basis lijn](sql-server-to-managed-instance-performance-baseline.md): 

- Gebruik de basis lijn voor het CPU-gebruik om een beheerd exemplaar in te richten dat overeenkomt met het aantal kernen dat door uw exemplaar van SQL Server wordt gebruikt. Het kan nodig zijn om resources te schalen zodat deze overeenkomen met de kenmerken voor het [genereren van hardware](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Gebruik de basis lijn voor het geheugen gebruik om een [vCore optie](../../managed-instance/resource-limits.md#service-tier-characteristics) te kiezen die geschikt is voor uw geheugen toewijzing. 
- Gebruik de I/O-latentie basis lijn van het subsysteem voor bestanden om te kiezen tussen de Algemeen (latentie hoger dan 5 MS) en Bedrijfskritiek (latentie van minder dan 3 MS)-service lagen. 
- Gebruik de basislijn doorvoer om de grootte van de gegevens en logboek bestanden vooraf toe te wijzen om de verwachte I/O-prestaties te krijgen. 

U kunt reken-en opslag Resources kiezen tijdens de implementatie en [deze later wijzigen met behulp van de Azure Portal](../../database/scale-resources.md), zonder uitval tijd voor uw toepassing. 

> [!IMPORTANT]
> Elk verschil in de [virtuele-netwerk vereisten voor beheerde instanties](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kan verhinderen dat u nieuwe instanties maakt of bestaande exemplaren gebruikt. Meer informatie over [het maken van nieuwe en het](../../managed-instance/virtual-network-subnet-create-arm-template.md)   configureren van [bestaande](../../managed-instance/vnet-existing-add-subnet.md)   netwerken. 

Een andere belang rijke overweging in de selectie van de doellaag van Azure SQL Managed instance (Algemeen versus Bedrijfskritiek) is de beschik baarheid van bepaalde functies, zoals In-Memory OLTP, die alleen beschikbaar zijn in de Bedrijfskritiek-laag. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM-alternatief

Uw bedrijf heeft mogelijk vereisten om SQL Server te maken [op Azure virtual machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) een geschikter doel dan Azure SQL Managed instance. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Deze Azure-service ondersteunt migratie in de offline modus voor toepassingen die downtime kunnen veroorloven tijdens het migratie proces. In tegens telling tot de continue migratie in de online modus, voert migratie van de offline modus eenmalige herstel van een volledige database back-up uit van de bron naar het doel. | 
|[Systeem eigen back-up en herstel](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed instance ondersteunt herstel van systeem eigen SQL Server database back-ups (. bak-bestanden). Het is de eenvoudigste migratie optie voor klanten die volledige database back-ups kunnen bieden om te Azure Storage. Volledige en differentiële back-ups worden ook ondersteund en gedocumenteerd in de [sectie over migratie-assets](#migration-assets) verderop in dit artikel.| 
|[Replay-service voor logboek registratie](../../managed-instance/log-replay-service-migrate.md) | Deze Cloud service is ingeschakeld voor SQL Managed instance op basis van SQL Server-technologie voor het verzenden van Logboeken. Het is een migratie optie voor klanten die volledige, differentiële en logboek database back-ups kunnen bieden om te Azure Storage. De replay-service voor logboek registratie wordt gebruikt om back-upbestanden van Azure Blob Storage te herstellen naar een door SQL beheerd exemplaar.| 
| | |

De volgende tabel bevat alternatieve hulpprogram ma's voor migratie: 

|**Technologie** |**Beschrijving**  |
|---------|---------|
|[Transactionele replicatie](../../managed-instance/replication-transactional-overview.md) | Gegevens repliceren van bron-SQL Server database tabellen naar een SQL Managed instance door een Publisher-abonnee type migratie optie te bieden terwijl transactionele consistentie wordt gehandhaafd. | 
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| Het [hulp programma voor bulksgewijs kopiëren (BCP)](/sql/tools/bcp-utility) kopieert gegevens van een exemplaar van SQL Server naar een gegevens bestand. Gebruik het hulp programma om de gegevens uit uw bron te exporteren en het gegevens bestand te importeren in het door SQL beheerde doel exemplaar. </br></br> Voor bewerkingen met een hoge snelheid voor bulksgewijs kopiëren kunt u met het [hulp programma voor bulksgewijs](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) kopiëren de overdrachts snelheid maximaliseren door gebruik te maken van parallelle Kopieer taken. | 
|[Wizard export exporteren/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) is een Windows-bestand met de extensie. BACPAC waarmee het schema en de gegevens van een Data Base worden ingekapseld. U kunt BACPAC gebruiken om gegevens uit een SQL Server bron te exporteren en de gegevens weer te importeren in Azure SQL Managed instance. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  De [Kopieer activiteit](../../../data-factory/copy-activity-overview.md) in azure Data Factory migreert gegevens van bron-SQL server data bases naar een SQL-beheerd exemplaar met behulp van ingebouwde connectors en een [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory ondersteunt een breed scala aan [connectors](../../../data-factory/connector-overview.md) voor het verplaatsen van gegevens van SQL Server bronnen naar een SQL Managed instance. |

## <a name="compare-migration-options"></a>Migratie opties vergelijken

Vergelijk migratie opties om het pad te kiezen dat geschikt is voor uw bedrijfs behoeften. 

De volgende tabel vergelijkt de migratie opties die wij aanraden: 

|Migratieoptie  |Wanneer gebruikt u dit?  |Overwegingen  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Migreer afzonderlijke data bases of meerdere data bases op schaal. </br> -Kan downtime bevatten tijdens het migratie proces. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM |  -Migraties op schaal kunnen worden geautomatiseerd via [Power shell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Tijd voor het volt ooien van de migratie is afhankelijk van de grootte van de data base en wordt beïnvloed door back-up-en herstel tijd. </br> -Er is mogelijk voldoende downtime vereist. |
|[Systeem eigen back-up en herstel](../../managed-instance/restore-sample-database-quickstart.md) | -Afzonderlijke line-of-Business-toepassings databases migreren.  </br> -Snelle en eenvoudige migratie zonder een afzonderlijke migratie service of een afzonderlijk hulp programma.  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Database back-up gebruikt meerdere threads voor het optimaliseren van gegevens overdracht naar Azure Blob Storage, maar partner bandbreedte en database grootte kunnen de overdrachts frequentie beïnvloeden. </br> -Downtime moet voldoen aan de tijd die nodig is voor het uitvoeren van een volledige back-up en herstel (dit is een omvang van de gegevens bewerking).| 
|[Replay-service voor logboek registratie](../../managed-instance/log-replay-service-migrate.md) | -Afzonderlijke line-of-Business-toepassings databases migreren.  </br> -Er is meer controle nodig voor database migraties.  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2008 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -De migratie omvat het maken van volledige database back-ups op SQL Server en het kopiëren van back-upbestanden naar Azure Blob Storage. De replay-service voor logboek registratie wordt gebruikt om back-upbestanden van Azure Blob Storage te herstellen naar een door SQL beheerd exemplaar. </br> -Data bases die tijdens het migratie proces worden hersteld, bevinden zich in een herstel modus en kunnen niet worden gebruikt om te lezen of te schrijven totdat het proces is voltooid.| 
| | | |

De volgende tabel vergelijkt de alternatieve migratie opties: 

|Methode of technologie |Wanneer gebruikt u dit? |Overwegingen  |
|---------|---------|---------|
|[Transactionele replicatie](../../managed-instance/replication-transactional-overview.md) | -Migreert voortdurend wijzigingen van de bron database tabellen naar het doel database tabellen van SQL Managed instance. </br> -Volledige of gedeeltelijke database migraties van geselecteerde tabellen (subset van een Data Base).  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2012 tot 2019) met enkele beperkingen </br> -AWS EC2  </br> -GCP Compute SQL Server-VM | </br> -Setup is relatief complex ten opzichte van andere migratie opties.   </br> -Biedt een optie voor continue replicatie om gegevens te migreren (zonder de data bases offline te halen).</br> -Transactionele replicatie heeft beperkingen om rekening mee te houden wanneer u de uitgever instelt op het bron SQL Server exemplaar. Zie de [beperkingen voor het publiceren van objecten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) voor meer informatie.  </br> -Mogelijkheid om [replicatie activiteiten te bewaken](/sql/relational-databases/replication/monitor/monitoring-replication) is beschikbaar.    |
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Voer volledige of gedeeltelijke gegevens migraties uit. </br> -Kan downtime bevatten. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM   | -Vereist uitval tijd voor het exporteren van gegevens uit de bron en het importeren naar het doel. </br> -De bestands indelingen en gegevens typen die in de export-of import bewerking worden gebruikt, moeten consistent zijn met tabel schema's. |
|[Wizard export exporteren/BACPAC](../../database/database-import.md)| -Afzonderlijke line-of-Business-toepassings databases migreren. </br>-Geschikt voor kleinere data bases.  </br>  Vereist geen afzonderlijke migratie service of hulp programma. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005 tot 2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM  |   </br> -Vereist downtime omdat gegevens moeten worden geëxporteerd bij de bron en moeten worden geïmporteerd op de bestemming.   </br> -De bestands indelingen en gegevens typen die in de export-of import bewerking worden gebruikt, moeten consistent zijn met tabel schema's om te voor komen dat er fouten worden afgekapt of gegevens typen die niet overeenkomen. </br> De tijd die nodig is om een Data Base met een groot aantal objecten te exporteren, kan aanzienlijk hoger zijn. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Migreer en/of Transformeer gegevens uit de bron-SQL Server-data bases.</br> -Het samen voegen van gegevens uit meerdere gegevens bronnen naar Azure SQL Managed instance is doorgaans voor werk belastingen van business intelligence (BI).   </br> -Vereist het maken van pijp lijnen voor gegevens verplaatsing in Data Factory om gegevens van bron naar bestemming te verplaatsen.   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) zijn een belang rijke overweging en zijn gebaseerd op factoren zoals pijplijn triggers, uitvoeringen van activiteiten en duur van de verplaatsing van gegevens. |
| | | |

## <a name="feature-interoperability"></a>Functie compatibiliteit 

Er zijn meer overwegingen bij het migreren van werk belastingen die afhankelijk zijn van andere SQL Server-functies. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

SQL Server Integration Services SSIS-pakketten en-projecten in SSISDB migreren naar Azure SQL Managed instance met behulp van [Azure database Migration service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Alleen SSIS-pakketten in SSISDB die beginnen met SQL Server 2012 worden ondersteund voor migratie. Oudere SSIS-pakketten converteren vóór migratie. Raadpleeg de [zelf studie over project conversie](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) voor meer informatie. 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

U kunt SQL Server Reporting Services (SSRS)-rapporten migreren naar gepagineerde rapporten in Power BI. Gebruik het [hulp programma voor RDL-migratie](https://github.com/microsoft/RdlMigration) om uw rapporten voor te bereiden en te migreren. Micro soft heeft dit hulp programma ontwikkeld om klanten te helpen bij het migreren van Report Definition Language rapporten (RDL) van hun SSRS-servers naar Power BI. Het is beschikbaar op GitHub en bevat een end-to-end overzicht van het migratiescenario. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services tabellaire modellen van SQL Server 2012 en hoger kunnen worden gemigreerd naar Azure Analysis Services. Dit is een PaaS-implementatie model (platform as a Service) voor het model voor Analysis Services tabellair in Azure. U vindt meer informatie over het migreren van on-premises modellen naar Azure Analysis Services in [deze video zelf studie](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

U kunt ook overwegen om uw on-premises Analysis Services tabellaire modellen te migreren naar [Power bi Premium met behulp van de nieuwe XMLA-eind punten voor lezen/schrijven](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Hoge beschikbaarheid

De SQL Server functies voor hoge Beschik baarheid, altijd op failover-cluster instanties en AlwaysOn-beschikbaarheids groepen worden verouderd op het door SQL beheerde doel exemplaar. Architectuur met hoge Beschik baarheid is al ingebouwd in de service lagen [Algemeen (standaard beschikbaarheids model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) en [bedrijfskritiek (Premium Availability model)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) voor SQL Managed instance. Het Premium-beschikbaarheids model biedt ook read scale-out, waarmee verbinding kan worden gemaakt met een van de secundaire knoop punten voor alleen-lezen doeleinden.     

Naast de architectuur met hoge Beschik baarheid die is opgenomen in het SQL Managed instance, kunt u met de functie [groepen voor automatische failover](../../database/auto-failover-group-overview.md) de replicatie en failover van data bases in een beheerd exemplaar naar een andere regio beheren. 

### <a name="sql-agent-jobs"></a>SQL-Agent taken

Gebruik de optie offline Azure Database Migration Service om [SQL-Agent taken](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)te migreren. Als dat niet het geval is, kunt u de taken in Transact-SQL (T-SQL) scripteren met behulp van SQL Server Management Studio en ze vervolgens hand matig opnieuw maken op het door SQL beheerde doel exemplaar. 

> [!IMPORTANT]
> Momenteel ondersteunt Azure Database Migration Service alleen taken met de stappen van het subsysteem T-SQL. Taken met SSIS-pakket stappen moeten hand matig worden gemigreerd. 

### <a name="logins-and-groups"></a>Aanmeldingen en groepen

Verplaats SQL-aanmeldingen van de SQL Server bron naar Azure SQL Managed instance met behulp van Database Migration Service in de offline modus.  Gebruik het deel venster [aanmeldingen selecteren](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) in de migratie wizard om aanmeldingen te migreren naar uw door SQL beheerde doel exemplaar. 

Azure Database Migration Service ondersteunt standaard alleen de migratie van SQL-aanmeldingen. U kunt de migratie van Windows-aanmeldingen echter als volgt inschakelen:

- Zorg ervoor dat de door SQL beheerde instantie met Azure Active Directory (Azure AD) Lees toegang heeft. Een gebruiker met de rol globale beheerder kan die toegang configureren via de Azure Portal.
- Azure Database Migration Service configureren om migraties van Windows-gebruikers of-groepen in te scha kelen. U kunt dit instellen via de Azure Portal op de pagina **configuratie** . Nadat u deze instelling hebt ingeschakeld, start u de service opnieuw op om de wijzigingen van kracht te laten worden.

Nadat u de service opnieuw hebt gestart, worden de Windows-gebruikers-of groeps aanmeldingen weer gegeven in de lijst met aanmeldingen die beschikbaar zijn voor migratie. Voor alle Windows-gebruikers of-groeps aanmeldingen die u migreert, wordt u gevraagd om de bijbehorende domein naam op te geven. Service gebruikers accounts (accounts met de domein naam NT AUTHORITY) en virtuele gebruikers accounts (accounts met de domain name NT-SERVICE) worden niet ondersteund. Zie voor meer informatie [over het migreren van Windows-gebruikers en-groepen in een SQL Server-exemplaar naar Azure SQL Managed instance met T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

U kunt ook het [Power shell-hulp programma](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) gebruiken dat speciaal is ontworpen voor micro soft Data Migration Architects. Het hulp programma maakt gebruik van Power shell om een T-SQL-script te maken voor het opnieuw maken van aanmeldingen en het selecteren van database gebruikers van de bron naar het doel. 

Het hulp programma Power shell wijst automatisch Windows Server-Active Directory accounts toe aan Azure AD-accounts, en kan een UPN-zoek opdracht voor elke aanmelding uitvoeren op basis van het bron Active Directory exemplaar. De hulp programma scripts aangepaste server-en database rollen, samen met het lidmaatschap van de rol en gebruikers machtigingen. Inge sloten data bases worden nog niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. 

### <a name="encryption"></a>Versleuteling

Wanneer u data bases migreert die worden beveiligd door  [transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   naar een beheerd exemplaar met behulp van de systeem eigen terugzet optie, [migreert u het bijbehorende certificaat](../../managed-instance/tde-certificate-migrate.md) van de bron SQL Server instantie naar het door SQL beheerde doel exemplaar *voordat* u de data base terugzet. 

### <a name="system-databases"></a>Systeem databases

Het herstellen van systeem databases wordt niet ondersteund. Als u objecten op exemplaar niveau wilt migreren (opgeslagen in de hoofd-en msdb-data base), scripteert u deze met behulp van T-SQL en maakt u ze opnieuw op het beheerde exemplaar van de bestemming. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabellen geoptimaliseerd voor geheugen)

SQL Server biedt een In-Memory OLTP-mogelijkheid. Het maakt gebruik van tabellen die zijn geoptimaliseerd voor geheugen, geoptimaliseerd voor geheugen en systeem eigen gecompileerde SQL-modules voor het uitvoeren van workloads met hoge door Voer en lage latentie vereisten voor transactionele verwerking. 

> [!IMPORTANT]
> In-Memory OLTP wordt alleen ondersteund in de laag Bedrijfskritiek in Azure SQL Managed instance. Het wordt niet ondersteund in de laag Algemeen.

Als u tabellen hebt geoptimaliseerd voor geheugen of geoptimaliseerd voor geheugen in uw on-premises SQL Server exemplaar en u wilt migreren naar een beheerd exemplaar van Azure SQL, moet u:

- Kies de laag Bedrijfskritiek voor uw door SQL beheerde doel exemplaar dat In-Memory OLTP ondersteunt.
- Als u wilt migreren naar de laag Algemeen in Azure SQL Managed instance, verwijdert u tabellen die zijn geoptimaliseerd voor geheugen, geoptimaliseerde tabel typen en systeem eigen gecompileerde SQL-modules die communiceren met objecten die zijn geoptimaliseerd voor geheugen voordat u uw data bases migreert. U kunt de volgende T-SQL-query gebruiken om alle objecten te identificeren die moeten worden verwijderd voordat de migratie naar de laag Algemeen:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Zie [prestaties optimaliseren met in-Memory technologieën in Azure SQL database en Azure SQL Managed instance](../../in-memory-oltp-overview.md)(Engelstalig) voor meer informatie over de technologieën in het geheugen.

## <a name="advanced-features"></a>Geavanceerde functies 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies in SQL Managed instance. Zo hoeft u zich geen zorgen te maken over het beheer van back-ups omdat de service dit voor u doet. U kunt [binnen de Bewaar periode](../../database/recovery-using-backups.md#point-in-time-restore)herstellen naar elk gewenst moment. Bovendien hoeft u zich geen zorgen te maken over het instellen van hoge Beschik baarheid, omdat [hoge Beschik baarheid is ingebouwd](../../database/high-availability-sla.md). 

U kunt de beveiliging versterken door gebruik te maken van [Azure AD-verificatie](../../database/authentication-aad-overview.md), [controle](../../managed-instance/auditing-configure.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking).

Naast geavanceerde beheer-en beveiligings functies biedt SQL Managed instance geavanceerde hulp middelen waarmee u [uw werk belasting kunt bewaken en afstemmen](../../database/monitor-tune-overview.md). Met [Azure SQL-analyse](../../../azure-monitor/insights/azure-sql.md) kunt u een groot aantal beheerde exemplaren op een gecentraliseerde manier bewaken.  [Automatisch afstemmen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   in beheerde instanties bewaakt voortdurend de prestaties van de uitvoering van het SQL-plan en worden de geïdentificeerde prestatie problemen automatisch opgelost. 

Sommige functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau (150). 

## <a name="migration-assets"></a>Migratie-assets 

Zie voor meer informatie de volgende resources die zijn ontwikkeld voor migratie projecten met een werkelijke wereld.

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt de aanbevolen doel platformen ' Best passend ', Cloud voorbereiding en een toepassings-en database herstel niveau voor een werk belasting. U kunt met één klik berekeningen en rapporten genereren, waarmee u grote en ongeëvenaarde evaluaties versnelt door een geautomatiseerd en uniform besluitvormings proces te bieden voor doel platforms.|
|[Hulp programma DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|U kunt DBLoader gebruiken om gegevens uit tekst bestanden met scheidings tekens te laden in SQL Server. Dit Windows-console hulpprogramma maakt gebruik van de SQL Server Native-laad interface van de client. De interface werkt in alle versies van SQL Server, samen met Azure SQL Managed instance.|
|[Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure SQL Managed instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Een Power shell-script kan een T-SQL-opdracht script maken om aanmeldingen opnieuw te maken en database gebruikers te selecteren van on-premises SQL Server naar Azure SQL Managed instance. Met het hulp programma kunt u automatische toewijzing van Windows Server Active Directory-accounts aan Azure AD-accounts, naast eventueel migratie van SQL Server systeem eigen aanmeldingen.|
|[Automatisering van perfmon Data-verzameling met behulp van logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|U kunt het hulp programma logman gebruiken om perfmon-gegevens te verzamelen (om inzicht te krijgen in de prestaties van de basis lijn) en aanbevelingen voor migratie doelen te verkrijgen. Dit hulp programma maakt gebruik van logman.exe om de opdracht te maken waarmee u prestatie meter items maakt, start, stopt en verwijdert die zijn ingesteld voor een extern SQL Server exemplaar.|
|[Database migratie naar Azure SQL Managed instance door volledige en differentiële back-ups te herstellen](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Dit technisch document bevat richt lijnen en stappen om migraties te helpen versnellen van SQL Server naar Azure SQL Managed instance als u alleen volledige en differentiële back-ups hebt (en geen mogelijkheid tot logboek back-up).|

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [migratie handleiding voor Azure SQL Managed instance](sql-server-to-managed-instance-guide.md)om te beginnen met SQL Server het migreren van uw SQL Server-Data Base naar Azure SQL Managed instance.

- Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een overzicht van services en hulpprogram ma's die u kunnen helpen bij data base-en gegevens migratie scenario's en speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Service lagen in Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Verschillen tussen SQL Server en Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het bepalen van de kosten en grootte van workloads die naar Azure zijn gemigreerd](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)over het beoordelen van de Application Access-laag.

- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een/B-test op de gegevenslaag van de gegevens toegang.
