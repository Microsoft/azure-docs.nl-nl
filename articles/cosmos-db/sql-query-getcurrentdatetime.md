---
title: GetCurrentDateTime in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentDateTime in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587301"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert de huidige UTC-datum en-tijd (Coordinated Universal Time) als een ISO 8601-teken reeks.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Retour typen
  
Retourneert de huidige UTC-datum en-8601 tijd in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ` waar:
  
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

GetCurrentDateTime () is een niet-deterministische functie. Het geretourneerde resultaat is UTC. Nauw keurigheid is 7 cijfers, met een nauw keurigheid van 100 nano seconden.

> [!NOTE]
> Deze systeem functie maakt geen gebruik van de index. Als u waarden wilt vergelijken met de huidige tijd, moet u de huidige tijd ophalen voordat de query wordt uitgevoerd en die constante teken reeks waarde gebruiken in de `WHERE` component.

## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld ziet u hoe u de huidige UTC-datum tijd kunt ophalen met behulp van de ingebouwde functie GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Hier volgt een voor beeld van een resultatenset.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
