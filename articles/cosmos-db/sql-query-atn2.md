---
title: ATN2 in Azure Cosmos DB-query taal
description: Meer informatie over hoe de ATN2-functie van SQL System in Azure Cosmos DB de principal-waarde van de boog tangens van y/x retourneert, uitgedrukt in radialen
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6db42713ec6f0eac64e0f1123825c21a4206fa99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341940"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de principal-waarde van de boog tangens van y/x, uitgedrukt in radialen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de ATN2 voor de opgegeven x-en y-onderdelen berekend.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Dit is de resultatenset.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
