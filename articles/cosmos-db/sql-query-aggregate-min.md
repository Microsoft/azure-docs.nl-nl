---
title: MIN in Azure Cosmos DB query taal
description: Meer informatie over de SQL-systeem functie min (MIN) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553380"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze statistische functie retourneert de minimum waarde van de waarden in de expressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*scalar_expr*  
   Is een scalaire expressie. 
  
## <a name="return-types"></a>Retour typen
  
Retourneert een scalaire expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt de minimum waarde van `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy). De argumenten in `MIN` kunnen Number, String, Boolean of null zijn. Alle niet-gedefinieerde waarden worden genegeerd.

Bij het vergelijken van verschillende typen gegevens wordt de volgende volg orde van prioriteit gebruikt (in oplopende volg orde):

- null
- booleaans
- getal
- tekenreeks

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies in Azure Cosmos DB](sql-query-system-functions.md)
- [Statistische functies in Azure Cosmos DB](sql-query-aggregate-functions.md)