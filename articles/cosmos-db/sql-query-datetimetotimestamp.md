---
title: DateTimeToTimestamp in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeToTimestamp in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 3b1cbd88b4cd6576b2c31fbeb2f3db86309c5ebf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597620"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Hiermee wordt de opgegeven datum/tijd geconverteerd naar een tijds tempel.
  
## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumenten

*Datum/tijd*  
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

## <a name="return-types"></a>Retour typen

Retourneert een ondertekende numerieke waarde, het huidige aantal milliseconden dat is verstreken sinds de UNIX-epoche, dat wil zeggen het aantal milliseconden dat is verstreken sinds 00:00:00 donderdag, 1 januari 1970.

## <a name="remarks"></a>Opmerkingen

DateTimeToTimestamp wordt geretourneerd `undefined` als de opgegeven waarde voor datum/tijd ongeldig is

## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt de datum/tijd geconverteerd naar een tijds tempel:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Hier volgt nog een voorbeeld:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
