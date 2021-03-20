---
title: IS_PRIMITIVE in Azure Cosmos DB query taal
description: Meer informatie over de functie IS_PRIMITIVE van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6006a56fd9a8ed6bcb323aebb4e075fa42a2966e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341872"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een primitieve is (teken reeks, Booleaans, numeriek of null).  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*expressie*  
   Is een expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden objecten gecontroleerd van JSON-Boole, getal, teken reeks, null, object, matrix en niet-gedefinieerde typen met behulp van de `IS_PRIMITIVE` functie.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Dit is de resultatenset.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Type controleren van functies Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
