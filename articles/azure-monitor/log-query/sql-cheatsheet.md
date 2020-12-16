---
title: Cheat-blad van SQL to Azure Monitor-logboek query | Microsoft Docs
description: Help voor gebruikers die bekend zijn met SQL in het schrijven van logboek query's in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606688"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Cheat-blad van SQL to Azure Monitor-logboek query 

De onderstaande tabel helpt gebruikers die bekend zijn met SQL om de Kusto-query taal te ontdekken om logboek query's te schrijven in Azure Monitor. Bekijk de T-SQL-opdracht voor het oplossen van algemene scenario's en de equivalenten in een Azure Monitor-logboek query.

## <a name="sql-to-azure-monitor"></a>SQL naar Azure Monitor

Beschrijving |SQL-query | Azure Monitor-logboek query
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Alle gegevens uit een tabel selecteren |`SELECT * FROM dependencies` |<code>dependencies</code>
Specifieke kolommen selecteren in een tabel |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
100 records selecteren uit een tabel |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Null-evaluatie |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Teken reeks vergelijking: gelijkheid |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Teken reeks vergelijking: subtekenreeks |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Vergelijking van teken reeksen: Joker tekens |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Datum vergelijking: laatste 1 dag |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Datum vergelijking: datum bereik |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Vergelijking van Boole |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Sorteren |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Groeperen, aggregatie |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Kolom aliassen, uitbreiden |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Eerste n records op maat |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
Union: met voor waarden |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Koppelen |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Volgende stappen

- Ga door de lessen over het [schrijven van logboek query's in azure monitor](get-started-queries.md).
