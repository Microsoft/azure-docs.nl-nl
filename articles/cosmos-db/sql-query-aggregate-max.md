---
title: MAX in Azure Cosmos DB query taal
description: Meer informatie over de maximale (MAX) SQL-systeem functie in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552382"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze statistische functie retourneert het maximum van de waarden in de expressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenten

*scalar_expr*  
   Is een scalaire expressie. 
  
## <a name="return-types"></a>Retour typen
  
Retourneert een scalaire expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt de maximum waarde van `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy). De argumenten in `MAX` kunnen Number, String, Boolean of null zijn. Alle niet-gedefinieerde waarden worden genegeerd.

Bij het vergelijken van verschillende typen gegevens wordt de volgende volg orde van prioriteit gebruikt (in aflopende volg orde):

- tekenreeks
- getal
- booleaans
- null

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies in Azure Cosmos DB](sql-query-system-functions.md)
- [Statistische functies in Azure Cosmos DB](sql-query-aggregate-functions.md)