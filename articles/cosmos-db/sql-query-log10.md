---
title: LOG10 in Azure Cosmos DB query taal
description: Meer informatie over de SQL-functie LOG10 in Azure Cosmos DB om de logaritme met grondtal 10 van de opgegeven numerieke expressie te retour neren
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338425"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de logaritme met grondtal 10 van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expression*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De functies LOG10 en POWER zijn inverse gerelateerd aan elkaar. Bijvoorbeeld: 10 ^ LOG10 (n) = n. Deze systeem functie maakt geen gebruik van de index.
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een variabele gedeclareerd en wordt de waarde LOG10 van de opgegeven variabele (100) geretourneerd.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Dit is de resultatenset.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
