---
title: SOM in Azure Cosmos DB query taal
description: Meer informatie over de SQL-functie Sum (SUM) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552389"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze statistische functie retourneert de som van de waarden in de expressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt de som van `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy). Als argumenten in een `SUM` teken reeks, een Booleaanse waarde of een null-waarde zijn, wordt de hele statistische systeem functie geretourneerd `undefined` . Als een van de argumenten een `undefined` waarde heeft, heeft dit geen invloed op de `SUM` berekening.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies in Azure Cosmos DB](sql-query-system-functions.md)
- [Statistische functies in Azure Cosmos DB](sql-query-aggregate-functions.md)