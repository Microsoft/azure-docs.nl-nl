---
title: PLAFOND in Azure Cosmos DB query taal
description: Meer informatie over hoe de SQL-functie CEILING in Azure Cosmos DB de kleinste gehele waarde retourneert die groter is dan, of gelijk is aan, de opgegeven numerieke expressie.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8c5fdda416aca698b9ad0a68ef050957f32aef31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332403"
---
# <a name="ceiling-azure-cosmos-db"></a>PLAFOND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert het kleinste gehele getal dat groter is dan of gelijk is aan de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden positieve numerieke, negatieve waarden en nulwaarden weer gegeven met de `CEILING` functie.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Dit is de resultatenset.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
