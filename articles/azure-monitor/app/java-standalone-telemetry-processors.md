---
title: Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java
description: Telemetrie-processors configureren in Azure Monitor Application Insights voor Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133171"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java

> [!NOTE]
> Deze functie bevindt zich nog in de preview-versie.

De Java 3,0-agent voor Application Insights heeft nu de mogelijkheid om telemetriegegevens te verwerken voordat de gegevens worden geëxporteerd.

Hier volgen enkele gebruiks voorbeelden van telemetrie-processors:
 * Masker gevoelige gegevens
 * Aangepaste dimensies voorwaardelijk toevoegen
 * De naam van de telemetrie bijwerken die wordt gebruikt voor aggregatie en weer gave
 * Attributen voor neerzetten of filteren om opname kosten te bepalen

## <a name="terminology"></a>Terminologie

Voordat we naar telemetrie-processors gaan, is het belang rijk om te begrijpen wat traceringen en beslagen zijn.

### <a name="traces"></a>Traceringen

Traceringen volgen de voortgang van een enkele aanvraag, een zogenaamde `trace` ,, omdat deze wordt verwerkt door services die deel uitmaken van een toepassing. De aanvraag kan worden geïnitieerd door een gebruiker of een toepassing. Elke werk eenheid in a `trace` wordt een `span` `trace` boom structuur genoemd. Een `trace` bestaat uit de enkelvoudige basis periode en een aantal onderliggende reeksen.

### <a name="span"></a>Verdelen

Omvat objecten die de werkzaamheden vertegenwoordigen die worden uitgevoerd door afzonderlijke services of onderdelen die zijn betrokken bij een aanvraag, wanneer deze via een systeem worden verzonden. A `span` bevat een `span context` , een set van globale unieke id's die de unieke aanvraag vertegenwoordigen waarvan elke reeks deel uitmaakt. 

Omvat het inkapselen:

* De naam van de span
* Een onveranderbaar `SpanContext` die een unieke identificatie vormt van de reeks
* Een bovenliggende bereik in de vorm van een `Span` , `SpanContext` , of Null
* Een `SpanKind`
* Een begin-time stamp
* Een eind punt time stamp
* [`Attributes`](#attributes)
* Een lijst met verstempelde gebeurtenissen
* A `Status` .

De levens cyclus van een span lijkt doorgaans op het volgende:

* Een aanvraag wordt ontvangen door een service. De reeks context wordt opgehaald uit de aanvraag headers, als deze bestaat.
* Er wordt een nieuwe reeks gemaakt als onderliggend element van de geëxtraheerde-reeks context; Als er geen bestaat, wordt er een nieuwe hoofd spanne gemaakt.
* De service handelt de aanvraag af. Aanvullende kenmerken en gebeurtenissen worden toegevoegd aan de reeks die nuttig is voor het leren van de context van de aanvraag, zoals de hostnaam van de computer die de aanvraag verwerkt, of klant-id's.
* Nieuwe reeksen kunnen worden gemaakt voor werk dat door subonderdelen van de service wordt uitgevoerd.
* Wanneer de service een externe aanroep naar een andere service uitvoert, wordt de huidige periode context geserialiseerd en doorgestuurd naar de volgende service door de reeks context in te voegen in de kop-of bericht envelop.
* Het werk dat door de service wordt uitgevoerd, is voltooid of niet. De duur van de reeks is op de juiste wijze ingesteld en de reeks is gemarkeerd als voltooid.

### <a name="attributes"></a>Kenmerken

`Attributes` een lijst met nul of meer sleutel-waardeparen die in een zijn ingekapseld `span` . Een kenmerk moet de volgende eigenschappen hebben:

De kenmerk sleutel, die een niet-null-of niet-lege teken reeks moet zijn.
De waarde van het kenmerk, ofwel:
* Een primitief type: teken reeks, Booleaanse waarde, drijvende komma met dubbele precisie (IEEE 754-1985) of een ondertekend 64 bits geheel getal.
* Een matrix met waarden voor primitieve typen. De matrix moet homo geen zijn, d.w.z. deze mag geen waarden van verschillende typen bevatten. Voor protocollen die geen systeem eigen ondersteuning bieden voor matrix waarden, moeten waarden worden weer gegeven als JSON-teken reeksen.

## <a name="supported-processors"></a>Ondersteunde processors:
 * Kenmerk processor
 * Span processor

## <a name="to-get-started"></a>Om aan de slag te gaan

Maak een configuratie bestand met de naam `applicationinsights.json` en plaats het in dezelfde map als `applicationinsights-agent-***.jar` de volgende sjabloon.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Insluitingen opnemen/uitsluiten

De kenmerk processor en de bereik processor bieden de optie om een reeks eigenschappen van een reeks te leveren om te bepalen of de reeks moet worden opgenomen of uitgesloten van de telemetrie-processor. Als u deze optie wilt configureren, onder `include` en/of `exclude` ten minste één of `matchType` `spanNames` `attributes` is vereist. De configuratie voor opnemen/uitsluiten wordt ondersteund om meer dan één opgegeven voor waarde te hebben. Alle opgegeven voor waarden moeten worden geëvalueerd als waar om een overeenkomst te vinden. 

**Vereist veld**: 
* `matchType` Hiermee wordt bepaald hoe items in `spanNames` en `attributes` matrices worden geïnterpreteerd. Mogelijke waarden zijn `regexp` en `strict`. 

**Optionele velden**: 
* `spanNames` moet overeenkomen met ten minste één van de items. 
* `attributes` Hiermee geeft u de lijst met kenmerken op die moeten worden vergeleken. Al deze kenmerken moeten exact overeenkomen om een overeenkomst te vinden.

> [!NOTE]
> Als beide `include` en `exclude` worden opgegeven, `include` worden de eigenschappen gecontroleerd voor de `exclude` Eigenschappen.

#### <a name="sample-usage"></a>Voorbeeldgebruik

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
Raadpleeg de documentatie van de [telemetrie-processor](./java-standalone-telemetry-processors-examples.md) voor voor meer informatie.

## <a name="attribute-processor"></a>Kenmerk processor 

De kenmerken processor wijzigt kenmerken van een reeks. Het biedt optioneel ondersteuning voor de mogelijkheid om reeksen op te nemen of uit te sluiten. Er wordt een lijst weer gegeven met acties die worden uitgevoerd in de volg orde die is opgegeven in het configuratie bestand. De ondersteunde acties zijn:

### `insert`

Hiermee wordt een nieuw kenmerk ingevoegd in de spans waar de sleutel nog niet bestaat.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
Voor de `insert` actie zijn de volgende acties vereist
  * `key`
  * een van `value` of `fromAttribute`
  * `action`:`insert`

### `update`

Hiermee wordt een kenmerk bijgewerkt in de beslagen waar de sleutel bestaat

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
Voor de `update` actie zijn de volgende acties vereist
  * `key`
  * een van `value` of `fromAttribute`
  * `action`:`update`


### `delete` 

Hiermee wordt een kenmerk uit een bereik verwijderd

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
Voor de `delete` actie zijn de volgende acties vereist
  * `key`
  * `action`: `delete`

### `hash`

Hashes (SHA1) een bestaande kenmerk waarde

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
Voor de `hash` actie zijn de volgende acties vereist
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Deze functie is alleen in 3.0.1 en hoger

Retourneert waarden met behulp van een reguliere expressie regel van de invoer sleutel naar de doel sleutels die in de regel zijn opgegeven. Als er al een doel sleutel bestaat, wordt deze overschreven. Het gedraagt zich op dezelfde manier als de instelling voor het bereik van de [processor](#extract-attributes-from-span-name) `toAttributes` met het bestaande kenmerk als de bron.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
Voor de `extract` actie zijn de volgende acties vereist
* `key`
* `pattern`
* `action` : `extract`

Raadpleeg de documentatie van de [telemetrie-processor](./java-standalone-telemetry-processors-examples.md) voor voor meer informatie.

## <a name="span-processors"></a>Span-processors

De reeks processor wijzigt de naam van de reeks of kenmerken van een reeks op basis van de naam van de span. Het biedt optioneel ondersteuning voor de mogelijkheid om reeksen op te nemen of uit te sluiten.

### <a name="name-a-span"></a>Een reeks een naam

De volgende instelling is vereist als onderdeel van de sectie Naam:

* `fromAttributes`: De kenmerk waarde voor de sleutels wordt gebruikt om een nieuwe naam te maken in de volg orde die is opgegeven in de configuratie. Alle kenmerk sleutels moeten worden opgegeven in de reik wijdte van de processor om de naam ervan te wijzigen.

De volgende instelling kan eventueel worden geconfigureerd:

* `separator`: Een teken reeks die wordt opgegeven, wordt gebruikt om waarden te splitsen
> [!NOTE]
> Als het wijzigen van de naam afhankelijk is van de kenmerken die worden gewijzigd door de attributen processor, moet u ervoor zorgen dat de bereik processor is opgegeven na de kenmerken processor in de pijplijn specificatie.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Kenmerken uit de span-naam extra heren

Hiermee wordt een lijst met reguliere expressies gemaakt die overeenkomen met de naam van de reeks en de kenmerken uit de subexpressies worden opgehaald. Moet worden opgegeven in de `toAttributes` sectie.

De volgende instellingen zijn vereist:

`rules` : Een lijst met regels om kenmerk waarden te extra heren uit een span-naam. De waarden in de bereik naam worden vervangen door geëxtraheerde kenmerk namen. Elke regel in de lijst is regex-patroon teken reeks. De naam van de span wordt gecontroleerd op basis van de regex. Als de regex overeenkomt, worden alle benoemde subexpressies van de regex geëxtraheerd als kenmerken en worden deze toegevoegd aan de reeks. Elke naam van een subexpressie wordt een kenmerk naam en een subexpressie die overeenkomt met een deel wordt de kenmerk waarde. Het overeenkomende deel in de bereik naam wordt vervangen door de geëxtraheerde kenmerk naam. Als de kenmerken al bestaan in de reeks, worden deze overschreven. Het proces wordt herhaald voor alle regels in de volg orde waarin ze zijn opgegeven. Elke volgende regel werkt op de naam van de span die de uitvoer na het verwerken van de vorige regel.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Lijst met kenmerken

Hieronder vindt u een lijst met enkele algemene span-kenmerken die kunnen worden gebruikt in de telemetrie-processors.

### <a name="http-spans"></a>HTTP-reeksen

| Kenmerk  | Type | Description | 
|---|---|---|
| `http.method` | tekenreeks | HTTP-aanvraag methode.|
| `http.url` | tekenreeks | Volledige URL van de HTTP-aanvraag in het formulier `scheme://host[:port]/path?query[#fragment]` . Normaal gesp roken wordt het fragment niet via HTTP verzonden, maar als dit wel bekend is, moet het echter wel worden opgenomen.|
| `http.status_code` | getal | [Status code van http-antwoord](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | tekenreeks | Type gebruikt HTTP-protocol |
| `http.user_agent` | tekenreeks | Waarde van de [http-](https://tools.ietf.org/html/rfc7231#section-5.5.3) header van de agent die door de client is verzonden. |

### <a name="jdbc-spans"></a>JDBC-reeksen

| Kenmerk  | Type | Description  |
|---|---|---|
| `db.system` | tekenreeks | Een id voor het Database Management System DBMS-product dat wordt gebruikt. |
| `db.connection_string` | tekenreeks | De connection string die wordt gebruikt om verbinding te maken met de data base. Het wordt aanbevolen om Inge sloten referenties te verwijderen.|
| `db.user` | tekenreeks | Gebruikers naam voor toegang tot de data base. |
| `db.name` | tekenreeks | Dit kenmerk wordt gebruikt om de naam te rapporteren van de data base die wordt geopend. Voor opdrachten waarmee de data base wordt overgeschakeld, moet dit worden ingesteld op de doel database (zelfs als de opdracht mislukt).|
| `db.statement` | tekenreeks | De data base-instructie die wordt uitgevoerd.|