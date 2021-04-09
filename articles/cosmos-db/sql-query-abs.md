---
title: ABS in Azure Cosmos DB-query taal
description: Meer informatie over hoe de absolute (ABS) functie van SQL-systeem in Azure Cosmos DB de positieve waarde van de opgegeven numerieke expressie retourneert
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b3047c395ce6ef55a4cad8083e98ccfc7105c7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332828"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de absolute (positieve) waarde van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u de resultaten van het gebruik van de `ABS` functie op drie verschillende getallen.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Dit is de resultatenset.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
