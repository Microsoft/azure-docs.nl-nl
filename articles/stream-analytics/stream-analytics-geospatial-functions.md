---
title: Inleiding tot Azure Stream Analytics georuimtelijke functies
description: In dit artikel worden georuimtelijke functies beschreven die worden gebruikt in Azure Stream Analytics taken.
author: krishna0815
ms.author: krishmam
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 8d01f43dd6e404bb8f8ae0898625ae1ea9d09fd6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020431"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Inleiding tot Stream Analytics georuimtelijke functies

Georuimtelijke functies in Azure Stream Analytics realtime analyse mogelijk maken van georuimtelijke gegevens in stream. Met slechts een paar regels code kunt u een oplossing voor productie kwaliteit ontwikkelen voor complexe scenario's. 

Voor beelden van scenario's die kunnen profiteren van georuimtelijke functies zijn:

* Negeren: delen
* Vloot beheer
* Bijhouden van assets
* Geoomheining
* Telefonisch volgen over sites

Stream Analytics query taal heeft zeven ingebouwde georuimtelijke functies: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** en **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

De `CreateLineString` functie accepteert punten en retourneert een geojson-Lines Tring, die als een lijn op een kaart kan worden getekend. U moet ten minste twee punten hebben om een Lines Tring te maken. De Lines Tring punten worden in de aangegeven volg orde verbonden.

De volgende query maakt gebruik `CreateLineString` van om een Lines Tring te maken met behulp van drie punten. Het eerste punt wordt gemaakt op basis van streaming-invoer gegevens, terwijl de andere twee hand matig worden gemaakt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Invoer voorbeeld  
  
|breedtegraad|lengte graad|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Uitvoer voorbeeld  

 {"type": "Lines Tring", "coördinaten": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"type": "Lines Tring", "coördinaten": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Ga voor meer informatie naar de [CreateLineString](/stream-analytics-query/createlinestring) -referentie.

## <a name="createpoint"></a>CreatePoint

De `CreatePoint` functie accepteert een breedte graad en lengte graad en retourneert een geojson-punt, dat op een kaart kan worden getekend. Uw breedte graad en lengte graad moeten een **float** -gegevens type zijn.

In het volgende voor beeld wordt een-query gebruikt `CreatePoint` om een punt te maken met behulp van de breedte graad en de lengte graad van streaming-invoer gegevens.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Invoer voorbeeld  
  
|breedtegraad|lengte graad|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Uitvoer voorbeeld
  
 {"type": "punt", "coördinaten": [-10,2, 3,0]}  
  
 {"type": "punt", "coördinaten": [20,2321,-87,33]}  

Ga voor meer informatie naar de [CreatePoint](/stream-analytics-query/createpoint) -referentie.

## <a name="createpolygon"></a>CreatePolygon

De `CreatePolygon` functie accepteert punten en retourneert een GEOjson-veelhoek record. De volg orde van de punten moet volgen op de juiste ring richting of linksom. Stel dat u vanaf het ene punt naar het andere wilt lopen in de volg orde waarin ze zijn gedeclareerd. Het midden van de veelhoek is de gehele tijd aan uw linkerkant.

Met de volgende voorbeeld query maakt `CreatePolygon` u een veelhoek van drie punten. De eerste twee punten worden hand matig gemaakt en het laatste punt wordt gemaakt op basis van de invoer gegevens.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Invoer voorbeeld  
  
|breedtegraad|lengte graad|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Uitvoer voorbeeld  

 {"type": "veelhoek", "coördinaten": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"type": "veelhoek", "coördinaten": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Ga voor meer informatie naar de [CreatePolygon](/stream-analytics-query/createpolygon) -referentie.


## <a name="st_distance"></a>ST_DISTANCE
De `ST_DISTANCE` functie retourneert de afstand tussen twee punten in meters. 

De volgende query gebruikt `ST_DISTANCE` voor het genereren van een gebeurtenis wanneer een gas kleiner is dan 10 km van de auto.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Ga voor meer informatie naar de [ST_DISTANCE](/stream-analytics-query/st-distance) -referentie.

## <a name="st_overlaps"></a>ST_OVERLAPS
De `ST_OVERLAPS` functie vergelijkt twee veelhoeken. Als de veelhoeken elkaar overlappen, retourneert de functie een 1. De functie retourneert 0 als de veelhoeken elkaar niet overlappen. 

De volgende query maakt gebruik `ST_OVERLAPS` van om een gebeurtenis te genereren wanneer een gebouw zich binnen een mogelijke Flooding-zone bevindt.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Met de volgende voorbeeld query wordt een gebeurtenis gegenereerd wanneer een storm is gericht op een auto.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Ga voor meer informatie naar de [ST_OVERLAPS](/stream-analytics-query/st-overlaps) -referentie.

## <a name="st_intersects"></a>ST_INTERSECTS
De `ST_INTERSECTS` functie vergelijkt twee Lines Tring. Als de Lines Tring Intersect, retourneert de functie 1. De functie retourneert 0 als de Lines Tring niet INTERSECT.

De volgende voorbeeld query wordt gebruikt `ST_INTERSECTS` om te bepalen of een paved-weg een wegsnij punt kruist.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Invoer voorbeeld  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "Lines Tring", "coördinaten": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "Lines Tring", "coördinaten": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"type": "Lines Tring", "coördinaten": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "Lines Tring", "coördinaten": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Uitvoer voorbeeld  

 1  
  
 0  

Ga voor meer informatie naar de [ST_INTERSECTS](/stream-analytics-query/st-intersects) -referentie.

## <a name="st_within"></a>ST_WITHIN
De `ST_WITHIN` functie bepaalt of een punt of veelhoek zich binnen een veelhoek bevindt. Als de veelhoek het punt of de veelhoek bevat, wordt de functie 1 geretourneerd. De functie retourneert 0 als het punt of de veelhoek zich niet in de gedeclareerde veelhoek bevindt.

In de volgende voorbeeld query wordt gebruikt `ST_WITHIN` om te bepalen of het bezorgings doel punt zich binnen de opgegeven Warehouse-veelhoek bevindt.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Invoer voorbeeld  
  
|deliveryDestination|uitslag|  
|-------------------------|---------------|  
|{"type": "punt", "coördinaten": [76,6, 10,1]}|{"type": "veelhoek", "coördinaten": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"type": "punt", "coördinaten": [15,0, 15,0]}|{"type": "veelhoek", "coördinaten": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Uitvoer voorbeeld  

 0  
  
 1  

Ga voor meer informatie naar de [ST_WITHIN](/stream-analytics-query/st-within) -referentie.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)