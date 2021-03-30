---
title: RTRIM in Azure Cosmos DB query taal
description: Meer informatie over SQL-systeem functie RTRIM in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0765269fb085c8d4fd8485790772a91c9f2e6373
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341532"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een teken reeks expressie nadat de Volg spaties zijn verwijderd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een geldige teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `RTRIM` in een query kunt gebruiken.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Dit is de resultatenset.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
