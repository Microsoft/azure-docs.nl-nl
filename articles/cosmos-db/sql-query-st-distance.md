---
title: ST_DISTANCE in Azure Cosmos DB query taal
description: Meer informatie over de functie ST_DISTANCE van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559927"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de afstand tussen de twee geojson Point-, veelhoek-, multiveelhoek-of lines Tring-expressies. Zie het artikel [georuimtelijke en geojson-locatie gegevens](sql-query-geospatial-intro.md) voor meer informatie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een wille keurige geldige geojson Point-, veelhoek-of lines Tring-object expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie met de afstand. Dit wordt uitgedrukt in meters voor het standaard referentie systeem.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u alle familie documenten kunt retour neren die binnen 30 km van de opgegeven locatie vallen met behulp van de `ST_DISTANCE` ingebouwde functie. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Dit is de resultatenset.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie is van belang voor een [georuimtelijke index](index-policy.md#spatial-indexes) , behalve in query's met aggregaties.

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
