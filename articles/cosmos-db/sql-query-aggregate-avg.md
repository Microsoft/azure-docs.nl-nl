---
title: Gem. Azure Cosmos DB query taal
description: Meer informatie over de functie gemiddelde van het SQL-systeem (Gem) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553386"
---
# <a name="avg-azure-cosmos-db"></a>Gem (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze statistische functie retourneert het gemiddelde van de waarden in de expressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt de gemiddelde waarde van `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy). Als argumenten in een `AVG` teken reeks, een Booleaanse waarde of een null-waarde zijn, wordt de hele statistische systeem functie geretourneerd `undefined` . Als een van de argumenten een `undefined` waarde heeft, heeft dit geen invloed op de `AVG` berekening.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies in Azure Cosmos DB](sql-query-system-functions.md)
- [Statistische functies in Azure Cosmos DB](sql-query-aggregate-functions.md)