---
title: EndsWith in Azure Cosmos DB-query taal
description: Meer informatie over de functie ENDSWITH SQL System in Azure Cosmos DB om een Booleaanse waarde te retour neren die aangeeft of de eerste teken reeks expressie eindigt met de tweede
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338829"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie eindigt op de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een teken reeks expressie.  
  
*str_expr2*  
   Is een teken reeks expressie die moet worden vergeleken met het einde van *str_expr1*.

*bool_expr* Optionele waarde voor het negeren van case. Als deze eigenschap is ingesteld op True, wordt in ENDSWITH een hoofdletter gevoelige zoek opdracht uitgevoerd. Indien niet opgegeven, is deze waarde false.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt gecontroleerd of de teken reeks "ABC" eindigt op "b" en "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Dit is de resultatenset.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

Het gebruik van de EndsWith wordt verhoogd naarmate de kardinaliteit van de eigenschap in de systeem functie toeneemt. Met andere woorden, als u controleert of een eigenschaps waarde eindigt met een bepaalde teken reeks, zijn de kosten voor de query-RU afhankelijk van het aantal mogelijke waarden voor die eigenschap.

Denk bijvoorbeeld aan twee eigenschappen: stad en land. De kardinaliteit van de stad is 5.000 en de kardinaliteit van het land is 200. Hier volgen twee voor beelden van query's:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

De eerste query gebruikt waarschijnlijk meer RUs dan de tweede query, omdat de kardinaliteit van de stad hoger is dan het land.

Als de grootte van de eigenschap in EndsWith groter is dan 1 KB voor sommige documenten, moet deze documenten worden geladen met de query-engine. In dit geval kan de query-engine EndsWith niet volledig evalueren met een index. De RU-kosten voor EndsWith zijn hoog als u een groot aantal documenten hebt met een eigenschaps grootte van meer dan 1 KB.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
