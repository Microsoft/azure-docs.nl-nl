---
title: T-SQL-lussen gebruiken
description: Tips voor het ontwikkelen van oplossingen met behulp van T-SQL-lussen en cursors vervangen voor exclusieve SQL-groepen in azure Synapse Analytics.
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
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683219"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>T-SQL-lussen gebruiken voor toegewezen SQL-groepen in azure Synapse Analytics

In dit artikel vindt u tips voor het ontwikkelen van exclusieve SQL-groeps oplossingen met behulp van T-SQL-lussen en het vervangen van cursors.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

Exclusieve SQL-groepen in azure Synapse ondersteunen de [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -lus voor het herhaaldelijk uitvoeren van instructie blokken. Deze lus wordt voortgezet zolang de opgegeven voor waarden waar zijn of totdat de code de lus met behulp van het sleutel woord desplit verbreekt.

Lussen zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van het besnelde, alleen-lezen RAS. En lussen zijn dus een geweldig alternatief voor het vervangen van cursors.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Cursors vervangen in toegewezen SQL-groep

Voordat u eerst aan de slag gaat, moet u echter de volgende vraag stellen: ' kan deze cursor herschrijven om op sets gebaseerde bewerkingen te gebruiken? '

In veel gevallen is het antwoord ja en is het vaak de beste aanpak. Een op sets gebaseerde bewerking voert vaak sneller uit dan een iteratieve, rij-by-rij-benadering.

Fast Forward-alleen-lezen cursors kunnen eenvoudig worden vervangen door een recursieve constructie. Het volgende voor beeld is een eenvoudige. In dit code voorbeeld worden de statistieken voor elke tabel in de data base bijgewerkt. Door de tabellen in de lus te herhalen, wordt elke opdracht in volg orde uitgevoerd.

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

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
