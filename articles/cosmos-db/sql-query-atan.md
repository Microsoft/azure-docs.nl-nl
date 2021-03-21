---
title: BOOGTAN in Azure Cosmos DB query taal
description: Meer informatie over hoe de arctangens (BOOGTAN) SQL-systeem functie in Azure Cosmos DB de hoek in radialen retourneert, waarvan de tangens de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50c678ad3f382ebdcf23c0ba125a47d7aaa7d7ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332437"
---
# <a name="atan-azure-cosmos-db"></a>BOOGTAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de hoek, in radialen, waarvan de tangens de opgegeven numerieke expressie is. Dit wordt ook wel de arctangens genoemd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de `ATAN` van de opgegeven waarde geretourneerd.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Dit is de resultatenset.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
