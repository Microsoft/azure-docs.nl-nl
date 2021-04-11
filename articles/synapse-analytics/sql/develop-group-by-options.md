---
title: GROUP BY-opties in Synapse SQL gebruiken
description: Met Synapse SQL kunt u oplossingen ontwikkelen door verschillende opties voor groeperen op te implementeren.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: c2e1ddbfb87df40a0e3683e7bca7539c26191a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671304"
---
# <a name="group-by-options-in-synapse-sql"></a>GROEPEREN op Opties in Synapse SQL

Met Synapse SQL kunt u oplossingen ontwikkelen door verschillende opties voor groeperen op te implementeren. 

## <a name="what-group-by-does"></a>De groep BY bevat

Met de component [Group by](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest&preserve-view=true) T-SQL worden gegevens geaggregeerd naar een samen vatting van rijen.

Een serverloze SQL-pool ondersteunt het hele bereik met opties voor groeperen op. De toegewezen SQL-groep ondersteunt een beperkt aantal opties voor groeperen op.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Opties voor groeperen op die worden ondersteund in de toegewezen SQL-groep

GROEPEREN op heeft een aantal opties die niet worden ondersteund door de exclusieve SQL-groep. Deze opties hebben de volgende tijdelijke oplossingen:

* GROEPEREN op met ROLLUP
* GROEPEER SETS
* GROEPEREN op met kubus

### <a name="rollup-and-grouping-sets-options"></a>Opties voor samen vouwen en groeperen van sets

De eenvoudigste optie is om UNION ALL te gebruiken om de rollup uit te voeren in plaats van te vertrouwen op de expliciete syntaxis. Het resultaat is precies hetzelfde

In het volgende voor beeld wordt de instructie GROUP BY gebruikt met de optie ROLLUP:

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
* Land/regio
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

### <a name="cube-options"></a>Kubus opties

Het is mogelijk om een GROUP BY WITH CUBE te maken met behulp van de methode UNION ALL. Het probleem is dat de code snel en lastig kan worden. Als u dit probleem wilt verhelpen, kunt u gebruikmaken van deze geavanceerdere benadering.

De eerste stap is het definiëren van de ' kubus ' waarmee alle aggregatie niveaus worden gedefinieerd die u wilt maken. Let op de CROSS-koppeling van de twee afgeleide tabellen, aangezien alle niveaus worden gegenereerd. De rest van de code is daar voor de opmaak.

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

In de volgende afbeelding ziet u de resultaten van [Create Table als selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true):

![Groeperen op kubus](./media/develop-group-by-options/develop-group-by-cube.png)

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

De derde stap is door loop de kubus van kolommen die de aggregatie uitvoeren. De query wordt één keer uitgevoerd voor elke rij in de tijdelijke tabel van #Cube. De resultaten worden opgeslagen in de tijdelijke tabel #Results:

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

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
