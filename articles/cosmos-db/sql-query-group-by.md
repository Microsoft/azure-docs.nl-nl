---
title: GROUP BY-component in Azure Cosmos DB
description: Meer informatie over de GROUP BY-component voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545414"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY-component in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De GROUP BY-component deelt de resultaten van de query op basis van de waarden van een of meer opgegeven eigenschappen.

## <a name="syntax"></a>Syntaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenten

- `<scalar_expression_list>`

   Hiermee geeft u de expressies op die worden gebruikt voor het delen van query resultaten.

- `<scalar_expression>`
  
   Elke scalaire expressie is toegestaan, met uitzonde ring van scalaire subquery's en scalaire aggregaties. Elke scalaire expressie moet ten minste één eigenschaps verwijzing bevatten. Er is geen limiet voor het aantal afzonderlijke expressies of de kardinaliteit van elke expressie.

## <a name="remarks"></a>Opmerkingen
  
  Wanneer een query gebruikmaakt van een GROUP BY-component, kan de SELECT-component alleen de subset van eigenschappen en systeem functies bevatten die zijn opgenomen in de component GROUP BY. Een uitzonde ring is een [statistische functie](sql-query-aggregate-functions.md)die kan worden weer gegeven in de component SELECT zonder dat deze is opgenomen in de component Group by. U kunt ook letterlijke waarden in de component SELECT altijd insluiten.

  De component GROUP BY moet na de component SELECT, FROM en WHERE en vóór de component OFFSET LIMIT staan. U kunt GROUP BY momenteel niet gebruiken met een ORDER BY-component, maar dit is gepland.

  De GROUP BY-component staat niet toe dat een van de volgende:
  
- Alias eigenschappen of aliasing systeem functies (aliasing is nog steeds toegestaan in de component SELECT)
- Subquery's
- Statistische systeem functies (deze zijn alleen toegestaan in de component SELECT)

Query's met een statistische systeem functie en een subquery met `GROUP BY` worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Voorbeelden

Deze voor beelden gebruiken de voedings gegevens die beschikbaar zijn via de [Azure Cosmos DB query Playground](https://www.documentdb.com/sql/demo).

Dit is bijvoorbeeld een query die het totale aantal items in elke foodGroup retourneert:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Sommige resultaten zijn (bovenste sleutel woord wordt gebruikt om de resultaten te beperken):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Deze query heeft twee expressies die worden gebruikt om de resultaten te delen:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Enkele resultaten zijn:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Deze query heeft een systeem functie in de GROUP BY-component:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Enkele resultaten zijn:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Deze query gebruikt zowel tref woorden als systeem functies in de expressie van de item eigenschap:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

U ziet deze uitvoer:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [Statistische functies](sql-query-aggregate-functions.md)
