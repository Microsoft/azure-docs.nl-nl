---
title: De basis van Azure Cosmos DB query taal
description: Meer informatie over de functie van de FLOOR-SQL-systeem in Azure Cosmos DB om de grootste integer te retour neren die kleiner dan of gelijk aan de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4696b90531b63a01fd4bd9260b24b9af5c6bbd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335600"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert het grootste gehele getal dat kleiner is dan of gelijk is aan de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden positieve numerieke, negatieve waarden en nulwaarden weer gegeven met de `FLOOR` functie.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Dit is de resultatenset.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
