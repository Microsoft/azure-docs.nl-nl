---
title: 'SQL Server naar SQL Managed instance: migratie overzicht'
description: Meer informatie over de verschillende hulpprogram ma's en opties die beschikbaar zijn voor het migreren van uw SQL Server-data bases naar Azure SQL Managed instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: ac2b535b2e6b7a6b4169d08dd1768d69e685a216
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561990"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Migratie overzicht: SQL Server naar een beheerd exemplaar van SQL
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Meer informatie over verschillende migratie opties en overwegingen voor het migreren van uw SQL Server naar Azure SQL Managed instance. 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)  
- Cloud-SQL voor SQL Server (Google Cloud Platform – GCP) 

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's. 

## <a name="overview"></a>Overzicht

[Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md) is een aanbevolen doel optie voor SQL Server werk belastingen waarvoor een volledig beheerde service is vereist zonder dat u virtuele machines of hun besturings systemen hoeft te beheren. Met SQL Managed Instance kunt u uw on-premises toepassingen op Azure zetten met minimale toepassings-of database wijzigingen, terwijl uw instanties volledig worden geïsoleerd met ondersteuning voor het gebruik van een systeem eigen virtuele netwerken (VNet). 

## <a name="considerations"></a>Overwegingen 

De belangrijkste factoren die u moet overwegen bij het evalueren van migratie opties zijn afhankelijk van: 
- Aantal servers en data bases
- Grootte van data bases
- Aanvaard bare bedrijfs downtime tijdens het migratie proces 

Een van de belangrijkste voor delen van het migreren van uw SQL-servers naar een SQL Managed instance is dat u ervoor kunt kiezen om het hele exemplaar te migreren of alleen een subset van afzonderlijke data bases. Plan het volgende in uw migratie proces zorgvuldig: 
- Alle data bases die moeten worden opgeslagen in hetzelfde exemplaar 
- Objecten op exemplaar niveau die vereist zijn voor uw toepassing, waaronder aanmeldingen, referenties, SQL-Agent taken en-Opera tors en triggers op server niveau. 

> [!NOTE]
> Azure SQL Managed instance garandeert een Beschik baarheid van 99,99%, zelfs in kritieke scenario's, waardoor overhead door sommige functies in SQL MI niet kan worden uitgeschakeld. Zie de [hoofd oorzaken van SQL Server en Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) -blog voor meer informatie. 


## <a name="choose-appropriate-target"></a>Het juiste doel kiezen

Enkele algemene richt lijnen om u te helpen bij het kiezen van de juiste servicelaag en kenmerken van SQL Managed instance om u te helpen bij de prestaties van uw [basis lijn](sql-server-to-managed-instance-performance-baseline.md): 

- Gebruik de basis lijn voor het CPU-gebruik om een beheerd exemplaar in te richten dat overeenkomt met het aantal kernen dat door uw exemplaar van SQL Server wordt gebruikt. Het kan nodig zijn om resources te schalen zodat deze overeenkomen met de kenmerken voor het [genereren van hardware](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Gebruik de basis lijn voor het geheugen gebruik om een [vCore optie](../../managed-instance/resource-limits.md#service-tier-characteristics) te kiezen die geschikt is voor uw geheugen toewijzing. 
- Gebruik de IO-latentie van basis lijn van het subsysteem voor het bestand om te kiezen tussen Algemeen (latentie hoger dan 5 MS) en Bedrijfskritiek (latentie van minder dan 3 MS)-service lagen. 
- Gebruik de basislijn doorvoer om de grootte van de gegevens en logboek bestanden vooraf toe te wijzen om de verwachte IO-prestaties te krijgen. 

U kunt reken-en opslag Resources kiezen tijdens de implementatie en deze vervolgens wijzigen nadat u de [Azure Portal](../../database/scale-resources.md) hebt gebruikt zonder uitval tijd voor uw toepassing. 

> [!IMPORTANT]
> Elk verschil in de [vereisten voor virtuele netwerken van het beheerde exemplaar](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kan verhinderen dat u nieuwe instanties maakt of bestaande exemplaren gebruikt. Meer informatie over [het maken van nieuwe en het](../../managed-instance/virtual-network-subnet-create-arm-template.md)   configureren van [bestaande](../../managed-instance/vnet-existing-add-subnet.md)   netwerken. 

Een andere belang rijke overweging in de selectie van de doellaag van Azure SQL Managed instance (Algemeen VS Bedrijfskritiek) is de beschik baarheid van bepaalde functies, zoals In-Memory OLTP, die alleen beschikbaar is in de Bedrijfskritiek-laag. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM-alternatief

Uw bedrijf heeft mogelijk vereisten die SQL Server op virtuele machines van Azure een meer geschikt doel dan Azure SQL Managed instance. 

Als het volgende van toepassing is op uw bedrijf, overweeg dan om over te stappen op een SQL Server VM: 

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
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | Met Azure Migrate voor Azure SQL kunt u uw SQL-gegevens op schaal ontdekken en beoordelen in VMware, met aanbevelingen voor de implementatie van Azure SQL, doel grootte en maandelijkse schattingen. | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Azure-service van de eerste partij die migratie in de offline modus ondersteunt voor toepassingen die downtime tijdens het migratie proces kunnen veroorloven. In tegens telling tot de continue migratie in de online modus, voert migratie van de offline modus eenmalige herstel van een volledige database back-up uit van de bron naar het doel. | 
|[Systeem eigen back-up en herstel](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed instance biedt ondersteuning voor herstel van systeem eigen SQL Server database back-ups (. bak-bestanden), waardoor het de eenvoudigste migratie optie is voor klanten die volledige database back-ups naar Azure Storage kunnen leveren. Volledige en differentiële back-ups worden ook ondersteund en beschreven in de [sectie migratie-assets](#migration-assets) verderop in dit artikel.| 
|[Replay-service voor logboeken (LRS)](../../managed-instance/log-replay-service-migrate.md) | Dit is een Cloud service die is ingeschakeld voor beheerde exemplaren op basis van de SQL Server technieken voor het vastleggen van logboek bestanden, waardoor het een migratie optie is voor klanten die volledige, differentiële en logboek database back-ups kunnen bieden in azure Storage. LRS wordt gebruikt om back-upbestanden van Azure Blob Storage te herstellen naar een door SQL beheerd exemplaar.| 
| | |

### <a name="alternative-tools"></a>Alternatieve hulpprogram ma's

De volgende tabel bevat alternatieve hulpprogram ma's voor migratie: 

|Technologie |Beschrijving  |
|---------|---------|
|[Transactionele replicatie](../../managed-instance/replication-transactional-overview.md) | Gegevens repliceren van bron-SQL Server database tabel (s) naar SQL Managed instance door een Publisher-abonnee type migratie optie te bieden terwijl transactionele consistentie wordt gehandhaafd. |  |
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| Het [hulp programma voor bulksgewijs kopiëren (BCP)](/sql/tools/bcp-utility) kopieert gegevens van een exemplaar van SQL Server naar een gegevens bestand. Gebruik het hulp programma BCP voor het exporteren van de gegevens uit uw bron en het importeren van het gegevens bestand in het door SQL beheerde doel exemplaar.</br></br> Voor het verplaatsen van gegevens naar Azure SQL Database, kunt u het [hulp programma](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) voor bulksgewijs kopiëren gebruiken om de overdrachts snelheid te maximaliseren door gebruik te maken van parallelle Kopieer taken. | 
|[Wizard export exporteren/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) is een Windows-bestand met een `.bacpac` extensie waarmee het schema en de gegevens van een Data Base worden ingekapseld. BACPAC kan worden gebruikt om gegevens uit een bron SQL Server te exporteren en het bestand weer te importeren in Azure SQL Managed instance.  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Met de [Kopieer activiteit](../../../data-factory/copy-activity-overview.md) in azure Data Factory worden gegevens van de bron-SQL Server Data Base (s) gemigreerd naar een SQL-beheerd exemplaar met behulp van ingebouwde connectors en een [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF ondersteunt een breed scala aan [connectors](../../../data-factory/connector-overview.md) voor het verplaatsen van gegevens van SQL Server bronnen naar een door SQL beheerd exemplaar. |
| | |

## <a name="compare-migration-options"></a>Migratie opties vergelijken

Vergelijk migratie opties om het pad te kiezen dat geschikt is voor uw bedrijfs behoeften. 

### <a name="recommended-options"></a>Aanbevolen opties

In de volgende tabel worden de aanbevolen migratie opties vergeleken: 

|Migratieoptie  |Wanneer gebruikt u dit?  |Overwegingen  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Migreer afzonderlijke data bases of meerdere data bases op schaal. </br> -Kan downtime tijdens het migratie proces inpassen. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM |  -Migraties op schaal kunnen worden geautomatiseerd via [Power shell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Het volt ooien van de migratie is afhankelijk van de grootte van de data base en van invloed op back-up-en herstel tijd. </br> -Er is mogelijk voldoende downtime vereist. |
|[Systeem eigen back-up en herstel](../../managed-instance/restore-sample-database-quickstart.md) | -Een afzonderlijke line-of-Business-toepassings database (s) migreren.  </br> -Snelle en eenvoudige migratie zonder een afzonderlijke migratie service of een afzonderlijk hulp programma.  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -Database back-up gebruikt meerdere threads voor het optimaliseren van gegevens overdracht naar Azure Blob-opslag, maar ISV-band breedte en grootte van de data base kunnen de overdrachts frequentie beïnvloeden. </br> -Downtime moet voldoen aan de tijd die nodig is voor het uitvoeren van een volledige back-up en herstel (dit is een omvang van de gegevens bewerking).| 
|[Replay-service voor logboeken (LRS)](../../managed-instance/log-replay-service-migrate.md) | -Een afzonderlijke line-of-Business-toepassings database (s) migreren.  </br> -Er is meer controle nodig voor database migraties.  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2008-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM | -De migratie omvat het maken van volledige database back-ups op SQL Server en het kopiëren van back-upbestanden naar Azure Blob Storage. LRS wordt gebruikt om back-upbestanden van Azure Blob Storage te herstellen naar een door SQL beheerd exemplaar. </br> -Data bases die tijdens het migratie proces worden hersteld, bevinden zich in een herstel modus en kunnen niet worden gebruikt om te lezen of te schrijven totdat het proces is voltooid.| 
| | | |

### <a name="alternative-options"></a>Alternatieve opties

De volgende tabel vergelijkt de alternatieve migratie opties: 

|Methode/technologie |Wanneer gebruikt u dit? |Overwegingen  |
|---------|---------|---------|
|[Transactionele replicatie](../../managed-instance/replication-transactional-overview.md) | -Migreert voortdurend wijzigingen van de bron database tabellen naar het doel database tabellen van SQL Managed instance. </br> -Volledige of gedeeltelijke database migraties van geselecteerde tabellen (subset van data base).  </br> </br> Ondersteunde bronnen: </br> -SQL Server (2012-2019) met enkele beperkingen </br> -AWS EC2  </br> -GCP Compute SQL Server-VM | </br> -Setup is relatief complex ten opzichte van andere migratie opties.   </br> -Biedt een optie voor continue replicatie om gegevens te migreren (zonder de data bases offline te halen).</br> -Transactionele replicatie heeft een aantal beperkingen om rekening mee te houden bij het instellen van de uitgever op de bron SQL Server. Zie de [beperkingen voor het publiceren van objecten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) voor meer informatie.  </br> -Mogelijkheid om [replicatie activiteiten te bewaken](/sql/relational-databases/replication/monitor/monitoring-replication) is beschikbaar.    |
|[Bulksgewijs kopiëren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Volledige of gedeeltelijke gegevens migraties migreren. </br> -Kan downtime bevatten. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM   | -Vereist uitval tijd voor het exporteren van gegevens uit de bron en het importeren naar het doel. </br> -De bestands indelingen en gegevens typen die worden gebruikt in de export/import moeten consistent zijn met tabel schema's. |
|[Wizard export exporteren/BACPAC](../../database/database-import.md)| -Een afzonderlijke line-of-Business-toepassings database (s) migreren. </br>-Geschikt voor kleinere data bases.  </br>  Vereist geen afzonderlijke migratie service of hulp programma. </br> </br> Ondersteunde bronnen: </br> -SQL Server (2005-2019) on-premises of Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server-VM  |   </br> -Vereist uitval tijd wanneer gegevens moeten worden geëxporteerd bij de bron en worden geïmporteerd op de doel locatie.   </br> -De bestands indelingen en gegevens typen die in de export/import worden gebruikt, moeten consistent zijn met de tabel schema's om te voor komen dat de gegevens niet overeenkomen met een Afbrekings fout. </br> De tijd die nodig is om een Data Base met een groot aantal objecten te exporteren, kan aanzienlijk hoger zijn. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| -Gegevens migreren en/of transformeren van bron-SQL Server Data Base (s).</br> -Het samen voegen van gegevens uit meerdere gegevens bronnen naar Azure SQL Managed instance, normaal gesp roken voor BI-workloads (Business Intelligence).   </br> -Vereist het maken van pijp lijnen voor gegevens verplaatsing in ADF om gegevens van bron naar bestemming te verplaatsen.   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) zijn een belang rijke overweging en zijn gebaseerd op de pijplijn triggers, de uitvoering van de activiteit, de duur van de verplaatsing van gegevens, enzovoort. |
| | | |

## <a name="feature-interoperability"></a>Functie compatibiliteit 

Er zijn aanvullende overwegingen bij het migreren van werk belastingen die afhankelijk zijn van andere SQL Server-functies. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

SQL Server Integration Services SSIS-pakketten en-projecten in SSISDB migreren naar Azure SQL Managed instance met behulp van [Azure database Migration service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Alleen SSIS-pakketten in SSISDB die beginnen met SQL Server 2012 worden ondersteund voor migratie. Verouderde SSIS-pakketten converteren vóór migratie. Raadpleeg de [zelf studie over project conversie](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) voor meer informatie. 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Rapporten van SQL Server Reporting Services (SSRS) kunnen worden gemigreerd naar gepagineerde rapporten in Power BI. Gebruik het [hulp programma voor RDL-migratie](https://github.com/microsoft/RdlMigration) om uw rapporten voor te bereiden en te migreren. Dit hulpprogramma is ontwikkeld door Microsoft om klanten te helpen bij het migreren van RDL-rapporten van hun SSRS-servers naar Power BI. Het is beschikbaar op GitHub en er wordt een end-to-end-scenario voor het migratie scenario gedocumenteerd. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services Tabellaire modellen van SQL Server 2012 en hoger kunnen worden gemigreerd naar Azure Analysis Services, een PaaS-implementatie model voor Analysis Services Tabellaire model in Azure. U vindt meer informatie over het migreren van on-premises modellen naar Azure Analysis Services in deze [video zelf studie](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

U kunt ook overwegen om uw on-premises Analysis Services Tabellaire modellen te migreren naar [Power bi Premium met behulp van de nieuwe XMLA-eind punten voor lezen/schrijven](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Power BI XMLA-eind punten voor lezen/schrijven bevindt zich momenteel in de open bare preview en mag niet worden overwogen voor productie werkbelastingen totdat de functionaliteit algemeen beschikbaar wordt.

#### <a name="high-availability"></a>Hoge beschikbaarheid

De functies voor de SQL Server met hoge Beschik baarheid op failover-cluster instanties en AlwaysOn-beschikbaarheids groepen worden verouderd op het doel exemplaar van Azure SQL Managed instance als een architectuur met hoge Beschik baarheid is al ingebouwd in zowel [Algemeen (standaard beschikbaarheids model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) als [bedrijfskritiek (Premium-beschikbaarheids model)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL beheerd exemplaar. Het Premium-beschikbaarheids model biedt ook read scale-out, waarmee verbinding kan worden gemaakt met een van de secundaire knoop punten voor alleen-lezen doeleinden.     

Naast de architectuur met hoge Beschik baarheid die is opgenomen in het SQL Managed instance, is er ook de functie [groepen voor automatische failover](../../database/auto-failover-group-overview.md) waarmee u de replicatie en failover van data bases in een beheerd exemplaar kunt beheren naar een andere regio. 

#### <a name="sql-agent-jobs"></a>SQL-Agent taken

Gebruik de optie offline Azure Database Migration Service (DMS) om [SQL-Agent taken](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)te migreren. Als dat niet het geval is, voert u de taken in Transact-SQL (T-SQL) uit met SQL Server Management Studio en maakt u deze hand matig opnieuw op het door SQL Managed instance-exemplaar. 

> [!IMPORTANT]
> Momenteel ondersteunt Azure DMS alleen taken met de stappen van het T-SQL-subsysteem. Taken met SSIS-pakket stappen moet hand matig worden gemigreerd. 

#### <a name="logins-and-groups"></a>Aanmeldingen en groepen

SQL-aanmeldingen van de bron SQL Server kunnen worden verplaatst naar een Azure SQL Managed instance met behulp van Database Migration Service (DMS) in de offline modus.  Met de Blade **[aanmeldingen selecteren](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** in de **migratie wizard** kunt u aanmeldingen migreren naar uw door SQL beheerde doel exemplaar. 

Standaard biedt Azure Database Migration Service alleen ondersteuning voor het migreren van SQL-aanmeldingen. U kunt echter het migreren van Windows-aanmeldingen als volgt inschakelen:

Zorg ervoor dat het beheerde exemplaar van SQL Managed Azure AD-Lees toegang heeft, dat kan worden geconfigureerd via de Azure Portal door een gebruiker met de rol van **globale beheerder** .
Configureer uw Azure Database Migration Service-exemplaar om migraties van Windows-gebruikers en -groepen in te schakelen, wat via de Azure-portal, op de pagina Configuratie, kan worden ingesteld. Nadat u deze instelling hebt ingeschakeld, start u de service opnieuw om de wijzigingen van kracht te laten worden.

Nadat de service opnieuw is gestart, worden de aanmeldingen van Windows-gebruikers en-groepen weergegeven in de lijst met aanmeldingen die beschikbaar zijn voor migratie. Voor alle aanmeldingen van Windows-gebruikers en-groepen die u migreert, wordt u gevraagd om de bijbehorende domeinnaam op te geven. Gebruikersaccounts voor services (account met domeinnaam NT AUTHORITY) en virtuele gebruikersaccounts (account met domeinnaam NT SERVICE) worden niet ondersteund.

Zie voor meer informatie [over het migreren van Windows-gebruikers en-groepen in een SQL Server-exemplaar naar Azure SQL Managed instance met T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

U kunt ook het hulp [programma Power shell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) gebruiken dat speciaal is ontworpen voor micro soft Data Migration Architects. Het hulp programma maakt gebruik van Power shell om een T-SQL-script te maken voor het opnieuw maken van aanmeldingen en het selecteren van database gebruikers van de bron naar het doel. Het hulp programma wijst automatisch Windows AD-accounts toe aan Azure AD-accounts en kan een UPN-lookup voor elke aanmelding uitvoeren op de bron Active Directory. Het hulp programma scripts aangepaste server-en database rollen, evenals rollidmaatschap, databaserol en gebruikers machtigingen. Inge sloten data bases worden momenteel niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. 

#### <a name="encryption"></a>Versleuteling

Bij het migreren van data bases die zijn beveiligd door  [transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   naar een beheerd exemplaar met behulp van de systeem eigen terugzet optie, [migreert u het bijbehorende certificaat](../../managed-instance/tde-certificate-migrate.md) van de bron SQL Server naar het door SQL beheerde doel exemplaar *voordat* u de data base terugzet. 

#### <a name="system-databases"></a>Systeem databases

Het herstellen van systeem databases wordt niet ondersteund. Als u objecten op exemplaar niveau wilt migreren (opgeslagen in Master-of msdb-data bases), scripteert u deze met behulp van Transact-SQL (T-SQL) en maakt u ze opnieuw op het beheerde exemplaar van de doel groep. 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabellen geoptimaliseerd voor geheugen)

SQL Server biedt In-Memory OLTP-mogelijkheid voor gebruik van tabellen die zijn geoptimaliseerd voor geheugen, geoptimaliseerd voor geheugen en systeem eigen, gecompileerde SQL-modules voor het uitvoeren van workloads met hoge door Voer en lage latentie verwerkings vereisten voor transactionele trans acties. 

> [!IMPORTANT]
> In-Memory OLTP wordt alleen ondersteund in de Bedrijfskritiek-laag van een Azure SQL Managed instance (en wordt niet ondersteund in de Algemeen-laag).

Als u voor geheugen geoptimaliseerde tabellen of tabel typen die zijn geoptimaliseerd voor geheugen hebt in uw on-premises SQL Server en u wilt migreren naar Azure SQL Managed instance, moet u:

- Kies Bedrijfskritiek laag voor uw doel exemplaar van Azure SQL Managed dat In-Memory OLTP ondersteunt, of
- Als u wilt migreren naar Algemeen tier in Azure SQL Managed instance, verwijdert u tabellen die zijn geoptimaliseerd voor geheugen, geoptimaliseerde tabel typen en systeem eigen gecompileerde SQL-modules die communiceren met objecten die zijn geoptimaliseerd voor geheugen voordat u uw data base (s) migreert. De volgende T-SQL-query kan worden gebruikt om alle objecten te identificeren die moeten worden verwijderd voordat de migratie naar Algemeen laag kan worden uitgevoerd:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Zie [prestaties optimaliseren met in-Memory technologieën in Azure SQL database en Azure SQL Managed instance](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview) (Engelstalig) voor meer informatie over de technologieën in het geheugen.

## <a name="leverage-advanced-features"></a>Geavanceerde functies gebruiken 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die beschikbaar worden gesteld door SQL Managed instance. Zo hoeft u zich geen zorgen meer te maken over het beheer van back-ups omdat de service dit voor u doet. U kunt [binnen de Bewaar periode](../../database/recovery-using-backups.md#point-in-time-restore)herstellen naar elk gewenst moment. Bovendien hoeft u zich geen zorgen te maken over het instellen van hoge Beschik baarheid, omdat [hoge Beschik baarheid is ingebouwd](../../database/high-availability-sla.md). 

U kunt de beveiliging versterken door [Azure Active Directory verificatie](../../database/authentication-aad-overview.md), [controle](../../managed-instance/auditing-configure.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)te gebruiken.

Naast geavanceerde beheer-en beveiligings functies biedt SQL Managed instance een aantal geavanceerde hulp middelen waarmee u [uw werk belasting kunt bewaken en afstemmen](../../database/monitor-tune-overview.md). Met [Azure SQL-analyse](../../../azure-monitor/insights/azure-sql.md) kunt u een groot aantal beheerde exemplaren op een gecentraliseerde manier bewaken.  [Automatisch afstemmen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   in beheerde instanties bewaakt voortdurend de prestaties van de uitvoerings statistieken van uw SQL-plan en worden de geïdentificeerde prestatie problemen automatisch opgelost. 

Sommige functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau (150). 

## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen die zijn ontwikkeld voor de echte wereld wijde migratie projecten voor meer hulp.

|Asset  |Beschrijving  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door het besluitvormings proces voor een geautomatiseerd en uniform doel platform te bieden.|
|[Hulp programma DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|De DBLoader kan worden gebruikt voor het laden van gegevens uit tekst bestanden met scheidings tekens naar SQL Server. Dit Windows-console hulpprogramma maakt gebruik van de SQL Server Native Client bulkload-interface, die werkt op alle versies van SQL Server, waaronder Azure SQL MI.|
|[Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure SQL Managed instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Een Power shell-script waarmee een T-SQL-opdracht script wordt gemaakt om aanmeldingen opnieuw te maken en database gebruikers te selecteren van on-premises SQL Server naar Azure SQL Managed instance. Met het hulp programma kunt u automatische toewijzing van Windows AD-accounts aan Azure AD-accounts en optioneel migratie van SQL Server systeem eigen aanmeldingen.|
|[Automatisering van perfmon Data Collection met logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Een hulp programma waarmee u gegevens kunt verzamelen om inzicht te krijgen in de basislijn prestaties die helpt bij de aanbeveling voor migratie doel. Dit hulp programma dat logman.exe gebruikt voor het maken van de opdracht voor het maken, starten, stoppen en verwijderen van prestatie meter items die zijn ingesteld op een externe SQL Server.|
|[White Paper: database migratie naar Azure SQL Managed instance door volledige en differentiële back-ups te herstellen](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Dit technisch document bevat richt lijnen en stappen voor het versnellen van de migratie van SQL Server naar Azure SQL Managed instance als u alleen volledige en differentiële back-ups (en geen mogelijkheid tot logboek back-up) hebt.|

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [migratie handleiding voor het SQL Server van SQL Managed instance](sql-server-to-managed-instance-guide.md)om te beginnen met het migreren van uw SQL Server naar Azure SQL Managed instance.

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Service lagen in Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Verschillen tussen SQL Server en Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.
