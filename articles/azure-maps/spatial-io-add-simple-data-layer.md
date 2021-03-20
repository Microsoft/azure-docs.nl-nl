---
title: Een eenvoudige gegevenslaag toevoegen | Microsoft Azure kaarten
description: Meer informatie over hoe u een eenvoudige gegevenslaag kunt toevoegen met behulp van de ruimtelijke IO-module, die wordt verschaft door Azure Maps Web-SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896154"
---
# <a name="add-a-simple-data-layer"></a>Een eenvoudige gegevenslaag toevoegen

De ruimtelijke IO-module biedt een `SimpleDataLayer` klasse. Deze klasse maakt het eenvoudig om stijl functies op de kaart weer te geven. Het kan zelfs gegevens sets weer geven met stijl eigenschappen en gegevens sets die gemengde geometrie typen bevatten. De eenvoudige gegevenslaag bereikt deze functionaliteit door meerdere weergave lagen te verpakken en stijl expressies te gebruiken. De stijl expressies zoeken naar algemene stijl eigenschappen van de functies in deze ingepakte lagen. De `atlas.io.read` functie en de `atlas.io.write` functie gebruiken deze eigenschappen om stijlen in een ondersteunde bestands indeling te lezen en te schrijven. Wanneer u de eigenschappen aan een ondersteunde bestands indeling hebt toegevoegd, kan het bestand voor verschillende doel einden worden gebruikt. Het bestand kan bijvoorbeeld worden gebruikt om de stijl functies op de kaart weer te geven.

Naast opmaak functies `SimpleDataLayer` biedt het een ingebouwde pop-upfunctie met een pop-upsjabloon. De pop-up wordt weer gegeven wanneer er op een functie wordt geklikt. De standaard pop-upfunctie kan, indien gewenst, worden uitgeschakeld. Deze laag biedt ook ondersteuning voor geclusterde gegevens. Wanneer er op een cluster wordt geklikt, wordt de kaart inzoomen op het cluster en wordt deze uitgebreid naar afzonderlijke punten en subclusteren.

De `SimpleDataLayer` klasse is bedoeld om te worden gebruikt voor grote gegevens sets met veel geometrie typen en veel stijlen die zijn toegepast op de functies. Bij gebruik van deze klasse wordt een overhead toegevoegd van zes lagen met stijl expressies. Er zijn dus gevallen waarin het efficiënter is om de kern weergave lagen te gebruiken. Gebruik bijvoorbeeld een kern laag om een aantal geometrie typen en enkele stijlen van een functie weer te geven

## <a name="use-a-simple-data-layer"></a>Een eenvoudige gegevenslaag gebruiken

De `SimpleDataLayer` klasse wordt gebruikt zoals de andere rendering-lagen worden gebruikt. De volgende code laat zien hoe u een eenvoudige gegevenslaag kunt gebruiken in een kaart:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Functies toevoegen aan de gegevens bron. Vervolgens wordt met de eenvoudige gegevenslaag aangegeven hoe de functies het beste kunnen worden weer gegeven. Stijlen voor afzonderlijke functies kunnen worden ingesteld als eigenschappen voor de functie. De volgende code toont een functie van een geojson-punt waarvoor een `color` eigenschap is ingesteld op `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Met de volgende code wordt de functie van het bovenstaande punt weer gegeven met behulp van de laag Simple data. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="De Simple data layer gebruiken" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Ga naar de pen en <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Gebruik de eenvoudige gegevenslaag</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De effectieve kracht van de eenvoudige gegevenslaag is als volgt:

- Er zijn verschillende typen functies in een gegevens bron. of
- Voor functies in de gegevensset zijn verschillende stijl eigenschappen afzonderlijk ingesteld. of
- U weet niet wat de gegevensset precies bevat.

Als u bijvoorbeeld XML-gegevensfeeds wilt parseren, weet u mogelijk niet de exacte stijlen en geometrie typen van de functies. In het volgende voor beeld ziet u de kracht van de eenvoudige gegevenslaag door de functies van een KML-bestand weer te geven. Er worden ook verschillende opties gedemonstreerd die de klasse van de eenvoudige gegevenslaag biedt.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor eenvoudige gegevenslaag" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Zie de <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>eenvoudige gegevenslaag opties</a> voor Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Deze eenvoudige gegevenslaag maakt gebruik van de klasse [pop-upsjabloon](map-add-popup.md#add-popup-templates-to-the-map) om KML ballonnen of functie-eigenschappen weer te geven als een tabel. Standaard wordt alle inhoud die in het pop-upvenster wordt weer gegeven in de sandbox geplaatst als een beveiligings onderdeel van een IFRAME. Er zijn echter beperkingen:
>
> - Alle scripts, formulieren, aanwijzer vergrendeling en de bovenste navigatie functionaliteit zijn uitgeschakeld. Koppelingen mogen worden geopend op een nieuw tabblad wanneer erop wordt geklikt. 
> - Oudere browsers die de `srcdoc` para meter niet op iframes ondersteunen, zijn beperkt tot het renderen van een kleine hoeveelheid inhoud.
> 
> Als u de gegevens die in pop-upvensters worden geladen, vertrouwt en u wilt dat deze scripts die in pop-upvensters worden geladen, toegang hebben tot uw toepassing, kunt u dit uitschakelen door de optie pop-upsjablonen `sandboxContent` in te stellen op false. 

## <a name="default-supported-style-properties"></a>Standaard ondersteunde stijl eigenschappen

Zoals eerder vermeld, plaatst de eenvoudige gegevens laag enkele van de kern weergave lagen: bellen, symbool, lijn, veelhoek en geëxtrudeerde veelhoek. Vervolgens worden expressies gebruikt om te zoeken naar geldige stijl eigenschappen voor afzonderlijke functies.

Azure Maps-en GitHub stijl eigenschappen zijn de twee belangrijkste sets met ondersteunde eigenschapnamen. De meeste eigenschapnamen van de verschillende opties voor Azure Maps Layer worden ondersteund als stijl eigenschappen van functies in de laag Simple data. Er zijn expressies toegevoegd aan sommige laag opties om stijl eigenschaps namen te ondersteunen die vaak worden gebruikt door GitHub. Deze eigenschapnamen worden gedefinieerd door [de geojson-toewijzings ondersteuning van github](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)en worden gebruikt voor het opmaken van geojson-bestanden die worden opgeslagen en weer gegeven in het platform. Alle stijl eigenschappen van de GitHub worden ondersteund in de eenvoudige gegevenslaag, met uitzonde ring van de `marker-symbol` stijl eigenschappen.

Als de lezer over een minder algemene stijl eigenschap komt, wordt deze geconverteerd naar de dichtstbijzijnde Azure Maps stijl eigenschap. Daarnaast kunnen de standaard stijl expressies worden overschreven door gebruik te maken `getLayers` van de functie van de eenvoudige gegevenslaag en het bijwerken van de opties op een van de lagen.

De volgende secties bevatten informatie over de standaard stijl eigenschappen die worden ondersteund door de laag Simple data. De volg orde van de ondersteunde eigenschaps naam is ook de prioriteit van de eigenschap. Als twee stijl eigenschappen voor dezelfde laag optie zijn gedefinieerd, heeft het eerste item in de lijst een hogere prioriteit. Kleuren kunnen elke CSS3 kleur waarde zijn. HEX-, RGB-, RGBA-, HSL-, HSLA-of benoemde kleur waarde.

### <a name="bubble-layer-style-properties"></a>Eigenschappen van de laag stijl

Als een functie een `Point` of een is `MultiPoint` en de functie geen `image` eigenschap heeft die als een aangepast pictogram wordt gebruikt om het punt als een symbool weer te geven, wordt de functie weer gegeven met een `BubbleLayer` .

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] de `size` waarden en worden `scale` beschouwd als scalaire waarden en worden vermenigvuldigd met `8`

\[2 \] als de `marker-size` optie github is opgegeven, worden de volgende waarden gebruikt voor de RADIUS.

| Grootte van markering | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Clusters worden ook weer gegeven met behulp van de laag bubble. De RADIUS van een cluster is standaard ingesteld op `16` . De kleur van het cluster is afhankelijk van het aantal punten in het cluster, zoals hieronder wordt beschreven:

| aantal punten | Kleur    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 6     | `green`  |

### <a name="symbol-style-properties"></a>Eigenschappen van symbool stijl

Als een functie a `Point` of a is `MultiPoint` en de functie en een eigenschap heeft `image` die als een aangepast pictogram wordt gebruikt om het punt als een symbool weer te geven, wordt de functie weer gegeven met een `SymbolLayer` .

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] als de `marker-size` optie github is opgegeven, worden de volgende waarden gebruikt voor de optie voor de pictogram grootte.

| Grootte van markering | Grootte van symbool |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Als de punt functie een cluster is, `point_count_abbreviated` wordt de eigenschap weer gegeven als een tekst label. Er wordt geen afbeelding weer gegeven.

### <a name="line-style-properties"></a>Eigenschappen van lijn stijl

Als de functie een `LineString` , `MultiLineString` , of is `Polygon` `MultiPolygon` , wordt de functie weer gegeven met een `LineLayer` .

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Eigenschappen van polygoon stijl

Als de functie een `Polygon` of een is `MultiPolygon` en de functie geen `height` eigenschap heeft of de `height` eigenschap is ingesteld op nul, wordt de functie weer gegeven met een `PolygonLayer` .

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Eigenschappen van geëxtrudeerde polygoon stijl

Als de functie een `Polygon` of een is `MultiPolygon` en een eigenschap heeft `height` met een waarde die groter is dan 0, wordt de functie weer gegeven met een `PolygonExtrusionLayer` .

| Laag optie | Ondersteunde eigenschaps naam (en) | Standaardwaarde |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Een OGC-kaartlaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kernbewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)