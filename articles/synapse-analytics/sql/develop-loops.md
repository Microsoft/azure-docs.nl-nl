---
title: T-SQL-lussen gebruiken
description: Tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met Synapse SQL in azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120883"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>T-SQL-lussen gebruiken met Synapse SQL in azure Synapse Analytics

In dit artikel vindt u belang rijke tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met Synapse SQL.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

Synapse SQL ondersteunt de [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) -lus voor het herhaaldelijk uitvoeren van instructie blokken. Deze lus wordt voortgezet zolang de opgegeven voor waarden waar zijn of totdat de code de lus met behulp van het sleutel woord desplit verbreekt. 

Lussen in Synapse SQL zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van het besnelde, alleen-lezen RAS. En lussen zijn dus een geweldig alternatief voor het vervangen van cursors.

## <a name="replace-cursors-in-synapse-sql"></a>Cursors vervangen in Synapse SQL

Voordat u aan de slag gaat, moet u rekening houden met de volgende vraag: ' kan deze cursor opnieuw schrijven voor het gebruik van op sets gebaseerde bewerkingen? ' In veel gevallen is het antwoord ja en is het vaak de beste aanpak. Een op sets gebaseerde bewerking voert vaak sneller uit dan een iteratieve, rij-by-rij-benadering.

Fast Forward-alleen-lezen cursors kunnen eenvoudig worden vervangen door een recursieve constructie. De volgende code is een eenvoudig voor beeld. In dit code voorbeeld worden de statistieken voor elke tabel in de data base bijgewerkt. Door de tabellen in de lus te herhalen, wordt elke opdracht in volg orde uitgevoerd.

Maak eerst een tijdelijke tabel die een uniek rijnummer bevat dat wordt gebruikt voor het identificeren van de afzonderlijke-instructies:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Initialiseer vervolgens de variabelen die vereist zijn voor het uitvoeren van de lus:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Instructies worden nu één voor een per keer uitgevoerd:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

De tijdelijke tabel die in de eerste stap is gemaakt, definitief verwijderen

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.