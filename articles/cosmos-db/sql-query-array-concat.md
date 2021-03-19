---
title: ARRAY_CONCAT in Azure Cosmos DB query taal
description: Meer informatie over hoe de SQL-systeem functie van de matrix in Azure Cosmos DB een matrix retourneert die het resultaat is van het samen voegen van twee of meer matrix waarden
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332755"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een matrix die het resultaat is van het samenvoegen van twee of meer matrixwaarden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is een matrix expressie die moet worden samengevoegd met de andere waarden. `ARRAY_CONCAT`Voor de functie zijn ten minste *twee arr_expr* argumenten vereist.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrix expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt uitgelegd hoe u twee matrices kunt samen voegen.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Dit is de resultatenset.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Matrix functies Azure Cosmos DB](sql-query-array-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
