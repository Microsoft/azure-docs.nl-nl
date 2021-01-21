---
title: Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java
description: Telemetrie-processors configureren in Azure Monitor Application Insights voor Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632577"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java

> [!NOTE]
> Deze functie bevindt zich nog in de preview-versie.

De Java 3,0-agent voor Application Insights heeft nu de mogelijkheid om telemetriegegevens te verwerken voordat de gegevens worden geëxporteerd.

Hier volgen enkele gebruiks voorbeelden van telemetrie-processors:
 * Masker gevoelige gegevens
 * Aangepaste dimensies voorwaardelijk toevoegen
 * De naam bijwerken die wordt gebruikt voor aggregatie en weer geven in de Azure Portal
 * Kenmerken van de drop span om opname kosten te bepalen

## <a name="terminology"></a>Terminologie

Voordat we naar telemetrie-processors gaan, is het belang rijk om te begrijpen waar de term vallen naar verwijst.

Een reeks is een algemene term voor een van de volgende drie dingen:

* Een binnenkomende aanvraag
* Een uitgaande afhankelijkheid (bijvoorbeeld een externe aanroep naar een andere service)
* Een in-process afhankelijkheid (bijvoorbeeld werk dat door subonderdelen van de service wordt uitgevoerd)

Voor de telemetrie-processors zijn de belang rijke onderdelen van een reeks:

* Name
* Kenmerken

De naam van de reeks is de primaire weer gave die wordt gebruikt voor aanvragen en afhankelijkheden in de Azure Portal.

De span-kenmerken vertegenwoordigen zowel de standaard eigenschappen als de aangepaste eigenschap van een bepaalde aanvraag of afhankelijkheid.

## <a name="telemetry-processor-types"></a>Typen telemetrie-processor

Er zijn momenteel twee soorten telemetrie-processors.

#### <a name="attribute-processor"></a>Kenmerk processor

Een kenmerk processor kan kenmerken invoegen, bijwerken, verwijderen of hashen.
Het kan ook worden geëxtraheerd (via een reguliere expressie) een of meer nieuwe kenmerken van een bestaand kenmerk.

#### <a name="span-processor"></a>Span processor

Een bereik processor biedt de mogelijkheid om de naam van de telemetrie bij te werken.
Het kan ook worden geëxtraheerd (via een reguliere expressie) een of meer nieuwe kenmerken van de span-naam.

> [!NOTE]
> De huidige telemetrie-processors verwerken alleen kenmerken van het type teken reeks en verwerken geen kenmerken van het type Boolean of getal.

## <a name="getting-started"></a>Aan de slag

Maak een configuratie bestand met de naam `applicationinsights.json` en plaats het in dezelfde map als `applicationinsights-agent-*.jar` de volgende sjabloon.

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

## <a name="includeexclude-criteria"></a>Criteria voor opnemen/uitsluiten

Zowel kenmerk-processors als-processors ondersteunen optionele `include` en `exclude` criteria.
Een processor wordt alleen toegepast op de beschik bare reeksen die voldoen aan de `include` criteria (indien van toepassing) _en_ komen niet overeen met de bijbehorende `exclude` criteria (indien opgegeven).

Als u deze optie wilt configureren, onder `include` en/of `exclude` ten minste één of `matchType` `spanNames` `attributes` is vereist.
De configuratie voor opnemen/uitsluiten wordt ondersteund om meer dan één opgegeven voor waarde te hebben.
Alle opgegeven voor waarden moeten worden geëvalueerd als waar om een overeenkomst te vinden. 

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
      }
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
      }
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
      }
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
      }
    ]
  }
]
```
Voor de `hash` actie zijn de volgende acties vereist
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Deze functie is alleen in 3.0.2 en hoger

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
      }
    ]
  }
]
```
Voor de `extract` actie zijn de volgende acties vereist
* `key`
* `pattern`
* `action` : `extract`

Raadpleeg de documentatie van de [telemetrie-processor](./java-standalone-telemetry-processors-examples.md) voor voor meer informatie.

## <a name="span-processor"></a>Span processor

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
