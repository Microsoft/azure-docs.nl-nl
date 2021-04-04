---
title: LTRIM in Azure Cosmos DB query taal
description: Meer informatie over de functie LTRIM SQL System in Azure Cosmos DB om een teken reeks expressie te retour neren nadat er voorloop spaties zijn verwijderd
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dec9bed0cae503825397920ef8e305c125f43154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335582"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een tekenreeksexpressie na het verwijderen van lege voorlooptekens.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `LTRIM` in een query kunt gebruiken.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Dit is de resultatenset.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
