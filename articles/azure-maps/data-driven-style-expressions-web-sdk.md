---
title: Gegevensgestuurde stijl expressies in de Azure Maps Web-SDK | Microsoft Azure kaarten
description: Meer informatie over gegevensgestuurde stijl expressies. Zie hoe u deze expressies gebruikt in de Web-SDK van Azure Maps om stijlen in kaarten aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680063"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Gegevensgestuurde stijl expressies (Web SDK)

Met expressies kunt u bedrijfs logica Toep assen op Opmaak opties die de eigenschappen zien die in elke vorm in een gegevens bron zijn gedefinieerd. Met expressies kunnen gegevens in een gegevens bron of laag worden gefilterd. Expressies kunnen bestaan uit voorwaardelijke logica, zoals if-instructies. En kunnen ze worden gebruikt voor het bewerken van gegevens met behulp van: teken reeks operators, logische Opera tors en wiskundige Opera tors.

Gegevensgestuurde stijlen verminderen de hoeveelheid code die nodig is voor het implementeren van bedrijfs logica rond stijlen. Bij gebruik in combi natie met lagen worden expressies geëvalueerd op het moment dat er een afzonderlijke thread wordt gegenereerd. Deze functionaliteit biedt betere prestaties vergeleken met het evalueren van bedrijfs logica op de UI-thread.

Deze video biedt een overzicht van gegevensgestuurde stijlen in de Azure Maps Web-SDK.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

Expressies worden weer gegeven als JSON-matrices. Het eerste element van een expressie in de matrix is een teken reeks die de naam van de expressie operator opgeeft. Bijvoorbeeld, "+" of "case". De volgende elementen (indien aanwezig) zijn de argumenten voor de expressie. Elk argument is een letterlijke waarde (een teken reeks, getal, Booleaans of `null` ) of een andere expressie matrix. De volgende pseudocode definieert de basis structuur van een expressie. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

De Azure Maps Web-SDK ondersteunt veel typen expressies. Expressies kunnen worden gebruikt in hun eigen of in combi natie met andere expressies.

| Type expressies | Beschrijving |
|---------------------|-------------|
| [Statistische expressie](#aggregate-expression) | Een expressie die een berekening definieert die wordt verwerkt via een set gegevens en kan worden gebruikt met de `clusterProperties` optie van een `DataSource` . |
| [Booleaanse expressies](#boolean-expressions) | Boole-expressies bieden een set Booleaanse Opera tors voor het evalueren van Boole-vergelijkingen. |
| [Kleur expressies](#color-expressions) | Kleur expressies maken het gemakkelijker om kleur waarden te maken en te bewerken. |
| [Voorwaardelijke expressies](#conditional-expressions) | Voorwaardelijke expressies bieden logische bewerkingen, zoals if-instructies. |
| [Gegevens expressies](#data-expressions) | Biedt toegang tot de eigenschaps gegevens in een functie. |
| [Interpoleer-en Step-expressies](#interpolate-and-step-expressions) | Interpolatie-en stap expressies kunnen worden gebruikt voor het berekenen van waarden in een geïnterpoleerde curve of stap functie. |
| [Laag-specifieke expressies](#layer-specific-expressions) | Speciale expressies die alleen van toepassing zijn op één laag. |
| [Wiskundige expressies](#math-expressions) | Biedt wiskundige Opera tors voor het uitvoeren van gegevensgestuurde berekeningen in het Framework van de expressie. |
| [Teken reeks operator expressies](#string-operator-expressions) | Teken reeks operator expressies voeren conversie bewerkingen uit op teken reeksen, zoals samen voegen en het converteren van de aanvraag. |
| [Type-expressies](#type-expressions) | Type-expressies bieden hulpprogram ma's voor het testen en omzetten van verschillende gegevens typen, zoals teken reeksen, getallen en Booleaanse waarden. |
| [Variabele bindings expressies](#variable-binding-expressions) | Met variabelen bindings expressies worden de resultaten van een berekening in een variabele opgeslagen en naar een andere locatie in een expressie verwezen, zonder dat de opgeslagen waarde opnieuw moet worden berekend. |
| [Expressie voor in-/uitzoomen](#zoom-expression) | Hiermee wordt het huidige zoom niveau van de kaart op de weergave tijd opgehaald. |

Alle voor beelden in dit document gebruiken de volgende functie om verschillende manieren te demonstreren waarin de verschillende typen expressies kunnen worden gebruikt. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>Gegevens expressies

Gegevens expressies bieden toegang tot de eigenschaps gegevens in een functie. 

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['at', number, array]` | waarde | Hiermee wordt een item uit een matrix opgehaald. |
| `['geometry-type']` | tekenreeks | Hiermee wordt het type geometrie van de functie opgehaald: Point, multi point, Lines Tring, multi line String, veelhoek, multiveelhoek. |
| `['get', string]` | waarde | Hiermee wordt de eigenschaps waarde opgehaald uit de eigenschappen van de huidige functie. Retourneert null als de aangevraagde eigenschap ontbreekt. |
| `['get', string, object]` | waarde | Hiermee wordt de eigenschaps waarde opgehaald uit de eigenschappen van het gegeven object. Retourneert null als de aangevraagde eigenschap ontbreekt. |
| `['has', string]` | booleaans | Hiermee wordt bepaald of de eigenschappen van een functie de opgegeven eigenschap hebben. |
| `['has', string, object]` | booleaans | Hiermee wordt bepaald of de eigenschappen van het object de opgegeven eigenschap hebben. |
| `['id']` | waarde | Hiermee haalt u de ID van de functie op als deze een bevat. |
| `['in', boolean | string | number, array]` | booleaans | Hiermee wordt bepaald of een item in een matrix voor komt |
| `['in', substring, string]` | booleaans | Hiermee wordt bepaald of een subtekenreeks voor komt in een teken reeks |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | getal | Retourneert de eerste positie waarop een item kan worden gevonden in een matrix of een subtekenreeks kan worden gevonden in een teken reeks of `-1` als de invoer niet kan worden gevonden. Hiermee wordt een optionele index voor het starten van de zoek opdracht geaccepteerd. |
| `['length', string | array]` | getal | Hiermee wordt de lengte van een teken reeks of een matrix opgehaald. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | teken reeks \| matrix | Retourneert een item uit een matrix of een subtekenreeks uit een teken reeks uit een opgegeven start index of tussen een start-index en een end-index als deze is ingesteld. De geretourneerde waarde is inclusief de begin index, maar niet van de eind index. |

**Voorbeelden**

Eigenschappen van een functie kunnen rechtstreeks in een expressie worden geopend met behulp van een `get` expressie. In dit voor beeld wordt de `zoneColor` waarde van de functie gebruikt om de eigenschap Color van een Bubble Layer op te geven. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Het bovenstaande voor beeld werkt prima, als alle punt functies de eigenschap hebben `zoneColor` . Als dat niet het geval is, gaat de kleur waarschijnlijk terug naar zwart. Als u de terugval kleur wilt wijzigen, gebruikt u een `case` expressie in combi natie met de `has` expressie om te controleren of de eigenschap bestaat. Als de eigenschap niet bestaat, retourneert u een terugval kleur.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Met bellen-en symbool lagen worden standaard de coördinaten van alle shapes in een gegevens bron weer gegeven. Dit gedrag kan de hoek punten van een veelhoek of een lijn markeren. De `filter` optie van de laag kan worden gebruikt om het type geometrie te beperken van de functies die worden weer gegeven, met behulp van een `['geometry-type']` expressie binnen een Boole-expressie. In het volgende voor beeld wordt een Bubble laag beperkt zodat alleen de `Point` functies worden weer gegeven.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

In het volgende voor beeld kunnen zowel `Point` als- `MultiPoint` functies worden weer gegeven. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Op dezelfde manier wordt het overzicht van veelhoeken in lijn lagen weer gegeven. Als u dit gedrag wilt uitschakelen in een line-laag, voegt u een filter toe dat alleen toestaat `LineString` en `MultiLineString` functies bevat.  

Hier volgen enkele aanvullende voor beelden van het gebruik van gegevens expressies:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Wiskundige expressies

Wiskundige expressies bieden wiskundige Opera tors voor het uitvoeren van gegevensgestuurde berekeningen in het Framework van de expressie.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['+', number, number, …]` | getal | Berekent de som van de opgegeven getallen. |
| `['-', number]` | getal | Trekt 0 af op het opgegeven aantal. |
| `['-', number, number]` | getal | De eerste getallen worden afgetrokken van het tweede getal. |
| `['*', number, number, …]` | getal | Vermenigvuldigt de opgegeven getallen met elkaar. |
| `['/', number, number]` | getal | Deelt het eerste getal door het tweede getal. |
| `['%', number, number]` | getal | Berekent het resterende aantal bij het delen van het eerste getal door het tweede getal. |
| `['^', number, number]` | getal | Berekent de waarde van de eerste waarde verheven tot de macht van het tweede getal. |
| `['abs', number]` | getal | Hiermee wordt de absolute waarde van het opgegeven getal berekend. |
| `['acos', number]` | getal | Berekent de arccosinus van het opgegeven getal. |
| `['asin', number]` | getal | Berekent de boog sinus van het opgegeven getal. |
| `['atan', number]` | getal | Berekent de arctangens van het opgegeven getal. |
| `['ceil', number]` | getal | Rondt het getal af naar het volgende geheel getal. |
| `['cos', number]` | getal | Berekent de co's van het opgegeven getal. |
| `['e']` | getal | Retourneert de wiskundige constante `e` . |
| `['floor', number]` | getal | Rondt het getal af naar het vorige gehele gehele getal. |
| `['ln', number]` | getal | Berekent de natuurlijke logaritme van het opgegeven getal. |
| `['ln2']` | getal | Retourneert de wiskundige constante `ln(2)` . |
| `['log10', number]` | getal | Berekent de logaritme met grondtal 10 van het opgegeven getal. |
| `['log2', number]` | getal | Berekent de logaritme met grondtal 2 van het opgegeven getal. |
| `['max', number, number, …]` | getal | Berekent het maximum aantal in de opgegeven reeks getallen. |
| `['min', number, number, …]` | getal | Berekent het minimale getal in de opgegeven reeks getallen. |
| `['pi']` | getal | Retourneert de wiskundige constante `PI` . |
| `['round', number]` | getal | Rondt het getal af op het dichtstbijzijnde gehele getal. De helft waarden worden afgerond van nul. Bijvoorbeeld, `['round', -1.5]` evalueert naar `-2` . |
| `['sin', number]` | getal | Berekent de sinus van het opgegeven getal. |
| `['sqrt', number]` | getal | Hiermee wordt de vierkantswortel van het opgegeven getal berekend. |
| `['tan', number]` | getal | Berekent de tangens van het opgegeven getal. |

## <a name="aggregate-expression"></a>Statistische expressie

Een statistische expressie definieert een berekening die wordt verwerkt via een set gegevens en kan worden gebruikt met de `clusterProperties` optie a `DataSource` . De uitvoer van deze expressies moet een getal of een Booleaanse waarde zijn. 

Een statistische expressie heeft drie waarden: een operator waarde, en begin waarde, en een expressie voor het ophalen van een eigenschap van elke functie in een gegevens om de cumulatieve bewerking op toe te passen. Deze expressie heeft de volgende indeling:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: een expressie functie die vervolgens wordt toegepast op alle waarden die worden berekend op basis van de `mapExpression` voor elk punt in het cluster. Ondersteunde Opera tors: 
    - Voor getallen: `+` , `*` , `max` , `min`
    - Voor Booleaanse waarden: `all` , `any`
- initialValue: een initiële waarde waarin de eerste berekende waarde wordt geaggregeerd.
- mapExpression: een expressie die wordt toegepast op elk punt in de gegevensset.

**Voorbeelden**

Als alle functies in een gegevensset een eigenschap hebben `revenue` die een getal is. Vervolgens kan de totale omzet van alle punten in een cluster, die zijn gemaakt op basis van de gegevensset, worden berekend. Deze berekening wordt uitgevoerd met behulp van de volgende statistische expressie: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Samengevoegde expressie

De `accumulated` expressie haalt de waarde van een cluster eigenschap op die tot nu toe is verzameld. Dit kan alleen worden gebruikt in de `clusterProperties` optie van een geclusterde `DataSource` bron.

**Gebruik**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Booleaanse expressies

Boole-expressies bieden een set Booleaanse Opera tors voor het evalueren van Boole-vergelijkingen.

Bij het vergelijken van waarden is de vergelijking strikt getypt. Waarden van verschillende typen worden altijd als ongelijk beschouwd. Gevallen waarin de typen bekend zijn om te worden geparseerd, worden beschouwd als ongeldig en er wordt een parseringsfout gegenereerd. 

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['!', boolean]` | booleaans | Logische negatie. Retourneert `true` of de invoer is `false` en `false` of de invoer is `true` . |
| `['!=', value, value]` | booleaans | Retourneert `true` of de invoer waarden niet gelijk zijn, `false` anders. |
| `['<', value, value]` | booleaans | Retourneert `true` of de eerste invoer strikt kleiner is dan de seconde, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `['<=', value, value]` | booleaans | Retourneert `true` of de eerste invoer kleiner dan of gelijk aan de tweede is, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `['==', value, value]` | booleaans | Retourneert `true` of de invoer waarden gelijk zijn, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `['>', value, value]` | booleaans | Retourneert `true` of de eerste invoer absoluut groter is dan de tweede, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `['>=' value, value]` | booleaans | Retourneert `true` of de eerste invoer groter is dan of gelijk is aan de tweede, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `['all', boolean, boolean, …]` | booleaans | Retourneert `true` of alle invoer waarden `true` , `false` anders. |
| `['any', boolean, boolean, …]` | booleaans | Retourneert `true` of een van de invoer waarden `true` is `false` . |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | booleaans | Retourneert `true` of de geëvalueerde functie volledig is opgenomen in een grens van de invoer geometrie, anders false. De invoer waarde kan een geldige geojson zijn van het type `Polygon` , `MultiPolygon` , `Feature` of `FeatureCollection` . Ondersteunde functies voor evaluatie:<br/><br/>-Punt: retourneert `false` als een punt zich op de grens bevindt of buiten de grens valt.<br/>-Lines Tring: retourneert of een `false` deel van een regel buiten de grens valt, de lijn snijdt de grens of het eind punt van een lijn op de grens. |

## <a name="conditional-expressions"></a>Voorwaardelijke expressies

Voorwaardelijke expressies bieden logische bewerkingen, zoals if-instructies.

Met de volgende expressies worden voorwaardelijke logica bewerkingen uitgevoerd op de invoer gegevens. De `case` expressie biedt bijvoorbeeld de logica ' If/then/else ' terwijl de `match` expressie lijkt op een ' switch-instructie '. 

### <a name="case-expression"></a>Case-expressie

Een `case` expressie is een type voorwaardelijke expressie waarmee de logica ' If/then/else ' wordt geboden. Dit type expressie wordt stapsgewijs door een lijst met Boole-voor waarden beschreven. Retourneert de uitvoer waarde van de eerste Booleaanse voor waarde om te evalueren naar waar.

De volgende pseudocode definieert de structuur van de `case` expressie. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Voorbeeld**

In het volgende voor beeld worden de verschillende Booleaanse voor waarden door lopen totdat er een wordt gevonden die hiermee wordt geëvalueerd `true` . vervolgens wordt de bijbehorende waarde geretourneerd. Als er geen Booleaanse voor waarde `true` wordt geëvalueerd, wordt een terugval waarde geretourneerd. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Overeenkomende expressie

Een `match` expressie is een type voorwaardelijke expressie die een switch-instructie als logica biedt. De invoer kan een wille keurige expressie zijn, zoals het `['get', 'entityType']` retour neren van een teken reeks of een getal. Elk label moet één letterlijke waarde of een matrix van letterlijke waarden zijn, waarvan de waarden alle teken reeksen of alle getallen moeten zijn. De invoer komt overeen als een van de waarden in de matrix overeenkomt. Elk label moet uniek zijn. Als het invoer type niet overeenkomt met het type van de labels, wordt het resultaat de terugval waarde.

De volgende pseudocode definieert de structuur van de `match` expressie. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Voorbeelden**

In het volgende voor beeld wordt met de `entityType` eigenschap van een punt functie in een Bubble Layer gezocht naar een overeenkomst. Als er een overeenkomst wordt gevonden, wordt deze opgegeven waarde geretourneerd of wordt de terugval waarde geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

In het volgende voor beeld wordt een matrix gebruikt om een set labels weer te geven die allemaal dezelfde waarde moeten retour neren. Deze methode is veel efficiënter dan elk label afzonderlijk te vermelden. Als in dit geval de `entityType` eigenschap "restaurant" of "grocery_store" is, wordt de kleur "Red" geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Coalesce-expressie

Een `coalesce` expressie wordt stapsgewijs door een reeks expressies uitgevoerd, totdat de eerste niet-null-waarde wordt opgehaald en deze waarde wordt geretourneerd. 

De volgende pseudocode definieert de structuur van de `coalesce` expressie. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Voorbeeld**

In het volgende voor beeld wordt een `coalesce` expressie gebruikt om de `textField` optie van een symbool laag in te stellen. Als de `title` eigenschap ontbreekt in de-functie of is ingesteld op `null` , zoekt de expressie vervolgens naar de `subTitle` eigenschap, als deze ontbreekt of `null` , wordt deze terugvallen op een lege teken reeks. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

In het volgende voor beeld wordt met behulp van een `coalesce` expressie het pictogram voor de eerste beschik bare afbeelding opgehaald uit de map sprite in een lijst met opgegeven afbeeldings namen.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Type-expressies

Type-expressies bieden hulpprogram ma's voor het testen en omzetten van verschillende gegevens typen, zoals teken reeksen, getallen en Booleaanse waarden.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Object [] | Hierbij wordt bevestigd dat de invoer een matrix is. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | booleaans | Er wordt bevestigd dat de invoer waarde een Boole is. Als er meerdere waarden worden gegeven, wordt elke waarde in de volg orde geëvalueerd totdat een Booleaanse waarde wordt verkregen. Als geen van de invoer Boole-waarden zijn, is de expressie een fout. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | collatie | Retourneert een collatie voor gebruik in vergelijkings bewerkingen met land instellingen. De opties hoofdletter gevoelig en diakritische gevoelig worden standaard ingesteld op ONWAAR. Het argument locale specificeert de IETF-taal code van de land instelling die moet worden gebruikt. Als er geen wordt gegeven, wordt de standaard land instelling gebruikt. Als de aangevraagde land instelling niet beschikbaar is, gebruikt de collatie een door het systeem gedefinieerde terugval land instelling. Opgelost-land instellingen gebruiken om de resultaten van het terugval gedrag van de land instelling te testen. |
| `['literal', array]`<br/><br/>`['literal', object]` | matrix \| object | Retourneert een letterlijke matrix of object waarde. Gebruik deze expressie om te voor komen dat een matrix of object als een expressie wordt geëvalueerd. Dit is nodig wanneer een matrix of object moet worden geretourneerd door een expressie. |
| `['image', string]` | tekenreeks | Hiermee wordt gecontroleerd of een opgegeven afbeeldings-ID is geladen in de Maps-installatie kopie sprite. Als dat het geval is, wordt de ID geretourneerd, anders wordt Null geretourneerd. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | getal | Hierbij wordt bevestigd dat de invoer waarde een getal is. Als er meerdere waarden worden gegeven, wordt elke waarde in de volg orde geëvalueerd totdat er een nummer wordt verkregen. Als geen van de invoer getallen zijn, is de expressie een fout. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Object | Hierbij wordt bevestigd dat de invoer waarde een object is.  Als er meerdere waarden worden gegeven, wordt elke waarde in de volg orde geëvalueerd totdat een object is verkregen. Als geen van de invoer objecten zijn, is de expressie een fout. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | tekenreeks | Hierbij wordt bevestigd dat de invoer waarde een teken reeks is. Als er meerdere waarden worden gegeven, wordt elke waarde in de volg orde geëvalueerd totdat er een teken reeks wordt verkregen. Als geen van de invoer teken reeksen zijn, is de expressie een fout. |
| `['to-boolean', value]` | booleaans | Hiermee wordt de invoer waarde geconverteerd naar een Boolean. Het resultaat is `false` wanneer de invoer een lege teken reeks,,, `0` `false` `null` of `NaN` ; anders is `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converteert de invoer waarde naar een kleur. Als er meerdere waarden worden gegeven, wordt elke waarde in de aangegeven volg orde geëvalueerd totdat de eerste geslaagde conversie is verkregen. Als geen van de invoer kan worden geconverteerd, is de expressie een fout. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | getal | Converteert de invoer waarde indien mogelijk naar een getal. Als de invoer is `null` of `false` , is het resultaat 0. Als de invoer is `true` , is het resultaat 1. Als de invoer een teken reeks is, wordt deze geconverteerd naar een getal met behulp van de [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) -teken reeks functie van de ECMAScript Language Specification. Als er meerdere waarden worden gegeven, wordt elke waarde in de aangegeven volg orde geëvalueerd totdat de eerste geslaagde conversie is verkregen. Als geen van de invoer kan worden geconverteerd, is de expressie een fout. |
| `['to-string', value]` | tekenreeks | Converteert de invoer waarde naar een teken reeks. Als de invoer is `null` , is het resultaat `""` . Als de invoer een Booleaanse waarde is, is het resultaat `"true"` of `"false"` . Als de invoer een getal is, wordt dit geconverteerd naar een teken reeks met behulp van de functie [toString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) -nummer van de ECMAScript Language Specification. Als de invoer een kleur is, wordt deze geconverteerd naar een CSS-kleur teken reeks `"rgba(r,g,b,a)"` . Anders wordt de invoer geconverteerd naar een teken reeks met behulp van de [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) -functie van de ECMAScript Language Specification. |
| `['typeof', value]` | tekenreeks | Retourneert een teken reeks die het type van de opgegeven waarde beschrijft. |

> [!TIP]
> Als er een fout bericht `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` wordt weer gegeven dat lijkt op de browser console, betekent dit dat er ergens in de code een expressie staat die een matrix heeft die geen teken reeks voor de eerste waarde heeft. Als u wilt dat de expressie een matrix retourneert, laat u de matrix teruglopen met de `literal` expressie. In het volgende voor beeld wordt de pictogram `offset` optie van een Symbol-laag ingesteld. deze moet een matrix zijn met twee cijfers, door gebruik te maken van een `match` expressie om te kiezen tussen twee verschuivings waarden op basis van de waarde van de  `entityType` eigenschap van de functie Point.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Kleur expressies

Kleur expressies maken het gemakkelijker om kleur waarden te maken en te bewerken.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Hiermee maakt u een kleur waarde van de onderdelen *rood*, *groen* en *blauw* die tussen `0` en moeten variëren `255` , en een alpha-onderdeel van `1` . Als een onderdeel zich buiten het bereik bevindt, is er een fout opgetreden in de expressie. |
| `['rgba', number, number, number, number]` | color | Hiermee maakt u een kleur waarde van *rode*, *groene* en *blauwe* onderdelen die tussen en moeten liggen `0` `255` en een alpha-component binnen een bereik van `0` en `1` . Als een onderdeel zich buiten het bereik bevindt, is er een fout opgetreden in de expressie. |
| `['to-rgba']` | \[getal, getal, getal, getal\] | Retourneert een matrix met vier elementen met de *rode*, *groene*, *blauwe* en *alpha* -onderdelen van de invoer kleur, in die volg orde. |

**Voorbeeld**

In het volgende voor beeld wordt een RGB-kleur waarde gemaakt met een *rode* waarde van `255` en waarden voor *groen* en *blauw* die worden berekend door te vermenigvuldigen `2.5` met de waarde van de `temperature` eigenschap. Wanneer de Tempe ratuur wordt gewijzigd, verandert de kleur in verschillende tinten *rood*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Teken reeks operator expressies

Teken reeks operator expressies voeren conversie bewerkingen uit op teken reeksen, zoals samen voegen en het converteren van de aanvraag. 

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['concat', string, string, …]` | tekenreeks | Meerdere teken reeksen samen voegen. Elke waarde moet een teken reeks zijn. Gebruik de `to-string` type-expressie om andere waardetypen zo nodig te converteren naar een teken reeks. |
| `['downcase', string]` | tekenreeks | Hiermee wordt de opgegeven teken reeks geconverteerd naar kleine letters. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| booleaans | Bepaalt of de invoer teken reeks een tekenset gebruikt die wordt ondersteund door de huidige lettertype verzameling. Bijvoorbeeld: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | tekenreeks | Retourneert het IETF-taal label van de land instelling die wordt gebruikt door de beschik bare collatie. Dit kan worden gebruikt om de standaard land instelling van het systeem te bepalen of om te bepalen of een aangevraagde land instelling is geladen. |
| `['upcase', string]` | tekenreeks | Converteert de opgegeven teken reeks naar hoofd letters. |

**Voorbeeld**

In het volgende voor beeld wordt de `temperature` eigenschap van de punt functie geconverteerd naar een teken reeks en wordt vervolgens ' °F ' aan het einde van het object aaneengeschakeld.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

De bovenstaande expressie geeft een pincode op de kaart weer met de tekst ' 64 °F ', zoals in de onderstaande afbeelding wordt weer gegeven.

<center>

![Voor beeld van een expressie voor ](media/how-to-expressions/string-operator-expression.png) een teken reeks operator </center>

## <a name="interpolate-and-step-expressions"></a>Interpoleer-en Step-expressies

Interpolatie-en stap expressies kunnen worden gebruikt voor het berekenen van waarden in een geïnterpoleerde curve of stap functie. Deze expressies nemen in een expressie op die bijvoorbeeld een numerieke waarde retourneert als invoer `['get',  'temperature']` . De invoer waarde wordt geëvalueerd ten opzichte van paren van invoer-en uitvoer waarden om de waarde te bepalen die het beste past bij de geïnterpoleerde curve of stap functie. De uitvoer waarden worden ' stops ' genoemd. De invoer waarden voor elke Stop moeten een getal zijn en moeten in oplopende volg orde staan. De uitvoer waarden moeten een getal, een matrix van getallen of een kleur zijn.

### <a name="interpolate-expression"></a>Interpole-expressie

Een `interpolate` expressie kan worden gebruikt om een continue, soepele set waarden te berekenen door te interpolatie tussen de stop waarden. Een `interpolate` expressie die kleur waarden retourneert, produceert een kleur overgang waarbij resultaat waarden worden geselecteerd uit.

Er zijn drie typen interpolatie methoden die kunnen worden gebruikt in een `interpolate` expressie:
 
* `['linear']` -Interpoleeert lineair tussen het paar stops.
* `['exponential', base]` -Interpoleert exponentieel tussen de stops. De `base` waarde bepaalt de snelheid waarmee de uitvoer toeneemt. Hoe hoger de waarde, hoe groter de uitvoer meer naar het hoge einde van het bereik. Een `base` waarde dicht bij 1 resulteert in een uitvoer die meer lineair toeneemt.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpoles met behulp van een [kubieke Bézier-curve](https://developer.mozilla.org/docs/Web/CSS/timing-function) die is gedefinieerd door de gegeven besturings punten.

Hier volgt een voor beeld van hoe de verschillende typen interpolatie eruitzien. 

| Lineair  | Exponentieel | Kubieke Bézier |
|---------|-------------|--------------|
| ![Grafiek met lineaire interpolatie](media/how-to-expressions/linear-interpolation.png) | ![Exponentiële interpolatie grafiek](media/how-to-expressions/exponential-interpolation.png) | ![Grafiek met kubieke Bezier-interpolatie](media/how-to-expressions/bezier-curve-interpolation.png) |

De volgende pseudocode definieert de structuur van de `interpolate` expressie. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Voorbeeld**

In het volgende voor beeld wordt een `linear interpolate` expressie gebruikt om de `color` eigenschap van een tekenlaag in te stellen op basis van de `temperature` eigenschap van de functie Point. Als de `temperature` waarde lager is dan 60, wordt ' Blue ' geretourneerd. Als de waarde tussen 60 en minder is dan 70, wordt geel geretourneerd. Als de waarde tussen 70 en minder dan 80, wordt ' oranje ' geretourneerd. Als de waarde 80 of hoger is, wordt ' Red ' geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

In de volgende afbeelding ziet u hoe de kleuren voor de bovenstaande expressie worden gekozen.
 
<center>

![Voor beeld ](media/how-to-expressions/interpolate-expression-example.png) van interpolatie-expressie </center>

### <a name="step-expression"></a>Expressie voor stap

Een `step` expressie kan worden gebruikt om discrete, getrapte resultaat waarden te berekenen door te evalueren van een [piecewise-constante functie](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) die door stops wordt gedefinieerd. 

De volgende pseudocode definieert de structuur van de `step` expressie. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Met stap expressies wordt de uitvoer waarde van de stop net vóór de invoer waarde geretourneerd of de eerste invoer waarde als de invoer kleiner is dan de eerste stop. 

**Voorbeeld**

In het volgende voor beeld wordt een `step` expressie gebruikt om de `color` eigenschap van een tekenlaag in te stellen op basis van de `temperature` eigenschap van de functie Point. Als de `temperature` waarde lager is dan 60, wordt ' Blue ' geretourneerd. Als de waarde tussen 60 en minder dan 70, wordt ' Yellow ' geretourneerd. Als de waarde tussen 70 en minder dan 80, wordt ' oranje ' geretourneerd. Als de waarde 80 of hoger is, wordt ' Red ' geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

In de volgende afbeelding ziet u hoe de kleuren voor de bovenstaande expressie worden gekozen.
 
<center>

![Voor beeld van een stap-expressie](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Toepassingsspecifieke expressies

Speciale expressies die alleen van toepassing zijn op specifieke lagen.

### <a name="heat-map-density-expression"></a>Dichtheids expressie voor heatmap

Met een dichtheids expressie voor heatmap wordt de waarde van de heatmap voor de kaart voor elke pixel in een heatmap van een hitte opgehaald en gedefinieerd als `['heatmap-density']` . Deze waarde is een getal tussen `0` en `1` . Deze wordt gebruikt in combi natie met een `interpolation` or- `step` expressie om de kleur overgang te definiëren die wordt gebruikt voor het inkleuren van de heatmap. Deze expressie kan alleen worden gebruikt in de [kleur optie](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) van de laag van de heatmap.

> [!TIP]
> De kleur bij index 0, in een interpolatie-expressie of de standaard kleur van een stap kleur, definieert de kleur van het gebied waar er geen gegevens zijn. De kleur bij index 0 kan worden gebruikt om een achtergrond kleur te definiëren. Veel gewenst om deze waarde in te stellen op transparant of semi-transparant zwart.

**Voorbeeld**

In dit voor beeld wordt een lijn-interpolatie-expressie gebruikt voor het maken van een vloeiend kleur verloop voor het renderen van de heatmap. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Naast het gebruik van een glad verloop om een heatmap in te vullen, kunnen kleuren worden opgegeven in een set bereiken met behulp van een `step` expressie. Als u een `step` expressie gebruikt voor het inkleuren van de hitte kaart, wordt de densiteit in bereiken weer gegeven die lijken op een contour-of radar stijl kaart.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Zie de documentatie [een hitte kaart toevoegen](map-add-heat-map-layer.md) voor meer informatie.

### <a name="line-progress-expression"></a>Expressie voor voortgangs lijn

Met een expressie voor de voortgangs lijn wordt de voortgang van een verloop lijn in een line-laag opgehaald en gedefinieerd als `['line-progress']` . Deze waarde is een getal tussen 0 en 1. Deze wordt gebruikt in combi natie met een `interpolation` or- `step` expressie. Deze expressie kan alleen worden gebruikt met de [optie strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) van de laag. 

> [!NOTE]
> Voor de `strokeGradient` optie van de laag moet de `lineMetrics` optie van de gegevens bron worden ingesteld op `true` .

**Voorbeeld**

In dit voor beeld wordt de `['line-progress']` expressie gebruikt om een kleur overgang toe te passen op de lijn van een regel.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Zie Live voor beeld](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressie voor tekst veld notatie

De expressie voor de tekst veld notatie kan worden gebruikt met de `textField` optie van de eigenschap Symbol Layers `textOptions` om gemengde tekst opmaak te bieden. Met deze expressie kan een set invoer teken reeksen en opmaak opties worden opgegeven. De volgende opties kunnen worden opgegeven voor elke invoer reeks in deze expressie.

 * `'font-scale'` -Hiermee geeft u de schaal factor voor de teken grootte op. Als deze waarde wordt opgegeven, wordt de `size` eigenschap van de `textOptions` afzonderlijke teken reeks overschreven.
 * `'text-font'` -Hiermee geeft u een of meer lettertype families op die moeten worden gebruikt voor deze teken reeks. Als deze waarde wordt opgegeven, wordt de `font` eigenschap van de `textOptions` afzonderlijke teken reeks overschreven.

De volgende pseudocode definieert de structuur van de tekst veld indelings expressie. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Voorbeeld**

In het volgende voor beeld wordt het tekst veld opgemaakt door een vet letter type toe te voegen en de teken grootte van de `title` eigenschap van de functie omhoog te schalen. In dit voor beeld wordt ook de `subTitle` eigenschap van de functie op een nieuwe regel toegevoegd, met een geschaalde teken grootte.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Met deze laag wordt de punt functie weer gegeven zoals in de onderstaande afbeelding:
 
<center>

![Afbeelding van punt functie met opgemaakte tekst veld ](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>De expressie getalnotatie

De `number-format` expressie kan alleen worden gebruikt met de `textField` optie van een Symbol-laag. Met deze expressie wordt het gegeven getal geconverteerd naar een ingedeelde teken reeks. Met deze expressie wordt de functie [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) van Java script ingepakt en wordt de volgende set opties ondersteund.

 * `locale` -Geef deze optie op voor het converteren van getallen naar teken reeksen op een manier die wordt uitgelijnd met de opgegeven taal. Geef een [BCP 47-taal label](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) door in deze optie.
 * `currency` -Om het getal te converteren naar een teken reeks die een valuta vertegenwoordigt. Mogelijke waarden zijn de [ISO 4217-valuta codes](https://en.wikipedia.org/wiki/ISO_4217), zoals "USD" voor de Amerikaanse dollar, "EUR" voor de euro of "CNY" voor de Chinese RMB.
 * `'min-fraction-digits'` -Geeft het minimale aantal decimalen aan dat moet worden meegenomen in de teken reeks versie van het getal.
 * `'max-fraction-digits'` -Hiermee geeft u het maximum aantal decimalen op dat moet worden meegenomen in de teken reeks versie van het getal.

De volgende pseudocode definieert de structuur van de tekst veld indelings expressie. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Voorbeeld**

In het volgende voor beeld wordt een `number-format` expressie gebruikt om te wijzigen hoe de `revenue` eigenschap van de punt functie wordt weer gegeven in de `textField` optie van een Symbol-laag, zodat deze een Amerikaanse dollar waarde wordt weer gegeven.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Met deze laag wordt de punt functie weer gegeven zoals in de onderstaande afbeelding:

<center>

![Voor beeld van een expressie voor ](media/how-to-expressions/number-format-expression.png) getalnotatie </center>

### <a name="image-expression"></a>Expressie voor afbeelding

Een afbeeldings expressie kan worden gebruikt met `image` de `textField` Opties en van een Symbol-laag en de `fillPattern` optie van de polygoon laag. Met deze expressie wordt gecontroleerd of de aangevraagde afbeelding in de stijl bestaat en retourneert de naam van de omgezette installatie kopie of `null` , afhankelijk van het feit of de afbeelding zich op dat moment in de stijl bevindt. Dit validatie proces is synchroon en vereist dat de afbeelding is toegevoegd aan de stijl voordat deze wordt aangevraagd in het argument afbeelding.

**Voorbeeld**

In het volgende voor beeld wordt een `image` expressie gebruikt om een pictogram inline met tekst in een symbool-laag toe te voegen. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Met deze laag wordt het tekst veld weer gegeven in de laag symbool, zoals wordt weer gegeven in de onderstaande afbeelding:

<center>

![Voor beeld ](media/how-to-expressions/image-expression.png) van een afbeeldings expressie </center>

## <a name="zoom-expression"></a>Expressie voor in-/uitzoomen

Een `zoom` expressie wordt gebruikt om het huidige zoom niveau van de kaart op te halen op weergave tijd en wordt gedefinieerd als `['zoom']` . Deze expressie retourneert een getal tussen het minimale en maximale zoom niveau bereik van de kaart. De besturings elementen voor de Azure Maps interactieve kaart voor web-en Android-ondersteuning 25 zoom niveaus, genummerd op 0 t/m 24. Als u de `zoom` expressie gebruikt, kunnen stijlen dynamisch worden gewijzigd wanneer het zoom niveau van de kaart wordt gewijzigd. De `zoom` expressie kan alleen worden gebruikt met `interpolate` and- `step` expressies.

**Voorbeeld**

De RADIUS van gegevens punten die worden weer gegeven in de laag van de heatmap hebben standaard een straal van vaste pixels voor alle zoom niveaus. Naarmate de kaart wordt ingezoomd, worden de gegevens samen en de heatmap van de kaart op verschillende manieren weer gegeven. Een `zoom` expressie kan worden gebruikt om de RADIUS voor elk zoom niveau zodanig te schalen dat elk gegevens punt overeenkomt met hetzelfde fysieke gebied van de kaart. De IT-laag maakt de heatmap statisch en consistent. Elk zoom niveau van de kaart heeft twee keer zoveel pixels verticaal en horizon taal als het vorige zoom niveau. Door de RADIUS te schalen, zodat deze wordt verdubbeld met elk zoom niveau, wordt een heatmap gemaakt die consistent is op alle zoom niveaus. U kunt dit doen met behulp van de `zoom` expressie met een `base 2 exponential interpolation` expressie, waarbij de pixel RADIUS is ingesteld voor het minimale zoom niveau en een geschaalde RADIUS voor het maximale zoom niveau dat wordt berekend, zoals `2 * Math.pow(2, minZoom - maxZoom)` hieronder wordt weer gegeven.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Zie Live voor beeld](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Variabele bindings expressies

Met variabelen bindings expressies worden de resultaten van een berekening in een variabele opgeslagen. Dat wil zeggen dat er meerdere keren naar de resultaten van de berekening kan worden verwezen in een expressie. Het is een handige optimalisatie voor expressies waarbij veel berekeningen worden betrokken.

| Expression | Retourtype | Beschrijving |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;Let,<br/>&nbsp;&nbsp;&nbsp;&nbsp;NAME1: teken reeks,<br/>&nbsp;&nbsp;&nbsp;&nbsp;waarde1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;naam2: teken reeks,<br/>&nbsp;&nbsp;&nbsp;&nbsp;waarde2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Slaat een of meer waarden op als variabelen voor gebruik door de `var` expressie in de onderliggende expressie waarmee het resultaat wordt geretourneerd. |
| `['var', name: string]` | alle | Verwijst naar een variabele die is gemaakt met behulp van de `let` expressie. |

**Voorbeeld**

In dit voor beeld wordt een expressie gebruikt waarmee de opbrengst wordt berekend ten opzichte van de Tempe ratuur en vervolgens een expressie wordt gebruikt `case` om verschillende Boole-bewerkingen op deze waarde te evalueren. De `let` expressie wordt gebruikt voor het opslaan van de opbrengst ten opzichte van de temperatuur verhouding, zodat deze slechts eenmaal hoeft te worden berekend. De `var` expressie verwijst naar deze variabele zo vaak als nodig is zonder dat deze opnieuw moet worden berekend.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden voor het implementeren van expressies:

> [!div class="nextstepaction"] 
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)

Meer informatie over de laag opties die expressies ondersteunen:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)