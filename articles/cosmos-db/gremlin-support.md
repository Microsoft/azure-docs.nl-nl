---
title: Gremlin-ondersteuning voor Azure Cosmos DB en compatibiliteit met TinkerPop-functies
description: Meer informatie over de Gremlin-taal van Apache TinkerPop. Meer informatie over de functies en stappen die beschikbaar zijn in Azure Cosmos DB en de compatibiliteitsverschillen van de TinkerPop Graph-engine.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: a149f0b331a77462aa53b948fedf25dd1331969e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "94683621"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Gremlin-graafondersteuning voor Azure Cosmos DB en compatibiliteit met TinkerPop-functies
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB ondersteunt de taal voor grafiekdoorkruising van [Apache Tinkerpop](https://tinkerpop.apache.org), ook wel [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps) genoemd. U kunt de Gremlin-taal gebruiken om grafiekentiteiten (hoekpunten en randen) te maken, eigenschappen binnen deze entiteiten te wijzigen, query’s en traversals uit te voeren, en entiteiten te verwijderen.

De Azure Cosmos DB Graph-engine volgt de specificatie voor [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)-doorkruisingstappen nauwkeurig, maar er zijn verschillen in de implementatie die specifiek zijn voor Azure Cosmos DB. In dit artikel bieden we een beknopt overzicht van Gremlin en worden de functies in Gremlin beschreven die worden ondersteund door de Gremlin-API.

## <a name="compatible-client-libraries"></a>Compatibele clientbibliotheken

In de volgende tabel ziet u populaire Gremlin-stuurprogramma’s die u kunt gebruiken met Azure Cosmos DB:

| Download | Bron | Aan de slag | Ondersteunde connectorversie |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Grafiek maken met behulp van .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Grafiek maken met behulp van Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Grafiek maken met behulp van Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Grafiek maken met behulp van Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP in GitHub](https://github.com/PommeVerte/gremlin-php) | [Grafiek maken met behulp van PHP](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | Deze bibliotheek is gebouwd door externe bijdragers. Het Azure Cosmos DB-team biedt geen ondersteuning of onderhoud voor de bibliotheek. |
| [Gremlin-console](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-documenten](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Grafiek maken met behulp van de Gremlin-console](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Ondersteunde grafiekobjecten

TinkerPop is een standaard die een breed bereik aan grafiektechnologieën beslaat. Daarom beschikt TinkerPop over een standaardterminologie om te beschrijven welke functies worden aangeboden door een grafiekprovider. Azure Cosmos DB biedt een permanente, beschrijfbare grafiekdatabase met een hoge gelijktijdigheid, die kan worden gepartitioneerd op meerdere servers of clusters. 

In de volgende tabel worden de TinkerPop-functies vermeld die zijn geïmplementeerd met Azure Cosmos DB: 

| Categorie | Azure Cosmos DB-implementatie |  Opmerkingen | 
| --- | --- | --- |
| Graph-functies | Biedt persistentie en ConcurrentAccess. Ontworpen om transacties te ondersteunen | Computermethoden kunnen worden geïmplementeerd via de Spark-connector. |
| Variabele functies | Biedt ondersteuning voor Boolean, Integer, Byte, Double, Float, Integer, Long, String | Biedt ondersteuning voor primitieve typen, is compatibel met complexe typen via een gegevensmodel |
| Hoekpuntfuncties | Biedt ondersteuning voor RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Biedt ondersteuning voor het maken, wijzigen en verwijderen van hoekpunten |
| Functies voor hoekpunteigenschappen | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Biedt ondersteuning voor het maken, wijzigen en verwijderen van hoekpunteigenschappen |
| Randfuncties | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Biedt ondersteuning voor het maken, wijzigen en verwijderen van randen |
| Functies voor randeigenschappen | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Biedt ondersteuning voor het maken, wijzigen en verwijderen van randeigenschappen |

## <a name="gremlin-wire-format"></a>Gremlin-draadindeling

Azure Cosmos DB gebruikt de JSON-indeling bij het retourneren van resultaten uit Gremlin-bewerkingen. Azure Cosmos DB ondersteunt momenteel de JSON-indeling. In het volgende fragment wordt bijvoorbeeld een JSON-representatie weergegeven van een hoekpunt *dat is geretourneerd naar de client* vanuit Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

De eigenschappen die worden gebruikt door de JSON-indeling voor hoekpunten, worden hieronder beschreven:

| Eigenschap | Beschrijving | 
| --- | --- | --- |
| `id` | De id voor het hoekpunt. Moet uniek zijn (in combinatie met de waarde `_partition`, indien van toepassing). Als er geen waarde wordt opgegeven, wordt deze automatisch opgegeven met een GUID | 
| `label` | Het label van het hoekpunt. Deze eigenschap wordt gebruikt om het entiteitstype te beschrijven. |
| `type` | Wordt gebruikt om hoekpunten te onderscheiden van niet-Graph-documenten |
| `properties` | Groep door de gebruiker gedefinieerde eigenschappen die zijn gekoppeld aan het hoekpunt. Elke eigenschap kan meerdere waarden hebben. |
| `_partition` | De partitiesleutel van het hoekpunt. Gebruikt voor [graph-partitionering](graph-partitioning.md). |
| `outE` | Deze eigenschap bevat een lijst met uitlopende randen vanaf een hoekpunt. Het opslaan van informatie over aangrenzing met het hoekpunt zorgt voor een snelle uitvoering van traversals. Randen zijn gegroepeerd op basis van de bijbehorende labels. |

En de rand bevat de volgende informatie voor hulp bij het navigeren naar andere delen van de grafiek.

| Eigenschap | Beschrijving |
| --- | --- |
| `id` | De id voor de rand. Moet uniek zijn (in combinatie met de waarde `_partition`, indien van toepassing) |
| `label` | Het label van de rand. Deze eigenschap is optioneel en wordt gebruikt om het relatietype te beschrijven. |
| `inV` | Deze eigenschap bevat een lijst met inkomende hoekpunten voor een rand. Het opslaan van informatie over aangrenzing aan de rand zorgt voor een snelle uitvoering van traversals. Hoekpunten zijn gegroepeerd op basis van de bijbehorende labels. |
| `properties` | Groep door de gebruiker gedefinieerde eigenschappen die zijn gekoppeld aan de rand. Elke eigenschap kan meerdere waarden hebben. |

Met elke eigenschap kunnen meerdere waarden worden opgeslagen binnen een matrix. 

| Eigenschap | Beschrijving |
| --- | --- |
| `value` | De waarde van de eigenschap

## <a name="gremlin-steps"></a>Gremlin-stappen

We gaan nu de Gremlin-stappen bekijken die worden ondersteund in Azure Cosmos DB. Zie [TinkerPop-naslagwerken](https://tinkerpop.apache.org/docs/3.3.2/reference) voor een compleet overzicht van Gremlin.

| stap | Beschrijving | TinkerPop 3.2-documentatie |
| --- | --- | --- |
| `addE` | Voegt een rand toe tussen twee hoekpunten | [stap: addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Voegt een hoekpunt toe aan de grafiek | [stap: addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Zorgt ervoor dat alle traversals een waarde retourneren | [stap: and](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Een stapmodulator om een variabele toe te wijzen aan de uitvoer van een stap | [stap: as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Een stapmodulator die wordt gebruikt met `group` en `order` | [stap: by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Retourneert de eerste traversal die wordt geretourneerd als resultaat | [stap: coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Retourneert een constante waarde. Wordt gebruikt met `coalesce`| [stap: constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Retourneert het aantal van de traversal | [stap: count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Retourneert de waarden, zonder de dubbele waarden | [stap: dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Verwijdert de waarden (hoekpunt/rand) | [stap: drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Hiermee wordt een beschrijving gemaakt van alle bewerkingen die zijn gegenereerd door de uitgevoerde Gremlin-stap | [executionProfile-stap](graph-execution-profile.md) |
| `fold` | Fungeert als barrière waarmee de combinatie van resultaten wordt berekend| [stap: fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Groepeert de waarden op basis van de opgegeven labels| [stap: group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Wordt gebruikt om eigenschappen, hoekpunten en randen te filteren. Biedt ondersteuning voor de varianten `hasLabel`, `hasId`, `hasNot` en `has`. | [stap: has](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Waarden invoeren in een stroom| [stap: inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Wordt gebruikt om een filter uit te voeren met behulp van een booleaanse expressie | [stap: is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Wordt gebruikt om het aantal items in de traversal te beperken| [stap: limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Verpakt een gedeelte van een traversal lokaal in, vergelijkbaar met een subquery | [stap: local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Wordt gebruikt om de ontkenning van een filter te produceren | [stap: not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Retourneert het resultaat van de opgegeven traversal, indien er een resultaat is, anders wordt het aanroepende element geretourneerd | [stap: optional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Zorgt ervoor dat minstens een van de traversals een waarde retourneert | [stap: or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Retourneert resultaten in de opgegeven sorteervolgorde | [stap: order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Retourneert het volledige pad van de traversal | [stap: path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projecteert de eigenschappen als een kaart | [stap: project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Retourneert de eigenschappen voor de opgegeven labels | [stap: properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtert op het opgegeven waardenbereik| [stap: range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Herhaalt de stap zo vaak als is opgegeven. Wordt gebruikt om een lus te maken | [stap: repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Wordt gebruikt voor voorbeeldresultaten van de traversal | [stap: sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Wordt gebruikt voor projectresultaten van de traversal |  [stap: select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Wordt gebruikt voor niet-blokkerende combinaties van de traversal | [stap: store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap met het begin van een bepaalde tekenreeks | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap met het einde van een bepaalde tekenreeks | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap met de inhoud van een bepaalde tekenreeks | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap die niet begint met een bepaalde tekenreeks | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap die niet eindigt met een bepaalde tekenreeks | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Functie voor het filteren van tekenreeksen. Deze functie wordt gebruikt als een predicaat voor de `has()`-stap die gelijk is aan een eigenschap die geen bepaalde tekenreeks bevat | [TextP-predicaten](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Paden van een hoekpunt combineren in een boomstructuur | [stap: tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Een iterator uitvoeren als stap| [stap: unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Resultaten van meerdere traversals samenvoegen| [stap: union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Bevat de stappen die nodig zijn voor traversals tussen hoekpunten en randen `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` en `otherV` voor | [stap: vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Wordt gebruikt om resultaten van de traversal te filteren. Biedt ondersteuning voor de operators `eq`, `neq`, `lt`, `lte`, `gt`, `gte` en `between`  | [stap: where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

De voor schrijven geoptimaliseerde engine van Azure Cosmos DB biedt standaard ondersteuning voor het automatisch indexeren van alle eigenschappen binnen hoekpunten en randen. Daarom worden query’s met filters, bereikquery’s, sorteringen of combinaties van elke willekeurige eigenschap verwerkt vanaf de index en efficiënt geleverd. Bekijk onze paper over [schema-agnostisch indexeren](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) voor meer informatie over hoe indexeren werkt in Azure Cosmos DB.

## <a name="behavior-differences"></a>Gedragsverschillen

* De Azure Cosmos DB Graph-engine voert de doorkruising ***breedte-eerst** _ uit, terwijl TinkerPop Gremlin diepte-eerst is. Dit gedrag behaalt betere prestaties in horizontaal schaalbare systemen zoals Cosmos DB.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

_ * **[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)** _ is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt dit nog niet. Gebruik `GremlinClient.SubmitAsync()` en geef doorkruising door als een tekenreeks.

Instellen van kardinaliteit met _ * **`property(set, 'xyz', 1)`** _ wordt momenteel niet ondersteund. Gebruik in plaats daarvan `property(list, 'xyz', 1)`. Zie [Vertex properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties) voor meer informatie.

_ De ***stap `match()`** _ is momenteel niet beschikbaar. Deze stap biedt declaratieve querymogelijkheden.

_ ***Objecten als eigenschappen** _ op hoekpunten of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

_ ***Sorteren op matrixeigenschappen** _ `order().by(<array property>)` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

_ ***Niet-primitieve JSON-typen** _ worden niet ondersteund. Gebruik de typen `string`, `number` of `true`/`false`. `null`-waarden worden niet ondersteund. 

Serialisatiefunctie _ ***GraphSONv3** _ wordt momenteel niet ondersteund. Gebruik `GraphSONv2` Serializer-, Reader- en Writer-klassen in de configuratie van de verbinding. De resultaten die door de Azure Cosmos DB Gremlin API worden geretourneerd, hebben niet dezelfde indeling als de GraphSON-indeling. 

_ **Lambda-expressies en functies** worden momenteel niet ondersteund. Dit geldt ook voor de functies `.map{<expression>}`, `.by{<expression>}`en `.filter{<expression>}`. Zie [A Note on Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas) voor meer informatie en om te leren hoe u deze functies kunt herschrijven met behulp van Gremlin-stappen.

* ***Transacties** _ worden niet ondersteund vanwege de gedistribueerde aard van het systeem.  Configureer het juiste consistentie model op het Gremlin-account om uw eigen schrijfbewerkingen te lezen en gebruik optimistische gelijktijdigheid om conflicterende schrijfbewerkingen op te lossen.

## <a name="known-limitations"></a>Bekende beperkingen

_ **Indexgebruik voor Gremlin-query's met `.V()`-stappen binnen doorkruising**: Momenteel gebruikt alleen de eerste aanroep van `.V()` in een doorkruising gebruik van de index om gekoppelde filters of predicaten op te lossen. Bij volgende aanroepen wordt de index niet geraadpleegd, wat de latentie en de kosten van de query zou kunnen verhogen.
    
    Assuming default indexing, a typical read Gremlin query that starts with the `.V()` step would use parameters in its attached filtering steps, such as `.has()` or `.where()` to optimize the cost and performance of the query. For example:

    ```java
    g.V().has('category', 'A')
    ```

    However, when more than one `.V()` step is included in the Gremlin query, the resolution of the data for the query might not be optimal. Take the following query as an example:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    This query will return two groups of vertices based on their property called `category`. In this case, only the first call, `g.V().has('category', 'A')` will make use of the index to resolve the vertices based on the values of their properties.

    A workaround for this query is to use subtraversal steps such as `.map()` and `union()`. This is exemplified below:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    You can review the performance of the queries by using the [Gremlin `executionProfile()` step](graph-execution-profile.md).

## <a name="next-steps"></a>Volgende stappen

* Aan de slag met het bouwen van een grafiektoepassing [met behulp van onze SDK’s](create-graph-dotnet.md) 
* Meer informatie over [ondersteuning voor grafieken](graph-introduction.md) in Azure Cosmos DB
