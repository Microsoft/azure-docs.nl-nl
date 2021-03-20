---
title: SELECT-component in Azure Cosmos DB
description: Meer informatie over de SQL SELECT-component voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335434"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-component in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Elke query bestaat uit een `SELECT` component en optioneel [van](sql-query-from.md) en [WHERE](sql-query-where.md) -componenten, per ANSI SQL-standaards. Normaal gesp roken wordt de bron in de `FROM` component geïnventariseerd en de `WHERE` component past een filter op de bron toe om een SUBSET van JSON-items op te halen. De `SELECT` component vervolgens projecteert de gevraagde JSON-waarden in de selectie lijst.

## <a name="syntax"></a>Syntaxis

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumenten
  
- `<select_specification>`  

  Eigenschappen of waarde die voor de resultatenset moet worden geselecteerd.  
  
- `'*'`  

  Geeft aan dat de waarde moet worden opgehaald zonder dat er wijzigingen worden aangebracht. Met name als de verwerkte waarde een object is, worden alle eigenschappen opgehaald.  
  
- `<object_property_list>`  
  
  Hiermee geeft u de lijst met eigenschappen op die moeten worden opgehaald. Elke geretourneerde waarde is een object met de opgegeven eigenschappen.  
  
- `VALUE`  

  Hiermee geeft u op dat de JSON-waarde moet worden opgehaald in plaats van het volledige JSON-object. Dit, in tegens telling tot `<property_list>` de geprojecteerde waarde in een object.  

- `DISTINCT`
  
  Hiermee geeft u op dat dubbele waarden van geprojecteerde eigenschappen moeten worden verwijderd.  

- `<scalar_expression>`  

  Expressie die de waarde vertegenwoordigt die moet worden berekend. Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  

## <a name="remarks"></a>Opmerkingen

De `SELECT *` syntaxis is alleen geldig als from-component precies één alias heeft gedeclareerd. `SELECT *` biedt een identiteits projectie. Dit kan nuttig zijn als er geen projectie nodig is. SELECT * is alleen geldig als de component FROM is opgegeven en slechts één invoer bron heeft geïntroduceerd.  
  
Beide `SELECT <select_list>` en `SELECT *` zijn ' syntactische ' en kunnen ook worden aangegeven met behulp van eenvoudige SELECT-instructies, zoals hieronder wordt weer gegeven.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is gelijk aan:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is gelijk aan:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Voorbeelden

In het volgende voor beeld van een SELECT-query wordt een resultaat `address` van de `Families` overeenkomst geretourneerd `id` `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-component](sql-query-where.md)
