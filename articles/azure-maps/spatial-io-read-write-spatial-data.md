---
title: Ruimtelijke gegevens lezen en schrijven | Microsoft Azure kaarten
description: Meer informatie over het lezen en schrijven van gegevens met behulp van de ruimtelijke IO-module, die wordt verschaft door Azure Maps Web-SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891326"
---
# <a name="read-and-write-spatial-data"></a>Ruimtelijke gegevens lezen en schrijven

In de volgende tabel ziet u de indeling van ruimtelijke bestanden die worden ondersteund voor lees-en schrijf bewerkingen met de ruimtelijke IO-module.

| Gegevensindeling       | Lezen | Schrijven |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Ruimtelijke CSV       | ✓  |  ✓  |
| Well-Known tekst   | ✓  |  ✓  |

In deze volgende secties vindt u een overzicht van de verschillende hulpprogram ma's voor het lezen en schrijven van ruimtelijke gegevens met behulp van de ruimtelijke IO-module.

## <a name="read-spatial-data"></a>Ruimtelijke gegevens lezen

De `atlas.io.read` functie is de belangrijkste functie die wordt gebruikt voor het lezen van veelgebruikte ruimtelijke gegevens indelingen, zoals KML, GPX, GeoRSS, geojson en CSV files met ruimtelijke gegevens. Deze functie kan ook gecomprimeerde versies van deze indelingen als een zip-bestand of een KMZ-bestand lezen. De KMZ-bestands indeling is een gecomprimeerde versie van KML die ook assets kan bevatten, zoals installatie kopieën. De Lees functie kan ook worden gebruikt in een URL die verwijst naar een bestand in een van deze indelingen. Url's moeten worden gehost op een eind punt waarvoor CORS is ingeschakeld of er moet een proxy service worden gegeven in de Lees opties. De proxy service wordt gebruikt voor het laden van bronnen op domeinen waarvoor CORS niet is ingeschakeld. De functie Read retourneert een Promise om de afbeeldings pictogrammen toe te voegen aan de kaart en gegevens asynchroon te verwerken om de impact op de UI-thread te minimaliseren.

Bij het lezen van een gecomprimeerd bestand, als een zip-of KMZ, wordt het uitgepakt en gescand op het eerste geldige bestand. Bijvoorbeeld doc. KML of een bestand met een andere geldige extensie, zoals:. KML,. XML, geojson,. json,. CSV,. TSV of. txt. Vervolgens worden afbeeldingen waarnaar wordt verwezen in KML-en GeoRSS-bestanden vooraf geladen om te controleren of ze toegankelijk zijn. Niet-toegankelijke afbeeldings gegevens kunnen een alternatieve terugval afbeelding laden of worden verwijderd uit de stijlen. Afbeeldingen die uit KMZ-bestanden zijn geëxtraheerd, worden geconverteerd naar gegevens-Uri's.

Het resultaat van de Lees functie is een `SpatialDataSet` object. Dit object breidt de geojson FeatureCollection-klasse uit. Het kan eenvoudig worden door gegeven `DataSource` aan een as-is om de functies ervan weer te geven op een kaart. Het `SpatialDataSet` bevat niet alleen informatie over de functies, maar kan ook KML-bedekkingen, verwerkings metrieken en andere details bevatten, zoals beschreven in de volgende tabel.

| Naam van eigenschap | Type | Beschrijving | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Selectie kader van alle gegevens in de gegevensset. |
| `features` | `Feature[]` | Geojson-functies in de gegevensset. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Een matrix van KML GroundOverlays. |
| `icons` | Record &lt; teken reeks, teken reeks&gt; | Een reeks pictogram-Url's. Key = pictogram naam, waarde = URL. |
| properties | alle | Eigenschaps informatie die op document niveau van een ruimtelijke gegevensset is opgegeven. |
| `stats` | `SpatialDataSetStats` | Statistieken over de inhoud en verwerkings tijd van een ruimtelijke gegevens verzameling. |
| `type` | `'FeatureCollection'` | Alleen-lezen geojson-type waarde. |

## <a name="examples-of-reading-spatial-data"></a>Voor beelden van het lezen van ruimtelijke gegevens

De volgende code laat zien hoe u een ruimtelijke gegevensset kunt lezen en deze op de kaart kunt weer geven met behulp van de- `SimpleDataLayer` klasse. De code gebruikt een GPX-bestand waarnaar een URL verwijst.

<br/>

<iframe height='500' scrolling='no' title='Ruimtelijke gegevens eenvoudig laden' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de ruimtelijke gegevens van de pen <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>laden eenvoudig</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de volgende code-demo ziet u hoe u KML, of KMZ, kunt lezen en laden voor de kaart. KML kunnen vloer-overlays bevatten, die in de vorm van een of worden weer geven `ImageLyaer` `OgcMapLayer` . Deze overlays moeten afzonderlijk van de functies op de kaart worden toegevoegd. Als de gegevensset aangepaste pictogrammen bevat, moeten deze pictogrammen ook worden geladen in de resources van de toewijzing voordat de functies worden geladen.

<br/>

<iframe height='500' scrolling='no' title='KML naar kaart laden' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de KML van de pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>loading to map</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt eventueel ook een proxy service voor toegang tot meerdere domein activa opgeven waarvoor geen CORS is ingeschakeld. De functie Read probeert eerst toegang te krijgen tot bestanden in een ander domein met behulp van CORS. Na de eerste keer dat het geen toegang krijgt tot een resource op een ander domein met behulp van CORS, worden alleen extra bestanden aangevraagd als er een proxy service is ingesteld. De functie Read voegt de bestands-URL toe aan het eind van de gegeven proxy-URL. Dit code fragment laat zien hoe een proxy service kan worden door gegeven aan de functie read:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

In de volgende demo ziet u hoe u een bestand met scheidings tekens leest en op de kaart rendert. In dit geval gebruikt de code een CSV-bestand met ruimtelijke gegevens kolommen.

<br/>

<iframe height='500' scrolling='no' title='Een bestand met scheidings tekens toevoegen' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>een bestand met scheidings tekens toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Ruimtelijke gegevens schrijven

Er zijn twee belang rijke schrijf functies in de ruimtelijke IO-module. De `atlas.io.write` functie genereert een teken reeks, terwijl de `atlas.io.writeCompressed` functie een gecomprimeerd zip-bestand genereert. Het gecomprimeerde zip-bestand zou een op tekst gebaseerd bestand kunnen bevatten met de ruimtelijke gegevens. Beide functies retour neren een Promise om de gegevens toe te voegen aan het bestand. En beide kunnen de volgende gegevens schrijven: `SpatialDataSet` , `DataSource` , `ImageLayer` , `OgcMapLayer` , functie verzameling, functie, geometrie of een matrix van een wille keurige combi natie van deze gegevens typen. Wanneer u met behulp van beide functies schrijft, kunt u de gewenste bestands indeling opgeven. Als de bestands indeling niet is opgegeven, worden de gegevens als KML geschreven.

Het onderstaande hulp programma demonstreert het meren deel van de schrijf opties die met de functie kunnen worden gebruikt `atlas.io.write` .

<br/>

<iframe height='700' scrolling='no' title='Opties voor het schrijven van ruimtelijke gegevens' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de opties voor het <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>schrijven van ruimtelijke gegevens</a> van de Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Voor beeld van het schrijven van ruimtelijke gegevens

In het volgende voor beeld kunt u ruimtelijke bestanden slepen en neerzetten en vervolgens op de kaart laden. U kunt geojson-gegevens exporteren van de kaart en deze in een van de ondersteunde ruimtelijke gegevens indelingen schrijven als een teken reeks of als een gecomprimeerd bestand.

<br/>

<iframe height='700' scrolling='no' title='Ruimtelijke bestanden naar kaart slepen en neerzetten' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>slepen en neerzetten van ruimtelijke bestanden naar map</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt eventueel ook een proxy service voor toegang tot meerdere domein activa opgeven waarvoor geen CORS is ingeschakeld. Dit code fragment laat zien hoe u een proxy service kunt opnemen:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Well-Known tekst lezen en schrijven (WKT)

[Bekende tekst](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) is een Open Geospatial Consortium-standaard (OGC) voor het vertegenwoordigen van ruimtelijke geometrie als tekst. Veel georuimtelijke systemen bieden ondersteuning voor WKT, zoals Azure SQL en Azure PostgreSQL met behulp van de PostGIS-invoeg toepassing. Net als bij de meeste OGC-standaarden zijn coördinaten ingedeeld als ' lengte graad breedte ' om te worden uitgelijnd met het ' x y-' Conventie. Een voor beeld: een punt op de lengte graad-110 en Latitude 45 kan worden geschreven als `POINT(-110 45)` gebruik van de WKT-indeling.

Bekende tekst kan worden gelezen met behulp van de `atlas.io.ogc.WKT.read` functie en geschreven met behulp van de `atlas.io.ogc.WKT.write` functie.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Voor beelden van het lezen en schrijven van Well-Known tekst (WKT)

De volgende code laat zien hoe u de bekende teken reeks kunt lezen `POINT(-122.34009 47.60995)` en deze op de kaart kunt weer geven met behulp van een Bubble laag.

<br/>

<iframe height='500' scrolling='no' title='Well-Known tekst lezen' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Well-Known tekst lezen</a> op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De volgende code toont de lees-en schrijf bewerkingen van bekende tekst heen en weer.

<br/>

<iframe height='700' scrolling='no' title='Well-Known tekst lezen en schrijven' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ga naar de pen <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Well-Known tekst lezen en schrijven</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> op <a href='https://codepen.io'>CodePen</a>() op de Azure Maps.
</iframe>

## <a name="read-and-write-gml"></a>GML lezen en schrijven

GML is een ruimtelijke XML-bestands specificatie die vaak wordt gebruikt als een uitbrei ding van andere XML-specificaties. Geojson-gegevens kunnen met behulp van de functie worden geschreven als XML met GML-tags `atlas.io.core.GmlWriter.write` . De XML die GML bevat, kan worden gelezen met behulp van de `atlas.io.core.GmlReader.read` functie. De functie Read heeft twee opties:

- De `isAxisOrderLonLat` optie-de as-volg orde van de coördinaten "breedte graad, lengte graad" of "lengte graad" kan variëren tussen gegevens sets en is niet altijd goed gedefinieerd. De GML Reader leest de coördinaten gegevens standaard als ' breedte graad, lengte graad ', maar als deze optie is ingesteld op True, wordt deze als ' lengte graad, breedte graad ' gelezen.
- De `propertyTypes` optie-deze optie is een opzoek tabel voor sleutel waarden waarbij de sleutel de naam is van een eigenschap in de gegevensset. De waarde is het object type waarmee de waarde moet worden omgezet tijdens het parseren. De ondersteunde type waarden zijn: `string` , `number` , en `boolean`  `date` . Als een eigenschap zich niet in de opzoek tabel bevindt of als het type niet is gedefinieerd, wordt de eigenschap geparseerd als een teken reeks.

De functie `atlas.io.read` wordt standaard ingesteld op de `atlas.io.core.GmlReader.read` functie wanneer deze detecteert dat de invoer gegevens XML zijn, maar de gegevens zijn niet een van de andere ruimtelijke XML-indelingen die worden ondersteund.

De verdeelt `GmlReader` coördinaten met een van de volgende srid's:

- EPSG: 4326 (voor keur)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674 mogelijk met een kleine fout marge.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>Meer bronnen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

[statische functies van atlas.io](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[de functies Atlas. io. OGC. WKT](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

[Kernbewerkingen gebruiken](spatial-io-core-operations.md)

[Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

[Een OGC-kaartlaag toevoegen](spatial-io-add-ogc-map-layer.md)