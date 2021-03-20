---
title: Azure Cosmos DB query taal aanmelden
description: Meer informatie over de SQL-functie voor logboek registratie in Azure Cosmos DB om de natuurlijke logaritme van de opgegeven numerieke expressie te retour neren
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 44a9d5b273e13886b0674b3b2e9f5f7a75e72fcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338574"
---
# <a name="log-azure-cosmos-db"></a>LOGBOEK (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de natuurlijke logaritme van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
*base*  
   Een optioneel numeriek argument waarmee de basis voor de logaritme wordt ingesteld.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  Standaard retourneert LOG () de natuurlijke logaritme. U kunt de basis van de logaritme met een andere waarde wijzigen met behulp van de optionele basis parameter.  
  
  De natuurlijke logaritme is de logaritme van het grondtal **e**, waarbij **e** een Irrational-constante is die ongeveer gelijk is aan 2,718281828.  
  
  De natuurlijke logaritme van de exponentiële waarde van een getal is het getal zelf: logboek (EXP (n)) = n. En de exponentiële waarde van de natuurlijke logaritme van een getal is het getal zelf: EXP (LOG (n)) = n.

  Deze systeem functie maakt geen gebruik van de index.
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een variabele gedeclareerd en wordt de logaritmische waarde van de opgegeven variabele (10) geretourneerd.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Dit is de resultatenset.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 In het volgende voor beeld wordt de `LOG` exponent van een getal berekend.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Dit is de resultatenset.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
