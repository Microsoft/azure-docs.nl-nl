---
title: ASELECT in Azure Cosmos DB query taal
description: Meer informatie over de functie RAND van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341651"
---
# <a name="rand-azure-cosmos-db"></a>ASELECT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een wille keurig gegenereerde numerieke waarde van [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retour typen

  Retourneert een numerieke expressie.

## <a name="remarks"></a>Opmerkingen

  `RAND` is een niet-deterministische functie. Herhaalde aanroepen van `RAND` worden niet dezelfde resultaten geretourneerd. Deze systeem functie maakt geen gebruik van de index.


## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een wille keurig gegenereerde numerieke waarde geretourneerd.
  
```sql
SELECT RAND() AS rand 
```  
  
 Dit is de resultatenset.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
