---
title: Georuimtelijke gegevens indexeren met Azure Cosmos DB
description: Ruimtelijke gegevens indexeren met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336211"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Georuimtelijke gegevens indexeren met Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De data base-engine van Azure Cosmos DB is ontworpen om echt schema neutraal te zijn en biedt ondersteuning voor de eerste klasse voor JSON. De geoptimaliseerde data base-engine voor schrijven van Azure Cosmos DB heeft in systeem eigen inzicht in de ruimtelijke gegevens die in de geojson-standaard worden weer gegeven.

In een kort gezegd wordt de geometrie geprojecteerd van Geodetic-coördinaten naar een 2D-vlak en vervolgens geleidelijk onderverdeeld in cellen met behulp van een **quadtree**. Deze cellen worden toegewezen aan 1D op basis van de locatie van de cel binnen een **Hilbert ruimte** die de lokale positie van punten behoudt. Wanneer locatie gegevens worden geïndexeerd, wordt er ook een proces met de naam **mozaïek patroon** genoemd, dat wil zeggen dat alle cellen die een locatie overlappen worden geïdentificeerd en opgeslagen als sleutels in de Azure Cosmos DB index. Op het moment dat de query is uitgevoerd, worden argumenten zoals punten en veelhoeken ook tessellated om de relevante celbereiken te extra heren. vervolgens wordt gebruikt om gegevens uit de index op te halen.

Als u een indexerings beleid opgeeft dat een ruimtelijke index voor `/*` (alle paden) bevat, worden alle gegevens die in de container zijn gevonden, geïndexeerd voor efficiënte ruimtelijke query's.

> [!NOTE]
> Azure Cosmos DB ondersteunt het indexeren van punten, line strings toe, veelhoeken en multiveelhoeken. Als u een van deze typen indexeert, worden alle andere typen automatisch geïndexeerd. Met andere woorden, als u veelhoeken indexeert, zullen we ook punten, line strings toe en multiveelhoeken indexeren. Het indexeren van een nieuw ruimtelijk type heeft geen invloed op de Write RU-kosten of index grootte, tenzij u geldige geojson-gegevens van dat type hebt.

## <a name="modifying-geospatial-configuration"></a>Georuimtelijke configuratie wijzigen

In uw container geeft de **georuimtelijke configuratie** aan hoe de ruimtelijke gegevens worden geïndexeerd. Geef één **georuimtelijke configuratie** per container op: Geografie of geometrie.

U kunt scha kelen tussen het ruimtelijke type **geografie** en **geometrie** in de Azure Portal. Het is belang rijk dat u een [geldig indexerings beleid voor ruimtelijke geometrie met een selectie kader](#geometry-data-indexing-examples) maakt voordat u overschakelt naar het ruimtelijke type geometrie.

U kunt als volgt de **georuimtelijke configuratie** instellen in **Data Explorer** in het Azure portal:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Georuimtelijke configuratie instellen":::

U kunt ook de `geospatialConfig` in de .NET SDK aanpassen om de **georuimtelijke configuratie** aan te passen:

Als u niets opgeeft, `geospatialConfig` wordt de standaard waarde ingesteld op het gegevens type geografie. Wanneer u de wijzigt `geospatialConfig` , worden alle bestaande georuimtelijke gegevens in de container opnieuw geïndexeerd.

Hier volgt een voor beeld van het wijzigen van het georuimtelijke gegevens type naar `geometry` door de eigenschap in te stellen `geospatialConfig` en een **boundingBox** toe te voegen:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Voor beelden van geografie gegevens indexering

Het volgende JSON-code fragment bevat een indexerings beleid waarvoor ruimtelijke indexering is ingeschakeld voor het **geografie** gegevens type. Het is geldig voor ruimtelijke gegevens met het geografie gegevens type en indexeert een geojson Point, veelhoek, multiveelhoek of lines Tring die zich binnen documenten bevinden voor ruimtelijke query's. Als u het indexerings beleid wijzigt met behulp van de Azure Portal, kunt u de volgende JSON voor het indexerings beleid opgeven om ruimtelijke indexering in te scha kelen in uw container:

**Container indexerings beleid JSON met geografie ruimtelijke indexeringen**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Als de geojson-waarde van de locatie in het document onjuist is gemanipuleerd of ongeldig is, wordt de index voor ruimtelijke query's niet geïndexeerd. U kunt locatie waarden valideren met behulp van ST_ISVALID en ST_ISVALIDDETAILED.

U kunt het [indexerings beleid ook wijzigen](how-to-manage-indexing-policy.md) met behulp van de Azure CLI, Power shell of een SDK.

## <a name="geometry-data-indexing-examples"></a>Voor beelden van geometrie gegevens indexeren

Met het gegevens type **geometrie** , vergelijkbaar met het gegevens type geografie, moet u relevante paden en typen opgeven die u wilt indexeren. Daarnaast moet u ook een `boundingBox` binnen het indexerings beleid opgeven om het gewenste gebied aan te geven dat voor dat specifieke pad moet worden geïndexeerd. Elk georuimtelijke pad vereist eigen `boundingBox` .

Het selectie kader bestaat uit de volgende eigenschappen:

- **xmin**: de minimale geïndexeerde x-coördinaat
- **ymin**: de minimale geïndexeerde y-coördinaat
- **xmax**: het maximum aantal geïndexeerde x-coördinaten
- **ymax**: het maximum aantal geïndexeerde y-coördinaten

Een selectie kader is vereist omdat geometrische gegevens een vlieg tuig in beslag nemen dat oneindig kan zijn. Ruimtelijke indexen vereisen echter een eindige spatie. Voor het gegevens type **geografie** is de aarde de grens en hoeft u geen begrenzingsvak in te stellen.

Maak een selectie kader dat alle (of de meeste) gegevens bevat. Alleen bewerkingen die zijn berekend op de objecten die zich volledig binnen het begrenzingsvak bevinden, kunnen de ruimtelijke index gebruiken. Als het selectie vakje groter is dan nodig is, heeft dit een negatieve invloed op de query prestaties.

Hier volgt een voor beeld van een indexerings beleid waarmee **geometrie** gegevens worden geïndexeerd waarbij **geospatialConfig** is ingesteld op `geometry` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Het bovenstaande indexerings beleid heeft een **boundingBox** van (-10, 10) voor x-coördinaten en (-20, 20) voor y-coördinaten. De container met het bovenstaande indexerings beleid indexeert alle punten, veelhoeken, multiveelhoeken en line strings toe die volledig binnen deze regio vallen.

> [!NOTE]
> Als u probeert een indexerings beleid met een **boundingBox** toe te voegen aan een container met `geography` gegevens type, zal dit mislukken. Wijzig de **geospatialConfig** van de container naar voordat u `geometry` een **boundingBox** toevoegt. U kunt gegevens toevoegen en de rest van uw indexerings beleid (zoals de paden en typen) wijzigen vóór of na het selecteren van het georuimtelijke gegevens type voor de container.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag gaat met georuimtelijke ondersteuning in Azure Cosmos DB, kunt u het volgende doen:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over het [opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [georuimtelijke en GEOjson-locatie gegevens in azure Cosmos DB](sql-query-geospatial-intro.md)
