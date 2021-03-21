---
title: GetCurrentTimestamp in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentTimestamp in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524258"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert het aantal milliseconden dat is verstreken sinds 00:00:00 donderdag, 1 januari 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Retour typen
  
Retourneert een ondertekende numerieke waarde, het huidige aantal milliseconden dat is verstreken sinds de UNIX-epoche, dat wil zeggen het aantal milliseconden dat is verstreken sinds 00:00:00 donderdag, 1 januari 1970.

## <a name="remarks"></a>Opmerkingen

GetCurrentTimestamp () is een niet-deterministische functie. Het geretourneerde resultaat is UTC (Coordinated Universal Time).

> [!NOTE]
> Deze systeem functie maakt geen gebruik van de index. Als u waarden wilt vergelijken met de huidige tijd, moet u de huidige tijd ophalen voordat de query wordt uitgevoerd en die constante teken reeks waarde gebruiken in de `WHERE` component.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u de huidige tijds tempel kunt ophalen met behulp van de ingebouwde functie GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Hier volgt een voor beeld van een resultatenset.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
