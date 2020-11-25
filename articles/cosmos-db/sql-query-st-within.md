---
title: ST_WITHIN in Azure Cosmos DB query taal
description: Meer informatie over de functie ST_WITHIN van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e808f6f56041e6cdc577164140910af8d6c42731
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004395"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een Boole-expressie die aangeeft of het geojson-object (Point, veelhoek, multiveelhoek of lines Tring) dat is opgegeven in het eerste argument zich binnen de geojson (Point, veelhoek, multiveelhoek of lines Tring) bevindt in het tweede argument.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een geojson Point-, veelhoek-of lines Tring-object expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u alle familie documenten in een veelhoek kunt vinden met `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Dit is de resultatenset.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [georuimtelijke index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
