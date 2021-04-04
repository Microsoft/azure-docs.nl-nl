---
title: DateTimePart in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimePart in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 97eaff405086190b60279ac7d5cf8bf441c8f840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336364"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert de waarde van de opgegeven DateTimePart tussen de opgegeven DateTime.
  
## <a name="syntax"></a>Syntaxis
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumenten
  
*DateTimePart*  
   Het deel van de datum waarvoor de waarde door DateTimePart wordt geretourneerd. Deze tabel bevat alle geldige DateTimePart-argumenten:

| DateTimePart | afkortingen        |
| ------------ | -------------------- |
| Year         | "Year", "yyyy", "JJ" |
| Maand        | "month", "mm", "m"   |
| Dag          | "dag", "dd", "d"     |
| Uur         | "uur", "uu"         |
| Minuut       | ' minuut ', ' mi ', ' n '  |
| Seconde       | "seconde", "SS", "s"  |
| Milliseconde  | ' milliseconde ', ' MS '  |
| Wacht  | "micro seconde", "mcs" |
| Nano seconden   | "nano seconden", "ns"   |

*Datum/tijd*  
   UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retour typen

Retourneert een positieve integerwaarde.

## <a name="remarks"></a>Opmerkingen

DateTimePart wordt `undefined` om de volgende redenen geretourneerd:

- De opgegeven DateTimePart-waarde is ongeldig
- De datum/tijd is geen geldige ISO 8601-datum/tijd

Deze systeem functie maakt geen gebruik van de index.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld van het retour neren van de gehele waarde van de maand:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Hier volgt een voor beeld waarin het aantal micro seconden wordt geretourneerd:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
