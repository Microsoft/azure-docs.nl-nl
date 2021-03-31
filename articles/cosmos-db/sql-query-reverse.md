---
title: Azure Cosmos DB query taal omkeren
description: Meer informatie over de functie voor het terugdraaien van SQL-systemen in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7d70d65609211ea18f566dbae42aca5231ed2eb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341571"
---
# <a name="reverse-azure-cosmos-db"></a>OMGEKEERD (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een tekenreekswaarde in de omgekeerde volgorde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt gebruiken `REVERSE` in een query.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Dit is de resultatenset.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
