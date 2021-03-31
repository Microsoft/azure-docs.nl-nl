---
title: INDEX_OF in Azure Cosmos DB query taal
description: Meer informatie over de functie INDEX_OF van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 228e23cd94f6d903af63e59ba0333d78bd5eacfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341711"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de beginpositie van het eerste exemplaar van de tweede tekenreeksexpressie binnen de eerste opgegeven tekenreeksexpressie, of -1 als de tekenreeks niet is gevonden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de teken reeks expressie die moet worden doorzocht.  
  
*str_expr2*  
   Is de teken reeks expressie waarnaar moet worden gezocht.  

*numeric_expr* Een optionele numerieke expressie waarmee de positie wordt ingesteld waarop de zoek opdracht wordt gestart. De eerste positie in *str_expr1* is 0. 
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de index van de verschillende subtekenreeksen in "ABC" geretourneerd.  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Dit is de resultatenset.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
