---
title: Bewaar beleid voor gegevens in-en uitschakelen-Azure SQL Edge
description: Meer informatie over het inschakelen en uitschakelen van het Bewaar beleid voor gegevens in Azure SQL Edge
keywords: SQL-rand, gegevens retentie
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861422"
---
# <a name="enable-and-disable-data-retention-policies"></a>Het Bewaar beleid voor gegevens in-en uitschakelen

In dit onderwerp wordt beschreven hoe u het Bewaar beleid voor gegevens inschakelt en uitschakelt voor een Data Base en een tabel. 

## <a name="enable-data-retention-for-a-database"></a>Bewaren van gegevens voor een Data Base inschakelen

In het volgende voor beeld ziet u hoe u het bewaren van gegevens inschakelt met behulp van [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-set-options).

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Controleren of bewaren van gegevens is ingeschakeld voor een Data Base

De volgende opdracht kan worden gebruikt om te controleren of het bewaren van gegevens is ingeschakeld voor een Data Base
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Bewaren van gegevens voor een tabel inschakelen

Gegevens retentie moet zijn ingeschakeld voor elke tabel waarvoor u wilt dat gegevens automatisch worden opgeschoond. Wanneer het bewaren van gegevens is ingeschakeld voor de data base en de tabel, scant een achtergrond systeem taak de tabel regel matig om eventuele verouderde rijen te identificeren en te verwijderen. Het bewaren van gegevens kan tijdens het maken van een tabel in een tabel worden ingeschakeld met behulp van de tabel [Create Table](/sql/t-sql/statements/create-table-transact-sql) of [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

In het volgende voor beeld ziet u hoe u de Bewaar periode voor gegevens inschakelt voor een tabel met behulp van de [tabel Create](/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

Het `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` deel van de opdracht create table stelt de Bewaar periode voor gegevens in de tabel in. De opdracht maakt gebruik van de volgende vereiste para meters 

- DATA_DELETION-geeft aan of het bewaren van gegevens is in-of uitgeschakeld.
- FILTER_COLUMN naam van de kolom in de tabel, die wordt gebruikt om te bepalen of de rijen verouderd zijn of niet. De filter kolom kan alleen een kolom met de volgende gegevens typen zijn 
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - Date time offset
- RETENTION_PERIOD-een integerwaarde gevolgd door de descriptor van een eenheid. De toegestane eenheden zijn dag, dagen, WEEK, weken, maand, maanden, jaar en jaar.

In het volgende voor beeld ziet u hoe u de Bewaar periode voor gegevens inschakelt voor een tabel met behulp van [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Controleren of bewaren van gegevens is ingeschakeld voor een tabel

De volgende opdracht kan worden gebruikt om de tabellen te controleren waarvoor het bewaren van gegevens is ingeschakeld

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Een waarde van data_retention_period =-1 en data_retention_period_unit als oneindig, geeft aan dat het bewaren van gegevens niet is ingesteld voor de tabel.

De volgende query kan worden gebruikt om de kolom te identificeren die wordt gebruikt als de filter_column voor het bewaren van gegevens. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>Instellingen voor het bewaren van data BASEs en tabel gegevens

De instelling gegevens retentie voor de data base en de tabel wordt in combi natie gebruikt om te bepalen of automatisch opschonen voor verouderde rijen op de tabellen wordt uitgevoerd. 

|Database optie | Tabel optie | Gedrag |
|----------------|--------------|----------|
| UIT | UIT | Het Bewaar beleid voor gegevens is uitgeschakeld en het automatisch en hand matig opschonen van verouderde records is uitgeschakeld.|
| UIT | AAN  | Het Bewaar beleid voor gegevens is ingeschakeld voor de tabel. Automatisch opruimen van verouderde records is uitgeschakeld, maar de hand matige opschoon methode kan worden gebruikt om verouderde records op te schonen. |
| AAN | UIT | Het Bewaar beleid voor gegevens is ingeschakeld op database niveau. Omdat de optie echter op tabel niveau is uitgeschakeld, is het opschonen van verouderde rijen niet op basis van een Bewaar bewerking.|
| AAN | AAN | Het beleid voor gegevens retentie is ingeschakeld voor zowel de Data Base als de tabellen. Het automatisch opschonen van verouderde records is ingeschakeld. |

## <a name="disable-data-retention-on-a-table"></a>Bewaren van gegevens uitschakelen voor een tabel 

Het bewaren van gegevens kan worden uitgeschakeld voor een tabel met behulp van [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql). De volgende opdracht kan worden gebruikt om gegevens retentie uit te scha kelen voor een tabel.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Bewaren van gegevens op een Data Base uitschakelen

Het bewaren van gegevens kan worden uitgeschakeld voor een tabel met behulp van [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-set-options). De volgende opdracht kan worden gebruikt om gegevens retentie uit te scha kelen voor een Data Base.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Volgende stappen
- [Bewaren van gegevens en automatisch opschonen van gegevens](data-retention-overview.md)
- [Historische gegevens beheren met Bewaar beleid](data-retention-cleanup.md)
