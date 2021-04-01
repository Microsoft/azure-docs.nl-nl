---
title: Primaire, refererende en unieke sleutels
description: Tabel beperkingen bieden ondersteuning voor het gebruik van een toegewezen SQL-groep in azure Synapse Analytics
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: emtehran
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88b63ce30000340a70811e9f623e4273ccbb272a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117279"
---
# <a name="primary-key-foreign-key-and-unique-key-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Primaire sleutel, refererende sleutel en unieke sleutel met behulp van een toegewezen SQL-groep in azure Synapse Analytics

Meer informatie over tabel beperkingen in een toegewezen SQL-groep, waaronder primaire sleutel, refererende sleutel en unieke sleutel.

## <a name="table-constraints"></a>Tabelbeperkingen

De toegewezen SQL-groep ondersteunt deze tabel beperkingen: 
- De primaire sleutel wordt alleen ondersteund als niet-geclusterd en niet afgedwongen worden gebruikt.    
- EEN unieke beperking wordt alleen ondersteund als er geen afgedwongen wordt toegepast.

Raadpleeg voor syntaxis [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) en [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

REFERERende-sleutel beperking wordt niet ondersteund in een toegewezen SQL-groep.  


## <a name="remarks"></a>Opmerkingen

Met een primaire sleutel en/of unieke sleutel kan exclusieve SQL-groeps engine een optimaal uitvoerings plan voor een query genereren.  Alle waarden in een primaire-sleutel kolom of een kolom met unieke beperkingen moeten uniek zijn.

Na het maken van een tabel met een primaire sleutel of een unieke beperking in de toegewezen SQL-groep moeten gebruikers ervoor zorgen dat alle waarden in die kolommen uniek zijn.  Een schending van dat kan ertoe leiden dat de query onnauwkeurig resultaat retourneert.  In dit voor beeld ziet u hoe een query onnauwkeurig resultaat kan retour neren als de kolom Primary Key of unique constraint dubbele waarden bevat.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Voorbeelden

Maak een exclusieve SQL-groeps tabel met een primaire sleutel: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```

Een exclusieve SQL-groeps tabel maken met een unieke beperking:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de tabellen voor uw toegewezen SQL-groep hebt gemaakt, is de volgende stap het laden van gegevens in de tabel. Zie [gegevens laden naar een toegewezen SQL-groep](load-data-wideworldimportersdw.md)voor een zelf studie voor het laden van een hand leiding.