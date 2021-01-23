---
title: CREATE TABLE ALS SELECTEREN (CTAS)
description: Uitleg en voor beelden van de instructie CREATE TABLE AS SELECT (CTAS) in Synapse SQL voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 68bab754142538fc6067cf2593ae6244a03a48d1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734811"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE ALS SELECTEREN (CTAS)

In dit artikel wordt de CREATE TABLE als SELECT (CTAS) T-SQL-instructie in Synapse SQL beschreven voor het ontwikkelen van oplossingen. Het artikel bevat ook code voorbeelden.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

De instructie [create table as select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (CTAS) is een van de belangrijkste T-SQL-functies die beschikbaar zijn. CTAS is een parallelle bewerking waarmee een nieuwe tabel wordt gemaakt op basis van de uitvoer van een SELECT-instructie. CTAS is de eenvoudigste en snelste manier om gegevens in een tabel met één opdracht te maken en in te voegen.

## <a name="selectinto-vs-ctas"></a>SELECTEREN... INTO versus CTAS

CTAS is een meer aanpas bare versie van de [optie Select... INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -instructie.

Hier volgt een voor beeld van een eenvoudige SELECT... SAMEN

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECTEREN... IN kunt u de distributie methode of het index type niet wijzigen als onderdeel van de bewerking. U maakt met `[dbo].[FactInternetSales_new]` behulp van het standaard distributie type van ROUND_ROBIN en de standaard tabel structuur van een GEclusterde column store-index.

Met CTAS kunt u daarentegen zowel de distributie van de tabel gegevens als het tabel structuur type opgeven. Het vorige voor beeld converteren naar CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Als u de index alleen probeert te wijzigen in uw CTAS-bewerking en de bron tabel is gedistribueerd met hash, houdt u dezelfde distributie kolom en hetzelfde gegevens type bij. Dit vereenvoudigt de verplaatsing van gegevens over meerdere distributie tijdens de bewerking. Dit is efficiënter.

## <a name="use-ctas-to-copy-a-table"></a>CTAS gebruiken om een tabel te kopiëren

Een van de meest voorkomende toepassingen van CTAS is het maken van een kopie van een tabel, zodat de DDL kan worden gewijzigd. Stel dat u de tabel oorspronkelijk hebt gemaakt als `ROUND_ROBIN` en nu wilt wijzigen in een tabel die wordt gedistribueerd op basis van een kolom. CTAS is hoe u de kolom distributie wijzigt. U kunt CTAS ook gebruiken om partitionering, indexering of kolom typen te wijzigen.

Stel dat u deze tabel hebt gemaakt met behulp van het standaard distributie type van `ROUND_ROBIN` en niet een distributie kolom opgeeft in de `CREATE TABLE` .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Nu u een nieuwe kopie van deze tabel wilt maken, met een `Clustered Columnstore Index` , zodat u kunt profiteren van de prestaties van geclusterde column Store-tabellen. U wilt deze tabel ook distribueren op `ProductKey` , omdat u verwacht dat u deelneemt aan deze kolom en u wilt voor komen dat gegevens worden verplaatst tijdens het samen voegen met `ProductKey` . Ten slotte wilt u ook partities toevoegen op `OrderDateKey` , zodat u snel oude gegevens kunt verwijderen door oude partities neer te zetten. Dit is de CTAS-instructie, waarmee u uw oude tabel kopieert naar een nieuwe tabel.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Ten slotte kunt u de naam van uw tabellen wijzigen om uw nieuwe tabel te wisselen en de oude tabel vervolgens te verwijderen.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS gebruiken om niet-ondersteunde functies te omzeilen

U kunt CTAS ook gebruiken om een aantal van de hieronder vermelde niet-ondersteunde functies te omzeilen. Deze methode kan vaak nuttig zijn, omdat uw code niet alleen compatibel is, maar vaak sneller wordt uitgevoerd op Synapse SQL. Deze prestaties zijn een resultaat van het volledig geevenwijdige ontwerp. Scenario's zijn onder andere:

* ANSI-samen voegingen voor UPDATEs
* ANSI-samen voegingen bij verwijderen
* MERGE-instructie

> [!TIP]
> Probeer eerst ' CTAS '. Het oplossen van een probleem met behulp van CTAS is doorgaans een goede benadering, zelfs als u meer gegevens schrijft als resultaat.

## <a name="ansi-join-replacement-for-update-statements"></a>Vervanging van ANSI-lidmaatschap voor update-instructies

Het kan zijn dat u een complexe update hebt. De update voegt meer dan twee tabellen samen met behulp van de syntaxis van de ANSI-samen voeging om de UPDATE uit te voeren of te verwijderen.

Stel dat u deze tabel moet bijwerken:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

De oorspronkelijke query heeft mogelijk iets als in dit voor beeld bekeken:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL ondersteunt geen ANSI-samen voegingen in de `FROM` component van een `UPDATE` -instructie, zodat u het vorige voor beeld niet kunt gebruiken zonder het te wijzigen.

U kunt een combi natie van een CTAS en een impliciete koppeling gebruiken om het vorige voor beeld te vervangen:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>Vervanging van ANSI-koppeling voor samen VOEGen 

In azure Synapse Analytics, [Merge](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true) (preview) met niet-OVEREENKOMEND op doel moet het doel een gedistribueerde hash-tabel zijn.  Gebruikers kunnen de ANSI-JOIN met [Update](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) of [Delete](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true) als tijdelijke oplossing gebruiken om de gegevens van de doel tabel te wijzigen op basis van het resultaat van het samen voegen met een andere tabel.  Hier volgt een voorbeeld.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Het gegevens type en de opties voor de null-waarde van uitvoer expliciet aangeven

Bij het migreren van code kunt u het volgende type Codeer patroon uitvoeren:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

U kunt denken dat u deze code moet migreren naar CTAS en dat u de juiste. Er is echter een verborgen probleem.

De volgende code levert niet hetzelfde resultaat op:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

U ziet dat de kolom ' result ' het gegevens type en de waarden voor nulwaarden van de expressie doorstuurt. Het gegevens type voorwaarts kan leiden tot subtiele afwijkingen in waarden als u niet voorzichtig bent.

Probeer dit voor beeld:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

De opgeslagen waarde voor resultaat wijkt af van. Als de persistente waarde in de kolom resultaat wordt gebruikt in andere expressies, wordt de fout nog steeds belang rijker.

![Scherm opname van CTAS-resultaten](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Dit is belang rijk voor gegevens migraties. Hoewel de tweede query weliswaar nauw keuriger is, is er een probleem. De gegevens verschillen ten opzichte van het bron systeem en die leiden tot de vraag naar integriteit in de migratie. Dit is een van de zeldzame gevallen waarin het ' onjuiste ' antwoord eigenlijk het juiste is.

De reden dat een verschil tussen de twee resultaten wordt weer geven, is te wijten aan impliciete type cast. In het eerste voor beeld definieert de tabel de definitie van de kolom. Wanneer de rij wordt ingevoegd, treedt er een impliciete type conversie op. In het tweede voor beeld is er geen impliciet type conversie, aangezien de expressie het gegevens type van de kolom definieert.

U ziet ook dat de kolom in het tweede voor beeld is gedefinieerd als een kolom die NULL-waarden bevat, terwijl in het eerste voor beeld dat niet het geval is. Wanneer de tabel in het eerste voor beeld is gemaakt, is de kolom met Null-waarden expliciet gedefinieerd. In het tweede voor beeld was het links naar de expressie en resulteert de standaard in een NULL-definitie.

Om deze problemen op te lossen, moet u de type conversie en de optie voor Null-waarden expliciet instellen in het gedeelte selecteren van de CTAS-instructie. U kunt deze eigenschappen niet instellen in de CREATE TABLE.
In het volgende voor beeld ziet u hoe u de code kunt herstellen:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Houd rekening met het volgende:

* U kunt CAST of CONVERT gebruiken.
* Gebruik ISNULL, niet COALESCE, om de NULL-waarde af te dwingen. Zie de volgende opmerking.
* ISNULL is de buitenste functie.
* Het tweede deel van de ISNULL is een constante, 0.

> [!NOTE]
> De functie voor het op de juiste wijze instellen van de null-waarde is essentieel voor het gebruik van ISNULL en geen COALESCE. COALESCE is geen deterministische functie, waardoor het resultaat van de expressie altijd NULL kan zijn. ISNULL wijkt af. Het is deterministisch. Wanneer het tweede deel van de functie ISNULL een constante of een letterlijke waarde is, is de resulterende waarden daarom niet NULL.

Het waarborgen van de integriteit van uw berekeningen is ook belang rijk voor het omschakelen van de tabel partitie. Stel dat u deze tabel als feiten tabel hebt gedefinieerd:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Het veld amount is echter een berekende expressie. Het maakt geen deel uit van de bron gegevens.

Als u de gepartitioneerde gegevensset wilt maken, kunt u de volgende code gebruiken:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

De query zou perfect goed worden uitgevoerd. Het probleem treedt op wanneer u de partitie-switch probeert uit te voeren. De tabel definities komen niet overeen. Om ervoor te zorgen dat de tabel definities overeenkomen, wijzigt u de CTAS om een functie toe te voegen `ISNULL` om het kenmerk null-waarde van de kolom te behouden.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

U kunt zien dat type consistentie en het onderhouden van de eigenschappen van de null-waarde voor een CTAS een technisch best practice is. Het helpt bij het behoud van de integriteit van uw berekeningen en zorgt er ook voor dat de partitie kan worden overgeschakeld.

CTAS is een van de belangrijkste instructies in Synapse SQL. Zorg ervoor dat u het goed begrijpt. Raadpleeg de [CTAS-documentatie](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht voor ontwikkel](sql-data-warehouse-overview-develop.md)aars voor meer tips voor ontwikkel aars.