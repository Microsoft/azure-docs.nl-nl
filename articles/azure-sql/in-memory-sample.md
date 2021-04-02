---
title: Voorbeeld van In-Memory
description: Probeer een voorbeeld van de Azure SQL Database in-Memory met OLTP en columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786578"
---
# <a name="in-memory-sample"></a>Voorbeeld van In-Memory
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Met In-Memory in Azure SQL Database kunt u de prestaties van uw toepassing verbeteren en mogelijk de kosten voor uw database verminderen. Door gebruik te maken van in-Memory in Azure SQL Database kunt u prestatieverbeteringen bekomen voor verschillende workloads.

In dit artikel ziet u twee voorbeelden die het gebruik van in-Memory OLTP en columnstore-indexen in Azure SQL Database illustreren.

Zie voor meer informatie:

- [Overzicht van in-Memory OLTP en gebruiksscenario's](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (bevat verwijzingen naar casestudy's van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Gids columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybride transactionele/analytische verwerking (HTAP), ook wel bekend onder de naam [real-time operationele analyses](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Het voorbeeld van In-Memory OLTP installeren

U kunt de AdventureWorksLT-voorbeelddatabase in enkele klikken aanmaken in het [Azure-portaal](https://portal.azure.com/). In de stappen in dit gedeelte wordt uitgelegd hoe u uw AdventureWorksLT-database kunt verrijken met In-Memory OLTP-objecten en worden de prestatieverbeteringen gedemonstreerd.

Voor een eenvoudigere, maar visueel aantrekkelijke prestatiedemo voor In-Memory OLTP, zie:

- Release: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Broncode: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Installatiestappen

1. Maak in het [Azure-portaal](https://portal.azure.com/) een Premium- of Bedrijfskritieke database op een server. Stel de **bron** in op de voorbeelddatabase AdventureWorksLT. Zie [Uw eerste data base in Azure SQL Database maken](database/single-database-create-quickstart.md) voor gedetailleerde instructies.

2. Maak verbinding met de database met behulp van SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms).

3. Kopieer het [script In-Memory OLTP Transact-SQL](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) naar uw klembord. Het script T-SQL maakt de benodigde In-Memory-objecten in de AdventureWorksLT-voorbeelddatabase die u in stap 1 hebt gemaakt.

4. Plak het T-SQL-script in SSMS en voer vervolgens het script uit. De CREATE TABLE-instructies in `MEMORY_OPTIMIZED = ON` zijn van cruciaal belang. Bijvoorbeeld:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Fout 40536

Als u fout 40536 krijgt wanneer u het T-SQL-script uitvoert, voert u het volgende T-SQL-script uit om te controleren of de database In-Memory ondersteunt:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Het resultaat **0** betekent dat In-Memory niet wordt ondersteund en **1** betekent dat het wel wordt ondersteund. Zorg ervoor dat de database zich in het Premium-serviceniveau bevindt om de oorzaak van het probleem vast te stellen.

### <a name="about-the-created-memory-optimized-items"></a>Over de aangemaakte items die zijn geoptimaliseerd voor geheugen

**Tabellen**: Het voorbeeld bevat de volgende tabellen die zijn geoptimaliseerd voor geheugen:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

U kunt tabellen die zijn geoptimaliseerd voor geheugen inspecteren met de **Objectverkenner** in SSMS. Klik met de rechtermuisknop op **Tabellen** > **Filter** > **Filterinstellingen** > **Is geoptimaliseerd voor geheugen**. De waarde is gelijk aan 1.

U kunt ook een query uitvoeren op de catalogusweergaven, zoals:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**In eigen systeem gecompileerde opgeslagen procedure**: U kunt SalesLT.usp_InsertSalesOrder_inmem inspecteren via een query in de catalogusweergave:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Het voorbeeld van een OTLTP-workload uitvoeren

Het enige verschil tussen de volgende twee *opgeslagen procedures* is dat de eerste procedure gebruikmaakt van tabelversies die zijn geoptimaliseerd voor geheugen, terwijl voor de tweede procedure de normale on-disk tabellen worden gebruikt:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

In deze sectie ziet u hoe u het handige **ostress.exe** exe-hulpprogramma gebruikt om de twee opgeslagen procedures op stressniveau uit te voeren. U kunt vergelijken hoe lang het duurt om de twee stressuitvoeringen te voltooien.

Wanneer u ostress.exe uitvoert, raden we u aan om parameterwaarden op te geven die zijn ontworpen voor de volgende twee bewerkingen:

- Voer een groot aantal gelijktijdige verbindingen uit met behulp van -n100.
- Laat elke verbinding verschillende honderden keer herhalen met behulp van -r500.

Het kan echter verstandig zijn om te beginnen met veel kleinere waarden, zoals -n10 en -r50, om ervoor te zorgen dat alles werkt.

### <a name="script-for-ostressexe"></a>Script voor ostress.exe

In deze sectie wordt het T-SQL-script weergegeven dat is ingesloten in onze ostress.exe-opdrachtregel. Het script maakt gebruik van items die zijn gemaakt door het T-SQL-script dat u eerder hebt geïnstalleerd.

Met het volgende script wordt een voorbeeld van een verkooporder met vijf regelitems ingevoegd in de volgende *tabellen* die zijn geoptimaliseerd voor geheugen:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Als u de versie *_ondisk* van het vorige T-SQL-script voor ostress.exe wilt maken, vervangt u beide exemplaren van de subtekenreeks *_inmem* door *_ondisk*. Deze vervangingen zijn van invloed op de namen van tabellen en opgeslagen procedures.

#### <a name="install-rml-utilities-and-ostress"></a>Installeer RML-hulpprogramma's en `ostress`

Idealiter gaat u ostress.exe uitvoeren op een virtuele Azure-machine (VM). U maakt een [Virtuele Azure-machine](https://azure.microsoft.com/documentation/services/virtual-machines/) in dezelfde geografische regio waarin uw AdventureWorksLT-database zich bevindt. In plaats daarvan kunt u ostress.exe uitvoeren op uw laptop.

Installeer de RML-hulpprogram ma's (Replay Markup Language) op de virtuele machine, of een host naar keuze. Ostress.exe maakt deel uit van de hulpprogramma's.

Zie voor meer informatie:

- De discussie oer ostress.exe in [Voorbeelddatabase voor In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Voorbeelddatabase voor In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- De [blog om ostress.exe te installeren](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Voer eerst de stressworkload *_inmem* stress uit

U kunt een venster met *RML-opdrachtprompt* gebruiken om onze opdrachtregel voor ostress.exe uit te voeren. De opdrachtregelparameters geven `ostress` de opdracht om:

- 100 verbindingen tegelijkertijd uit te voeren (-n100).
- Elke verbinding het T-SQL-script 50 keer te laten uitvoeren (-r50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

De voorgaande ostress.exe-opdrachtregel uitvoeren:

1. De gegevensinhoud van de database opnieuw instellen door de volgende opdracht uit te voeren in SSMS, om alle gegevens te verwijderen die zijn ingevoegd bij eerdere uitvoeringen:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopieer de tekst van de voorgaande ostress.exe-opdrachtregel naar het klembord.

3. Vervang de `<placeholders>` voor de parameters -S -U -P -d door de juiste echte waarden.

4. Voer de bewerkte opdrachtregel uit in een venster met de RML-opdrachtprompt.

#### <a name="result-is-a-duration"></a>Het resultaat is een duur

Als `ostress.exe` voltooid is, wordt de uitvoeringsduur als laatste regel van de uitvoer in het RML-opdrachtpromptvenster geschreven. Een korte testuitvoering duurde bijvoorbeeld ongeveer 1,5 minuten:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Stel het opnieuw in, bewerk het voor *_ondisk* en voer dan opnieuw uit

Nadat u het resultaat van de *_inmem*-uitvoering hebt verkregen, voert u de volgende stappen uit voor de *_ondisk*-uitvoering:

1. Stel de database opnieuw in door de volgende opdracht uit te voeren in SSMS om alle gegevens te verwijderen die zijn ingevoegd bij de eerdere uitvoering:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Bewerk de opdrachtregel voor ostress.exe om *_inmem* overal door *_ondisk* te vervangen.

3. Voer ostress.exe voor de tweede keer uit en leg vast hoelang het duurt.

4. Stel de database opnieuw in (om op een verantwoorde manier een grote hoeveelheid testgegevens te verwijderen).

#### <a name="expected-comparison-results"></a>Verwachte vergelijkingsresultaten

Onze In-Memory-tests hebben laten zien dat de prestaties **negen keer** beter zijn voor deze eenvoudige workload, waarbij `ostress` worden uitgevoerd op een virtuele Azure-machine in dezelfde Azure-regio als de database.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Het voorbeeld van in-memory Analytics installeren

In deze sectie gaat u de IO en de statistische resultaten vergelijken wanneer u een columnstore-index gebruikt versus wanneer u een traditionele b-tree-index gebruikt.

Voor realtime analyses van een OLTP-workload kunt u vaak best een niet-geclusterde columnstore-index gebruiken. Zie [Beschreven columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview) voor meer informatie.

### <a name="prepare-the-columnstore-analytics-test"></a>De analysetest van columnstore voorbereiden

1. Gebruik het Azure-portaal om een nieuwe AdventureWorksLT-database te maken op basis van het voorbeeld.
   - Gebruik die exacte naam.
   - Kies een Premium servicelaag.

2. Kopieer de [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) naar het klembord.
   - Het script T-SQL maakt de benodigde In-Memory-objecten in de AdventureWorksLT-voorbeelddatabase die u in stap 1 hebt gemaakt.
   - Het script maakt de dimensietabel en twee feitentabellen aan. De feitentabellen worden elk gevuld met 3,5 miljoen rijen.
   - Het kan 15 minuten duren om het script uit te voeren.

3. Plak het T-SQL-script in SSMS en voer vervolgens het script uit. Het trefwoord **COLUMNSTORE** in de instructie **CREATE INDEX** is van cruciaal belang, zoals in:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Stel AdventureWorksLT in op compatibiliteitsniveau 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Niveau 130 is niet direct gerelateerd aan de In-Memory-functies. Maar niveau 130 biedt doorgaans snellere queryprestaties dan 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Sleuteltabellen en columnstore-indexen

- dbo.FactResellerSalesXL_CCI is een tabel met een geclusterde columnstore-index met geavanceerde compressie op het *gegevens* niveau.

- dbo.FactResellerSalesXL_PageCompressed is een tabel die een equivalente reguliere geclusterde index heeft, die alleen op het *pagina* niveau gecomprimeerd wordt.

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Belangrijkste query's om de columnstore-index te vergelijken

Er zijn [verschillende T-SQL-querytypen die u kunt uitvoeren](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) om prestatieverbeteringen te bekijken. Let in stap 2 van het T-SQL-script op dit paar query's. Ze verschillen op slechts één regel:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Een geclusterde columnstore-index bevindt zich in de FactResellerSalesXL\_CCI-tabel.

Het volgende T-SQL-scriptfragment drukt statistieken af voor IO en TIME voor de query van elke tabel.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

In een database in prijscategorie P2 kunt u ongeveer negen keer zoveel prestatiewinsten verwachten voor deze query wanneer u de geclusterde columnstore-index gebruikt in plaats van de traditionele index. Met P15 kunt u ongeveer 57 keer meer prestatiewinsten verwachten wanneer u de columnstore-index gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart 1: In-Memory OLTP voor snellere T-SQL-prestaties](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](in-memory-oltp-configure.md)

- [In-Memory OLTP-opslag controleren](in-memory-oltp-monitor-space.md) voor In-Memory OLTP

## <a name="additional-resources"></a>Aanvullende bronnen

### <a name="deeper-information"></a>Meer gedetailleerde informatie

- [Meer informatie over hoe Quorum de workload voor belangrijke databases verdubbelt en tegelijkertijd de DTU met 70% verlaagt met In-Memory OLTP in Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Blogbericht over In-Memory OLTP in Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Meer informatie over In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Meer informatie over columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Meer informatie over realtime operationele analyses](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Zie [Veelvoorkomende workloadpatronen en overwegingen bij migratie](/previous-versions/dn673538(v=msdn.10)) (waarin wordt beschreven welke de workloadpatronen zijn waarbij In-Memory OLTP vaak significante prestatiewinsten biedt)

#### <a name="application-design"></a>Toepassingsontwerp

- [In-Memory OLTP (In-Memory-optimalisatie)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](in-memory-oltp-configure.md)

#### <a name="tools"></a>Hulpprogramma's

- [Azure-portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)