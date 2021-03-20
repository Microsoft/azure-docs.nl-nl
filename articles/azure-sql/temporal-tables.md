---
title: Aan de slag met tijdelijke tabellen
description: Leer hoe u met tijdelijke tabellen in Azure SQL Database en Azure SQL Managed instance aan de slag kunt gaan.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618655"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Aan de slag met tijdelijke tabellen in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Tijdelijke tabellen zijn een Programmeer bare functie van Azure SQL Database en Azure SQL Managed instance waarmee u de volledige geschiedenis van wijzigingen in uw gegevens kunt bijhouden en analyseren, zonder dat aangepaste code ring nodig is. In tijdelijke tabellen blijven gegevens nauw gerelateerd aan de tijd context, zodat opgeslagen feiten alleen kunnen worden geïnterpreteerd als geldig in de specifieke periode. Deze eigenschap van tijdelijke tabellen maakt een efficiënte analyse op basis van tijd mogelijk en haalt inzicht op uit gegevens ontwikkeling.

## <a name="temporal-scenario"></a>Tijdelijk scenario

In dit artikel worden de stappen beschreven voor het gebruik van tijdelijke tabellen in een toepassings scenario. Stel dat u gebruikers activiteiten wilt bijhouden op een nieuwe website die geheel of op een bestaande website wordt ontwikkeld die u wilt uitbreiden met de analyse van gebruikers activiteit. In dit vereenvoudigde voor beeld gaan we ervan uit dat het aantal bezochte webpagina's gedurende een bepaalde periode een indicator is die moet worden vastgelegd en bewaakt in de website database die wordt gehost op Azure SQL Database of Azure SQL Managed instance. Het doel van de historische analyse van gebruikers activiteit is het ophalen van invoer voor het opnieuw ontwerpen van de website en de betere ervaring voor de bezoekers.

Het database model voor dit scenario is zeer eenvoudig: de metrische gegevens van de activiteit van de gebruiker worden weer gegeven met één veld met een geheel getal, **PageVisited** en wordt vastgelegd samen met basis informatie over het gebruikers profiel. Voor op tijd gebaseerde analyse houdt u bovendien een reeks rijen voor elke gebruiker, waarbij elke rij het aantal pagina's vertegenwoordigt dat een bepaalde gebruiker binnen een bepaalde periode heeft bezocht.

![Schema](./media/temporal-tables/AzureTemporal1.png)

Gelukkig hoeft u geen inspanning in uw app te zetten om deze activiteiten gegevens te behouden. Met tijdelijke tabellen wordt dit proces geautomatiseerd, zodat u volledig flexibel bent tijdens het ontwerpen van websites en meer tijd kunt best Eden aan de gegevens analyse zelf. Het enige wat u moet doen, is om ervoor te zorgen dat de **WebSiteInfo** -tabel is geconfigureerd als [tijdelijke systeem versie](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table). De exacte stappen voor het gebruik van tijdelijke tabellen in dit scenario worden hieronder beschreven.

## <a name="step-1-configure-tables-as-temporal"></a>Stap 1: tabellen configureren als tijdelijke

Afhankelijk van of u nu een nieuwe ontwikkeling start of een bestaande toepassing bijwerkt, maakt u tijdelijke tabellen of wijzigt u bestaande door tijdelijke kenmerken toe te voegen. In het algemeen geldt dat uw scenario een combi natie van deze twee opties kan zijn. Voer deze actie uit met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)of een ander hulp programma voor het ontwikkelen van Transact-SQL.

> [!IMPORTANT]
> Het is raadzaam om altijd de nieuwste versie van Management Studio te gebruiken om gesynchroniseerd te blijven met updates voor Azure SQL Database en Azure SQL Managed instance. [SQL Server Management Studio bijwerken](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Nieuwe tabel maken

Gebruik context menu-item ' New System-Versioned Table ' in SSMS Objectverkenner om de query-editor te openen met een sjabloon script voor de tijdelijke tabel en gebruik vervolgens ' waarden opgeven voor sjabloon parameters ' (CTRL + SHIFT + M) om de sjabloon te vullen:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

Kies in SSDT de sjabloon tijdelijke tabel (systeem versie) wanneer u nieuwe items toevoegt aan het data base-project. Hiermee opent u de ontwerp functie voor tabellen en kunt u eenvoudig de tabel indeling opgeven:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

U kunt ook een tijdelijke tabel maken door de Transact-SQL-instructies rechtstreeks op te geven, zoals wordt weer gegeven in het onderstaande voor beeld. Houd er rekening mee dat de verplichte elementen van elke tijdelijke tabel de periode definitie en de SYSTEM_VERSIONING-component met een verwijzing naar een andere gebruikers tabel zijn waarin historische rij versies worden opgeslagen:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Wanneer u een tijdelijke systeem versie tabel maakt, wordt automatisch de bijbehorende geschiedenis tabel met de standaard configuratie gemaakt. De standaard geschiedenis tabel bevat een geclusterde B-Tree-index voor de periode kolommen (End, start) met pagina compressie ingeschakeld. Deze configuratie is optimaal voor het meren deel van scenario's waarin tijdelijke tabellen worden gebruikt, met name voor het [controleren van gegevens](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit).

In dit geval is het raadzaam om op tijd gebaseerde trend analyses uit te voeren op een langere gegevens geschiedenis en met grotere gegevens sets, zodat de opslag keuze voor de geschiedenis tabel een geclusterde column store-index is. Een geclusterde column Store biedt zeer goede compressie en prestaties voor analytische query's. Tijdelijke tabellen bieden u de flexibiliteit om indexen voor de huidige en tijdelijke tabellen volledig onafhankelijk te configureren.

> [!NOTE]
> Column Store-indexen zijn beschikbaar in de lagen Bedrijfskritiek, Algemeen en Premium en in de Standard-laag, S3 en hoger.

Het volgende script toont hoe de standaard index op de geschiedenis tabel kan worden gewijzigd in de geclusterde column Store:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Tijdelijke tabellen worden weer gegeven in de Objectverkenner met het specifieke pictogram voor een eenvoudigere identificatie, terwijl de geschiedenis tabel wordt weer geven als een onderliggend knoop punt.

![Die kan worden uitgetert](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Bestaande tabel wijzigen in tijdelijke

We gaan naar het alternatieve scenario waarin de WebsiteUserInfo-tabel al bestaat, maar is niet ontworpen om een geschiedenis van wijzigingen te bijhouden. In dit geval kunt u de bestaande tabel gewoon uitbreiden om tijdelijk te worden, zoals wordt weer gegeven in het volgende voor beeld:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Stap 2: uw workload regel matig uitvoeren

Het belangrijkste voor deel van tijdelijke tabellen is dat u uw website niet hoeft te wijzigen of aanpassen op een manier om wijzigingen op te sporen. Nadat de tijdelijke tabellen zijn gemaakt, blijven de eerdere rijkoppen telkens wanneer u wijzigingen op uw gegevens uitvoert transparant persistent.

Als u wilt gebruikmaken van automatische wijzigingen bijhouden voor dit specifieke scenario, kunt u de kolom **PagesVisited** altijd bijwerken telkens wanneer een gebruiker de sessie op de website beëindigt:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Het is belang rijk te weten dat de update query niet de exacte tijd hoeft te kennen wanneer de werkelijke bewerking plaatsvond en hoe historische gegevens behouden blijven voor toekomstige analyse. Beide aspecten worden automatisch afgehandeld door Azure SQL Database en Azure SQL Managed instance. In het volgende diagram ziet u hoe er voor elke update geschiedenis gegevens worden gegenereerd.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Stap 3: historische gegevens analyse uitvoeren

Als tijdelijke systeem versie beheer nu is ingeschakeld, is historische gegevens analyse slechts één query van u. In dit artikel worden enkele voor beelden gegeven van veelvoorkomende analyse scenario's: voor meer informatie kunt u verschillende opties verkennen, die zijn geïntroduceerd in de [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) -component.

Voer de volgende query uit om de Top 10-gebruikers weer te geven die op het aantal bezochte webpagina's per uur geleden worden besteld:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

U kunt deze query eenvoudig aanpassen om de site bezoeken vanaf een dag geleden, een maand geleden of op elk gewenst moment in het verleden te analyseren.

Gebruik het volgende voor beeld om een algemene statistische analyse uit te voeren voor de vorige dag:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Als u wilt zoeken naar activiteiten van een specifieke gebruiker, binnen een bepaalde tijd, gebruikt u de component CONTAINed IN:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafische visualisatie is met name handig voor tijdelijke query's, omdat u de trends en gebruiks patronen op een intuïtieve manier eenvoudig kunt weer geven:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Schema voor veranderende tabellen

Normaal gesp roken moet u het tijdelijke tabel schema wijzigen tijdens het uitvoeren van app-ontwikkeling. Hiervoor moet u normale ALTER TABLE-instructies en Azure SQL Database-of Azure SQL Managed instance gebruiken om wijzigingen in de geschiedenis tabel op de juiste wijze door te geven. Het volgende script toont hoe u extra kenmerk kunt toevoegen voor tracering:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Op dezelfde manier kunt u de kolom definitie wijzigen terwijl uw workload actief is:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Ten slotte kunt u een kolom verwijderen die u niet meer nodig hebt.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

U kunt ook de meest recente [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) gebruiken om het tijdelijke tabel schema te wijzigen terwijl u verbonden bent met de data base (online modus) of als onderdeel van het database project (offline modus).

## <a name="controlling-retention-of-historical-data"></a>De retentie van historische gegevens beheren

Met tijdelijke systeem versie tabellen kan de tabel geschiedenis de omvang van de data base groter dan de reguliere tabellen. Een grote en steeds groeiende geschiedenis tabel kan een probleem vormen als gevolg van zuivere opslag kosten en een prestatie belasting opleggen op tijdelijke query's. Daarom is het ontwikkelen van een Bewaar beleid voor gegevens in de geschiedenis tabel een belang rijk aspect van het plannen en beheren van de levens cyclus van elke tijdelijke tabel. Met Azure SQL Database en Azure SQL Managed instance hebt u de volgende benaderingen voor het beheren van historische gegevens in de tijdelijke tabel:

- [Tabellen partitioneren](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Aangepast opschoon script](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over tijdelijke tabellen [tijdelijke tabellen](/sql/relational-databases/tables/temporal-tables)uitchecken.
- Ga naar Channel 9 om het [verhaal van de tijdelijke implementatie](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) van een klant te horen en een [Live tijdelijke demonstratie](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)te bekijken.
