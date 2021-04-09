---
title: DateTimeToTicks in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeToTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336330"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Hiermee wordt de opgegeven datum/tijd geconverteerd naar Ticks. Eén tick vertegenwoordigt 100 nano seconden of 1 10-millionth van een seconde. 

## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumenten
  
*Datum/tijd*  
   UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retour typen

Retourneert een ondertekende numerieke waarde, het huidige aantal 100-nano seconden Ticks dat is verstreken sinds de UNIX-epoche. Met andere woorden, DateTimeToTicks retourneert het aantal 100-nano seconden-tikken dat sinds 00:00:00 donderdag, 1 januari 1970, is verstreken.

## <a name="remarks"></a>Opmerkingen

DateTimeDateTimeToTicks wordt geretourneerd `undefined` als de datum/tijd geen geldige ISO 8601-datum is

Deze systeem functie maakt geen gebruik van de index.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld waarin het aantal maten wordt geretourneerd:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Hier volgt een voor beeld waarin het aantal maat streepjes wordt geretourneerd zonder het aantal seconden op te geven:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
