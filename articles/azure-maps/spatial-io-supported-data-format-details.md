---
title: Details van ondersteunde gegevens indeling | Microsoft Azure kaarten
description: Meer informatie over hoe gescheiden ruimtelijke gegevens worden geparseerd in de ruimtelijke IO-module.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91310166"
---
# <a name="supported-data-format-details"></a>Details van ondersteunde gegevensindeling

In dit artikel vindt u specifieke informatie over de ondersteuning voor lezen en schrijven voor alle XML-labels en Well-Known tekst Geometry-typen. Ook wordt beschreven hoe de gescheiden ruimtelijke gegevens worden geparseerd in de ruimtelijke IO-module.

## <a name="supported-xml-namespaces"></a>Ondersteunde XML-naam ruimten

De ruimtelijke IO-module ondersteunt XML-labels van de volgende naam ruimten.

| Naam ruimte voorvoegsel | Naam ruimte-URI   | Notities                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Alleen-lezen ondersteuning in GeoRSS-bestanden.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Alleen-lezen ondersteuning in GeoRSS-bestanden.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Alleen-lezen ondersteuning in GPX-bestanden. Parseert en maakt gebruik van DisplayColor. Alle andere eigenschappen die zijn toegevoegd aan de meta gegevens van de vorm. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Ondersteund in GPX-bestanden. Gebruikt lijn kleur. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Alleen-lezen. GeoRSS schrijft met behulp van Atom-indeling.              |

## <a name="supported-xml-elements"></a>Ondersteunde XML-elementen

De ruimtelijke IO-module ondersteunt de volgende XML-elementen. Alle XML-tags die niet worden ondersteund, worden geconverteerd naar een JSON-object. Vervolgens wordt elke tag toegevoegd als een eigenschap in het `properties` veld van de bovenliggende shape of laag.

### <a name="kml-elements"></a>KML-elementen

De ruimtelijke IO-module ondersteunt de volgende KML-elementen.

| Naam van element         | Lezen    | Schrijven   | Notities                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | gedeeltelijke | ja     | Het object wordt geparseerd maar wordt niet gebruikt voor het positioneren van de vorm.                                                                    |
| `AddressDetails`     | gedeeltelijke | nee      | Het object wordt geparseerd maar wordt niet gebruikt voor het positioneren van de vorm.                                                                    |
| `atom:author`        | ja     | ja     |                                                                                                                            |
| `atom:link`          | ja     | ja     |                                                                                                                            |
| `atom:name`          | ja     | ja     |                                                                                                                            |
| `BalloonStyle`       | gedeeltelijke | gedeeltelijke | `displayMode` wordt niet ondersteund. Geconverteerd naar een `PopupTemplate` . Als u wilt schrijven, voegt `popupTemplate` u een eigenschap toe als een eigenschap van de functie waarvoor u deze wilt schrijven. |
| `begin`              | ja     | ja     |                                                                                                                            |
| `color`              | ja     | ja     | Inclusief `#AABBGGRR` en `#BBGGRR` . Geparseerd naar een CSS-kleur teken reeks                                                           |
| `colorMode`          | ja     | nee      |                                                                                                                            |
| `coordinates`        | ja     | ja     |                                                                                                                            |
| `Data`               | ja     | ja     |                                                                                                                            |
| `description`        | ja     | ja     |                                                                                                                            |
| `displayName`        | ja     | ja     |                                                                                                                            |
| `Document`           | ja     | ja     |                                                                                                                            |
| `drawOrder`          | gedeeltelijke | nee      | Lezen voor de grond bedekkingen en worden gebruikt om ze te sorteren. 
| `east`               | ja     | ja     |                                                                                                                            |
| `end`                | ja     | ja     |                                                                                                                            |
| `ExtendedData`       | ja     | ja     | Biedt ondersteuning voor niet-getypte `Data` , `SimpleData` of `Schema` -en entiteits vervangingen van het formulier `$[dataName]` .                      |
| `extrude`            | gedeeltelijke | gedeeltelijke | Alleen ondersteund voor veelhoeken. Meerdere geometrie met veelhoeken met verschillende hoogten wordt onderverdeeld in afzonderlijke functies. Lijn stijlen worden niet ondersteund. Veelhoeken met een hoogte van 0 worden weer gegeven als een platte veelhoek. Bij het lezen wordt de hoogte van de eerste coördinaat in de buitenste ring toegevoegd als eigenschap height van de polygoon. Vervolgens wordt de hoogte van de eerste coördinaat gebruikt om de veelhoek op de kaart weer te geven. |
| `fill`               | ja     | ja     |                                                                                                                            |
| `Folder`             | ja     | ja     |                                                                                                                            |
| `GroundOverlay`      | ja     | ja     | `color` wordt niet ondersteund                                                                                                   |
| `heading`            | gedeeltelijke | nee      | Geparseerd maar niet gerenderd door `SimpleDataLayer` . Schrijft alleen als de gegevens worden opgeslagen in de eigenschap van de vorm.                 |
| `hotSpot`            | ja     | gedeeltelijke | Schrijft alleen als de gegevens worden opgeslagen in de eigenschap van de vorm. Eenheden worden alleen als ' pixels ' gegenereerd.                         |
| `href`               | ja     | ja     |                                                                                                                            |
| `Icon`               | gedeeltelijke | gedeeltelijke | Geparseerd maar niet gerenderd door `SimpleDataLayer` . Schrijft alleen de eigenschap icon van de vorm als deze een URI-gegevens bevat. Alleen `href` wordt ondersteund. |
| `IconStyle`          | gedeeltelijke | gedeeltelijke | `icon`, `heading` , `colorMode` en `hotspots` waarden worden geparseerd, maar ze worden niet weer gegeven door `SimpleDataLayer`         |
| `innerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `kml`                | ja     | ja     |                                                                                                                            |
| `LabelStyle`         | nee      | nee      |                                                                                                                            |
| `LatLonBox`          | ja     | ja     |                                                                                                                            |
| `gx:LatLonQuad`      | ja     | ja     |                                                                                                                            |
| `LinearRing`         | ja     | ja     |                                                                                                                            |
| `LineString`         | ja     | ja     |                                                                                                                            |
| `LineStyle`          | ja     | ja     | `colorMode` wordt niet ondersteund.                                                                                         |
| `Link`               | ja     | nee      | Alleen de `href` eigenschap wordt ondersteund voor netwerk koppelingen.                                                                   |
| `MultiGeometry`      | gedeeltelijke | gedeeltelijke | Kan worden opgesplitst in afzonderlijke functies wanneer ze worden gelezen.                                                                     |
| `name`               | ja     | ja     |                                                                                                                            |
| `NetworkLink`        | ja     | nee      | Koppelingen moeten zich in hetzelfde domein als het document bestaan.                                                                  |
| `NetworkLinkControl` | nee      | nee      |                                                                                                                            |
| `north`              | ja     | ja     |                                                                                                                            |
| `open`               | ja     | ja     |                                                                                                                            |
| `outerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `outline`            | ja     | ja     |                                                                                                                            |
| `overlayXY`          | nee      | nee      |                                                                                                                            |
| `Pair`               | gedeeltelijke | nee      | Alleen de `normal` stijl in een `StyleMap` wordt ondersteund. `highlight` wordt niet ondersteund.                                   |
| `phoneNumber`        | ja     | ja     |                                                                                                                            |
| `PhotoOverlay`       | nee      | nee      |                                                                                                                            |
| `Placemark`          | ja     | ja     |                                                                                                                            |
| `Point`              | ja     | ja     |                                                                                                                            |
| `Polygon`            | ja     | ja     |                                                                                                                            |
| `PolyStyle`          | ja     | ja     |                                                                                                                            |
| `Region`             | gedeeltelijke | gedeeltelijke | `LatLongBox` wordt ondersteund op document niveau.                                                                      |
| `rotation`           | nee      | nee      |                                                                                                                            |
| `rotationXY`         | nee      | nee      |                                                                                                                            |
| `scale`              | nee      | nee      |                                                                                                                            |
| `Schema`             | ja     | ja     |                                                                                                                            |
| `SchemaData`         | ja     | ja     |                                                                                                                            |
| `schemaUrl`          | gedeeltelijke | ja     | Biedt geen ondersteuning voor het laden van stijlen van externe documenten die niet zijn opgenomen in een KMZ.                             |
| `ScreenOverlay`      | nee      | nee      |                                                                                                                            |
| `screenXY`           | nee      | nee      |                                                                                                                            |
| `SimpleData`         | ja     | ja     |                                                                                                                            |
| `SimpleField`        | ja     | ja     |                                                                                                                            |
| `size`               | nee      | nee      |                                                                                                                            |
| `Snippet`            | gedeeltelijke | gedeeltelijke | `maxLines` het kenmerk wordt genegeerd.                                                                                  |
| `south`              | ja     | ja     |                                                                                                                            |
| `Style`              | ja     | ja     |                                                                                                                            |
| `StyleMap`           | gedeeltelijke | nee      | Alleen de stijl standaard in een `StyleMap` wordt ondersteund.                                                                        |
| `styleUrl`           | gedeeltelijke | ja     | Url's voor externe stijlen worden niet ondersteund.                                                                         |
| `text`               | ja     | ja     | Vervangen van `$[geDirections]` wordt niet ondersteund                                                                          |
| `textColor`          | ja     | ja     |                                                                                                                            |
| `TimeSpan`           | ja     | ja     |                                                                                                                            |
| `TimeStamp`          | ja     | ja     |                                                                                                                            |
| `value`              | ja     | ja     |                                                                                                                            |
| `viewRefreshMode`    | gedeeltelijke | nee      |  Als er wordt verwezen naar een WMS-service, `onStop` wordt alleen voor de grond overlays ondersteund. Wordt toegevoegd `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` aan de URL en wordt bijgewerkt als de kaart wordt verplaatst.  |
| `visibility`         | ja     | ja     |                                                                                                                            |
| `west`               | ja     | ja     |                                                                                                                            |
| `when`               | ja     | ja     |                                                                                                                            |
| `width`              | ja     | ja     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS-elementen

De ruimtelijke IO-module ondersteunt de volgende GeoRSS-elementen.

| Naam van element             | Lezen    | Schrijven | Notities                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | ja     | ja   |                                                                                                |
| `atom:category`          | ja     | ja   |                                                                                                |
| `atom:content`           | ja     | ja   |                                                                                                |
| `atom:contributor`       | ja     | ja   |                                                                                                |
| `atom:email`             | ja     | ja   |                                                                                                |
| `atom:entry`             | ja     | ja   |                                                                                                |
| `atom:feed`              | ja     | ja   |                                                                                                |
| `atom:icon`              | ja     | ja   |                                                                                                |
| `atom:id`                | ja     | ja   |                                                                                                |
| `atom:link`              | ja     | ja   |                                                                                                |
| `atom:logo`              | ja     | ja   |                                                                                                |
| `atom:name`              | ja     | ja   |                                                                                                |
| `atom:published`         | ja     | ja   |                                                                                                |
| `atom:rights`            | ja     | ja   |                                                                                                |
| `atom:source`            | ja     | ja   |                                                                                                |
| `atom:subtitle`          | ja     | ja   |                                                                                                |
| `atom:summary`           | ja     | ja   |                                                                                                |
| `atom:title`             | ja     | ja   |                                                                                                |
| `atom:updated`           | ja     | ja   |                                                                                                |
| `atom:uri`               | ja     | ja   |                                                                                                |
| `geo:lat`                | ja     | nee    | Geschreven als een `georss:point` .                                                                   |
| `geo:lon`                | ja     | nee    | Geschreven als een `georss:point` .                                                                   |
| `geo:long`               | ja     | nee    | Geschreven als een `georss:point` .                                                                   |
| `georss:box`             | ja     | nee    | Lezen als een veelhoek en een `subType` eigenschap van ' Rectangle ' gegeven                                |
| `georss:circle`          | ja     | ja   |                                                                                                |
| `georss:elev`            | ja     | ja   |                                                                                                |
| `georss:featurename`     | ja     | ja   |                                                                                                |
| `georss:featuretypetag`  | ja     | ja   |                                                                                                |
| `georss:floor`           | ja     | ja   |                                                                                                |
| `georss:line`            | ja     | ja   |                                                                                                |
| `georss:point`           | ja     | ja   |                                                                                                |
| `georss:polygon`         | ja     | ja   |                                                                                                |
| `georss:radius`          | ja     | ja   |                                                                                                |
| `georss:relationshiptag` | ja     | ja   |                                                                                                |
| `georss:where`           | ja     | ja   |                                                                                                |
| `geourl:latitude`        | ja     | nee    | Geschreven als een `georss:point` .                                                                   |
| `geourl:longitude`       | ja     | nee    | Geschreven als een `georss:point` .                                                                   |
| `position`               | ja     | nee    | Met sommige XML-feeds wordt GML gewrappt met een positie code in plaats van deze op te maken met een `georss:where` tag. Leest deze tag, maar schrijft met behulp van een `georss:where` tag. |
| `rss`                    | ja     | nee    | GeoRSS geschreven in ATOM-indeling.                                                                 |
| `rss:author`             | ja     | gedeeltelijke | Geschreven als een `atom:author` .                                                                 |
| `rss:category`           | ja     | gedeeltelijke | Geschreven als een `atom:category` .                                                               |
| `rss:channel`            | ja     | nee    |                                                                                                |
| `rss:cloud`              | ja     | nee    |                                                                                                |
| `rss:comments`           | ja     | nee    |                                                                                                |
| `rss:copyright`          | ja     | gedeeltelijke | Geschreven als een `atom:rights` if-vorm heeft geen `rights` `properties` eigenschap al.       |
| `rss:description`        | ja     | gedeeltelijke | Geschreven als een `atom:content` if-vorm heeft geen `content` `properties` eigenschap al.      |
| `rss:docs`               | ja     | nee    |                                                                                                |
| `rss:enclosure`          | ja     | nee    |                                                                                                |
| `rss:generator`          | ja     | nee    |                                                                                                |
| `rss:guid`               | ja     | gedeeltelijke | Geschreven als een `atom:id` if-vorm heeft geen `id` `properties` eigenschap al.         |
| `rss:image`              | ja     | gedeeltelijke | Geschreven als een `atom:logo` if-vorm heeft geen `logo` `properties` eigenschap al.      |
| `rss:item`               | ja     | gedeeltelijke | Geschreven als een `atom:entry` .                                                                  |
| `rss:language`           | ja     | nee    |                                                                                                |
| `rss:lastBuildDate`      | ja     | gedeeltelijke | Geschreven als een `atom:updated` if-vorm heeft geen `updated` `properties` eigenschap al.     |
| `rss:link`               | ja     | gedeeltelijke | Geschreven als een `atom:link` .                                                                   |
| `rss:managingEditor`     | ja     | gedeeltelijke | Geschreven als een `atom:contributor` .                                                            |
| `rss:pubDate`            | ja     | gedeeltelijke | Geschreven als een `atom:published` if-vorm heeft geen `published` `properties` eigenschap al.  |
| `rss:rating`             | ja     | nee    |                                                                                                |
| `rss:skipDays`           | ja     | nee    |                                                                                                |
| `rss:skipHours`          | ja     | nee    |                                                                                                |
| `rss:source`             | ja     | gedeeltelijke | Geschreven als een `atom:source` `atom:link` .                                       |
| `rss:textInput`          | ja     | nee    |                                                                                                |
| `rss:title`              | ja     | gedeeltelijke | Geschreven als een `atom:title` .                                                                  |
| `rss:ttl`                | ja     | nee    |                                                                                                |
| `rss:webMaster`          | ja     | nee    |                                                                                                |

### <a name="gml-elements"></a>GML-elementen

De ruimtelijke IO-module ondersteunt de volgende GML-elementen. 

| Naam van element            | Lezen | Schrijven | Notities                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ja  | nee    | Geschreven als `gml:posList` .                                                              |
| `gml:curveMember`       | ja  | nee    |                                                                                        |
| `gml:curveMembers`      | ja  | nee    |                                                                                        |
| `gml:Box`               | ja  | nee    | Geschreven als `gml:Envelope` .                                                             |
| `gml:description`       | ja  | ja   |                                                                                        |
| `gml:Envelope`          | ja  | ja   |                                                                                        |
| `gml:exterior`          | ja  | ja   |                                                                                        |
| `gml:Feature`           | ja  | nee    | Geschreven als een vorm.                                                                    |
| `gml:FeatureCollection` | ja  | nee    | Geschreven als een geometrie verzameling.                                                      |
| `gml:featureMember`     | ja  | nee    | Geschreven als een geometrie verzameling.                                                      |
| `gml:geometry`          | ja  | nee    | Geschreven als een vorm.                                                                    |
| `gml:geometryMember`    | ja  | ja   |                                                                                        |
| `gml:geometryMembers`   | ja  | ja   |                                                                                        |
| `gml:identifier`        | ja  | ja   |                                                                                        |
| `gml:innerBoundaryIs`   | ja  | nee    | Geschreven met `gml.interior` .                                                          |
| `gml:interior`          | ja  | ja   |                                                                                        |
| `gml:LinearRing`        | ja  | ja   |                                                                                        |
| `gml:LineString`        | ja  | ja   |                                                                                        |
| `gml:lineStringMember`  | ja  | ja   |                                                                                        |
| `gml:lineStringMembers` | ja  | nee    |                                                                                        |
| `gml:MultiCurve`        | ja  | nee    | Alleen leden worden gelezen `gml:LineString` . Geschreven als `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | gedeeltelijke  | gedeeltelijke   | Alleen lezen als een FeatureCollection.                                              |
| `gml:MultiLineString`   | ja  | ja   |                                                                                        |
| `gml:MultiPoint`        | ja  | ja   |                                                                                        |
| `gml:MultiPolygon`      | ja  | ja   |                                                                                        |
| `gml:MultiSurface`      | ja  | nee    | Alleen leden worden gelezen `gml:Polygon` . Geschreven als `gml.MultiPolygon`                        |
| `gml:name`              | ja  | ja   |                                                                                        |
| `gml:outerBoundaryIs`   | ja  | nee    | Geschreven met `gml.exterior` .                                                          |
| `gml:Point`             | ja  | ja   |                                                                                        |
| `gml:pointMember`       | ja  | ja   |                                                                                        |
| `gml:pointMembers`      | ja  | nee    |                                                                                        |
| `gml:Polygon`           | ja  | ja   |                                                                                        |
| `gml:polygonMember`     | ja  | ja   |                                                                                        |
| `gml:polygonMembers`    | ja  | nee    |                                                                                        |
| `gml:pos`               | ja  | ja   |                                                                                        |
| `gml:posList`           | ja  | ja   |                                                                                        |
| `gml:surfaceMember`     | ja  | ja   |                                                                                        |

#### <a name="additional-notes"></a>aanvullende opmerkingen

- Member-elementen worden doorzocht op een geometrie die kan worden overspoeld binnen onderliggende elementen. Deze zoek bewerking is nood zakelijk omdat veel XML-indelingen die van GML zijn, geen geometrie mag hebben als een direct onderliggend element van een lid.
- `srsName` wordt gedeeltelijk ondersteund voor WGS84-coördinaten en de volgende codes:[EPSG: 4326](https://epsg.io/4326)) en Web Mercator ([EPSG: 3857](https://epsg.io/3857) of een van de alternatieve codes. Elk ander coördinaten systeem wordt geparseerd als WGS84 as-is.
- Tenzij wordt opgegeven bij het lezen van een XML-feed, wordt de volg orde van de as bepaald op basis van hints in de XML-feed. Er wordt een voor keur gegeven voor de volg orde "breedte graad, lengte graad".
- Tenzij er een aangepaste GML-naam ruimte is opgegeven voor de eigenschappen bij het schrijven naar een GML-bestand, worden er geen aanvullende eigenschappen meer toegevoegd.

### <a name="gpx-elements"></a>GPX-elementen

De ruimtelijke IO-module ondersteunt de volgende GPX-elementen.

| Naam van element             | Lezen    | Schrijven   | Notities                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ja     | ja     |                                                                                             |
| `gpx:author`             | ja     | ja     |                                                                                             |
| `gpx:bounds`             | ja     | ja     | Wordt geconverteerd naar een LocationRect wanneer deze wordt gelezen.                                                    |
| `gpx:cmt`                | ja     | ja     |                                                                                             |
| `gpx:copyright`          | ja     | ja     |                                                                                             |
| `gpx:desc`               | ja     | ja     | Wordt gekopieerd naar een beschrijvings eigenschap wanneer het lezen wordt uitgelijnd met andere XML-indelingen.               |
| `gpx:dgpsid`             | ja     | ja     |                                                                                             |
| `gpx:ele`                | ja     | ja     |                                                                                             |
| `gpx:extensions`         | gedeeltelijke | gedeeltelijke | Bij lezen worden de stijl gegevens geëxtraheerd. Alle andere uitbrei dingen worden afgevlakt in een eenvoudig JSON-object. Alleen vorm stijl gegevens worden geschreven. |
| `gpx:geoidheight`        | ja     | ja     |                                                                                             |
| `gpx:gpx`                | ja     | ja     |                                                                                             |
| `gpx:hdop`               | ja     | ja     |                                                                                             |
| `gpx:link`               | ja     | ja     |                                                                                             |
| `gpx:magvar`             | ja     | ja     |                                                                                             |
| `gpx:metadata`           | ja     | ja     |                                                                                             |
| `gpx:name`               | ja     | ja     |                                                                                             |
| `gpx:pdop`               | ja     | ja     |                                                                                             |
| `gpx:rte`                | ja     | ja     |                                                                                             |
| `gpx:rtept`              | ja     | ja     |                                                                                             |
| `gpx:sat`                | ja     | ja     |                                                                                             |
| `gpx:src`                | ja     | ja     |                                                                                             |
| `gpx:sym`                | ja     | ja     | De waarde wordt vastgelegd, maar wordt niet gebruikt om het pictogram punaise te wijzigen.                               |
| `gpx:text`               | ja     | ja     |                                                                                             |
| `gpx:time`               | ja     | ja     |                                                                                             |
| `gpx:trk`                | ja     | ja     |                                                                                             |
| `gpx:trkpt`              | ja     | ja     |                                                                                             |
| `gpx:trkseg`             | ja     | ja     |                                                                                             |
| `gpx:type`               | ja     | ja     |                                                                                             |
| `gpx:vdop`               | ja     | ja     |                                                                                             |
| `gpx:wpt`                | ja     | ja     |                                                                                             |
| `gpx_style:color`        | ja     | ja     |                                                                                             |
| `gpx_style:line`         | gedeeltelijke | gedeeltelijke | `color`, `opacity` , `width` , `lineCap` worden ondersteund.                                           |
| `gpx_style:opacity`      | ja     | ja     |                                                                                             |
| `gpx_style:width`        | ja     | ja     |                                                                                             |
| `gpxx:DisplayColor`      | ja     | nee      | Wordt gebruikt om de kleur van een vorm op te geven. Wanneer u schrijft, wordt `gpx_style:line` er in plaats daarvan kleur gebruikt.  |
| `gpxx:RouteExtension`    | gedeeltelijke | nee      | Alle eigenschappen worden in gelezen `properties` . Wordt alleen `DisplayColor` gebruikt.                     |
| `gpxx:TrackExtension`    | gedeeltelijke | nee      | Alle eigenschappen worden in gelezen `properties` . Wordt alleen `DisplayColor` gebruikt.                     |
| `gpxx:WaypointExtension` | gedeeltelijke | nee      | Alle eigenschappen worden in gelezen `properties` . Wordt alleen `DisplayColor` gebruikt.                     |
| `gpx:keywords`           | ja     | ja     |                                                                                             |
| `gpx:fix`                | ja     | ja     |                                                                                             |

#### <a name="additional-notes"></a>aanvullende opmerkingen

Bij het schrijven;

- Multipointers worden opgedeeld in afzonderlijke waypoints.
- Veelhoeken en multiveelhoeken worden geschreven als sporen. 
  
## <a name="supported-well-known-text-geometry-types"></a>Ondersteunde Well-Known tekst Geometry-typen

| Type geometrie | Lezen | Schrijven |
|--------------|:----:|:-----:|
| SPREEK | x | x |
| PUNT Z | x | x | 
| PUNT M | x | x<sup>[2]</sup> |
| PUNT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Lines Tring | x | x |
| LINES TRING Z | x | x | 
| LINES TRING M | x | x<sup>[2]</sup> |
| LINES TRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGOON | x | x |
| VEELHOEK Z | x | x |
| VEELHOEK M | x | x<sup>[2]</sup> |
| VEELHOEK ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Point | x | x |
| MULTI POINT Z | x | x | 
| MULTI POINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTI line String | x | x |
| MULTI LINE STRING Z | x | x | 
| MULTI LINE STRING M | x | x<sup>[2]</sup> |
| MULTI LINE STRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multi POLYGON | x | x |
| MULTIVEELHOEK Z | x | x | 
| MULTIVEELHOEK M | x | x<sup>[2]</sup> |
| MULTIVEELHOEK ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] Er wordt alleen een Z-para meter vastgelegd en als derde waarde toegevoegd in de positie waarde.

\[de \] para meter 2 M is niet vastgelegd.

## <a name="delimited-spatial-data-support"></a>Ondersteuning voor ruimtelijke gegevens met scheidings tekens

Gescheiden ruimtelijke gegevens, zoals CSV (bestand met door komma's gescheiden waarden), bevatten vaak kolommen met ruimtelijke gegevens. Er kunnen bijvoorbeeld kolommen zijn met informatie over breedte graad en lengte graad. In Well-Known tekst indeling kan er sprake zijn van een kolom die ruimtelijke geometrie gegevens bevat.

### <a name="spatial-data-column-detection"></a>Detectie van ruimtelijke gegevens kolom

Bij het lezen van een bestand met scheidings tekens dat ruimtelijke gegevens bevat, wordt de koptekst geanalyseerd om te bepalen welke kolommen locatie velden bevatten. Als de header type-informatie bevat, wordt deze gebruikt om de celwaarden te casten naar het juiste type. Als er geen header is opgegeven, wordt de eerste rij geanalyseerd en gebruikt voor het genereren van een koptekst. Bij het analyseren van de eerste rij wordt een controle uitgevoerd om kolom namen te vergelijken met de volgende namen op een niet-hoofdletter gevoelige manier. De volg orde van de namen is de prioriteit, als er twee of meer namen in een bestand voor komen.

#### <a name="latitude"></a>Breedtegraad

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Lengtegraad

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Terrein

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografie

De eerste rij met gegevens wordt gescand op teken reeksen in Well-Known tekst indeling. 

### <a name="delimited-data-column-types"></a>Gegevens kolom typen met scheidings tekens

Bij het scannen van de rij met koppen, worden alle typen gegevens die in de kolom naam staan, geëxtraheerd en gebruikt om de cellen in die kolom te casten. Hier volgt een voor beeld van een kolom naam die een type waarde heeft: ' columnName (typeName) '. De volgende niet-hoofdletter gevoelige type namen worden ondersteund:

#### <a name="numbers"></a>Getallen

- EDM. int64
- int
- long
- EDM. Double
- float
- double
- getal

#### <a name="booleans"></a>Booleaans

- EDM. Boolean
- booleaans
- booleaans

#### <a name="dates"></a>Datums

- EDM. datetime
- datum
- datum/tijd

#### <a name="geography"></a>Geografie

- EDM. Geografie
- Geografie

#### <a name="strings"></a>Tekenreeksen

- EDM. String
- varchar
- tekst
- tekenreeks

Als er geen type gegevens kunnen worden geëxtraheerd uit de header en de optie dynamisch typen is ingeschakeld bij het lezen, wordt elke cel afzonderlijk geanalyseerd om te bepalen welk gegevens type het meest geschikt is voor cast als.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

[Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)
