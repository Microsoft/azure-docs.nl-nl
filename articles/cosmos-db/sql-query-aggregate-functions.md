---
title: Statistische functies in Azure Cosmos DB
description: Meer informatie over de syntaxis van de statistische SQL-functie, typen statistische functies die door Azure Cosmos DB worden ondersteund.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553385"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Statistische functies in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Statistische functies voeren een berekening uit op een set waarden in de `SELECT` component en retour neren een enkele waarde. Met de volgende query wordt bijvoorbeeld het aantal items in een container geretourneerd:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Typen statistische functies

De SQL-API biedt ondersteuning voor de volgende statistische functies. `SUM` en werken met `AVG` numerieke waarden, en `COUNT` , `MIN` en `MAX` werk op getallen, teken reeksen, Boole-tekens en nullen.

| Functie | Beschrijving |
|-------|-------------|
| [GEMIDDELD](sql-query-aggregate-avg.md) | Retourneert het gemiddelde van de waarden in de expressie. |
| [COUNT](sql-query-aggregate-count.md) | Retourneert het aantal items in de expressie. |
| [MAX](sql-query-aggregate-max.md) | Retourneert de maximumwaarde in de expressie. |
| [MIN](sql-query-aggregate-min.md) | Retourneert de minimumwaarde in de expressie. |
| [SUM](sql-query-aggregate-sum.md) | Retourneert de som van alle waarden in de expressie. |


U kunt ook alleen de scalaire waarde van het aggregatie retour neren met behulp van het sleutel woord VALUE. De volgende query retourneert bijvoorbeeld het aantal waarden als één getal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

U ziet deze uitvoer:

```json
    [ 2 ]
```

U kunt ook aggregaties met filters combi neren. Met de volgende query wordt bijvoorbeeld het aantal items met de adres status van weer gegeven `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

U ziet deze uitvoer:

```json
    [ 1 ]
```

## <a name="remarks"></a>Opmerkingen

Deze statistische systeem functies profiteren van een [bereik index](index-policy.md#includeexclude-strategy). Als u een,,, `AVG` `COUNT` `MAX` `MIN` of `SUM` op een eigenschap verwacht, moet u [het relevante pad in het indexerings beleid toevoegen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeemfuncties](sql-query-system-functions.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)