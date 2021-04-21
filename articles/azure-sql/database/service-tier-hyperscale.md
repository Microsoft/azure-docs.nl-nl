---
title: Wat is de Hyperscale-servicelaag?
description: In dit artikel wordt de Hyperscale-servicelaag in het aankoopmodel op basis van vCore in Azure SQL Database beschreven en wordt uitgelegd hoe deze verschilt van de Algemeen- en Bedrijfskritiek-servicelagen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773768"
---
# <a name="hyperscale-service-tier"></a>Hyperscale-servicelaag

Azure SQL Database is gebaseerd op SQL Server Database Engine-architectuur die is aangepast voor de cloudomgeving om een beschikbaarheid van 99,99% te garanderen, zelfs in het geval van infrastructuurfouten. Er zijn drie architectuurmodellen die worden gebruikt in Azure SQL Database:

- Algemeen/Standard
- Hyperscale
- Bedrijfskritiek/Premium

De Hyperscale-servicelaag in Azure SQL Database is de nieuwste servicelaag in het aankoopmodel op basis van vCore. Deze servicelaag is een uiterst schaalbare opslag- en rekenprestatielaag die gebruik maakt van de Azure-architectuur om de opslag- en rekenresources uit te schalen voor een Azure SQL Database die aanzienlijk hoger is dan de limieten die beschikbaar zijn voor de Algemeen- en Bedrijfskritiek-servicelagen.

> [!NOTE]
>
> - Zie Servicelagen Algemeen Bedrijfskritiek servicelagen voor meer informatie over de Algemeen- en [](service-tier-general-purpose.md) Bedrijfskritiek-servicelagen [](service-tier-business-critical.md) in het aankoopmodel op basis van vCore. Zie aankoopmodellen en resources voor een vergelijking van het aankoopmodel op basis van vCore met het aankoopmodel op basis van DTU Azure SQL Database [aankoopmodellen en -resources.](purchasing-models.md)
> - De Hyperscale-servicelaag is momenteel alleen beschikbaar voor Azure SQL Database en niet Azure SQL Managed Instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Wat zijn de mogelijkheden van Hyperscale?

De Hyperscale-servicelaag in Azure SQL Database biedt de volgende extra mogelijkheden:

- Ondersteuning voor maximaal 100 TB aan databasegrootte
- Bijna onmiddellijk back-ups van databases (op basis van momentopnamen van bestanden die zijn opgeslagen in Azure Blob Storage), ongeacht de grootte zonder I/O-impact op rekenbronnen  
- Snelle databaseherstelbewerkingen (op basis van momentopnamen van bestanden) in minuten in plaats van uren of dagen (geen gegevensbewerking)
- Hogere algemene prestaties vanwege hogere logboekdoorvoer en snellere doorvoertijden voor transacties, ongeacht gegevensvolumes
- Snel uitschalen: u kunt een of meer alleen-lezen knooppunten inrichten voor offloading van uw leesworkload en voor gebruik als hot-stand-by
- Snel omhoog schalen: u kunt uw rekenbronnen in constante tijd omhoog schalen voor zware werkbelastingen wanneer dat nodig is. Vervolgens kunt u de rekenbronnen weer omlaag schalen wanneer dat niet nodig is.

De servicelaag Hyperscale verwijdert veel van de praktische limieten die traditioneel in clouddatabases worden gezien. Waar de meeste andere databases worden beperkt door de resources die beschikbaar zijn in één knooppunt, gelden dergelijke limieten niet voor databases in de Hyperscale-servicelaag. Dankzij de flexibele opslagarchitectuur groeit de opslag naar behoefte. Hyperscale-databases worden in feite niet gemaakt met een gedefinieerde maximale grootte. Een Hyperscale-database groeit naar behoefte en u wordt alleen gefactureerd voor de capaciteit die u gebruikt. Voor leesintensieve werkbelastingen biedt de Hyperscale-servicelaag snelle uitschaaling door zo nodig extra leesreplica's in terichten voor het offloaden van leesworkloads.

Daarnaast is de tijd die nodig is om databaseback-ups te maken of omhoog of omlaag te schalen niet langer gekoppeld aan de hoeveelheid gegevens in de database. Hyperscale-databases kunnen vrijwel onmiddellijk worden geback-upt. U kunt een database in enkele minuten ook in tientallen terabytes omhoog of omlaag schalen. Met deze mogelijkheid bent u niet meer bezorgd over de eerste configuratieopties die u hebt.

Zie Kenmerken van servicelagen voor meer informatie over de rekengrootten voor de [Hyperscale-servicelaag.](service-tiers-vcore.md#service-tiers)

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wie moet de Hyperscale-servicelaag overwegen?

De Hyperscale-servicelaag is bedoeld voor de meeste zakelijke workloads, omdat deze een grote flexibiliteit en hoge prestaties biedt met onafhankelijk schaalbare reken- en opslagresources. Met de mogelijkheid om de opslag automatisch te schalen tot 100 TB, is het een uitstekende keuze voor klanten die:

- Grote databases on-premises hebben en hun toepassingen willen moderniseren door over te gaan naar de cloud
- Zijn al in de cloud en worden beperkt door de maximale databasegroottebeperkingen van andere servicelagen (1-4 TB)
- Kleinere databases hebben, maar snelle verticale en horizontale schaalbaarheid van rekenkracht, hoge prestaties, directe back-up en snel terugzetten van databases vereisen.

De servicelaag Hyperscale ondersteunt een breed scala aan SQL Server-workloads, van pure OLTP tot pure analyse, maar is voornamelijk geoptimaliseerd voor OLTP- en HTAP-workloads (hybride transactie- en analytische verwerking).

> [!IMPORTANT]
> Elastische pools bieden geen ondersteuning voor de Hyperscale-servicelaag.

## <a name="hyperscale-pricing-model"></a>Prijsmodel voor Hyperscale

De Hyperscale-servicelaag is alleen beschikbaar in [het vCore-model](service-tiers-vcore.md). In lijn met de nieuwe architectuur verschilt het prijsmodel enigszins van Algemeen of Bedrijfskritiek-servicelagen:

- **Compute:**

  De prijs van de Hyperscale-rekeneenheid is per replica. De [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) wordt automatisch toegepast op leesschaalreplica's. We maken standaard een primaire replica en één alleen-lezen replica per Hyperscale-database.  Gebruikers kunnen het totale aantal replica's aanpassen, inclusief de primaire replica van 1-5.

- **Opslag:**

  U hoeft niet de maximale gegevensgrootte op te geven bij het configureren van een Hyperscale-database. In de hyperscale-laag worden kosten in rekening gebracht voor opslag voor uw database op basis van de werkelijke toewijzing. Opslag wordt automatisch toegewezen tussen 40 GB en 100 TB, in stappen van 10 GB. Indien nodig kunnen meerdere gegevensbestanden tegelijk groeien. Een Hyperscale-database wordt gemaakt met een begingrootte van 10 GB en begint elke 10 minuten met 10 GB te groeien totdat deze de grootte van 40 GB bereikt.

Zie Prijzen voor hyperscale voor [meer informatie Azure SQL Database prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architectuur van gedistribueerde functies

In tegenstelling tot traditionele database-engines die alle functies voor gegevensbeheer op één locatie/proces hebben gecentraliseerd (zelfs als gedistribueerde databases in productie tegenwoordig meerdere exemplaren van een monolithische gegevensent engine hebben), scheidt een Hyperscale-database de queryverwerkingsen engine, waarbij de semantiek van verschillende gegevensen engines afwijkt van de onderdelen die langetermijnopslag en duurzaamheid voor de gegevens bieden. Op deze manier kan de opslagcapaciteit naar behoefte soepel worden geschaald (de eerste doel is 100 TB). Alleen-lezen replica's delen dezelfde opslagonderdelen, dus er is geen gegevenskopie vereist om een nieuwe leesbare replica te maken.

In het volgende diagram ziet u de verschillende typen knooppunten in een Hyperscale-database:

![architectuur](./media/service-tier-hyperscale/hyperscale-architecture.png)

Een Hyperscale-database bevat de volgende verschillende typen onderdelen:

### <a name="compute"></a>Compute

Het reken-knooppunt is de plek waar de relationele engine zich bebouwt. Hier vinden taal-, query- en transactieverwerking plaats. Alle gebruikersinteracties met een Hyperscale-database vinden via deze rekenknooppunten plaatsvinden. Rekenknooppunten hebben ssd-caches (in het voorgaande diagram gelabeld als RBPEX - Resilient Buffer Pool Extension) om het aantal netwerkritten te minimaliseren dat nodig is om een pagina met gegevens op te halen. Er is één primair rekenpunt waar alle lees-/schrijfworkloads en transacties worden verwerkt. Er zijn een of meer secundaire rekenknooppunten die fungeren als hot stand-byknooppunten voor failoverdoeleinden, en die ook fungeren als alleen-lezen rekenknooppunten voor het offloaden van leesworkloads (als deze functionaliteit gewenst is).

De database-engine die wordt uitgevoerd op Hyperscale-rekenknooppunten is hetzelfde als in andere Azure SQL Database-servicelagen. Wanneer gebruikers communiceren met de database-engine op Hyperscale-rekenknooppunten, zijn het ondersteunde surface area- en enginegedrag hetzelfde als in andere servicelagen, met uitzondering van [bekende beperkingen.](#known-limitations)

### <a name="page-server"></a>Paginaserver

Paginaservers zijn systemen die een geschaalde opslagen engine vertegenwoordigen.  Elke paginaserver is verantwoordelijk voor een subset van de pagina's in de database.  Nominaal bepaalt elke paginaserver maximaal 128 GB of maximaal 1 TB aan gegevens. Er worden geen gegevens gedeeld op meer dan één paginaserver (buiten paginaserverreplica's die worden bewaard voor redundantie en beschikbaarheid). De taak van een paginaserver is om databasepagina's op aanvraag naar de rekenknooppunten te versturen en de pagina's bij te werken wanneer transacties gegevens bijwerken. Paginaservers worden up-to-date gehouden door logboekrecords uit de logboekservice af te spelen. Paginaservers behouden ook de dekking van caches op basis van SSD om de prestaties te verbeteren. Langetermijnopslag van gegevenspagina's wordt opgeslagen in Azure Storage voor extra betrouwbaarheid.

### <a name="log-service"></a>Logboekservice

De logboekservice accepteert logboekrecords van de primaire rekenreplica, zet ze op in een duurzame cache en zet de logboekrecords door naar de rest van de rekenreplica's (zodat ze hun caches kunnen bijwerken), evenals de relevante paginaserver(s), zodat de gegevens daar kunnen worden bijgewerkt. Op deze manier worden alle gegevenswijzigingen van de primaire rekenreplica via de logboekservice doorgegeven aan alle secundaire rekenreplica's en paginaservers. Ten slotte worden de logboekrecords naar langetermijnopslag in Azure Storage, een vrijwel oneindige opslagopslagplaats. Met dit mechanisme is het niet meer nodig om logboeken regelmatig af te melden. De logboekservice heeft ook lokaal geheugen en SSD-caches om de toegang tot logboekrecords te versnellen.

### <a name="azure-storage"></a>Azure Storage

Azure Storage bevat alle gegevensbestanden in een database. Paginaservers houden gegevensbestanden in Azure Storage up-to-date. Deze opslag wordt gebruikt voor back-updoeleinden en voor replicatie tussen Azure-regio's. Back-ups worden geïmplementeerd met behulp van opslagmomentopnamen van gegevensbestanden. Herstelbewerkingen met behulp van momentopnamen zijn snel, ongeacht de gegevensgrootte. Gegevens kunnen worden hersteld naar elk tijdstip binnen de bewaarperiode van de back-up van de database.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Back-ups zijn gebaseerd op momentopnamen van bestanden en zijn daarom vrijwel onmiddellijk. Opslag- en rekenscheiding maakt het mogelijk om de back-up-/herstelbewerking naar de opslaglaag te pushen om de verwerkingsbelasting van de primaire rekenreplica te verminderen. Als gevolg hiervan heeft databaseback-up geen invloed op de prestaties van het primaire reken knooppunt. Herstel naar een bepaald tijdstip wordt op dezelfde manier uitgevoerd door terug te keren naar momentopnamen van bestanden en is dus geen grootte van de gegevensbewerking. Het herstellen van een Hyperscale-database in dezelfde Azure-regio is een constante bewerking en zelfs databases met meerdere terabytes kunnen in minuten worden hersteld in plaats van uren of dagen. Het maken van nieuwe databases door een bestaande back-up te herstellen, maakt ook gebruik van deze functie: het maken van databasekopieen voor ontwikkelings- of testdoeleinden, zelfs van databases van meerdere terabytes, kan in enkele minuten worden uitgevoerd.

Zie Restore a Hyperscale database to a different region (Een Hyperscale-database herstellen naar een andere regio) voor geo-herstel [van Hyperscale-databases.](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Schaal- en prestatievoordelen

Dankzij de mogelijkheid om snel extra alleen-lezen rekenknooppunten op te zetten/omlaag te zetten, biedt de Hyperscale-architectuur aanzienlijke mogelijkheden voor leesschaal en kan het primaire rekenknooppunt vrij worden voor het verwerken van meer schrijfaanvragen. Bovendien kunnen de rekenknooppunten snel omhoog/omlaag worden geschaald vanwege de architectuur voor gedeelde opslag van de Hyperscale-architectuur.

## <a name="create-a-hyperscale-database"></a>Een Hyperscale-database maken

U kunt een Hyperscale-database maken met [de Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/create-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)of [CLI.](/cli/azure/sql/db#az_sql_db_create) Hyperscale-databases zijn alleen beschikbaar met behulp van het [aankoopmodel op basis van vCore.](service-tiers-vcore.md)

Met de volgende T-SQL-opdracht maakt u een Hyperscale-database. U moet zowel de editie als de servicedoelstelling opgeven in de `CREATE DATABASE` instructie . Raadpleeg de [resourcelimieten voor](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) een lijst met geldige servicedoelstellingen.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Hiermee maakt u een Hyperscale-database op Gen5-hardware met vier kernen.

## <a name="upgrade-existing-database-to-hyperscale"></a>Bestaande database upgraden naar Hyperscale

U kunt uw bestaande databases in Azure SQL Database naar Hyperscale met behulp van [de Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/alter-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)of [CLI.](/cli/azure/sql/db#az_sql_db_update) Op dit moment is dit een migratie in één keer. U kunt databases niet van Hyperscale naar een andere servicelaag verplaatsen, anders dan door gegevens te exporteren en te importeren. Voor proofs of concept (POC's) raden we u aan een kopie van uw productiedatabases te maken en de kopie te migreren naar Hyperscale. Het migreren van een bestaande database in Azure SQL Database naar de Hyperscale-laag is een grootte van de gegevensbewerking.

Met de volgende T-SQL-opdracht wordt een database verplaatst naar de servicelaag Hyperscale. U moet zowel de editie als de servicedoelstelling opgeven in de `ALTER DATABASE` -instructie.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Verbinding maken met een leesschaalreplica van een Hyperscale-database

In Hyperscale-databases bepaalt het argument in de connection string die door de client wordt geleverd, of de verbinding wordt doorgeleid naar de schrijfreplica of naar een secundaire replica met `ApplicationIntent` alleen-lezengegevens. Als de is ingesteld op en de database geen secundaire replica heeft, wordt de verbinding gerouteerd naar de primaire replica en is `ApplicationIntent` `READONLY` de `ReadWrite` standaardinstelling gedrag.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Secundaire Hyperscale-replica's zijn allemaal identiek, met dezelfde serviceniveaudoelstelling als de primaire replica. Als er meer dan één secundaire replica aanwezig is, wordt de workload verdeeld over alle beschikbare secundaire replica's. Elke secundaire replica wordt onafhankelijk bijgewerkt. Verschillende replica's kunnen dus een andere gegevenslatentie hebben ten opzichte van de primaire replica.

## <a name="database-high-availability-in-hyperscale"></a>Hoge beschikbaarheid van databases in Hyperscale

Net als bij alle andere servicelagen garandeert Hyperscale duurzaamheid van gegevens voor vastgelegde transacties, ongeacht de beschikbaarheid van rekenreplica's. De mate van downtime omdat de primaire replica niet beschikbaar is, is afhankelijk van het type failover (gepland versus ongepland) en van de aanwezigheid van ten minste één secundaire replica. Bij een geplande failover (dat wil zeggen een onderhoudsgebeurtenis) maakt het systeem de nieuwe primaire replica voordat een failover wordt geïnitieerd, of gebruikt het een bestaande secundaire replica als failoverdoel. In een niet-geplande failover (dat wil zeggen een hardwarefout op de primaire replica), gebruikt het systeem een secundaire replica als failoverdoel, indien aanwezig, of maakt het een nieuwe primaire replica uit de pool met beschikbare rekencapaciteit. In het laatste geval is de duur van de downtime langer vanwege de extra stappen die nodig zijn om de nieuwe primaire replica te maken.

Zie SLA voor Hyperscale SLA voor [Azure SQL Database.](https://azure.microsoft.com/support/legal/sla/sql-database/)

## <a name="disaster-recovery-for-hyperscale-databases"></a>Herstel na noodherstel voor Hyperscale-databases

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Een Hyperscale-database herstellen naar een andere regio

Als u een Hyperscale-database in Azure SQL Database wilt herstellen naar een andere regio dan de regio waarin deze momenteel wordt gehost, als onderdeel van een noodherstelbewerking, een noodherstelbewerking, een oefening, een herlocatie of een andere reden, is de primaire methode om een geo-herstel van de database uit te voeren. Dit omvat exact dezelfde stappen als wat u zou gebruiken om een andere database in SQL Database naar een andere regio te herstellen:

1. Maak een [server](logical-servers.md) in de doelregio als u daar nog geen geschikte server hebt.  Deze server moet eigendom zijn van hetzelfde abonnement als de oorspronkelijke (bron)server.
2. Volg de instructies in het [onderwerp geo-herstel](./recovery-using-backups.md#geo-restore) van de pagina over het herstellen van een database in Azure SQL Database automatische back-ups.

> [!NOTE]
> Omdat de bron en het doel zich in afzonderlijke regio's hebben, kan de database geen momentopnameopslag delen met de brondatabase, zoals in niet-geo-herstel, die snel worden voltooid, ongeacht de grootte van de database. In het geval van een geo-herstel van een Hyperscale-database is dit een gegevensbewerking met een grootte, zelfs als het doel zich in de gekoppelde regio van de geo-gerepliceerde opslag. Daarom duurt geo-herstel in verhouding tot de grootte van de database die wordt hersteld. Als het doel zich in de gekoppelde regio, wordt de gegevensoverdracht binnen een regio, die aanzienlijk sneller dan een gegevensoverdracht tussen regio's, maar het is nog steeds een grootte van de gegevens bewerking.

## <a name="available-regions"></a><a name=regions></a>Beschikbare regio's

De Azure SQL Database Hyperscale-laag is beschikbaar in alle regio's, maar standaard ingeschakeld in de onderstaande regio's. Als u een Hyperscale-database wilt maken in een regio waarin Hyperscale niet standaard is ingeschakeld, kunt u een aanvraag voor onboarding verzenden via Azure Portal. Zie Verhogingen van [quotum aanvragen voor Azure SQL Database](quota-increase-request.md) voor instructies. Gebruik de volgende richtlijnen wanneer u uw aanvraag indient:

- Gebruik het [toegangstype](quota-increase-request.md#region) Regio SQL Database quotumtype.
- Voeg in de beschrijving de reken-SKU/totale kernen toe, inclusief leesbare replica's, en geef aan dat u Hyperscale-capaciteit aanvraagt.
- Geef ook een projectie op van de totale grootte van alle databases gedurende een periode in TB.

Ingeschakelde regio's:
- Australië - oost
- Australië - zuidoost
- Australië - centraal
- Brazilië - zuid
- Canada - midden
- Canada - oost
- Central US
- China - oost 2
- China - noord 2
- Azië - oost
- VS - oost
- VS - oost 2
- Frankrijk - centraal
- Duitsland - west-centraal
- Japan - oost
- Japan - west
- Korea - centraal
- Korea - zuid
- VS - noord-centraal
- Europa - noord
- Noorwegen - oost
- Noorwegen - west
- Zuid-Afrika - noord
- VS - zuid-centraal
- Azië - zuidoost
- Zwitserland - west
- Verenigd Koninkrijk Zuid
- Verenigd Koninkrijk West
- US DoD Central
- US DoD East
- Us Govt Arizona
- US Govt Texas
- VS - west-centraal
- Europa -west
- VS - west
- VS - west 2

## <a name="known-limitations"></a>Bekende beperkingen

Dit zijn de huidige beperkingen voor de Hyperscale-servicelaag vanaf ga.  Er wordt actief aan gewerkt om zoveel mogelijk van deze beperkingen te verwijderen.

| Probleem | Beschrijving |
| :---- | :--------- |
| In het deelvenster Back-ups beheren voor een server worden geen Hyperscale-databases weergegeven. Deze worden gefilterd vanuit de weergave.  | Hyperscale heeft een afzonderlijke methode voor het beheren van back-ups, dus de Long-Term-instellingen voor retentie en retentie van back-ups naar een bepaald tijdstip zijn niet van toepassing. Hyperscale-databases worden daarom niet weergegeven in het deelvenster Back-up beheren.<br><br>Voor databases die vanuit andere Azure SQL Database-servicelagen naar Hyperscale zijn gemigreerd, [](automated-backups-overview.md#backup-retention) worden back-ups vóór de migratie bewaard voor de duur van de bewaarperiode voor back-ups van de brondatabase. Deze back-ups kunnen worden gebruikt om [de](recovery-using-backups.md#programmatic-recovery-using-automated-backups) brondatabase te herstellen naar een bepaald tijdstip vóór de migratie.|
| Terugzetten naar eerder tijdstip | Een niet-Hyperscale-database kan niet worden hersteld als een Hyperscale-database en een Hyperscale-database kan niet worden hersteld als een niet-Hyperscale-database. Voor een niet-Hyperscale-database die is gemigreerd naar Hyperscale door de servicelaag te wijzigen, herstelt u naar een bepaald tijdstip vóór de migratie en wordt binnen de bewaarperiode voor back-ups van de database [programmatisch ondersteund.](recovery-using-backups.md#programmatic-recovery-using-automated-backups) De herstelde database is niet-Hyperscale. |
| Wanneer u Azure SQL Database servicelaag in Hyperscale, mislukt de bewerking als de database gegevensbestanden bevat die groter zijn dan 1 TB | In sommige gevallen is het mogelijk om [](file-space-manage.md#shrinking-data-files) dit probleem op te lossen door de grote bestanden te verkleinen tot minder dan 1 TB voordat u probeert de servicelaag te wijzigen in Hyperscale. Gebruik de volgende query om de huidige grootte van databasebestanden te bepalen. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | Azure SQL Managed Instance wordt momenteel niet ondersteund met Hyperscale-databases. |
| Elastische pools |  Elastische pools worden momenteel niet ondersteund met Hyperscale.|
| Migratie naar Hyperscale is momenteel een een way-bewerking | Zodra een database is gemigreerd naar Hyperscale, kan deze niet rechtstreeks worden gemigreerd naar een niet-Hyperscale-servicelaag. Op dit moment is de enige manier om een database te migreren van Hyperscale naar niet-Hyperscale, te exporteren/importeren met behulp van een BACPAC-bestand of andere technologieën voor gegevensver movement (bulksgewijs kopiëren, Azure Data Factory, Azure Databricks, SSIS, enzovoort) Bacpac exporteren/importeren vanuit Azure Portal, vanuit PowerShell met [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) of [New-AzSqlDatabaseImport,](/powershell/module/az.sql/new-azsqldatabaseimport)vanuit Azure CLI met [az sql db export](/cli/azure/sql/db#az_sql_db_export) en az sql db [import](/cli/azure/sql/db#az_sql_db_import)en vanuit [REST API](/rest/api/sql/) wordt niet ondersteund. Bacpac importeren/exporteren voor kleinere Hyperscale-databases (maximaal 200 GB) wordt ondersteund met SSMS en [SqlPackage](/sql/tools/sqlpackage) versie 18.4 en hoger. Voor grotere databases kan het exporteren/importeren van BACPAC lang duren en kan het om verschillende redenen mislukken.|
| Migratie van databases met In-Memory OLTP-objecten | Hyperscale ondersteunt een subset van In-Memory OLTP-objecten, waaronder tabeltypen die zijn geoptimaliseerd voor geheugen, tabelvariabelen en systeemeigen gecompileerde modules. Wanneer er echter een soort OLTPIn-Memory objecten aanwezig zijn in de database die wordt gemigreerd, wordt migratie van Premium- en Bedrijfskritiek-servicelagen naar Hyperscale niet ondersteund. Als u een dergelijke database wilt migreren naar Hyperscale, moeten In-Memory OLTP-objecten en hun afhankelijkheden worden verwijderd. Nadat de database is gemigreerd, kunnen deze objecten opnieuw worden gemaakt. Duurzame en niet-duurzame, voor geheugen geoptimaliseerde tabellen worden momenteel niet ondersteund in Hyperscale en moeten worden gewijzigd in schijftabellen.|
| Geo-replicatie  | U kunt geo-replicatie nog niet configureren voor Azure SQL Database Hyperscale. |
| Database kopiëren | Het kopiëren van de database op Hyperscale is nu beschikbaar als openbare preview. |
| Intelligente databasefuncties | Met uitzondering van de optie Plan forceer, worden alle andere opties voor Automatisch afstemmen nog niet ondersteund op Hyperscale: opties lijken mogelijk ingeschakeld te zijn, maar er worden geen aanbevelingen of acties gedaan. |
| Inzicht in queryprestaties | Query Performance Insights wordt momenteel niet ondersteund voor Hyperscale-databases. |
| Database verkleinen | DBCC SHRINKDATABASE of DBCC SHRINKFILE wordt momenteel niet ondersteund voor Hyperscale-databases. |
| Database-integriteitscontrole | DBCC CHECKDB wordt momenteel niet ondersteund voor Hyperscale-databases. DBCC CHECKFILEGROUP en DBCC CHECKTABLE kunnen worden gebruikt als tijdelijke oplossing. Zie [Gegevensintegriteit in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) voor meer informatie over gegevensintegriteitsbeheer in Azure SQL Database. |

## <a name="next-steps"></a>Volgende stappen

- Zie Veelgestelde vragen over Hyperscale voor [veelgestelde vragen over Hyperscale.](service-tier-hyperscale-frequently-asked-questions-faq.md)
- Zie Servicelagen voor meer informatie [over servicelagen](purchasing-models.md)
- Zie [Overzicht van resourcelimieten op een server](resource-limits-logical-server.md) voor informatie over limieten op server- en abonnementsniveau.
- Zie limieten voor aankoopmodels voor één database Azure SQL Database aankoopmodellimieten op basis van vCore voor [één database.](resource-limits-vcore-single-databases.md)
- Zie [Veelvoorkomende SQL-functies](features-comparison.md) voor een lijst met functies en vergelijkingen.
