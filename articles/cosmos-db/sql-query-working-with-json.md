---
title: Werken met JSON in Azure Cosmos DB
description: Meer informatie over het opvragen en openen van geneste JSON-eigenschappen en het gebruik van speciale tekens in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337826"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Werken met JSON in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In de Azure Cosmos DB SQL (core)-API worden items opgeslagen als JSON. Dit betekent dat het typesysteem en expressies alleen geschikt zijn voor JSON-typen. Zie voor meer informatie de [JSON-specificatie](https://www.json.org/).

We beschrijven een aantal belang rijke aspecten van het werken met JSON:

- JSON-objecten beginnen altijd met een `{` accolade links en eindigen met een `}` rechter accolade
- U kunt JSON-eigenschappen binnen elkaar [nesten](#nested-properties)
- Waarden van JSON-eigenschappen kunnen matrices zijn
- JSON-eigenschapnamen zijn hoofdletter gevoelig
- De naam van de JSON-eigenschap kan een wille keurige teken reeks waarde zijn (inclusief spaties of tekens die geen letters zijn)

## <a name="nested-properties"></a>Geneste eigenschappen

U kunt geneste JSON openen met een punt-accessor. U kunt geneste JSON-eigenschappen in uw query's op dezelfde manier gebruiken als u andere eigenschappen kunt gebruiken.

Hier volgt een document met geneste JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

In dit geval zijn de `state` , `country` en de `city` Eigenschappen genest binnen de `address` eigenschap.

In het volgende voor beeld worden twee geneste eigenschappen geprojecteerd: `f.address.state` en `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Werken met matrices

Naast geneste eigenschappen ondersteunt JSON ook matrices.

Hier volgt een voor beeld van een-document met een matrix:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Wanneer u met matrices werkt, kunt u een specifiek element binnen de matrix openen door te verwijzen naar de positie:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

In de meeste gevallen gebruikt u echter een [subquery](sql-query-subquery.md) of [Self-koppeling](sql-query-join.md) wanneer u met matrices werkt.

Hier ziet u bijvoorbeeld een document waarin het dagelijkse saldo van de Bank rekening van een klant wordt weer gegeven.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Als u een query wilt uitvoeren die alle klanten heeft geleerd die op een bepaald moment een negatief saldo hebben [, kunt u gebruiken met](sql-query-subquery.md#exists-expression) een subquery:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Verschil tussen Null en niet-gedefinieerd

Als een eigenschap niet in een item is gedefinieerd, is de waarde ervan `undefined` . Een eigenschap met de waarde `null` moet expliciet worden gedefinieerd en een `null` waarde toegewezen.

Bekijk bijvoorbeeld dit voor beeld-item:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

In dit voor beeld heeft de eigenschap `isRegistered` een waarde van `undefined` omdat deze wordt wegge laten uit het item. De eigenschap `creationDate` heeft een `null` waarde.

Azure Cosmos DB ondersteunt twee nuttige typen die systeem functies voor `null` en `undefined` Eigenschappen controleren:

* [IS_NULL](sql-query-is-null.md) : controleert of een eigenschaps waarde is `null`
* [IS_DEFINED](sql-query-is-defined.md) : controleert of een eigenschaps waarde is gedefinieerd

U vindt meer informatie over [ondersteunde Opera tors](sql-query-operators.md) en hun gedrag voor `null` en `undefined` waarden.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Gereserveerde sleutel woorden en speciale tekens in JSON

U kunt eigenschappen openen met behulp van de operator voor de geciteerde eigenschap `[]` . Zo is `SELECT c.grade` bijvoorbeeld het equivalent van `SELECT c["grade"]`. Deze syntaxis is handig als u een eigenschap wilt escapepen die spaties, speciale tekens bevat of dezelfde naam heeft als een SQL-tref woord of gereserveerd woord.

Hier ziet u bijvoorbeeld een document met een eigenschap met de naam `order` en een eigenschap `price($)` die speciale tekens bevat:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Als u een query uitvoert die de `order` eigenschap of eigenschap bevat, wordt er `price($)` een syntaxis fout weer gegeven.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Het resultaat is:

`
Syntax error, incorrect syntax near 'order'
`

U moet dezelfde query's opnieuw schrijven zoals hieronder wordt beschreven:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON-expressies

Projectie biedt ook ondersteuning voor JSON-expressies, zoals wordt weer gegeven in het volgende voor beeld:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

In het voor gaande voor beeld `SELECT` moet de component een JSON-object maken en aangezien het voor beeld geen sleutel bevat, gebruikt de component de naam van de impliciete argument variabele `$1` . Met de volgende query worden twee impliciete argument variabelen geretourneerd: `$1` en `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Aliasing

U kunt expliciet alias waarden in query's. Als een query twee eigenschappen met dezelfde naam heeft, gebruikt u aliasing om de naam van een of beide eigenschappen te wijzigen, zodat ze in het verwachte resultaat worden disambiguated.

### <a name="examples"></a>Voorbeelden

Het `AS` tref woord dat wordt gebruikt voor aliasing is optioneel, zoals wordt weer gegeven in het volgende voor beeld bij het projecteren van de tweede waarde als `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing met gereserveerde sleutel woorden of speciale tekens

U kunt aliasing niet gebruiken om een waarde te projecteren als een eigenschaps naam met een spatie, een speciaal teken of een gereserveerd woord. Als u de projectie van een waarde wilt wijzigen in bijvoorbeeld een eigenschaps naam met een spatie, kunt u een [JSON-expressie](#json-expressions)gebruiken.

Hier volgt een voorbeeld:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [WHERE-component](sql-query-where.md)
