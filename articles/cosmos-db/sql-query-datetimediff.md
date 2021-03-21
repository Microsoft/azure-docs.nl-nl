---
title: DateTimeDiff in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeDiff in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595631"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Retourneert het aantal (als een ondertekend geheel getal) van de opgegeven DateTimePart grenzen tussen de opgegeven *begin datum* en *eind datum*.
  
## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>Argumenten
  
*DateTimePart*  
   Het deel van de datum waarop DateTimeAdd een geheel getal toevoegt. Deze tabel bevat alle geldige DateTimePart-argumenten:

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

*Begin*  
    UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ` waarin:
  
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

*EndDate*  
   UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retour typen

Retourneert een ondertekende integerwaarde.

## <a name="remarks"></a>Opmerkingen

DateTimeDiff wordt `undefined` om de volgende redenen geretourneerd:

- De opgegeven DateTimePart-waarde is ongeldig
- Start date of EndDate is geen geldige ISO 8601-datum/tijd

DateTimeDiff retourneert altijd een ondertekende integerwaarde en is een meting van het aantal DateTimePart grenzen dat niet wordt gemeten in het tijds interval.

## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt het aantal dagen tussen en gekruist `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

In het volgende voor beeld wordt het aantal jaar grenzen tussen en gepasseerd `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

In het volgende voor beeld wordt het aantal uur tussen en gekruist `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` . Hoewel deze datum/tijd-waarden meer dan 0,99 uur uit elkaar liggen, `DateTimeDiff` wordt 0 geretourneerd, omdat er geen uurs grenzen werden overschreden.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
