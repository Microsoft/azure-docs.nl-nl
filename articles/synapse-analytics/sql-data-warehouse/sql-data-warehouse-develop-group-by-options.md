---
title: Groeperen op opties gebruiken
description: Tips voor het implementeren van groeps opties voor specifieke SQL-groepen in azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cdac3b1e502a203c272db75eb4345446b0f7c0c8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462766"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Groeperen op Opties voor toegewezen SQL-groepen in azure Synapse Analytics

In dit artikel vindt u tips voor het implementeren van groeps opties in toegewezen SQL-groepen.

## <a name="what-does-group-by-do"></a>Wat doet GROUP BY?

Met de component [Group by](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL worden gegevens geaggregeerd naar een samen vatting van rijen. GROEPEREN op heeft een aantal opties die niet worden ondersteund door de exclusieve SQL-groep. Deze opties hebben de volgende tijdelijke oplossingen:

* GROEPEREN op met ROLLUP
* GROEPEER SETS
* GROEPEREN op met kubus

## <a name="rollup-and-grouping-sets-options"></a>Opties voor samen vouwen en groeperen van sets

De eenvoudigste optie is om UNION ALL te gebruiken om de rollup uit te voeren in plaats van te vertrouwen op de expliciete syntaxis. Het resultaat is precies hetzelfde.

In het volgende voor beeld met de instructie GROUP BY met de optie samen vouwen:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Als u ROLLUP gebruikt, vraagt het vorige voor beeld de volgende aggregaties aan:

* Land en regio
* Land
* Eindtotaal

Als u het pakket wilt vervangen en dezelfde resultaten wilt retour neren, kunt u alle gebruiken en expliciet de vereiste aggregaties opgeven:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Als u GROEPERINGs SETS wilt vervangen, is het voorbeeld principe van toepassing. U hoeft alleen UNION alle secties te maken voor de aggregatie niveaus die u wilt zien.

## <a name="cube-options"></a>Kubus opties

Het is mogelijk om een GROUP BY WITH CUBE te maken met behulp van de methode UNION ALL. Het probleem is dat de code snel en lastig kan worden. Als u dit probleem wilt verhelpen, kunt u gebruikmaken van deze geavanceerdere benadering.

In het vorige voor beeld is de eerste stap het definiëren van de ' kubus ' waarmee alle aggregatie niveaus worden gedefinieerd die u wilt maken.

Let op de CROSS-koppeling van de twee afgeleide tabellen, aangezien hiermee alle niveaus voor ons worden gegenereerd. De rest van de code is daar voor de volgende opmaak:

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

In de volgende afbeelding ziet u de resultaten van de CTAS:

![Groeperen op kubus](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

De tweede stap is het opgeven van een doel tabel voor het opslaan van tussentijdse resultaten:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

De derde stap bestaat uit het herhalen van de kubus van kolommen die de aggregatie uitvoeren. De query wordt één keer uitgevoerd voor elke rij in de tijdelijke tabel van #Cube. De resultaten worden opgeslagen in de tijdelijke tabel #Results:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Ten slotte kunt u de resultaten retour neren door te lezen uit de tijdelijke tabel #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Door de code in secties te splitsen en een lus-construct te genereren, wordt de code beter beheerbaar en onderhouden.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
