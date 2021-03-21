---
title: ST_INTERSECTS in Azure Cosmos DB query taal
description: Meer informatie over de functie ST_INTERSECTS van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c123446c7d7f654f0e3ace6c9d92983558509c75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559952"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een booleaanse expressie die aangeeft of het geojson-object (Point, veelhoek, multiveelhoek of lines Tring) dat is opgegeven in het eerste argument, de geojson (Point, veelhoek, multiveelhoek of lines Tring) in het tweede argument INTERSECT.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een geojson Point-, veelhoek-of lines Tring-object expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u alle gebieden met de opgegeven veelhoek kunt vinden.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Dit is de resultatenset.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie is van belang voor een [georuimtelijke index](index-policy.md#spatial-indexes) , behalve in query's met aggregaties.

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
