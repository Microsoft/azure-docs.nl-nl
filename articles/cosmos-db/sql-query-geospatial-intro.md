---
title: Georuimtelijke en geojson-locatie gegevens in Azure Cosmos DB
description: Meer informatie over het maken van ruimtelijke objecten met Azure Cosmos DB en de SQL-API.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692127"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Georuimtelijke en geojson-locatie gegevens in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dit artikel is een inleiding tot de georuimtelijke functionaliteit in Azure Cosmos DB. Nadat u onze documentatie voor georuimtelijk indexeren hebt gelezen, kunt u de volgende vragen beantwoorden:

* Hoe kan ik ruimtelijke gegevens in Azure Cosmos DB op te slaan?
* Hoe kan ik ruimtelijke gegevens opvragen in Azure Cosmos DB in SQL en LINQ?
* Ruimtelijke indexering in Azure Cosmos DB Hoe kan ik in-of uitschakelen?

## <a name="spatial-data-use-cases"></a>Gebruiks voorbeelden van ruimtelijke gegevens

Georuimtelijke gegevens omvatten vaak proximity-query's, bijvoorbeeld ' zoeken naar alle koffie winkels bij mijn huidige locatie '. Veelvoorkomende use cases zijn:

* Geolocatie analyse, gerichte, specifieke marketing initiatieven.
* Op locatie gebaseerde persoonlijke instellingen voor meerdere branches, zoals Retail en gezondheids zorg.
* Logistiek verbetering, voor transport optimalisatie.
* Risico analyse, met name voor verzekerings-en financiële bedrijven.
* Situatie bewustzijn, voor waarschuwingen en meldingen.

## <a name="introduction-to-spatial-data"></a>Inleiding tot ruimtelijke gegevens

Ruimtelijke gegevens beschrijven de positie en vorm van objecten in een ruimte. In de meeste toepassingen komen deze overeen met objecten op de aarde en georuimtelijke gegevens. Ruimtelijke gegevens kunnen worden gebruikt om de locatie van een persoon, een plaats van belang of de grens van een stad of een Lake te vertegenwoordigen.

De SQL-API van Azure Cosmos DB ondersteunt twee ruimtelijke gegevens typen: het gegevens type **geometrie** en het gegevens type **geografie** .

- Het type **geometrie** vertegenwoordigt gegevens in een Euclidean-coördinaten systeem (plat)
- Het **geografie** type staat voor gegevens in een coördinaten systeem met round-Earth.

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

Azure Cosmos DB ondersteunt het indexeren en doorzoeken van georuimtelijke punt gegevens die worden weer gegeven met de [GEOjson-specificatie](https://tools.ietf.org/html/rfc7946). Geojson-gegevens structuren zijn altijd geldige JSON-objecten, zodat ze kunnen worden opgeslagen en opgevraagd met behulp van Azure Cosmos DB zonder gespecialiseerde hulpprogram ma's of bibliotheken.

Azure Cosmos DB ondersteunt de volgende ruimtelijke gegevens typen:

- Spreek
- Lines Tring
- Polygoon
- Multi Polygon

### <a name="points"></a>Punten

Een **punt** geeft aan dat er één positie in de ruimte is. In georuimtelijke gegevens staat een punt voor de exacte locatie, een adres van een boodschappen archief, een kiosk, een auto Mobile of een stad.  Een punt wordt weer gegeven in geojson (en Azure Cosmos DB) met behulp van het coördinaten paar of de lengte graad en breedte graad.

Hier volgt een voor beeld van een JSON voor een punt:

**Punten in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Ruimtelijke gegevens typen kunnen worden inge sloten in een Azure Cosmos DB-document, zoals wordt weer gegeven in dit voor beeld van een gebruikers profiel dat locatie gegevens bevat:

**Profiel gebruiken met de locatie die is opgeslagen in Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punten in een geometrie coördinaten systeem

Voor het gegevens type **Geometry** specificeert geojson-specificatie de horizontale as en de verticale as seconde.

### <a name="points-in-a-geography-coordinate-system"></a>Punten in een geografisch coördinaten systeem

Voor het **geografische** gegevens type geeft de geojson-specificatie de lengte graad en de tweede breedte aan. Net als bij andere toewijzings toepassingen zijn lengte graad en breedte graad hoek en worden weer gegeven in graden. De lengte waarden worden gemeten vanaf de Prime-meridiaan en liggen tussen-180 graden en 180,0 graden, en de Latitude-waarden worden gemeten vanaf de eerste en tussen-90,0 graden en 90,0 graden.

Azure Cosmos DB interpreteert de coördinaten volgens het WGS-84-referentie systeem. Hieronder vindt u meer informatie over het coördineren van referentie systemen.

### <a name="linestrings"></a>Line strings toe

**Line strings toe** vertegenwoordigen een reeks van twee of meer punten in ruimte en de lijn segmenten waarmee ze zijn verbonden. In georuimtelijke gegevens worden line strings toe doorgaans gebruikt om snelwegen of rivieren aan te geven.

**Line strings toe in geojson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygonen

Een **veelhoek** is een grens van verbonden punten die een gesloten Lines Tring vormen. Veelhoeken worden vaak gebruikt om natuurlijke vormen te vertegenwoordigen, zoals meren of politieke jurisdicties zoals steden en Staten. Hier volgt een voor beeld van een veelhoek in Azure Cosmos DB:

**Veelhoeken in geojson**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> De geojson-specificatie vereist dat voor geldige veelhoeken het laatste opgegeven coördinaten paar hetzelfde moet zijn als de eerste, om een gesloten vorm te maken.
>
> Punten binnen een veelhoek moeten worden opgegeven in de volg orde van linksom. Een veelhoek die in de volg orde van de klok wordt opgegeven, vertegenwoordigt de inverse van de regio.
>
>

### <a name="multipolygons"></a>Point

Een **multiveelhoek** is een matrix met nul of meer veelhoeken. **Multiveelhoeken** kunnen geen zijden overlappen of een gemeen schappelijk gebied hebben. Ze kunnen op een of meer punten raken.

**Multiveelhoeken in geojson**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Referentie systemen coördineren

Omdat de vorm van de aarde onregelmatig is, worden coördinaten van georuimtelijke gegevens weer gegeven in veel coördinaten referentie systemen (CRS), elk met hun eigen frames van referentie-en meet eenheden. Bijvoorbeeld, het ' National Grid of UK ' is een referentie systeem is nauw keurig voor het Verenigd Konink rijk, maar niet buiten het.

Het populairste CRS dat momenteel wordt gebruikt, is de World Geodetic System  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS-apparaten en veel toewijzings Services, waaronder Google Maps en Bing Maps-Api's, gebruiken WGS-84. Azure Cosmos DB ondersteunt het indexeren en doorzoeken van georuimtelijke gegevens op geografische locatie alleen met behulp van de WGS-84-CRS.

## <a name="creating-documents-with-spatial-data"></a>Documenten maken met ruimtelijke gegevens
Wanneer u documenten maakt die geojson-waarden bevatten, worden ze automatisch geïndexeerd met een ruimtelijke index in overeenstemming met het indexerings beleid van de container. Als u werkt met een Azure Cosmos DB SDK in een dynamisch getypeerde taal, zoals python of Node.js, moet u een geldige geojson maken.

**Document maken met georuimtelijke gegevens in Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Als u met de SQL-api's werkt, kunt u de `Point` klassen, `LineString` , `Polygon` en `MultiPolygon` in de `Microsoft.Azure.Cosmos.Spatial` naam ruimte gebruiken om locatie-informatie in uw toepassings objecten in te sluiten. Deze klassen helpen de serialisatie en deserialisatie van ruimtelijke gegevens te vereenvoudigen in geojson.

**Document maken met georuimtelijke gegevens in .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Als u niet beschikt over de breedte graad en lengte graad, maar de fysieke adressen of locatie naam als plaats of land/regio hebt, kunt u de werkelijke coördinaten opzoeken met behulp van een geocoderings service zoals Bing Maps REST services. Lees [hier](/bingmaps/rest-services/)meer over de geocodering van Bing Maps.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag gaat met georuimtelijke ondersteuning in Azure Cosmos DB, kunt u het volgende doen:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over het [opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [ruimtelijke gegevens in index met Azure Cosmos DB](sql-query-geospatial-index.md)