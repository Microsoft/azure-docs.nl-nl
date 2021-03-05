---
title: SQL-tref woorden voor Azure Cosmos DB
description: Meer informatie over SQL-tref woorden voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 1f3c4ef56feb77e9b01375b8b5dbdb567f5bfadb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179966"
---
# <a name="keywords-in-azure-cosmos-db"></a>Tref woorden in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden tref woorden beschreven die kunnen worden gebruikt in Azure Cosmos DB SQL-query's.

## <a name="between"></a>BETWEEN

U kunt het `BETWEEN` sleutel woord gebruiken om query's uit te drukken op bereiken van teken reeks-of numerieke waarden. Met de volgende query worden bijvoorbeeld alle items geretourneerd waarin de categorie van het eerste kind 1-5 is, inclusief.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

U kunt ook het `BETWEEN` sleutel woord in de `SELECT` -component gebruiken, zoals in het volgende voor beeld.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In de SQL-API, in tegens telling tot ANSI SQL, kunt u de bereik query's expliciet op Eigenschappen van gemengde typen uitvoeren. Dit kan bijvoorbeeld `grade` een getal zijn zoals `5` in sommige items en een teken reeks zoals `grade4` in anderen. In deze gevallen, zoals in Java script, resulteert de vergelijking tussen de twee verschillende typen in `Undefined` , zodat het item wordt overgeslagen.

## <a name="distinct"></a>DISTINCT

Het `DISTINCT` sleutel woord elimineert dubbele waarden in de projectie van de query.

In dit voor beeld worden de waarden van de query projecten voor elke achternaam:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

U ziet deze uitvoer:

```json
[
    "Andersen"
]
```

U kunt ook unieke objecten projecteren. In dit geval bestaat het veld lastName niet in een van de twee documenten, dus retourneert de query een leeg object.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

U ziet deze uitvoer:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` kan ook worden gebruikt in de projectie binnen een subquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Deze query projecteert een matrix die de OpgegevenNaam van elk kind bevat met dubbele items verwijderd. Deze matrix heeft een alias als ChildNames en geprojecteerd in de buitenste query.

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

Query's met een statistische systeem functie en een subquery met `DISTINCT` worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>ZO

Retourneert een Booleaanse waarde, afhankelijk van het feit of een specifieke teken reeks overeenkomt met een opgegeven patroon. Een patroon kan bestaan uit gewone tekens en Joker tekens. U kunt logische equivalente query's schrijven met behulp van het `LIKE` sleutel woord of de systeem functie [RegexMatch](sql-query-regexmatch.md) . U ziet hetzelfde index gebruik, ongeacht wat u kiest. Daarom moet u gebruiken `LIKE` Als u de syntaxis meer dan reguliere expressies gebruikt.

> [!NOTE]
> Omdat `LIKE` kan gebruikmaken van een index, moet u [een bereik index maken](./index-policy.md) voor eigenschappen die u vergelijkt met `LIKE` .

U kunt de volgende joker tekens gebruiken, bijvoorbeeld:

| Joker teken | Beschrijving                                                  | Voorbeeld                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Een wille keurige teken reeks van nul of meer tekens                      | WHERE c. beschrijving als '% SO% PS% '      |
| _ (onderstrepings teken)     | Eén wille keurig teken                                       | WHERE c. beschrijving als% SO_PS%      |
| [ ]                  | Eén wille keurig teken binnen het opgegeven bereik ([a-f]) of set ([abcdef]). | WHERE c. beschrijving als '% SO [t-z] PS% '  |
| [^]                  | Eén wille keurig teken niet binnen het opgegeven bereik ([^ a-f]) of set ([^ abcdef]). | WHERE c. beschrijving zoals '% SO [^ ABC] PS% ' |


### <a name="using-like-with-the--wildcard-character"></a>Gebruiken als met het Joker teken%

Het `%` teken komt overeen met een wille keurige teken reeks van nul of meer tekens. Als u bijvoorbeeld een `%` aan het begin en het einde van het patroon plaatst, retourneert de volgende query alle items met een beschrijving die bevat `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Als u alleen een `%` teken aan het einde van het patroon gebruikt, retourneert u alleen items met een beschrijving die is gestart met `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Gebruiken niet als

In het onderstaande voor beeld worden alle items geretourneerd met een beschrijving die niet de `fruit` volgende bevat:

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>De Escape-component gebruiken

U kunt zoeken naar patronen die een of meer joker tekens bevatten met de ESCAPE-component. Als u bijvoorbeeld wilt zoeken naar beschrijvingen waarin de teken reeks `20-30%` is opgenomen, wilt u het niet `%` als Joker teken interpreteren.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Joker tekens gebruiken als letterlijke waarden

U kunt joker tekens tussen vier Kante haken plaatsen om ze als letterlijke tekens te behandelen. Wanneer u een Joker teken tussen vier Kante haken plaatst, verwijdert u alle speciale kenmerken. Hier volgen enkele voorbeelden:

| Patroon           | Betekenis |
| ----------------- | ------- |
| ZOALS ' 20-30 [%] ' | 20-30%  |
| LIKE "[_] n"     | _n      |
| LIKE "[[]"    | [       |
| ZOALS '] '        | ]       |

## <a name="in"></a>IN

Gebruik het sleutel woord IN om te controleren of een opgegeven waarde overeenkomt met een wille keurige waarde in een lijst. De volgende query retourneert bijvoorbeeld alle familie-items waarbij de `id` is `WakefieldFamily` of `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

In het volgende voor beeld worden alle items geretourneerd waarbij de status een van de opgegeven waarden is:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

De SQL-API biedt ondersteuning voor het [herhalen van JSON-matrices](sql-query-object-array.md#Iteration), waarbij een nieuwe constructie wordt toegevoegd via het sleutel woord in in de bron van.

Als u de partitie sleutel in het filter opneemt `IN` , wordt de query automatisch gefilterd op de relevante partities.

## <a name="top"></a>Boven

Met het sleutel woord TOP wordt het eerste `N` aantal query resultaten in een niet-gedefinieerde volg orde geretourneerd. Als best practice gebruikt u TOP met de- `ORDER BY` component om de resultaten te beperken tot het eerste `N` aantal geordende waarden. Het combi neren van deze twee componenten is de enige manier om te zoals verwacht geven op welke rijen het bovenste effect heeft.

U kunt TOP gebruiken met een constante waarde, zoals in het volgende voor beeld of met een variabele waarde met behulp van query's met para meters.

```sql
    SELECT TOP 1 *
    FROM Families f
```

U ziet deze uitvoer:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [Samenvoegingen](sql-query-join.md)
- [Subquery's](sql-query-subquery.md)
