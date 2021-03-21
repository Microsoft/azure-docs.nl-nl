---
title: GetCurrentTicks in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524272"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert het aantal 100 nano seconden-maat streepjes dat sinds 00:00:00 donderdag, 1 januari 1970, is verstreken.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Retour typen

Retourneert een ondertekende numerieke waarde, het huidige aantal 100-nano seconden Ticks dat is verstreken sinds de UNIX-epoche. Met andere woorden, GetCurrentTicks retourneert het aantal nano seconden-maat streepjes van 100 die zijn verstreken sinds 00:00:00 donderdag, 1 januari 1970.

## <a name="remarks"></a>Opmerkingen

GetCurrentTicks () is een niet-deterministische functie. Het geretourneerde resultaat is UTC (Coordinated Universal Time).

> [!NOTE]
> Deze systeem functie maakt geen gebruik van de index. Als u waarden wilt vergelijken met de huidige tijd, moet u de huidige tijd ophalen voordat de query wordt uitgevoerd en die constante teken reeks waarde gebruiken in de `WHERE` component.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld waarin de huidige tijd wordt geretourneerd, gemeten in maat streepjes:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
