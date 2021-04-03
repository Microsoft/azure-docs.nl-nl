---
title: SQL-subquery's voor Azure Cosmos DB
description: Meer informatie over SQL-subquery's en de meest voorkomende use-cases en verschillende typen subquery's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340852"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Voor beelden van SQL-subquery voor Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Een subquery is een query die is genest in een andere query. Een subquery wordt ook wel een binnenste query of binnenste selectie genoemd. De instructie die een subquery bevat, wordt meestal een buitenste query genoemd.

In dit artikel worden SQL-subquery's en de veelvoorkomende use cases in Azure Cosmos DB beschreven. Alle voorbeeld query's in dit document kunnen worden uitgevoerd op een voedings gegevensset die vooraf is geladen op de [Azure Cosmos DB query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typen subquery's

Er zijn twee hoofd typen subquery's:

* **Gecorreleerd**: een subquery die verwijst naar waarden van de buitenste query. De subquery wordt eenmaal geëvalueerd voor elke rij die de buitenste query processen uitvoert.
* **Niet-correled**: een subquery die onafhankelijk is van de buitenste query. Het kan zelfstandig worden uitgevoerd, zonder dat hiervoor de buitenste query nodig is.

> [!NOTE]
> Azure Cosmos DB ondersteunt alleen gecorreleerde subquery's.

Subquery's kunnen verder worden geclassificeerd op basis van het aantal rijen en kolommen dat ze retour neren. Er zijn drie typen:
* **Tabel**: retourneert meerdere rijen en meerdere kolommen.
* **Meerdere waarden**: retourneert meerdere rijen en één kolom.
* **Scalair**: retourneert één rij en één kolom.

SQL-query's in Azure Cosmos DB retour neren altijd één kolom (een eenvoudige waarde of een complex document). Daarom zijn alleen multi-waarden en scalaire subquery's van toepassing op Azure Cosmos DB. U kunt een subquery met meerdere waarden alleen in de component FROM als een relationele expressie gebruiken. U kunt een scalaire subquery gebruiken als scalaire expressie in de component SELECT of WHERE, of als een relationele expressie in de component FROM.

## <a name="multi-value-subqueries"></a>Subquery's met meerdere waarden

Subquery's met meerdere waarden retour neren een set documenten en worden altijd gebruikt in de component FROM. Ze worden gebruikt voor:

* DEELNAME expressies optimaliseren. 
* Het evalueren van dure expressies eenmaal en het verwijzen naar meerdere keren.

## <a name="optimize-join-expressions"></a>KOPPELINGs expressies optimaliseren

Subquery's voor meerdere waarden kunnen samenvoegings expressies optimaliseren door predikaten te pushen na elke Select-veel'-expressie in plaats van alle kruis koppelingen in de component WHERE.

Laten we nu eens naar deze query kijken:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Voor deze query komt de index overeen met een document met een tag met de naam ' zuigeling Formula '. Het is een nutriënt item met een waarde tussen 0 en 10 en een onderhouds item met een bedrag dat groter is dan 1. De expressie voor samen voegen voert het cross-product uit van alle items met tags, nutriënten en zorgt voor matrices voor elk overeenkomend document voordat een filter wordt toegepast. 

Vervolgens wordt het filter predicaat toegepast op elke <c, t, n, s> tuple. Als bijvoorbeeld een overeenkomend document 10 items in elk van de drie matrices had, wordt het uitgevouwen tot 1 x 10 x 10 x 10 (1.000) Tuples. U kunt subquerys hier gebruiken om gekoppelde matrix items te filteren voordat u met de volgende expressie samenvoegt.

Deze query is gelijk aan de voor gaande, maar gebruikt subquery's:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Stel dat er slechts één item in de matrix Tags overeenkomt met het filter en dat er vijf items zijn voor beide nutriënten en dat er matrices worden gereserveerd. De samenvoegings expressies worden vervolgens uitgebreid tot 1 x 1 x 5 x 5 = 25 items, in tegens telling tot 1.000 items in de eerste query.

## <a name="evaluate-once-and-reference-many-times"></a>Eenmaal en een verwijzing naar een aantal keren evalueren

Subquery's kunnen helpen bij het optimaliseren van query's met dure expressies zoals door de gebruiker gedefinieerde functies (Udf's), complexe teken reeksen of reken kundige expressies. U kunt een subquery samen met een JOINEXPRESSIE expressie gebruiken om de expressie één keer te evalueren, maar hiernaar te verwijzen.

Met de volgende query wordt de UDF `GetMaxNutritionValue` twee keer uitgevoerd:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Hier volgt een gelijkwaardige query die de UDF slechts eenmaal uitvoert:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Let op het cross-product gedrag van expressies voor samen voegen. Als de UDF-expressie kan worden geëvalueerd als niet-gedefinieerd, moet u ervoor zorgen dat de expressie voor samen voegen altijd één rij produceert door een object uit de subquery te retour neren in plaats van de waarde rechtstreeks.
>

Hier volgt een vergelijkbaar voor beeld waarin een object wordt geretourneerd in plaats van een waarde:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

De methode is niet beperkt tot Udf's. Dit is van toepassing op een mogelijke dure expressie. U kunt bijvoorbeeld dezelfde benadering volgen met de wiskundige functie `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Deelname met externe referentie gegevens simuleren

Mogelijk moet u vaak verwijzen naar statische gegevens die zelden worden gewijzigd, zoals maat eenheden of land codes. Het is beter om dergelijke gegevens niet te dupliceren voor elk document. Voor komen dat deze duplicatie wordt bespaard op opslag en de schrijf prestaties verbeteren door de document grootte kleiner te houden. U kunt een subquery gebruiken om de interne join-semantiek te simuleren met een verzameling referentie gegevens.

Denk bijvoorbeeld aan deze set referentie gegevens:

| **Eenheid** | **Naam**            | **Vermenigvuldig** | **Basis eenheid** |
| -------- | ------------------- | -------------- | ------------- |
| lopen       | Nanogram            | 1,00 e-09       | Gram          |
| Fe       | Microgram           | 1,00 e-06       | Gram          |
| mg       | Nauw           | 1,00 e-03       | Gram          |
| g        | Gram                | 1,00 e + 00       | Gram          |
| kilo       | Kilo            | 1,00 e + 03       | Gram          |
| Mg       | Megagram            | 1,00 e + 06       | Gram          |
| GG       | Gigagram            | 1,00 e + 09       | Gram          |
| Één       | Nanojoule           | 1,00 e-09       | Joule         |
| µJ       | Microjoule          | 1,00 e-06       | Joule         |
| mJ       | Millijoule          | 1,00 e-03       | Joule         |
| J        | Joule               | 1,00 e + 00       | Joule         |
| kJ       | Kilojoule           | 1,00 e + 03       | Joule         |
| MJ       | Megajoule           | 1,00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1,00 e + 09       | Joule         |
| overeenkomst      | Calorie             | 1,00 e + 00       | calorie       |
| kcal     | Calorie             | 1,00 e + 03       | calorie       |
| IE       | Internationale eenheden |                |               |


De volgende query imiteert de samen voeging met deze gegevens, zodat u de naam van de eenheid toevoegt aan de uitvoer:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Scalaire subquery's

Een scalaire subquery-expressie is een subquery die resulteert in een enkele waarde. De waarde van de scalaire subquery-expressie is de waarde van de projectie (SELECT-component) van de subquery.  U kunt een scalaire subquery-expressie gebruiken op veel plaatsen waar een scalaire expressie geldig is. U kunt bijvoorbeeld een scalaire subquery in een expressie in de component SELECT en WHERE gebruiken.

Het gebruik van een scalaire subquery helpt echter niet altijd te optimaliseren. Het door geven van een scalaire subquery als argument aan een systeem of door de gebruiker gedefinieerde functies biedt bijvoorbeeld geen voor deel in het gebruik van resource-eenheden (RU) of latentie.

Scalaire subquery's kunnen verder worden geclassificeerd als:
* Eenvoudige expressies scalaire subquery's
* Cumulatieve scalaire subquery's

## <a name="simple-expression-scalar-subqueries"></a>Eenvoudige expressies scalaire subquery's

Een scalaire subquery met eenvoudige expressie is een gecorreleerde subquery met een SELECT-component die geen statistische expressies bevat. Deze subquery's bieden geen optimalisatie voordelen omdat de compiler deze converteert naar een grotere eenvoudige expressie. Er is geen gecorreleerde context tussen de binnenste en de buitenste query's.

Hier volgen enkele voor beelden:

**Voorbeeld 1**

```sql
SELECT 1 AS a, 2 AS b
```

U kunt deze query opnieuw schrijven met behulp van een scalaire, eenvoudige expressie, om het volgende te doen:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Beide query's produceren deze uitvoer:

```json
[
  { "a": 1, "b": 2 }
]
```

**Voorbeeld 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

U kunt deze query opnieuw schrijven met behulp van een scalaire, eenvoudige expressie, om het volgende te doen:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Query uitvoer:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Voorbeeld 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

U kunt deze query opnieuw schrijven met behulp van een scalaire, eenvoudige expressie, om het volgende te doen:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Query uitvoer:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Cumulatieve scalaire subquery's

Een gecombineerde scalaire subquery is een subquery met een statistische functie in de projectie of het filter waarmee een enkele waarde wordt geëvalueerd.

**Voor beeld 1:**

Hier volgt een subquery met één statistische functie-expressie in de projectie:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Query uitvoer:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Voorbeeld 2**

Hier volgt een subquery met meerdere statistische functie-expressies:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Query uitvoer:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Voorbeeld 3**

Hier volgt een query met een statistische subquery in zowel de projectie als het filter:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Query uitvoer:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Een optimale manier om deze query te schrijven, is om lid te worden van de subquery en om te verwijzen naar de subquery-alias in de SELECT-en WHERE-componenten. Deze query is efficiënter omdat u de subquery alleen in de instructie samen voegen moet uitvoeren en niet in de projectie en het filter.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTs-expressie

Azure Cosmos DB ondersteunt bestaande expressies. Dit is een cumulatieve scalaire subquery die is ingebouwd in de Azure Cosmos DB SQL-API. EXISTs is een booleaanse expressie die een subquery-expressie aanneemt en retourneert True als de subquery rijen retourneert. Anders wordt false geretourneerd.

Omdat de Azure Cosmos DB SQL-API geen onderscheid maakt tussen Boole-expressies en andere scalaire expressies, kunt u gebruiken in zowel SELECT-als WHERE-componenten. Dit is in tegens telling tot T-SQL, waarbij een booleaanse expressie (bijvoorbeeld bestaat, tussen en IN) is beperkt tot het filter.

Als de EXISTs-subquery een enkelvoudige waarde retourneert die niet is gedefinieerd, wordt door berekend naar onwaar. Bekijk bijvoorbeeld de volgende query die als onwaar resulteert:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Als het sleutel woord VALUE in de voor gaande subquery wordt wegge laten, wordt de query geëvalueerd als True:
```sql
SELECT EXISTS (SELECT undefined) 
```

De subquery plaatst de lijst met waarden in de geselecteerde lijst in een-object. Als de geselecteerde lijst geen waarden bevat, retourneert de subquery de enkele waarde ' {} '. Deze waarde is gedefinieerd, dus resulteert in waar.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Voor beeld: ARRAY_CONTAINS herschrijven en samen voegen als bestaan

Een veelgebruikte use-case van ARRAY_CONTAINS is het filteren van een document door het bestaan van een item in een matrix. In dit geval controleren we of de labels matrix een item met de naam ' oranje ' bevat.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

U kunt dezelfde query die moet worden gebruikt, opnieuw schrijven:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Daarnaast kan ARRAY_CONTAINS alleen controleren of een waarde gelijk is aan een wille keurig element in een matrix. Als u complexere filters voor matrix eigenschappen nodig hebt, gebruikt u samen voegen.

Bekijk de volgende query die op basis van de eenheden en `nutritionValue` Eigenschappen in de matrix filtert: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Voor elk van de documenten in de verzameling wordt een kruis product uitgevoerd met de bijbehorende matrix elementen. Met deze KOPPELINGs bewerking kunt u filteren op Eigenschappen in de matrix. Het gebruik van deze query is echter aanzienlijk. Als bijvoorbeeld 1.000 documenten in elke matrix 100 items bevatten, wordt deze uitgebreid tot 1.000 x 100 (dat wil zeggen, 100.000) Tuples.

Met behulp van EXISTs kunt u dit kost bare cross-product voor komen:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In dit geval filtert u op matrix elementen binnen de subquery EXISTs. Als een matrix element overeenkomt met het filter, wordt het door project en EXISTs geëvalueerd als waar.

U kunt ook een alias bestaande en ernaar verwijzen in de projectie:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Query uitvoer:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>MATRIX expressie

U kunt de matrix expressie gebruiken om de resultaten van een query als een matrix te projecteren. U kunt deze expressie alleen gebruiken binnen de component SELECT van de query.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Query uitvoer:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Net als bij andere subquery's zijn er filters met de matrix expressie mogelijk.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Query uitvoer:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Matrix expressies kunnen ook worden opgehaald na de component FROM in subquery's.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Query uitvoer:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
