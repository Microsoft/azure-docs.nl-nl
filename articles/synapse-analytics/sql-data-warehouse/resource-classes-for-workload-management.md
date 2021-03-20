---
title: Resource klassen voor workload Management
description: Richt lijnen voor het gebruik van resource klassen voor het beheren van gelijktijdigheids-en reken resources voor query's in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7714ce748eb172565357723924ab2212e9559e1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685324"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Werkbelasting beheer met resource klassen in azure Synapse Analytics

Richt lijnen voor het gebruik van resource klassen voor het beheren van geheugen en gelijktijdigheid voor Synapse SQL-pool query's in azure Synapse.  

## <a name="what-are-resource-classes"></a>Wat zijn resource klassen

De prestatie capaciteit van een query wordt bepaald door de resource klasse van de gebruiker.  Resource klassen zijn vooraf gedefinieerde resource limieten in Synapse SQL-pool die de reken resources en gelijktijdigheid voor het uitvoeren van query's regelen. Resource klassen kunnen u helpen bij het configureren van resources voor uw query's door limieten in te stellen voor het aantal query's dat gelijktijdig wordt uitgevoerd en op de reken resources die zijn toegewezen aan elke query.  Er is sprake van een afweging tussen geheugen en gelijktijdigheid.

- Kleinere resource klassen verminderen het maximale geheugen per query, maar verhogen gelijktijdigheid.
- Grotere bron klassen verhogen het maximale geheugen per query, maar verminderen gelijktijdig.

Er zijn twee typen resource klassen:

- Statische bronnen klassen, die goed zijn afgestemd op de gelijktijdige gelijktijdigheid van een gegevensverzamelings grootte die is opgelost.
- Dynamische bron klassen, die goed zijn afgestemd op gegevens sets die groter worden en die de prestaties van het service niveau verhogen.

Resource klassen gebruiken gelijktijdigheids sleuven om het Resource verbruik te meten.  [Gelijktijdigheids sleuven](#concurrency-slots) worden verderop in dit artikel beschreven.

- Zie [geheugen en gelijktijdigheids limieten](memory-concurrency-limits.md)voor het weer geven van het resource gebruik voor de resource klassen.
- Als u de resource klasse wilt aanpassen, kunt u de query uitvoeren onder een andere gebruiker of [het lidmaatschap van de resource klasse van de huidige gebruiker wijzigen](#change-a-users-resource-class) .

### <a name="static-resource-classes"></a>Statische resource klassen

Statische resource klassen wijzen dezelfde hoeveelheid geheugen toe, ongeacht het huidige prestatie niveau, dat wordt gemeten in [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md). Aangezien query's dezelfde geheugen toewijzing krijgen, ongeacht het prestatie niveau, kunnen er met [het uitschalen van het Data Warehouse](quickstart-scale-compute-portal.md) meer query's worden uitgevoerd binnen een resource klasse.  Statische bron klassen zijn ideaal als het gegevens volume een bekende en constante is.

De statische resource klassen worden geïmplementeerd met de volgende vooraf gedefinieerde database rollen:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- Bron staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamische resource klassen

Dynamische resource klassen wijzen een variabele hoeveelheid geheugen toe, afhankelijk van het huidige service niveau. Statische bron klassen zijn nuttig voor een hogere gelijktijdigheid en statische gegevens volumes, maar dynamische resource klassen zijn beter geschikt voor een groeiende of variabele hoeveelheid gegevens.  Wanneer u omhoog schaalt naar een groter service niveau, krijgen uw query's automatisch meer geheugen.  

De dynamische resource klassen worden geïmplementeerd met de volgende vooraf gedefinieerde database rollen:

- smallrc
- mediumrc
- largerc
- xlargerc

De geheugen toewijzing voor elke resource klasse is als volgt.

| Serviceniveau  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12,5%             | 12,5%                  | 21                    | 70%                    |
| DW300c         | 8%                | 10%                    | 21                    | 70%                    |
| DW400c         | 6,25%             | 10%                    | 21                    | 70%                    |
| DW500c         | 5%                | 10%                    | 21                    | 70%                    |
| DW1000c naar<br> DW30000c | 3%       | 10%                    | 21                    | 70%                    |

### <a name="default-resource-class"></a>Standaard resource klasse

Standaard is elke gebruiker lid van de dynamische resource klasse **smallrc**.

De resource klasse van de service beheerder is vastgesteld op smallrc en kan niet worden gewijzigd.  De service beheerder is de gebruiker die tijdens het inrichtings proces is gemaakt.  De service beheerder in deze context is de aanmeldings naam die is opgegeven voor de aanmelding van de server beheerder bij het maken van een nieuwe Synapse SQL-groep met een nieuwe server.

> [!NOTE]
> Gebruikers of groepen die als Active Directory beheerder zijn gedefinieerd, zijn ook service beheerders.
>
>

## <a name="resource-class-operations"></a>Bewerkingen voor de resource klasse

Resource klassen zijn ontworpen om de prestaties te verbeteren voor gegevens beheer en manipulatie activiteiten. Complexe query's kunnen ook profiteren van het uitvoeren van een grote resource klasse. Query prestaties voor grote samen voegingen en sorteringen kunnen bijvoorbeeld worden verbeterd wanneer de resource klasse groot genoeg is om de query uit te voeren in het geheugen.

### <a name="operations-governed-by-resource-classes"></a>Bewerkingen die zijn onderhevig aan resource klassen

Deze bewerkingen zijn onderworpen aan resource klassen:

- INVOEGEN-SELECTEREN, BIJWERKEN, VERWIJDEREN
- SELECTEREN (bij het opvragen van gebruikers tabellen)
- ALTER INDEX-Rebuild of reorganiseren
- ALTER TABLE REBUILD
- CREATE INDEX
- EEN GECLUSTERDE COLUMN STORE-INDEX MAKEN
- CREATE TABLE ALS SELECTEREN (CTAS)
- Gegevens laden
- Gegevens verplaatsings bewerkingen die worden uitgevoerd door de gegevens verplaatsings service (DMS)

> [!NOTE]  
> SELECT-instructies op dynamische beheer weergaven (Dmv's) of andere systeem weergaven vallen niet onder een van de gelijktijdigheids limieten. U kunt het systeem bewaken, ongeacht het aantal query's dat erop wordt uitgevoerd.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Bewerkingen die niet onder resource klassen vallen

Sommige query's worden altijd uitgevoerd in de smallrc-resource klasse, zelfs als de gebruiker lid is van een grotere resource klasse. Deze vrijgestelde query's tellen niet mee voor de limiet voor gelijktijdigheid. Als de limiet voor gelijktijdigheid 16 is, kunnen veel gebruikers bijvoorbeeld selecteren uit de systeem weergaven zonder dat dit van invloed is op de beschik bare gelijktijdigheids sleuven.

De volgende instructies zijn vrijgesteld van resource klassen en worden altijd uitgevoerd in smallrc:

- TABEL maken of verwijderen
- ALTER TABLE... PARTITIE SCHA KELEN, splitsen of samen VOEGen
- ALTER INDEX DISABLE
- DROP INDEX
- STATISTIEKEN maken, bijwerken of verwijderen
- TRUNCATE TABLE
- AUTORISATIE WIJZIGEN
- CREATE LOGIN (AANMELDING MAKEN)
- GEBRUIKER maken, wijzigen of verwijderen
- PROCEDURE maken, wijzigen of verwijderen
- WEER gave maken of verwijderen
- WAARDEN INVOEGEN
- SELECTEREN uit systeem weergaven en Dmv's
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Gelijktijdigheids sleuven

Gelijktijdigheids sleuven zijn een handige manier om de resources bij te houden die beschikbaar zijn voor het uitvoeren van query's. Ze zijn vergelijkbaar met tickets die u koopt om stoelen op een concert te reserveren, omdat het aantal stoelen beperkt is. Het totale aantal gelijktijdigheids sleuven per Data Warehouse wordt bepaald door het service niveau. Voordat een query kan worden uitgevoerd, moet deze voldoende gelijktijdigheids sleuven kunnen reserveren. Wanneer een query is voltooid, worden de gelijktijdigheids sleuven vrijgegeven.  

- Een query die wordt uitgevoerd met 10 gelijktijdigheids sleuven, kan toegang krijgen tot vijf maal meer reken bronnen dan een query die wordt uitgevoerd met 2 gelijktijdigheids sleuven.
- Als voor elke query 10 gelijktijdigheids sleuven zijn vereist en er 40 gelijktijdigheids sleuven zijn, kunnen er maar 4 query's gelijktijdig worden uitgevoerd.

Alleen resource-beheerste query's gebruiken gelijktijdigheids sleuven. Systeem query's en sommige triviale query's gebruiken geen sleuven. Het exacte aantal verbruikte gelijktijdigheids sleuven wordt bepaald door de resource klasse van de query.

## <a name="view-the-resource-classes"></a>De resource klassen weer geven

Resource klassen worden geïmplementeerd als vooraf gedefinieerde database rollen. Er zijn twee typen resource klassen: dynamisch en statisch. Als u een lijst met resource klassen wilt weer geven, gebruikt u de volgende query:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>De resource klasse van een gebruiker wijzigen

Resource klassen worden geïmplementeerd door gebruikers toe te wijzen aan database rollen. Wanneer een gebruiker een query uitvoert, wordt de query uitgevoerd met de resource klasse van de gebruiker. Als een gebruiker bijvoorbeeld lid is van de databaserol staticrc10, worden de query's uitgevoerd met kleine hoeveel heden geheugen. Als een database gebruiker lid is van de xlargerc-of staticrc80-database rollen, worden de query's uitgevoerd met grote hoeveel heden geheugen.

Als u de resource klasse van een gebruiker wilt verhogen, gebruikt u [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om de gebruiker toe te voegen aan een databaserol van een grote resource klasse.  De onderstaande code voegt een gebruiker toe aan de databaserol largerc.  Elke aanvraag ontvangt 22% van het systeem geheugen.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Gebruik [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)om de resource klasse te verlagen.  Als ' loaduser ' geen lid of andere resource klassen is, gaan ze naar de standaard smallrc-resource klasse met een geheugen toekenning van 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Prioriteit van resource klasse

Gebruikers kunnen lid zijn van meerdere resource klassen. Wanneer een gebruiker deel uitmaakt van meer dan één resource klasse:

- Dynamische resource klassen hebben voor rang op statische resource klassen. Als een gebruiker bijvoorbeeld lid is van zowel mediumrc (dynamisch) als staticrc80 (static), worden query's uitgevoerd met mediumrc.
- Grotere bron klassen hebben voor rang op kleinere resource klassen. Als een gebruiker bijvoorbeeld lid is van mediumrc en largerc, worden query's uitgevoerd met largerc. Als een gebruiker lid is van zowel staticrc20 als statirc80, worden er ook query's uitgevoerd met staticrc80-resource toewijzingen.

## <a name="recommendations"></a>Aanbevelingen

>[!NOTE]
>Overweeg het beheer van de werk belasting te benutten ([isolatie van werk belasting](sql-data-warehouse-workload-isolation.md), [classificatie](sql-data-warehouse-workload-classification.md) en [urgentie](sql-data-warehouse-workload-importance.md)) voor meer controle over uw werk belasting en voorspel bare prestaties.  
>
>

We raden u aan een gebruiker te maken die is toegewezen voor het uitvoeren van een specifiek type query of het laden van een bewerking. Geef deze gebruiker een permanente resource klasse in plaats van de resource klasse regel matig te wijzigen. Statische resource klassen bieden meer algemene controle over de werk belasting, dus we raden aan gebruik te maken van statische resource klassen voordat u dynamische resource klassen overweegt.

### <a name="resource-classes-for-load-users"></a>Resource klassen voor het laden van gebruikers

`CREATE TABLE` maakt standaard gebruik van geclusterde column Store-indexen. Het comprimeren van gegevens naar een column store-index is een geheugenintensieve bewerking en de geheugen druk kan de index kwaliteit verminderen. Geheugen druk kan ertoe leiden dat een hogere resource klasse nodig is voor het laden van gegevens. Om ervoor te zorgen dat er voldoende geheugen beschikbaar is, kunt u een gebruiker maken die is aangewezen voor het uitvoeren van belastingen en die gebruiker toewijzen aan een hogere resource klasse.

Het geheugen dat nodig is om de belasting efficiënt te verwerken, is afhankelijk van de aard van de geladen tabel en de grootte van de gegevens. Zie [maximale Rijg roep-kwaliteit](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)voor meer informatie over geheugen vereisten.

Wanneer u de geheugen vereiste hebt bepaald, kiest u of u de gebruiker laden wilt toewijzen aan een statische of dynamische bron klasse.

- Gebruik een statische resource klasse wanneer tabel geheugen vereisten binnen een specifiek bereik vallen. Hiermee wordt de belasting uitgevoerd met het juiste geheugen. Wanneer u het Data Warehouse schaalt, heeft de belasting geen meer geheugen nodig. Door gebruik te maken van een statische resource klasse blijven de geheugen toewijzingen constant. Met deze consistentie wordt geheugen bespaard en kunnen meer query's gelijktijdig worden uitgevoerd. We raden aan dat nieuwe oplossingen eerst gebruikmaken van de statische resource klassen, aangezien deze meer controle bieden.
- Gebruik een dynamische resource klasse wanneer de geheugen vereisten van de tabel aanzienlijk verschillen. De belasting vergt mogelijk meer geheugen dan het huidige DWU-of cDWU-niveau biedt. Wanneer het Data Warehouse wordt geschaald, wordt er meer geheugen toegevoegd voor het laden van bewerkingen, waardoor de belasting sneller kan worden uitgevoerd.

### <a name="resource-classes-for-queries"></a>Resource klassen voor query's

Sommige query's zijn reken intensief en andere niet.  

- Kies een dynamische resource klasse als query's complex zijn, maar geen hoge gelijktijdigheid nodig hebben.  Het genereren van dagelijkse of wekelijkse rapporten is bijvoorbeeld nood zakelijk voor resources. Als de rapporten grote hoeveel heden gegevens verwerken, biedt het schalen van het Data Warehouse meer geheugen voor de bestaande resource klasse van de gebruiker.
- Kies een statische resource klasse wanneer de resource verwachtingen de hele dag variëren. Een statische resource klasse werkt bijvoorbeeld goed wanneer het Data Warehouse door veel mensen wordt doorzocht. Bij het schalen van het Data Warehouse wordt de hoeveelheid geheugen die aan de gebruiker is toegewezen, niet gewijzigd. Daarom kunnen meer query's parallel op het systeem worden uitgevoerd.

De juiste geheugen subsidies zijn afhankelijk van een groot aantal factoren, zoals de hoeveelheid gegevens die wordt opgevraagd, de aard van de tabel schema's en verschillende samen voegingen, Select en Group predikaten. Over het algemeen kunt u met het toewijzen van meer geheugen query's sneller uitvoeren, maar vermindert u de totale gelijktijdigheid. Als gelijktijdigheid geen probleem is, heeft het toewijzen van geheugen geen schade aan door voer.

Gebruik verschillende resource klassen om prestaties af te stemmen. In de volgende sectie vindt u een opgeslagen procedure waarmee u de beste bron klasse kunt opwaarderen.

## <a name="example-code-for-finding-the-best-resource-class"></a>Voorbeeld code voor het zoeken naar de beste resource klasse

U kunt de volgende opgeslagen procedure gebruiken voor het afrekenen van gelijktijdigheid en geheugen toekenning per resource klasse op een bepaalde SLO en de beste bron klasse voor CCI-bewerkingen op basis van een niet-gepartitioneerde CCI-tabel in een bepaalde resource klasse:

Dit is het doel van deze opgeslagen procedure:

1. Om de gelijktijdigheids-en geheugen toekenning per resource klasse op een bepaalde SLO te bekijken. De gebruiker moet NULL voor schema en TableName opgeven, zoals wordt weer gegeven in dit voor beeld.  
2. Voor een overzicht van de beste resource klasse voor de geheugenintensieve bewerkingen (laden, tabel kopiëren, index opnieuw samen stellen, enzovoort) voor een niet-gepartitioneerde CCI-tabel in een bepaalde resource klasse. De opgeslagen procedure maakt gebruik van het tabel schema om de vereiste geheugen toekenning te bepalen.

### <a name="dependencies--restrictions"></a>Beperkingen voor het & van afhankelijkheden

- Deze opgeslagen procedure is niet ontworpen voor het berekenen van de geheugen vereiste voor een gepartitioneerde CCI-tabel.
- Voor deze opgeslagen procedure worden geen geheugen vereisten in rekening gebracht voor het SELECT-gedeelte van CTAS/INSERT-SELECT en wordt ervan uitgegaan dat het een SELECT is.
- Deze opgeslagen procedure maakt gebruik van een tijdelijke tabel, die beschikbaar is in de sessie waarin deze opgeslagen procedure is gemaakt.
- Deze opgeslagen procedure is afhankelijk van de huidige aanbiedingen (bijvoorbeeld hardwareconfiguratie, DMS-configuratie) en als een van die wijzigingen, werkt deze opgeslagen proc niet goed.  
- Deze opgeslagen procedure is afhankelijk van bestaande gelijktijdigheids limieten. als deze wijziging wordt toegepast, werkt deze opgeslagen procedure niet goed.  
- Deze opgeslagen procedure is afhankelijk van bestaande resource klasse-aanbiedingen en als deze wijziging wordt toegepast, werkt deze opgeslagen procedure niet goed.  

>[!NOTE]  
>Als u geen uitvoer krijgt na het uitvoeren van een opgeslagen procedure met opgegeven para meters, kunnen er twee gevallen zijn.
>
>1. De para meter DW bevat een ongeldige SLO-waarde
>2. Of er is geen overeenkomende resource klasse voor de bewerking CCI in de tabel.
>
>Zo is bij DW100c de hoogste beschik bare geheugen toekenning 1 GB en als het tabel schema breed genoeg is voor het overschrijden van de vereiste van 1 GB.

### <a name="usage-example"></a>Voor beeld van gebruik

Syntaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Geef een NULL-para meter op om de huidige DWU te extra heren uit de DW DB of geef een ondersteund DWU op in de vorm ' DW100c '
2. @SCHEMA_NAME: Geef een schema naam van de tabel op
3. @TABLE_NAME: Geef een tabel naam op voor de interesse

Voor beelden van deze opgeslagen procedure:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Met de volgende instructie maakt u Tabel1 die in de voor gaande voor beelden wordt gebruikt.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedure

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Volgende stappen

Zie [een Data Base beveiligen in Synapse SQL](sql-data-warehouse-overview-manage-security.md)voor meer informatie over het beheren van database gebruikers en beveiliging. Zie [geheugen optimalisaties voor column Store-compressie](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)voor meer informatie over hoe grotere resource klassen de geclusterde column store-index kwaliteit kunnen verbeteren.
