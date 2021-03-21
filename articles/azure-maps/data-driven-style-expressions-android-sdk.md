---
title: Gegevensgestuurde stijl expressies in Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over gegevensgestuurde stijl expressies. Zie expressies gebruiken in de Azure Maps Android-SDK om stijlen in kaarten aan te passen.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097411"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Gegevensgestuurde stijl expressies (Android SDK)

Met expressies kunt u bedrijfs logica Toep assen op Opmaak opties die de eigenschappen zien die in elke vorm in een gegevens bron zijn gedefinieerd. Met expressies kunnen gegevens in een gegevens bron of laag worden gefilterd. Expressies kunnen bestaan uit voorwaardelijke logica, zoals if-instructies. En kunnen ze worden gebruikt voor het bewerken van gegevens met behulp van: teken reeks operators, logische Opera tors en wiskundige Opera tors.

Gegevensgestuurde stijlen verminderen de hoeveelheid code die nodig is voor het implementeren van bedrijfs logica rond stijlen. Bij gebruik in combi natie met lagen worden expressies geëvalueerd op het moment dat er een afzonderlijke thread wordt gegenereerd. Deze functionaliteit biedt betere prestaties vergeleken met het evalueren van bedrijfs logica op de UI-thread.

De Azure Maps Android SDK ondersteunt bijna alle dezelfde stijl expressies als de Web-SDK van Azure Maps, zodat alle dezelfde concepten die in de [gegevensgestuurde stijl expressies (Web-SDK)](data-driven-style-expressions-web-sdk.md) worden beschreven, kunnen worden overgedragen in een Android-app. Alle stijl expressies in de Azure Maps Android SDK zijn beschikbaar onder de `com.microsoft.azure.maps.mapcontrol.options.Expression` naam ruimte. Er zijn veel verschillende typen stijl expressies.

| Type expressies | Beschrijving |
|---------------------|-------------|
| [Booleaanse expressies](#boolean-expressions) | Boole-expressies bieden een set Booleaanse Opera tors voor het evalueren van Boole-vergelijkingen. |
| [Kleur expressies](#color-expressions) | Kleur expressies maken het gemakkelijker om kleur waarden te maken en te bewerken. |
| [Voorwaardelijke expressies](#conditional-expressions) | Voorwaardelijke expressies bieden logische bewerkingen, zoals if-instructies. |
| [Gegevens expressies](#data-expressions) | Biedt toegang tot de eigenschaps gegevens in een functie. |
| [Interpoleer-en Step-expressies](#interpolate-and-step-expressions) | Interpolatie-en stap expressies kunnen worden gebruikt voor het berekenen van waarden in een geïnterpoleerde curve of stap functie. |
| [Op JSON gebaseerde expressies](#json-based-expressions) | Maakt het eenvoudig om onbewerkte op JSON gebaseerde expressies te hergebruiken die zijn gemaakt voor de Web-SDK met de Android SDK. |  
| [Laag-specifieke expressies](#layer-specific-expressions) | Speciale expressies die alleen van toepassing zijn op één laag. |
| [Wiskundige expressies](#math-expressions) | Biedt wiskundige Opera tors voor het uitvoeren van gegevensgestuurde berekeningen in het Framework van de expressie. |
| [Teken reeks operator expressies](#string-operator-expressions) | Teken reeks operator expressies voeren conversie bewerkingen uit op teken reeksen, zoals samen voegen en het converteren van de aanvraag. |
| [Type-expressies](#type-expressions) | Type-expressies bieden hulpprogram ma's voor het testen en omzetten van verschillende gegevens typen, zoals teken reeksen, getallen en Booleaanse waarden. |
| [Variabele bindings expressies](#variable-binding-expressions) | Met variabelen bindings expressies worden de resultaten van een berekening in een variabele opgeslagen en naar een andere locatie in een expressie verwezen, zonder dat de opgeslagen waarde opnieuw moet worden berekend. |
| [Expressie voor in-/uitzoomen](#zoom-expression) | Hiermee wordt het huidige zoom niveau van de kaart op de weergave tijd opgehaald. |

> [!NOTE]
> De syntaxis voor expressies is grotendeels identiek in Java en Kotlin. Als u de documentatie hebt ingesteld op Kotlin, maar code blokken voor Java ziet, is de code in beide talen identiek.

In alle voor beelden in deze sectie van het document wordt gebruikgemaakt van de volgende functie om verschillende manieren te demonstreren waarop deze expressies kunnen worden gebruikt.

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

De volgende code laat zien hoe u deze geojson-functie hand matig in een app kunt maken.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

De volgende code laat zien hoe u de stringified-versie van het JSON-object deserialiseren in een geojson-functie in een app.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>Op JSON gebaseerde expressies

De Azure Maps Web-SDK biedt ook ondersteuning voor gegevensgestuurde stijl expressies die worden weer gegeven met een JSON-matrix. Deze dezelfde expressies kunnen opnieuw worden gemaakt met behulp van de systeem eigen `Expression` klasse in de Android-SDK. Deze op JSON gebaseerde expressies kunnen ook worden omgezet in een teken reeks met behulp van een webfunctie zoals `JSON.stringify` en door gegeven aan de- `Expression.raw(String rawExpression)` methode. Neem bijvoorbeeld de volgende JSON-expressie op.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

De stringified-versie van de bovenstaande expressie zou zijn `"['get','title']"` en als volgt in de Android-SDK kunnen worden gelezen.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

Met deze aanpak kunt u eenvoudig stijl expressies opnieuw gebruiken tussen mobiele apps en webtoepassingen die gebruikmaken van Azure Maps.

Deze video biedt een overzicht van gegevensgestuurde stijlen in Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Gegevens expressies

Gegevens expressies bieden toegang tot de eigenschaps gegevens in een functie.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `accumulated()` | getal | Hiermee haalt u de waarde van een cluster eigenschap op die tot nu toe is verzameld. |
| `at(number | Expression, Expression)` | waarde | Hiermee wordt een item uit een matrix opgehaald. |
| `geometryType()` | tekenreeks | Hiermee wordt het type geometrie van de functie opgehaald: Point, multi point, Lines Tring, multi line String, veelhoek, multiveelhoek. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | waarde | Hiermee wordt de eigenschaps waarde opgehaald uit de eigenschappen van het gegeven object. Retourneert null als de aangevraagde eigenschap ontbreekt. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | booleaans | Hiermee wordt bepaald of de eigenschappen van een functie de opgegeven eigenschap hebben. |
| `id()` | waarde | Hiermee haalt u de ID van de functie op als deze een bevat. |
| `in(string | number | Expression, Expression)` | booleaans | Hiermee wordt bepaald of een item in een matrix voor komt |
| `length(string | Expression)` | getal | Hiermee wordt de lengte van een teken reeks of een matrix opgehaald. |
| `properties()`| waarde | Hiermee wordt het object functie-eigenschappen opgehaald. |

De volgende Web SDK-stijl expressies worden niet ondersteund in de Android-SDK:

- index-van
- gereedschap

**Voorbeelden**

Eigenschappen van een functie kunnen rechtstreeks in een expressie worden geopend met behulp van een `get` expressie. In dit voor beeld wordt de `zoneColor` waarde van de functie gebruikt om de eigenschap Color van een Bubble Layer op te geven.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

Het bovenstaande voor beeld werkt prima, als alle punt functies de eigenschap hebben `zoneColor` . Als dat niet het geval is, gaat de kleur waarschijnlijk terug naar zwart. Als u de terugval kleur wilt wijzigen, gebruikt u een `switchCase` expressie in combi natie met de `has` expressie om te controleren of de eigenschap bestaat. Als de eigenschap niet bestaat, retourneert u een terugval kleur.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

Met bellen-en symbool lagen worden standaard de coördinaten van alle shapes in een gegevens bron weer gegeven. Dit gedrag kan de hoek punten van een veelhoek of een lijn markeren. De `filter` optie van de laag kan worden gebruikt om het type geometrie te beperken van de functies die worden weer gegeven, met behulp van een `geometryType` expressie binnen een Boole-expressie. In het volgende voor beeld wordt een Bubble laag beperkt zodat alleen de `Point` functies worden weer gegeven.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

In het volgende voor beeld kunnen zowel `Point` als- `MultiPoint` functies worden weer gegeven.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Op dezelfde manier wordt het overzicht van veelhoeken in lijn lagen weer gegeven. Als u dit gedrag wilt uitschakelen in een line-laag, voegt u een filter toe dat alleen toestaat `LineString` en `MultiLineString` functies bevat.  

Hier volgen enkele aanvullende voor beelden van het gebruik van gegevens expressies:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Wiskundige expressies

Wiskundige expressies bieden wiskundige Opera tors voor het uitvoeren van gegevensgestuurde berekeningen in het Framework van de expressie.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `abs(number | Expression)` | getal | Hiermee wordt de absolute waarde van het opgegeven getal berekend. |
| `acos(number | Expression)` | getal | Berekent de arccosinus van het opgegeven getal. |
| `asin(number | Expression)` | getal | Berekent de boog sinus van het opgegeven getal. |
| `atan(number | Expression)` | getal | Berekent de arctangens van het opgegeven getal. |
| `ceil(number | Expression)` | getal | Rondt het getal af naar het volgende geheel getal. |
| `cos(number | Expression)` | getal | Berekent de co's van het opgegeven getal. |
| `division(number, number)` \| `division(Expression, Expression)` | getal | Deelt het eerste getal door het tweede getal. Equivalente Web SDK-expressie: `/` |
| `e()` | getal | Retourneert de wiskundige constante `e` . |
| `floor(number | Expression)` | getal | Rondt het getal af naar het vorige gehele gehele getal. |
| `log10(number | Expression)` | getal | Berekent de logaritme met grondtal 10 van het opgegeven getal. |
| `log2(number | Expression)` | getal | Berekent de logaritme met grondtal 2 van het opgegeven getal. |
| `ln(number | Expression)` | getal | Berekent de natuurlijke logaritme van het opgegeven getal. |
| `ln2()` | getal | Retourneert de wiskundige constante `ln(2)` . |
| `max(numbers... | expressions...)` | getal | Berekent het maximum aantal in de opgegeven reeks getallen. |
| `min(numbers... | expressions...)` | getal | Berekent het minimale getal in de opgegeven reeks getallen. |
| `mod(number, number)` \| `mod(Expression, Expression)` | getal | Berekent het resterende aantal bij het delen van het eerste getal door het tweede getal. Equivalente Web SDK-expressie: `%` |
| `pi()` | getal | Retourneert de wiskundige constante `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | getal | Berekent de waarde van de eerste waarde verheven tot de macht van het tweede getal. |
| `product(numbers... | expressions...)` | getal | Vermenigvuldigt de opgegeven getallen met elkaar. Equivalente Web SDK-expressie: `*` |
| `round(number | Expression)` | getal | Rondt het getal af op het dichtstbijzijnde gehele getal. De helft waarden worden afgerond van nul. Bijvoorbeeld, `round(-1.5)` evalueert naar `-2` . |
| `sin(number | Expression)` | getal | Berekent de sinus van het opgegeven getal. |
| `sqrt(number | Expression)` | getal | Hiermee wordt de vierkantswortel van het opgegeven getal berekend. |
| `subtract(number | Expression` | getal | Trekt 0 af op het opgegeven aantal. |
| `subtract(number | Expression, number | Expression)` | getal | De eerste getallen worden afgetrokken van het tweede getal. |
| `sum(numbers... | expressions...)` | getal | Berekent de som van de opgegeven getallen. |
| `tan(number | Expression)` | getal | Berekent de tangens van het opgegeven getal. |

## <a name="boolean-expressions"></a>Booleaanse expressies

Boole-expressies bieden een set Booleaanse Opera tors voor het evalueren van Boole-vergelijkingen.

Bij het vergelijken van waarden is de vergelijking strikt getypt. Waarden van verschillende typen worden altijd als ongelijk beschouwd. Gevallen waarin de typen bekend zijn om te worden geparseerd, worden beschouwd als ongeldig en er wordt een parseringsfout gegenereerd.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `all(Expression...)` | booleaans | Retourneert `true` of alle invoer waarden `true` , `false` anders. |
| `any(Expression...)` | booleaans | Retourneert `true` of een van de invoer waarden `true` is `false` . |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de invoer waarden gelijk zijn, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de eerste invoer absoluut groter is dan de tweede, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de eerste invoer groter is dan of gelijk is aan de tweede, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de eerste invoer strikt kleiner is dan de seconde, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de eerste invoer kleiner dan of gelijk aan de tweede is, `false` anders. De argumenten moeten beide ofwel teken reeksen of beide getallen zijn. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleaans | Retourneert `true` of de invoer waarden niet gelijk zijn, `false` anders. |
| `not(Expression | boolean)` | booleaans | Logische negatie. Retourneert `true` of de invoer is `false` en `false` of de invoer is `true` . |

## <a name="conditional-expressions"></a>Voorwaardelijke expressies

Voorwaardelijke expressies bieden logische bewerkingen, zoals if-instructies.

Met de volgende expressies worden voorwaardelijke logica bewerkingen uitgevoerd op de invoer gegevens. De `switchCase` expressie biedt bijvoorbeeld de logica ' If/then/else ' terwijl de `match` expressie lijkt op een ' switch-instructie '.

### <a name="switch-case-expression"></a>Case-expressie voor Switch

Een `switchCase` expressie is een type voorwaardelijke expressie waarmee de logica ' If/then/else ' wordt geboden. Dit type expressie wordt stapsgewijs door een lijst met Boole-voor waarden beschreven. Retourneert de uitvoer waarde van de eerste Booleaanse voor waarde om te evalueren naar waar.

De volgende pseudocode definieert de structuur van de `switchCase` expressie.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Voorbeeld**

In het volgende voor beeld worden de verschillende Booleaanse voor waarden door lopen totdat er een wordt gevonden die hiermee wordt geëvalueerd `true` . vervolgens wordt de bijbehorende waarde geretourneerd. Als er geen Booleaanse voor waarde `true` wordt geëvalueerd, wordt een terugval waarde geretourneerd.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Overeenkomende expressie

Een `match` expressie is een type voorwaardelijke expressie die een switch-instructie als logica biedt. De invoer kan een wille keurige expressie zijn, zoals het `get( "entityType")` retour neren van een teken reeks of een getal. Elke Stop moet een label hebben dat bestaat uit één letterlijke waarde of een matrix van letterlijke waarden, waarvan de waarden alle teken reeksen of alle getallen moeten zijn. De invoer komt overeen als een van de waarden in de matrix overeenkomt. Elk stop label moet uniek zijn. Als het invoer type niet overeenkomt met het type van de labels, wordt het resultaat de standaard terugval waarde.

De volgende pseudocode definieert de structuur van de `match` expressie.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Voorbeelden**

In het volgende voor beeld wordt met de `entityType` eigenschap van een punt functie in een Bubble Layer gezocht naar een overeenkomst. Als er een overeenkomst wordt gevonden, wordt deze opgegeven waarde geretourneerd of wordt de terugval waarde geretourneerd.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

In het volgende voor beeld wordt een matrix gebruikt om een set labels weer te geven die allemaal dezelfde waarde moeten retour neren. Deze methode is veel efficiënter dan elk label afzonderlijk te vermelden. Als in dit geval de `entityType` eigenschap "restaurant" of "grocery_store" is, wordt de kleur "Red" geretourneerd.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Coalesce-expressie

Een `coalesce` expressie wordt stapsgewijs door een reeks expressies uitgevoerd, totdat de eerste niet-null-waarde wordt opgehaald en deze waarde wordt geretourneerd.

De volgende pseudocode definieert de structuur van de `coalesce` expressie.

```java
coalesce(Expression... input)
```

**Voorbeeld**

In het volgende voor beeld wordt een `coalesce` expressie gebruikt om de `textField` optie van een symbool laag in te stellen. Als de `title` eigenschap ontbreekt in de-functie of is ingesteld op `null` , zoekt de expressie vervolgens naar de `subTitle` eigenschap, als deze ontbreekt of `null` , wordt deze terugvallen op een lege teken reeks.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Type-expressies

Type-expressies bieden hulpprogram ma's voor het testen en omzetten van verschillende gegevens typen, zoals teken reeksen, getallen en Booleaanse waarden.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `array(Expression)` | Object [] | Hierbij wordt bevestigd dat de invoer een matrix is. |
| `bool(Expression)` | booleaans | Er wordt bevestigd dat de invoer waarde een Boole is. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collatie | Retourneert een collatie voor gebruik in vergelijkings bewerkingen met land instellingen. De opties hoofdletter gevoelig en diakritische gevoelig worden standaard ingesteld op ONWAAR. Het argument locale specificeert de IETF-taal code van de land instelling die moet worden gebruikt. Als er geen wordt gegeven, wordt de standaard land instelling gebruikt. Als de aangevraagde land instelling niet beschikbaar is, gebruikt de collatie een door het systeem gedefinieerde terugval land instelling. Opgelost-land instellingen gebruiken om de resultaten van het terugval gedrag van de land instelling te testen.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | \|teken reeks object van Boole-waarde \| \| \| [] | Retourneert een letterlijke matrix of object waarde. Gebruik deze expressie om te voor komen dat een matrix of object als een expressie wordt geëvalueerd. Dit is nodig wanneer een matrix of object moet worden geretourneerd door een expressie. |
| `number(Expression)` | getal | Hierbij wordt bevestigd dat de invoer waarde een getal is. |
| `object(Expression)` | Object | Hierbij wordt bevestigd dat de invoer waarde een object is. |
| `string(Expression)` | tekenreeks | Hierbij wordt bevestigd dat de invoer waarde een teken reeks is. |
| `toArray(Expression)` | Object [] | Hiermee wordt de expressie geconverteerd naar een JSON-object matrix. |
| `toBool(Expression)` | booleaans | Hiermee wordt de invoer waarde geconverteerd naar een Boolean. |
| `toNumber(Expression)` | getal | Converteert de invoer waarde indien mogelijk naar een getal. |
| `toString(Expression)` | tekenreeks | Converteert de invoer waarde naar een teken reeks. |
| `typeoOf(Expression)` | tekenreeks | Retourneert een teken reeks die het type van de opgegeven waarde beschrijft. |

## <a name="color-expressions"></a>Kleur expressies

Kleur expressies maken het gemakkelijker om kleur waarden te maken en te bewerken.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `color(int)` | color | Converteert een waarde van een geheel getal in een kleur expressie. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Hiermee maakt u een kleur waarde van de onderdelen *rood*, *groen* en *blauw* die tussen `0` en moeten variëren `255` , en een alpha-onderdeel van `1` . Als een onderdeel zich buiten het bereik bevindt, is er een fout opgetreden in de expressie. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Hiermee maakt u een kleur waarde van *rode*, *groene* en *blauwe* onderdelen die tussen en moeten liggen `0` `255` en een alpha-component binnen een bereik van `0` en `1` . Als een onderdeel zich buiten het bereik bevindt, is er een fout opgetreden in de expressie. |
| `toColor(Expression)` | color  | Converteert de invoer waarde naar een kleur. |
| `toRgba(Expression)` | color | Retourneert een matrix met vier elementen met de rode, groene, blauwe en alpha-onderdelen van de invoer kleur, in die volg orde. |

**Voorbeeld**

In het volgende voor beeld wordt een RGB-kleur waarde gemaakt met een *rode* waarde van `255` en waarden voor *groen* en *blauw* die worden berekend door te vermenigvuldigen `2.5` met de waarde van de `temperature` eigenschap. Wanneer de Tempe ratuur wordt gewijzigd, verandert de kleur in verschillende tinten *rood*.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Als alle kleur parameters getallen zijn, hoeft u deze niet in te pakken met de `literal` expressie. Bijvoorbeeld:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Teken reeks kleur waarden kunnen worden omgezet in een kleur met behulp van de `android.graphics.Color.parseColor` methode. Met de volgende conversie wordt een hex-kleur teken reeks geconverteerd naar een kleur expressie die kan worden gebruikt met een laag.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Teken reeks operator expressies

Teken reeks operator expressies voeren conversie bewerkingen uit op teken reeksen, zoals samen voegen en het converteren van de aanvraag.

| Expression | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | tekenreeks | Meerdere teken reeksen samen voegen. Elke waarde moet een teken reeks zijn. Gebruik de `toString` type-expressie om andere waardetypen zo nodig te converteren naar een teken reeks. |
| `downcase(string)` \| `downcase(Expression)` | tekenreeks | Hiermee wordt de opgegeven teken reeks geconverteerd naar kleine letters. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| booleaans | Bepaalt of de invoer teken reeks een tekenset gebruikt die wordt ondersteund door de huidige lettertype verzameling. Bijvoorbeeld: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | tekenreeks | Retourneert het IETF-taal label van de land instelling die wordt gebruikt door de beschik bare collatie. Dit kan worden gebruikt om de standaard land instelling van het systeem te bepalen of om te bepalen of een aangevraagde land instelling is geladen. |
| `upcase(string)` \| `upcase(Expression)` | tekenreeks | Converteert de opgegeven teken reeks naar hoofd letters. |

**Voorbeeld**

In het volgende voor beeld wordt de `temperature` eigenschap van de punt functie geconverteerd naar een teken reeks en wordt vervolgens ' °F ' aan het einde van het object aaneengeschakeld.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

De bovenstaande expressie geeft een pincode op de kaart weer met de tekst ' 64 °F ', zoals in de onderstaande afbeelding wordt weer gegeven.

![Voor beeld van een expressie voor een teken reeks operator](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Interpoleer-en Step-expressies

Interpolatie-en stap expressies kunnen worden gebruikt voor het berekenen van waarden in een geïnterpoleerde curve of stap functie. Deze expressies nemen in een expressie op die bijvoorbeeld een numerieke waarde retourneert als invoer `get("temperature")` . De invoer waarde wordt geëvalueerd ten opzichte van paren van invoer-en uitvoer waarden om de waarde te bepalen die het beste past bij de geïnterpoleerde curve of stap functie. De uitvoer waarden worden ' stops ' genoemd. De invoer waarden voor elke Stop moeten een getal zijn en moeten in oplopende volg orde staan. De uitvoer waarden moeten een getal, een matrix van getallen of een kleur zijn.

### <a name="interpolate-expression"></a>Interpole-expressie

Een `interpolate` expressie kan worden gebruikt om een continue, soepele set waarden te berekenen door te interpolatie tussen de stop waarden. Een `interpolate` expressie die kleur waarden retourneert, produceert een kleur overgang waarbij resultaat waarden worden geselecteerd uit. De `interpolate` expressie heeft de volgende indelingen:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Er zijn drie typen interpolatie methoden die kunnen worden gebruikt in een `interpolate` expressie:

| Naam | Beschrijving |
|------|-------------|
| `linear()` | Interpoleert lineair tussen het paar van de stops.  |
| `exponential(number)` \| `exponential(Expression)` | Interpoleert exponentieel tussen de stops. Een ' base ' is opgegeven en bepaalt de snelheid waarmee de uitvoer wordt verhoogd. Hoe hoger de waarde, hoe groter de uitvoer meer naar het hoge einde van het bereik. Een ' basis ' waarde dicht bij 1 resulteert in een uitvoer die meer lineair toeneemt.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpoleeert het gebruik van een [kubieke Bézier-curve](https://developer.mozilla.org/docs/Web/CSS/timing-function) die is gedefinieerd door de opgegeven besturings punten. |

De `stop` expressie heeft de indeling `stop(stop, value)` .

Hier volgt een voor beeld van hoe de verschillende typen interpolatie eruitzien.

| Lineair  | Exponentieel | Kubieke Bézier |
|---------|-------------|--------------|
| ![Grafiek met lineaire interpolatie](media/how-to-expressions/linear-interpolation.png) | ![Exponentiële interpolatie grafiek](media/how-to-expressions/exponential-interpolation.png) | ![Grafiek met kubieke Bezier-interpolatie](media/how-to-expressions/bezier-curve-interpolation.png) |

**Voorbeeld**

In het volgende voor beeld wordt een `linear interpolate` expressie gebruikt om de `bubbleColor` eigenschap van een tekenlaag in te stellen op basis van de `temperature` eigenschap van de functie Point. Als de `temperature` waarde lager is dan 60, wordt ' Blue ' geretourneerd. Als de waarde tussen 60 en minder is dan 70, wordt geel geretourneerd. Als de waarde tussen 70 en minder dan 80, wordt ' oranje ' ( `#FFA500` ) geretourneerd. Als de waarde 80 of hoger is, wordt ' Red ' geretourneerd.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

In de volgende afbeelding ziet u hoe de kleuren voor de bovenstaande expressie worden gekozen.

![Voor beeld van interpolatie-expressie](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Expressie voor stap

Een `step` expressie kan worden gebruikt om discrete, getrapte resultaat waarden te berekenen door te evalueren van een [piecewise-constante functie](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) die door stops wordt gedefinieerd.

De `interpolate` expressie heeft de volgende indelingen:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Met stap expressies wordt de uitvoer waarde van de stop net vóór de invoer waarde geretourneerd of de eerste invoer waarde als de invoer kleiner is dan de eerste stop.

**Voorbeeld**

In het volgende voor beeld wordt een `step` expressie gebruikt om de `bubbleColor` eigenschap van een tekenlaag in te stellen op basis van de `temperature` eigenschap van de functie Point. Als de `temperature` waarde lager is dan 60, wordt ' Blue ' geretourneerd. Als de waarde tussen 60 en minder dan 70, wordt ' Yellow ' geretourneerd. Als de waarde tussen 70 en minder dan 80, wordt ' oranje ' geretourneerd. Als de waarde 80 of hoger is, wordt ' Red ' geretourneerd.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

In de volgende afbeelding ziet u hoe de kleuren voor de bovenstaande expressie worden gekozen.

![Voor beeld van een stap-expressie](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Toepassingsspecifieke expressies

Speciale expressies die alleen van toepassing zijn op specifieke lagen.

### <a name="heat-map-density-expression"></a>Dichtheids expressie voor heatmap

Met een dichtheids expressie voor heatmap wordt de waarde van de heatmap voor de kaart voor elke pixel in een heatmap van een hitte opgehaald en gedefinieerd als `heatmapDensity` . Deze waarde is een getal tussen `0` en `1` . Deze wordt gebruikt in combi natie met een `interpolation` or- `step` expressie om de kleur overgang te definiëren die wordt gebruikt voor het inkleuren van de heatmap. Deze expressie kan alleen worden gebruikt in de `heatmapColor` optie van de laag van de heatmap.

> [!TIP]
> De kleur bij index 0, in een interpolatie-expressie of de standaard kleur van een stap kleur, definieert de kleur van het gebied waar er geen gegevens zijn. De kleur bij index 0 kan worden gebruikt om een achtergrond kleur te definiëren. Veel gewenst om deze waarde in te stellen op transparant of semi-transparant zwart.

**Voorbeeld**

In dit voor beeld wordt een lijn-interpolatie-expressie gebruikt voor het maken van een vloeiend kleur verloop voor het renderen van de heatmap.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Naast het gebruik van een glad verloop om een heatmap in te vullen, kunnen kleuren worden opgegeven in een set bereiken met behulp van een `step` expressie. Als u een `step` expressie gebruikt voor het inkleuren van de hitte kaart, wordt de densiteit in bereiken weer gegeven die lijken op een contour-of radar stijl kaart.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Zie de documentatie [een hitte kaart toevoegen](map-add-heat-map-layer-android.md) voor meer informatie.

### <a name="line-progress-expression"></a>Expressie voor voortgangs lijn

Met een expressie voor de voortgangs lijn wordt de voortgang van een verloop lijn in een line-laag opgehaald en gedefinieerd als `lineProgress()` . Deze waarde is een getal tussen 0 en 1. Deze wordt gebruikt in combi natie met een `interpolation` or- `step` expressie. Deze expressie kan alleen worden gebruikt met de `strokeGradient` optie van de laag.

> [!NOTE]
> Voor de `strokeGradient` optie van de laag moet de `lineMetrics` optie van de gegevens bron worden ingesteld op `true` .

**Voorbeeld**

In dit voor beeld wordt de `lineProgress()` expressie gebruikt om een kleur overgang toe te passen op de lijn van een regel.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Zie Live voor beeld](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressie voor tekst veld notatie

De `format` expressie kan worden gebruikt met de `textField` optie van de Symbol-laag om gemengde tekst opmaak te bieden. Deze expressie heeft een of meer `formatEntry` expressies waarmee een teken reeks en set worden opgegeven die `formatOptions` moeten worden toegevoegd aan het tekst veld.

| Expressie | Beschrijving |
|------------|-------------|
| `format(Expression...)` | Retourneert opgemaakte tekst met aantekeningen voor gebruik in gemengde tekst veld vermeldingen. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Retourneert een ingedeelde teken reeks vermelding voor gebruik in de `format` expressie. |

De volgende beschik bare indelings opties zijn:

| Expressie | Beschrijving |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Hiermee geeft u de schaal factor voor de teken grootte op. Als deze waarde wordt opgegeven, wordt de `textSize` eigenschap voor de afzonderlijke teken reeks overschreven. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Geeft een kleur aan die op een tekst wordt toegepast tijdens het renderen. |

**Voorbeeld**

In het volgende voor beeld wordt het tekst veld opgemaakt door een vet letter type toe te voegen en de teken grootte van de `title` eigenschap van de functie omhoog te schalen. In dit voor beeld wordt ook de `subTitle` eigenschap van de functie op een nieuwe regel toegevoegd, met een geschaalde teken grootte.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Met deze laag wordt de punt functie weer gegeven zoals in de onderstaande afbeelding:

![Afbeelding van punt functie met opgemaakte tekst veld](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Expressie voor in-/uitzoomen

Een `zoom` expressie wordt gebruikt om het huidige zoom niveau van de kaart op te halen op weergave tijd en wordt gedefinieerd als `zoom()` . Deze expressie retourneert een getal tussen het minimale en maximale zoom niveau bereik van de kaart. De besturings elementen voor de Azure Maps interactieve kaart voor web-en Android-ondersteuning 25 zoom niveaus, genummerd op 0 t/m 24. Als u de `zoom` expressie gebruikt, kunnen stijlen dynamisch worden gewijzigd wanneer het zoom niveau van de kaart wordt gewijzigd. De `zoom` expressie kan alleen worden gebruikt met `interpolate` and- `step` expressies.

**Voorbeeld**

De RADIUS van gegevens punten die worden weer gegeven in de laag van de heatmap hebben standaard een straal van vaste pixels voor alle zoom niveaus. Naarmate de kaart wordt ingezoomd, worden de gegevens samen en de heatmap van de kaart op verschillende manieren weer gegeven. Een `zoom` expressie kan worden gebruikt om de RADIUS voor elk zoom niveau zodanig te schalen dat elk gegevens punt overeenkomt met hetzelfde fysieke gebied van de kaart. De IT-laag maakt de heatmap statisch en consistent. Elk zoom niveau van de kaart heeft twee keer zoveel pixels verticaal en horizon taal als het vorige zoom niveau. Door de RADIUS te schalen, zodat deze wordt verdubbeld met elk zoom niveau, wordt een heatmap gemaakt die consistent is op alle zoom niveaus. U kunt dit doen met behulp van de `zoom` expressie met een `base 2 exponential interpolation` expressie, waarbij de pixel RADIUS is ingesteld voor het minimale zoom niveau en een geschaalde RADIUS voor het maximale zoom niveau dat wordt berekend, zoals `2 * Math.pow(2, minZoom - maxZoom)` hieronder wordt weer gegeven.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Variabele bindings expressies

Met variabelen bindings expressies worden de resultaten van een berekening in een variabele opgeslagen. Dat wil zeggen dat er meerdere keren naar de resultaten van de berekening kan worden verwezen in een expressie. Het is een handige optimalisatie voor expressies waarbij veel berekeningen worden betrokken.

| Expression | Retourtype | Beschrijving |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Slaat een of meer waarden op als variabelen voor gebruik door de `var` expressie in de onderliggende expressie waarmee het resultaat wordt geretourneerd. |
| `var(Expression expression)` \| `var(string variableName)` | Object | Verwijst naar een variabele die is gemaakt met behulp van de `let` expressie. |

**Voorbeeld**

In dit voor beeld wordt een expressie gebruikt waarmee de opbrengst wordt berekend ten opzichte van de Tempe ratuur en vervolgens een expressie wordt gebruikt `case` om verschillende Boole-bewerkingen op deze waarde te evalueren. De `let` expressie wordt gebruikt voor het opslaan van de opbrengst ten opzichte van de temperatuur verhouding, zodat deze slechts eenmaal hoeft te worden berekend. De `var` expressie verwijst naar deze variabele zo vaak als nodig is zonder dat deze opnieuw moet worden berekend.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lagen die expressies ondersteunen:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Een heatmap toevoegen](map-add-heat-map-layer-android.md)
