---
title: Werken met matrices en objecten in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis voor het maken van matrices en objecten in Azure Cosmos DB. Dit artikel bevat ook enkele voor beelden voor het uitvoeren van bewerkingen op Matrix objecten
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493784"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Werken met matrices en objecten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Een belang rijke functie van de Azure Cosmos DB SQL API is matrix en het maken van objecten. In dit document worden voor beelden gebruikt die opnieuw kunnen worden gemaakt met behulp van de [Family-gegevensset](sql-query-getting-started.md#upload-sample-data).

Hier volgt een voor beeld van een item in deze gegevensset:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Matrices

U kunt matrices maken, zoals wordt weer gegeven in het volgende voor beeld:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

U ziet deze uitvoer:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

U kunt ook de [matrix expressie](sql-query-subquery.md#array-expression) gebruiken om een matrix te maken op basis van de resultaten van de [subquery](sql-query-subquery.md) . Met deze query worden alle afzonderlijke namen van onderliggende items in een matrix opgehaald.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

U ziet deze uitvoer:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteratie

De SQL-API biedt ondersteuning voor het herhalen van JSON-matrices, met het [sleutel woord in](sql-query-keywords.md#in) van de bron. In het volgende voorbeeld:

```sql
SELECT *
FROM Families.children
```

U ziet deze uitvoer:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

Met de volgende query wordt iteratie uitgevoerd `children` in de `Families` container. De uitvoer matrix wijkt af van de voor gaande query. In dit voor beeld `children` worden de resultaten gesplitst en afgevlakt in één matrix:  

```sql
SELECT *
FROM c IN Families.children
```

U ziet deze uitvoer:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

U kunt verder filteren op elke afzonderlijke vermelding van de matrix, zoals wordt weer gegeven in het volgende voor beeld:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

U ziet deze uitvoer:

```json
[{
  "givenName": "Lisa"
}]
```

U kunt ook aggregatie over het resultaat van een matrix herhaling. Met de volgende query wordt bijvoorbeeld het aantal onderliggende items van alle families geteld:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

U ziet deze uitvoer:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Wanneer u het sleutel woord IN gebruikt voor iteratie, kunt u geen eigenschappen meer filteren of projecteren buiten de matrix. In plaats daarvan moet u [samen voegingen](sql-query-join.md)gebruiken.

Lees voor meer voor beelden onze [blog post over het werken met matrices in azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Samenvoegingen](sql-query-join.md)
