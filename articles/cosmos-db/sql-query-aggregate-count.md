---
title: TELLEN in Azure Cosmos DB query taal
description: Meer informatie over de SQL-systeem functie aantal (aantal) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552377"
---
# <a name="count-azure-cosmos-db"></a>AANTAL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze systeem functie retourneert het aantal waarden in de expressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*scalar_expr*  
   Is een scalaire expressie
  
## <a name="return-types"></a>Retour typen
  
Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt het totale aantal items in een container geretourneerd:
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT kan elke scalaire expressie als invoer hebben. De onderstaande query produceert een equivalente resultaat:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy) voor alle eigenschappen in het query filter.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies in Azure Cosmos DB](sql-query-system-functions.md)
- [Statistische functies in Azure Cosmos DB](sql-query-aggregate-functions.md)