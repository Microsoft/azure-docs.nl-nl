---
title: EXP in Azure Cosmos DB query taal
description: Meer informatie over de SQL-systeem functie exponent (EXP) in Azure Cosmos DB om de exponentiële waarde van de opgegeven numerieke expressie te retour neren
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335617"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de exponentiële waarde van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De constante **e** (2,718281...) is de basis van natuurlijke logaritmen.  
  
  De exponent van een getal is de constante **e** verheven tot de macht van het getal. Bijvoorbeeld EXP (1.0) = e ^ 1.0 = 2.71828182845905 en EXP (10) = e ^ 10 = 22026.4657948067.  
  
  De exponentiële waarde van de natuurlijke logaritme van een getal is het getal zelf: EXP (LOG (n)) = n. En de natuurlijke logaritme van de exponentiële waarde van een getal is het getal zelf: logboek (EXP (n)) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een variabele gedeclareerd en wordt de exponentiële waarde van de opgegeven variabele (10) geretourneerd.  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Dit is de resultatenset.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 In het volgende voor beeld wordt de exponentiële waarde van de natuurlijke logaritme van 20 en de natuurlijke logaritme van de exponent van 20. Omdat deze functies ingrijpende functies van elkaar zijn, is de retour waarde met afronding voor drijvende-komma berekeningen in beide gevallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Dit is de resultatenset.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
