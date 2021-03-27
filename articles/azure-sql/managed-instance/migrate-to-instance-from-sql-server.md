---
title: Migreren van SQL Server naar een beheerd exemplaar van Azure SQL
description: Meer informatie over het migreren van een Data Base van SQL Server naar Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: ccc6acfd27a1430a4f6a31886c06322c5c09e224
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628370"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migratie van SQL Server-exemplaren naar Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel vindt u informatie over de methoden voor het migreren van een exemplaar van SQL Server 2005 of hoger naar [Azure SQL Managed instance](sql-managed-instance-paas-overview.md). Voor informatie over het migreren naar een enkele data base of elastische pool raadpleegt [u migratie overzicht: SQL Server naar SQL database](../migration-guides/database/sql-server-to-sql-database-overview.md). Zie [migreren naar Azure SQL](../migration-guides/index.yml)voor migratie-informatie over het migreren van andere platforms en richt lijnen over hulpprogram ma's en opties.

> [!NOTE]
> Als u snel een Azure SQL Managed instance wilt starten en proberen, kunt u in plaats van deze pagina naar de [Snelstartgids-gids](quickstart-content-reference-guide.md) gaan.

Op hoog niveau ziet het database migratie proces er als volgt uit:

![Migratieproces](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Bepaal de compatibiliteit met SQL Managed instance](#assess-sql-managed-instance-compatibility) waarbij u er zeker van moet zijn dat er geen blokkerende problemen zijn die uw migraties kunnen voor komen.
  
  Deze stap omvat ook het maken van een [basis lijn](#create-a-performance-baseline) om het resource gebruik in uw bron SQL Server exemplaar te bepalen. Deze stap is nodig als u een goed geomvangte beheerde instantie wilt implementeren en wilt controleren of de prestaties na de migratie niet worden beïnvloed.
- [Kies connectiviteits opties voor apps](connect-application-instance.md).
- [Implementeer naar een optimale beheerde instantie](#deploy-to-an-optimally-sized-managed-instance) waarin u technische kenmerken (aantal vCores, hoeveelheid geheugen) en de prestatie tier (Bedrijfskritiek, algemeen) van uw beheerde instantie kiest.
- [Selecteer migratie methode en migreer](#select-a-migration-method-and-migrate) waar u uw data bases migreert met behulp van offline migratie (systeem eigen back-up maken/terugzetten, data base importeren/exporteren) of online migratie (Azure Data Migration service, transactionele replicatie).
- Controleer [toepassingen](#monitor-applications) om er zeker van te zijn dat u de prestaties verwacht.

> [!NOTE]
> Als u een afzonderlijke Data Base wilt migreren naar een enkele data base of een elastische pool, raadpleegt [u een SQL Server-Data Base migreren naar Azure SQL database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Compatibiliteit met SQL Managed instance evalueren

Bepaal eerst of SQL Managed instance compatibel is met de database vereisten van uw toepassing. SQL Managed instance is ontworpen om eenvoudig lift-en Shift-migratie te bieden voor de meeste bestaande toepassingen die gebruikmaken van SQL Server. Het kan echter ook voor komen dat u functies of mogelijkheden nodig hebt die nog niet worden ondersteund en dat de kosten voor het implementeren van een tijdelijke oplossing te hoog zijn.

Gebruik [Data Migration Assistant](/sql/dma/dma-overview) om mogelijke compatibiliteits problemen te detecteren die invloed hebben op de database functionaliteit op Azure SQL database. Als er een aantal problemen met de blok kering zijn gerapporteerd, moet u mogelijk een alternatieve optie overwegen, zoals [SQL Server op de Azure-VM](https://azure.microsoft.com/services/virtual-machines/sql-server/). Hier volgen enkele voorbeelden:

- Als u rechtstreeks toegang tot het besturings systeem of het bestands systeem nodig hebt, bijvoorbeeld voor het installeren van derden of aangepaste agents op dezelfde virtuele machine met SQL Server.
- Als u strikte afhankelijkheid hebt voor functies die nog niet worden ondersteund, zoals FileStream/bestands tabel, poly base en trans acties met meerdere exemplaren.
- Als u absoluut een specifieke versie van SQL Server moet blijven (2012, bijvoorbeeld).
- Als uw reken vereisten veel lager zijn dan aanbiedingen voor beheerde instanties (een vCore, bijvoorbeeld), en de consolidatie van de data base is geen acceptabele optie.

Als u alle geïdentificeerde migratie blokkeringen hebt opgelost en u doorgaat met de migratie naar SQL Managed instance, moet u er rekening mee houden dat sommige wijzigingen van invloed kunnen zijn op de prestaties van uw workload:

- Een verplicht volledig herstel model en een regel matig geautomatiseerd back-upschema kunnen invloed hebben op de prestaties van uw werk belasting of onderhouds-en ETL-acties als u regel matig eenvoudig of bulksgewijs geregistreerd model hebt gebruikt of back-ups op aanvraag hebt gestopt.
- Verschillende configuraties op server-of database niveau, zoals traceer vlaggen of compatibiliteits niveaus.
- Nieuwe functies die u gebruikt, zoals TDE (transparent data base Encryption) of groepen voor automatische failover, kunnen invloed hebben op CPU-en IO-gebruik.

SQL Managed instance garandeert een Beschik baarheid van 99,99%, zelfs in kritieke scenario's, waardoor overhead die door deze functies wordt veroorzaakt, niet kan worden uitgeschakeld. Zie [de hoofd oorzaken van SQL Server en Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)voor meer informatie.

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

Zie [prestaties optimaliseren met in-Memory technologieën in Azure SQL database en Azure SQL Managed instance](../in-memory-oltp-overview.md) (Engelstalig) voor meer informatie over de technologieën in het geheugen.

### <a name="create-a-performance-baseline"></a>Een basis lijn voor prestaties maken

Als u de prestaties van uw werk belasting op een beheerd exemplaar wilt vergelijken met de oorspronkelijke werk belasting die wordt uitgevoerd op SQL Server, moet u een prestatie basislijn maken die wordt gebruikt voor de vergelijking.

De basis lijn voor prestaties is een set para meters, zoals gemiddeld/Maxi maal CPU-gebruik, gemiddelde/maximale schijf-i/o-latentie, door Voer, IOPS, gemiddelde/Max. levens duur van de pagina en het gemiddelde van de maximale grootte van tempdb. Als u na de migratie vergelijk bare of nog betere para meters wilt hebben, is het belang rijk om de basislijn waarden voor deze para meters te meten en vast te leggen. Naast de systeem parameters moet u een set representatieve query's of de belangrijkste query's in uw workload selecteren en de minimale/gemiddelde/maximale duur en het CPU-gebruik voor de geselecteerde query's meten. Met deze waarden kunt u de prestaties van de werk belasting die wordt uitgevoerd op het beheerde exemplaar vergelijken met de oorspronkelijke waarden in uw bron SQL Server exemplaar.

Enkele van de para meters die u moet meten op uw SQL Server-exemplaar zijn:

- [Controleer het CPU-gebruik op uw SQL Server-exemplaar](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) en noteer het gemiddelde en maximum CPU-gebruik.
- [Controleer het geheugen gebruik op uw SQL Server-exemplaar](/sql/relational-databases/performance-monitor/monitor-memory-usage) en bepaal de hoeveelheid geheugen die wordt gebruikt door verschillende onderdelen, zoals de buffer groep, de plannings cache, de column-Store-groep, [in-Memory OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage), enzovoort. Bovendien moet u de gemiddelde en piek waarden voor het prestatie meter item pagina-levens verwachting geheugen vinden.
- Controleer het gebruik van de schijf-i/o op het bron SQL Server exemplaar met behulp van [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) weergave of [prestatie meter items](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Controleer de werk belasting-en query prestaties of uw SQL Server-exemplaar door dynamische beheer weergaven of query Store te controleren als u migreert van een SQL Server 2016 + versie. Bepaal de gemiddelde duur en het CPU-gebruik van de belangrijkste query's in uw workload om ze te vergelijken met de query's die worden uitgevoerd op het beheerde exemplaar.

> [!Note]
> Als u een probleem ondervindt met uw werk belasting op SQL Server zoals een hoog CPU-gebruik, een constante geheugen druk of TempDB-of parameterisering-problemen, moet u deze proberen op te lossen in uw bron SQL Server-exemplaar voordat u de basis lijn en migratie uitvoert. Het migreren van bekende problemen naar een nieuw systeem kan leiden tot onverwachte resultaten en een vergelijking van de prestaties ongeldig maken.

Als resultaat van deze activiteit moet u de gemiddelde en piek waarden voor CPU, geheugen en i/o-gebruik op uw bron systeem hebben gedocumenteerd, evenals de gemiddelde en maximale duur en het CPU-gebruik van de dominante en de meest kritieke query's in uw werk belasting. U moet deze waarden later gebruiken om de prestaties van uw werk belasting op een beheerd exemplaar te vergelijken met de basislijn prestaties van de werk belasting op het bron SQL Server exemplaar.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementeren naar een optimale beheerde instantie

Een door SQL beheerd exemplaar is afgestemd op on-premises workloads die worden gepland om naar de cloud te gaan. Er wordt een [Nieuw aankoop model](../database/service-tiers-vcore.md) geïntroduceerd dat meer flexibiliteit biedt bij het selecteren van het juiste niveau van resources voor uw workloads. In de on-premises wereld bent u waarschijnlijk gewend om de grootte van deze werk belastingen te wijzigen met behulp van fysieke kernen en i/o-band breedte. Het inkoop model voor een beheerd exemplaar is gebaseerd op virtuele kernen of ' vCores ', met extra opslag en IO afzonderlijk beschikbaar. Het vCore-model is een eenvoudigere manier om inzicht te krijgen in uw reken vereisten in de Cloud en wat u nu on-premises gebruikt. Met dit nieuwe model kunt u de juiste grootte van uw doel omgeving in de Cloud. Enkele algemene richt lijnen die u kunnen helpen bij het kiezen van de juiste service-laag en-kenmerken, worden hier beschreven:

- U kunt op basis van het CPU-gebruik van de basis lijn een beheerd exemplaar inrichten dat overeenkomt met het aantal kernen dat u gebruikt op SQL Server. in dat geval moeten de CPU-kenmerken mogelijk worden geschaald om te voldoen aan [de VM-kenmerken waar het beheerde exemplaar is geïnstalleerd](resource-limits.md#hardware-generation-characteristics).
- Kies op basis van het geheugen gebruik van de basis lijn [de servicelaag die overeenkomt met het geheugen](resource-limits.md#hardware-generation-characteristics). De hoeveelheid geheugen kan niet rechtstreeks worden gekozen. Daarom moet u het beheerde exemplaar selecteren met de hoeveelheid vCores die overeenkomt met het geheugen (bijvoorbeeld 5,1 GB/vCore in GEN5).
- Op basis van de IO-latentie basis lijn van het subsysteem bestand, kiest u tussen de Algemeen (latentie groter dan 5 MS) en Bedrijfskritiek (latentie van minder dan 3 MS)-service lagen.
- Op basis van de basislijn door Voer, wordt de grootte van de gegevens of logboek bestanden vooraf toegewezen om de verwachte IO-prestaties te verkrijgen.

U kunt reken-en opslag Resources kiezen tijdens de implementatie en deze later wijzigen zonder dat er downtime voor uw toepassing wordt gebruikt met behulp van de [Azure Portal](../database/scale-resources.md):

![Grootte van beheerd exemplaar](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor meer informatie over het maken van de VNet-infra structuur en een beheerd exemplaar.

> [!IMPORTANT]
> Het is belang rijk om uw doel-VNet en-subnet in overeenstemming te blijven met de [vnet-vereisten voor beheerde exemplaren](connectivity-architecture-overview.md#network-requirements). Eventuele incompatibiliteiten kunnen verhinderen dat u nieuwe instanties maakt of die u al hebt gemaakt. Meer informatie over [het maken van nieuwe en het](virtual-network-subnet-create-arm-template.md) [configureren van bestaande](vnet-existing-add-subnet.md) netwerken.

## <a name="select-a-migration-method-and-migrate"></a>Een migratie methode selecteren en migreren

SQL Managed instance streeft naar gebruikers scenario's met massale data base-migratie van on-premises of Azure VM-database implementaties. Ze zijn de optimale keuze wanneer u de back-end van de toepassingen die regel matig gebruikmaken van het exemplaar niveau en/of de functionaliteit voor meerdere data bases, wilt opheffen en verplaatsen. Als dit het geval is, kunt u een volledige instantie verplaatsen naar een overeenkomende omgeving in azure zonder dat u uw toepassingen opnieuw moet ontwerpen.

Als u SQL-exemplaren wilt verplaatsen, moet u zorgvuldig plannen:

- De migratie van alle data bases die moeten worden Co (die op hetzelfde exemplaar worden uitgevoerd).
- De migratie van objecten op exemplaar niveau waarvan uw toepassing afhankelijk is, zoals aanmeldingen, referenties, SQL-Agent taken en-Opera tors en triggers op server niveau.

SQL Managed instance is een beheerde service waarmee u enkele van de reguliere DBA-activiteiten kunt delegeren aan het platform wanneer ze zijn ingebouwd. Daarom hoeven bepaalde gegevens op exemplaar niveau niet te worden gemigreerd, zoals onderhouds taken voor reguliere back-ups of AlwaysOn-configuratie, omdat [hoge Beschik baarheid](../database/high-availability-sla.md) is ingebouwd.

SQL Managed instance ondersteunt de volgende opties voor database migratie (momenteel zijn dit de enige migratie methoden die worden ondersteund):

- Azure Database Migration Service-migratie met bijna nul uitval tijd.
- Systeem eigen `RESTORE DATABASE FROM URL` -maakt gebruik van systeem eigen back-ups van SQL Server en vergt enige downtime.

### <a name="azure-database-migration-service"></a>Azure Database Migration-service

[Azure database Migration service](../../dms/dms-overview.md) is een volledig beheerde service die is ontworpen om naadloze migratie van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn om bestaande data bases van derden en SQL Server naar Azure te verplaatsen. Implementatie-opties in open bare preview omvatten data bases in Azure SQL Database en SQL Server data bases in een virtuele machine van Azure. Database Migration Service is de aanbevolen migratie methode voor uw bedrijfs werkbelastingen.

Als u SQL Server Integration Services (SSIS) op SQL Server on-premises gebruikt, biedt Database Migration Service nog geen ondersteuning voor het migreren van de SSIS-catalogus (SSISDB) waarin SSIS-pakketten worden opgeslagen, maar u kunt wel Azure-SSIS Integration Runtime (IR) inrichten in Azure Data Factory, waardoor er een nieuwe SSISDB wordt gemaakt in een beheerd exemplaar, zodat u uw pakketten opnieuw kunt implementeren. Zie [Azure-SSIS IR maken in azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Zie [uw on-premises data base migreren naar een beheerd exemplaar met behulp van database Migration service](../../dms/tutorial-sql-server-to-managed-instance.md)voor meer informatie over dit scenario en de configuratie stappen voor database Migration service.  

### <a name="native-restore-from-url"></a>Systeemeigen HERSTEL via URL

Het herstellen van systeem eigen back-ups (. bak-bestanden) die is gemaakt [Azure Storage](https://azure.microsoft.com/services/storage/)op basis van een SQL Server-exemplaar, is een van de belangrijkste mogelijkheden van SQL Managed instance die snelle en eenvoudige database migratie mogelijk maakt.

In het volgende diagram ziet u een overzicht van het proces:

![Diagram toont SQL Server met een pijl met de naam BACKUP/upload naar URL stromen naar Azure Storage en een tweede pijl met het label herstellen van URL die van Azure Storage naar een beheerd exemplaar van SQL wordt genoemd.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

De volgende tabel bevat meer informatie over de methoden die u kunt gebruiken, afhankelijk van de bron SQL Server versie die u gebruikt:

|Stap|SQL-engine en-versie|Back-up/herstel methode|
|---|---|---|
|Back-up naar Azure Storage plaatsen|Voorafgaand aan 2012 SP1 CU2|Upload het. bak-bestand rechtstreeks naar Azure Storage|
||2012 SP1 CU2-2016|Directe back-up met afgeschaft [met referentie](/sql/t-sql/statements/restore-statements-transact-sql) syntaxis|
||2016 en hoger|Directe back-ups met behulp [van SAS-referenties](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Herstellen van Azure Storage naar een beheerd exemplaar|[HERSTELLEN vanaf URL met SAS-referentie](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Wanneer u een door [transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) beveiligde data base migreert naar een beheerd exemplaar met behulp van de systeem eigen terugzet optie, moet het bijbehorende certificaat van de on-premises of Azure VM-SQL Server worden gemigreerd voordat de data base wordt hersteld. Zie [Een TDE-certificaat migreren naar een beheerd exemplaar](tde-certificate-migrate.md) voor gedetailleerde stappen.
> - Het herstellen van systeem databases wordt niet ondersteund. Als u objecten op exemplaar niveau wilt migreren (opgeslagen in Master-of msdb-data bases), raden we u aan ze uit te voeren en T-SQL-scripts op het doel exemplaar uitvoeren.

Zie [herstellen van een back-up naar een beheerd exemplaar](restore-sample-database-quickstart.md)voor een Snelstartgids waarin wordt getoond hoe u een back-up van een Data Base herstelt naar een beheerd exemplaar met behulp van een SAS-referentie.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Toepassingen bewaken

Wanneer u de migratie naar een beheerd exemplaar hebt voltooid, moet u het toepassings gedrag en de prestaties van uw werk belasting volgen. Dit proces omvat de volgende activiteiten:

- [Vergelijk de prestaties van de werk belasting die wordt uitgevoerd op het beheerde exemplaar](#compare-performance-with-the-baseline) met de [prestatie basislijn die u hebt gemaakt op het bron SQL Server exemplaar](#create-a-performance-baseline).
- [Bewaak de prestaties van uw werk belasting](#monitor-performance) voortdurend om mogelijke problemen en verbeteringen te identificeren.

### <a name="compare-performance-with-the-baseline"></a>Prestaties vergelijken met de basis lijn

De eerste activiteit die u onmiddellijk na een geslaagde migratie moet uitvoeren, is door de prestaties van de werk belasting te vergelijken met de prestaties van de werk belasting van de basis lijn. Het doel van deze activiteit is om te bevestigen dat de prestaties van de werk belasting op uw beheerde exemplaar voldoen aan uw behoeften.

Database migratie naar een beheerd exemplaar houdt de data base-instellingen en het oorspronkelijke compatibiliteits niveau in de meeste gevallen bij. De oorspronkelijke instellingen blijven waar mogelijk bewaard om het risico van bepaalde prestatie verminderingen ten opzichte van uw bron SQL Server exemplaar te verminderen. Als het compatibiliteits niveau van een gebruikers database 100 of hoger was vóór de migratie, blijft deze ongewijzigd na de migratie. Als het compatibiliteits niveau van een gebruikers database 90 was vóór de migratie, in de bijgewerkte data base, wordt het compatibiliteits niveau ingesteld op 100. Dit is het laagste ondersteunde compatibiliteits niveau in een beheerd exemplaar. Het compatibiliteits niveau van de systeem databases is 140. Omdat de migratie naar een beheerd exemplaar daad werkelijk wordt gemigreerd naar de nieuwste versie van de SQL Server data base-engine, moet u er rekening mee houden dat u de prestaties van uw workload opnieuw moet testen om problemen met verrassende te voor komen.

Zorg ervoor dat u de volgende activiteiten hebt voltooid voor een vereiste:

- Uw instellingen op het beheerde exemplaar uitlijnen met de instellingen van de bron SQL Server instantie door verschillende instanties, data bases, tempdb-instellingen en configuraties te onderzoeken. Zorg ervoor dat u geen instellingen hebt gewijzigd, zoals compatibiliteits niveaus of versleuteling voordat u de eerste prestatie vergelijking uitvoert. u kunt ook het risico accepteren dat sommige van de nieuwe functies die u hebt ingeschakeld, invloed hebben op sommige query's. Als u de migratie Risico's wilt verminderen, wijzigt u het database compatibiliteits niveau pas nadat de prestaties zijn gecontroleerd.
- Implementeer [opslag best practice richt lijnen voor algemeen](https://techcommunity.microsoft.com), zoals het vooraf toewijzen van de grootte van de bestanden om betere prestaties te krijgen.
- Meer informatie over de [belangrijkste omgevings verschillen die de prestatie verschillen kunnen veroorzaken tussen een beheerd exemplaar en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)en de Risico's identificeren die van invloed kunnen zijn op de prestaties.
- Zorg ervoor dat het ingeschakelde query archief en automatisch afstemmen op uw beheerde exemplaar blijven staan. Met deze functies kunt u de prestaties van de werk belasting meten en automatisch de mogelijke prestatie problemen oplossen. Informatie over het gebruik van query Store als een optimaal hulp programma voor het verkrijgen van informatie over de prestaties van de werk belasting vóór en na de wijziging van het compatibiliteits niveau van de data base, zoals wordt uitgelegd in [stabiliteit van prestaties bewaren tijdens de upgrade naar een nieuwere versie van SQL Server](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Wanneer u de omgeving hebt voor bereid die vergelijkbaar is met uw on-premises omgeving, kunt u beginnen met het uitvoeren van de werk belasting en de prestaties meten. Het meet proces moet dezelfde para meters bevatten die u hebt gemeten [tijdens het maken van de basislijn prestaties van uw werkbelastingscapaciteit op het bron SQL Server exemplaar](#create-a-performance-baseline).
Als gevolg hiervan moet u prestatie parameters vergelijken met de basis lijn en kritieke verschillen identificeren.

> [!NOTE]
> In veel gevallen is het niet mogelijk om exact overeenkomende prestaties te verkrijgen voor het beheerde exemplaar en SQL Server. Azure SQL Managed instance is een SQL Server data base-engine, maar de configuratie van de infra structuur en de hoge Beschik baarheid van een beheerd exemplaar kan leiden tot een aantal verschillen. Het kan voor komen dat sommige query's sneller worden uitgevoerd, terwijl sommige anderen langzamer kunnen zijn. Het doel van het vergelijken is om te controleren of de prestaties van de werk belasting in het beheerde exemplaar overeenkomen met de prestaties op SQL Server (gemiddeld) en om eventuele kritieke query's te identificeren met de prestaties die niet overeenkomen met de oorspronkelijke prestaties.

De resultaten van de prestatie vergelijking kunnen er als volgt uitzien:

- De prestaties van de werk belasting op het beheerde exemplaar zijn uitgelijnd of beter dan de prestaties van de werk belasting op SQL Server. In dit geval hebt u bevestigd dat de migratie is voltooid.
- Het meren deel van de prestatie parameters en de query's in de werk belasting werken prima, met enkele uitzonde ringen met verminderde prestaties. In dit geval moet u de verschillen en hun belang identificeren. Als er sprake is van een aantal belang rijke query's met verminderde prestaties, moet u onderzoeken of de onderliggende SQL-plannen zijn gewijzigd of dat de query's enkele resource limieten hebben bereikt. Dit probleem kan worden veroorzaakt door een aantal hints toe te passen op de essentiële query's (bijvoorbeeld gewijzigde compatibiliteits niveau, verouderde cardinality Estimator) ofwel rechtstreeks hetzij met plan richtlijnen, opnieuw te opbouwen of door statistieken en indexen te maken die van invloed kunnen zijn op de plannen.
- De meeste query's zijn langzamer op een beheerd exemplaar vergeleken met uw bron SQL Server exemplaar. In dit geval kunt u proberen om de hoofd oorzaken van het verschil te identificeren, zoals het [bereiken van een bepaalde resource limiet](resource-limits.md#service-tier-characteristics) , zoals io-limieten, geheugen limiet, limiet voor de logboek frequentie, enzovoort. Als er geen resource limieten zijn die het verschil kunnen veroorzaken, probeert u het compatibiliteits niveau van de data base te wijzigen of data base-instellingen te wijzigen, zoals de schatting van verouderde kardinaliteit en de test opnieuw te starten. Bekijk de aanbevelingen van het beheerde exemplaar of de query Store-weer gaven om de query's te identificeren die de prestaties van teruggedraaide.

> [!IMPORTANT]
> Azure SQL Managed instance heeft een ingebouwde functie voor automatische correctie van plannen die standaard is ingeschakeld. Deze functie zorgt ervoor dat query's die goed werken in het plakken, in de toekomst niet worden gedegradeerd. Zorg ervoor dat deze functie is ingeschakeld en dat u de werk belasting lang genoeg hebt uitgevoerd met de oude instellingen voordat u nieuwe instellingen wijzigt om het beheerde exemplaar in te scha kelen voor meer informatie over de basislijn prestaties en-plannen.

Breng de wijziging van de para meters aan of werk de service lagen bij om de optimale configuratie te convergeren tot u de werk belasting krijgt die aan uw behoeften voldoet.

### <a name="monitor-performance"></a>Prestaties bewaken

SQL Managed instance biedt veel geavanceerde hulpprogram ma's voor het controleren en oplossen van problemen, en u moet ze gebruiken om de prestaties van uw exemplaar te bewaken. Enkele van de para meters die u moet bewaken, zijn:

- Het CPU-gebruik van de instantie om te bepalen of het aantal vCores dat u hebt ingericht, het juiste resultaat is voor uw werk belasting.
- Pagina-Life verwachting op uw beheerde instantie om te bepalen [of u meer geheugen nodig hebt](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Met statistieken `INSTANCE_LOG_GOVERNOR` , zoals of `PAGEIOLATCH` , wordt aangegeven of u opslag-i/o-problemen hebt, met name op de laag algemeen, waar u mogelijk bestanden vooraf moet toewijzen om betere IO-prestaties te krijgen.

## <a name="leverage-advanced-paas-features"></a>Maak gebruik van geavanceerde PaaS-functies

Als u zich op een volledig beheerd platform bevindt en u hebt gecontroleerd of de prestaties van de werk belasting overeenkomen met uw SQL Server de werk belasting, kunt u gebruikmaken van de voor delen die automatisch worden gegeven als onderdeel van de service.

Zelfs als u tijdens de migratie geen wijzigingen aanbrengt in het beheerde exemplaar, is er een hoge kans dat u een aantal van de nieuwe functies inschakelt terwijl u uw exemplaar gaat gebruiken om te profiteren van de nieuwste verbeteringen in de data base-engine. Sommige wijzigingen zijn alleen ingeschakeld wanneer het [database compatibiliteits niveau is gewijzigd](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

U hoeft bijvoorbeeld geen back-ups te maken op een beheerd exemplaar. de service voert automatisch back-ups voor u uit. U hoeft zich geen zorgen meer te maken over het plannen, maken en beheren van back-ups. Met SQL Managed Instance kunt u op elk gewenst moment binnen deze Bewaar periode herstellen met behulp van [PITR (Point in time Recovery)](../database/recovery-using-backups.md#point-in-time-restore). Bovendien hoeft u zich geen zorgen te maken over het instellen van hoge Beschik baarheid, omdat [hoge Beschik baarheid](../database/high-availability-sla.md) is ingebouwd.

U kunt de beveiliging versterken door [Azure Active Directory verificatie](../database/security-overview.md), [controle](auditing-configure.md), [detectie van bedreigingen](../database/azure-defender-for-sql.md), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)te gebruiken.

Naast geavanceerde beheer-en beveiligings functies biedt een beheerd exemplaar een aantal geavanceerde hulp middelen waarmee u [uw werk belasting kunt bewaken en afstemmen](../database/monitor-tune-overview.md). Met [Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) kunt u een groot aantal beheerde exemplaren bewaken en de controle van een grote hoeveelheid exemplaren en data bases centraliseren. [Automatisch afstemmen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in beheerde instanties bewaakt voortdurend de prestaties van de uitvoerings statistieken van uw SQL-plan en corrigeert de geïdentificeerde prestatie problemen automatisch.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor meer informatie over Azure SQL Managed instance.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een zelf studie met een back-up van het type terugzetten.
- Zie [uw on-premises data base migreren naar Azure SQL Managed instance met database Migration service voor een](../../dms/tutorial-sql-server-to-managed-instance.md)zelf studie over de migratie met behulp van database Migration service.