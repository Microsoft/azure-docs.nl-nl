---
title: Een query uitvoeren op georuimtelijke gegevens met Azure Cosmos DB
description: Ruimtelijke gegevens opvragen met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336109"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Een query uitvoeren op georuimtelijke gegevens met Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dit artikel bevat informatie over het opvragen van georuimtelijke gegevens in Azure Cosmos DB met behulp van SQL en LINQ. Het opslaan en openen van georuimtelijke gegevens wordt momenteel alleen ondersteund door Azure Cosmos DB SQL-API-accounts. Azure Cosmos DB ondersteunt de volgende Open Geospatial Consortium (OGC) ingebouwde functies voor georuimtelijke query's. Zie voor meer informatie over de volledige set ingebouwde functies in de SQL-taal [query systeem functies in azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Ingebouwde ruimtelijke SQL-functies

Hier volgt een lijst met georuimtelijke systeem functies die nuttig zijn voor het uitvoeren van query's in Azure Cosmos DB:

|**Gebruik**|**Beschrijving**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Retourneert de afstand tussen de twee GeoJSON Point-, Polygon- of LineString-expressies.|
|ST_WITHIN (spatial_expr, spatial_expr) | Retourneert een Booleaanse expressie die aangeeft of het eerste GeoJSON-object (Point, Polygon of LineString) zich bevindt in het tweede GeoJSON-object (punt, Polygon of LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retourneert een Booleaanse expressie die aangeeft of de twee opgegeven GeoJSON-objecten (Point, Polygon of LineString) elkaar snijden.|
|ST_ISVALID| Retourneert een Booleaanse waarde die aangeeft of de opgegeven GeoJSON Point-, Polygon- of LineString-expressie geldig is.|
| ST_ISVALIDDETAILED| Retourneert een JSON-waarde met een Booleaanse waarde als de opgegeven geojson Point-, veelhoek-of lines Tring-expressie geldig is. Als dit ongeldig is, wordt de reden geretourneerd als een teken reeks waarde.|

Ruimtelijke functies kunnen worden gebruikt om nabijheidsquery's uit te voeren op ruimtelijke gegevens. Dit is bijvoorbeeld een query waarmee alle familie documenten worden geretourneerd die binnen 30 km van de opgegeven locatie vallen met behulp van de `ST_DISTANCE` ingebouwde functie.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Als u ruimtelijke indexeringen opneemt in het indexerings beleid, worden ' query's op afstand ' efficiënt bediend via de index. Zie [georuimtelijke indexering](sql-query-geospatial-index.md)voor meer informatie over Spatial indexeren. Als u geen ruimtelijke index voor de opgegeven paden hebt, voert de query een scan uit van de container.

`ST_WITHIN` kan worden gebruikt om te controleren of een punt binnen een veelhoek ligt. Meestal worden veelhoeken gebruikt om grenzen te vertegenwoordigen zoals post codes, status grenzen of natuurlijke vorm. Als u ruimtelijke indexering opneemt in uw indexerings beleid, worden ' in ' query's efficiënt bediend via de index.

Veelhoek argumenten in `ST_WITHIN` kunnen slechts één ring bevatten, dat wil zeggen dat de veelhoeken geen gaten mogen bevatten.

**Query**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Net als bij het werken met niet-overeenkomende typen in Azure Cosmos DB query, als de locatie waarde die is opgegeven in een van beide argumenten onjuist of ongeldig is, wordt geëvalueerd naar niet- **gedefinieerd** en wordt het geëvalueerde document overgeslagen uit de query resultaten. Als uw query geen resultaten retourneert, voert `ST_ISVALIDDETAILED` u uit om de fout op te lossen waarom het ruimtelijke type ongeldig is.
>
>

Azure Cosmos DB biedt ook ondersteuning voor het uitvoeren van inverse query's, dat wil zeggen, u kunt veelhoeken of lijnen indexeren in Azure Cosmos DB en vervolgens een query uitvoeren voor de gebieden die een opgegeven punt bevatten. Dit patroon wordt vaak gebruikt in logistiek om te identificeren, bijvoorbeeld wanneer een truck een aangewezen gebied binnengaat of verlaat.

**Query**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Results**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` en `ST_ISVALIDDETAILED` kan worden gebruikt om te controleren of een ruimtelijk object geldig is. Met de volgende query wordt bijvoorbeeld de geldigheid van een punt met de waarde voor de breedte van een buiten bereik (-132,8) gecontroleerd. `ST_ISVALID` retourneert een Boole-waarde en `ST_ISVALIDDETAILED` retourneert de Boolean en een teken reeks met de reden waarom deze als ongeldig wordt beschouwd.

**Query**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Deze functies kunnen ook worden gebruikt om veelhoeken te valideren. Hier gebruiken we bijvoorbeeld `ST_ISVALIDDETAILED` voor het valideren van een veelhoek die niet is gesloten.

**Query**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-query's in de .NET SDK

De SQL .NET SDK heeft ook betrekking op stub-methoden `Distance()` en `Within()` voor gebruik binnen LINQ-expressies. De SQL LINQ-provider zet deze methode om naar de equivalente SQL ingebouwde functie aanroepen (ST_DISTANCE en ST_WITHIN respectievelijk).

Hier volgt een voor beeld van een LINQ-query waarmee alle documenten in de Azure Cosmos-container worden gevonden waarvan `location` de waarde binnen een straal van 30 km van het opgegeven punt valt met behulp van LINQ.

**LINQ-query voor afstand**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Hier volgt een query voor het zoeken naar alle documenten waarvan `location` de opgegeven Box/veelhoek zich bevindt.

**LINQ-query voor in**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag gaat met georuimtelijke ondersteuning in Azure Cosmos DB, kunt u het volgende doen:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over [georuimtelijke en GEOjson-locatie gegevens in azure Cosmos DB](sql-query-geospatial-intro.md)
* Meer informatie over [ruimtelijke gegevens in index met Azure Cosmos DB](sql-query-geospatial-index.md)
