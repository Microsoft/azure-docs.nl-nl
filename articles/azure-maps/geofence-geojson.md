---
title: Geojson-gegevens indeling voor geofence | Microsoft Azure kaarten
description: Meer informatie over Azure Maps geofence-gegevens. Zie het gebruik van de geofence ophalen en het plaatsen van geofence-Api's bij het ophalen van de positie van coördinaten ten opzichte van een geofence.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897191"
---
# <a name="geofencing-geojson-data"></a>Geojson-gegevens geoomheining

Met de Azure Maps [geofence ophalen](/rest/api/maps/spatial/getgeofence) en [post geofence](/rest/api/maps/spatial/postgeofence) -api's kunt u de nabijheid van een coördinaat ophalen ten opzichte van een opgegeven geofence of set omheiningen. In dit artikel wordt beschreven hoe u de geofence-gegevens voorbereidt die kunnen worden gebruikt in de Azure Maps GET en POST-API.

De gegevens voor geofence of set geofences worden vertegenwoordigd door `Feature` object en `FeatureCollection` object in `GeoJSON` indeling, dat is gedefinieerd in [rfc7946](https://tools.ietf.org/html/rfc7946). Naast het volgende:

* Het geojson-object type kan een `Feature` object of een `FeatureCollection` object zijn.
* Het object type Geometry kan een,,,,, `Point` `MultiPoint` en zijn `LineString` `MultiLineString` `Polygon` `MultiPolygon` `GeometryCollection` .
* Alle functie-eigenschappen moeten een bevatten `geometryId` , die wordt gebruikt om de geofence te identificeren.
* De functie met `Point` , `MultiPoint` , `LineString` , `MultiLineString` moet `radius` in eigenschappen bevatten. `radius` waarde wordt gemeten in meters, de `radius` waarde ligt tussen 1 en 10000.
* De functie met `polygon` en het `multipolygon` type geometrie heeft geen RADIUS-eigenschap.
* `validityTime` is een optionele eigenschap waarmee de gebruiker een verlopen tijd en geldigheids periode voor de geofence-gegevens kan instellen. Als u niets opgeeft, verlopen de gegevens nooit en zijn ze altijd geldig.
* De `expiredTime` is de verloop datum en-tijd van geoomheinings gegevens. Als de waarde van `userTime` in de aanvraag later is dan deze waarde, worden de bijbehorende geofence-gegevens beschouwd als verlopen gegevens en wordt er geen query uitgevoerd. Wanneer de geometryId van deze geofence-gegevens worden opgenomen in `expiredGeofenceGeometryId` de matrix binnen de geofence-respons.
* De `validityPeriod` is een lijst met de geldigheids periode van de geofence. Als de waarde van `userTime` in de aanvraag buiten de geldigheids periode valt, worden de bijbehorende geofence-gegevens als ongeldig beschouwd en wordt er geen query uitgevoerd. Het geometryId van deze geofence-gegevens is opgenomen in de `invalidPeriodGeofenceGeometryId` matrix binnen de geofence-respons. In de volgende tabel worden de eigenschappen van het element validityPeriod weer gegeven.

| Naam | Type | Vereist  | Beschrijving |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum/tijd  | true | De begin datum en-tijd van de geldigheids periode. |
| endTime   | Datum/tijd  | true |  De eind datum van de geldigheids periode. |
| recurrenceType | tekenreeks | onjuist |   Het terugkeer type van de periode. De waarde kan `Daily` ,, `Weekly` of zijn `Monthly` `Yearly` . De standaardwaarde is `Daily`.|
| businessDayOnly | Boolean-waarde | onjuist |  Geef aan of de gegevens alleen geldig zijn tijdens werk dagen. De standaardwaarde is `false`.|


* Alle coördinaat waarden worden weer gegeven als [lengte graad, breedte graad] gedefinieerd in `WGS84` .
* Voor elke functie, met `MultiPoint` ,, `MultiLineString` `MultiPolygon` , of `GeometryCollection` , worden de eigenschappen toegepast op alle elementen. bijvoorbeeld: alle punten in `MultiPoint` zullen dezelfde RADIUS gebruiken om een geofence van meerdere cirkels te vormen.
* In het scenario van een punt kan de geometrie van een cirkel worden weer gegeven met behulp van een `Point` Geometry-object met eigenschappen die zijn opgenomen in de [uitbrei ding van geojson-geometrie](./extend-geojson.md).      

Hieronder volgt een voor beeld van een aanvraag tekst voor een geofence die wordt weer gegeven als een cirkel geofence-geometrie in `GeoJSON` met behulp van een middel punt en een RADIUS. De geldigheids periode van de geofence-gegevens begint van 2018-10-22, 9:00 tot tot 17:00 uur, die elke dag wordt herhaald, met uitzonde ring van het weekend. `expiredTime` Hiermee wordt aangegeven dat deze geofence-gegevens als verlopen worden beschouwd als `userTime` de aanvraag later is dan `2019-01-01` .  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```