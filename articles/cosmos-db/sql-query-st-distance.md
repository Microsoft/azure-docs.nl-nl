---
title: ST_DISTANCE in Azure Cosmos DB query taal
description: Meer informatie over de functie ST_DISTANCE van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 736cf488efbdc095823a1fa603f191442d907de3
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004463"
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

Deze systeem functie maakt deel uit van een [georuimtelijke index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
