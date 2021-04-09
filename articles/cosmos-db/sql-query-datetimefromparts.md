---
title: DateTimeFromParts in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeFromParts in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592088"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert een datum/tijd waarde die is samengesteld op basis van invoer waarden.
  
## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumenten
  
*numberYear* Gehele waarde voor het jaar in de notatie `YYYY`

*numberMonth*  
   Geheel getal voor de maand in de notatie `MM`

*numberDay*  
   Gehele waarde voor de dag in de notatie `DD`

*numberHour* (optioneel) geheel getal voor het uur in de notatie `hh`

*numberMinute* (optioneel) geheel getal voor de minuut in de notatie `mm`

*numberSecond* (optioneel) geheel getal voor de seconde in de notatie `ss`

*numberOfFractionsOfSecond* (optioneel) geheel getal als waarde voor de Fractie van een seconde in de notatie `.fffffff`

## <a name="return-types"></a>Retour typen

Retourneert een UTC-datum en-8601 tijd teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ` waarin:
  
|Indeling|Beschrijving|
|-|-|
|DD|jaar met vier cijfers|
|MM|maand van twee cijfers (01 = januari, etc.)|
|AFSCHRIJVING|2-cijferige dag van de maand (01 tot en met 31)|
|T|de aanzienlijke voor het begin van de tijd elementen|
|hh|twee cijfers per uur (00 tot en met 23)|
|mm|twee cijfers minuten (00 tot en met 59)|
|ss|seconden van twee cijfers (00 tot en met 59)|
|.fffffff|aantal seconden van zeven cijfers|
|Z|UTC (Coordinated Universal Time)|
  
 Zie [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601) voor meer informatie over de ISO 8601-indeling.

## <a name="remarks"></a>Opmerkingen

Als de opgegeven gehele getallen een ongeldige datum/tijd maken, wordt DateTimeFromParts geretourneerd `undefined` .

Als een optioneel argument niet wordt opgegeven, is de waarde ervan 0.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld waarin alleen de vereiste argumenten voor het maken van een datum/tijd worden opgenomen:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Hier volgt nog een voor beeld waarin ook enkele optionele argumenten worden gebruikt voor het maken van een datum/tijd:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Hier volgt nog een voor beeld waarbij ook alle optionele argumenten worden gebruikt voor het maken van een datum/tijd:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
