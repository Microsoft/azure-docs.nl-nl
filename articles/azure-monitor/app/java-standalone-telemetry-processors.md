---
title: Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java
description: Meer informatie over het configureren van telemetrie-processors in Azure Monitor Application Insights voor Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024112"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java

> [!NOTE]
> De functie voor telemetrie-processors is beschikbaar als preview-versie.

De Java 3,0-agent voor Application Insights kan telemetrie-gegevens verwerken voordat de gegevens worden geëxporteerd.

Hier volgen enkele gebruiks voorbeelden voor telemetrie-processors:
 * Masker gevoelige gegevens.
 * Aangepaste dimensies voorwaardelijk toevoegen.
 * Werk de naam van de span bij, die wordt gebruikt voor het samen voegen van vergelijk bare telemetrie in de Azure Portal.
 * Specifieke span-kenmerk (en) neerzetten om opname kosten te bepalen.

> [!NOTE]
> Zie [steekproef onderdrukkingen](./java-standalone-sampling-overrides.md)als u specifieke (hele) reeksen wilt verwijderen voor het beheren van opname kosten.

## <a name="terminology"></a>Terminologie

Voordat u meer informatie over telemetrie-processors, moet u de term *span* begrijpen. Een reeks is een algemene term voor:

* Een binnenkomende aanvraag.
* Een uitgaande afhankelijkheid (bijvoorbeeld een externe aanroep naar een andere service).
* Een in-process afhankelijkheid (werk dat wordt gedaan door subonderdelen van de service).

Voor telemetrie-processors zijn deze onderdelen van het bereik belang rijk:

* Name
* Kenmerken

De bereik naam is de primaire weer gave voor aanvragen en afhankelijkheden in de Azure Portal. Reeks kenmerken vertegenwoordigen zowel de standaard eigenschappen als de aangepaste eigenschap van een bepaalde aanvraag of afhankelijkheid.

## <a name="telemetry-processor-types"></a>Typen telemetrie-processor

Op dit moment zijn de twee typen telemetrie-processors kenmerk processors en-processors.

Een kenmerk processor kan kenmerken invoegen, bijwerken, verwijderen of hashen.
Het kan ook een reguliere expressie gebruiken om een of meer nieuwe kenmerken van een bestaand kenmerk op te halen.

Een bereik processor kan de naam van de telemetrie bijwerken.
Het kan ook een reguliere expressie gebruiken om een of meer nieuwe kenmerken uit de span-naam op te halen.

> [!NOTE]
> Op dit moment verwerken telemetrie-processors alleen kenmerken van het type teken reeks. Ze verwerken geen kenmerken van het type Boolean of getal.

## <a name="getting-started"></a>Aan de slag

Maak een configuratie bestand met de naam *applicationinsights.jsop* om te beginnen. Sla het bestand op in dezelfde map als *applicationinsights-agent- \* . jar*. Gebruik de volgende sjabloon.

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

## <a name="include-criteria-and-exclude-criteria"></a>Criteria opnemen en criteria uitsluiten

Zowel kenmerk-processors als-processors ondersteunen optionele `include` en `exclude` criteria.
Een processor wordt alleen toegepast op de insluitingen die overeenkomen met de `include` criteria (als deze zijn opgegeven) _en_ voldoen niet aan de `exclude` criteria (indien deze zijn opgegeven).

Als u deze optie wilt configureren onder `include` of `exclude` (of beide), geeft u ten minste één `matchType` en of op `spanNames` `attributes` .
De configuratie include-exclude heeft meer dan een opgegeven voor waarde.
Alle opgegeven voor waarden moeten worden geëvalueerd als waar om te leiden tot een overeenkomst. 

* **Vereist veld**: `matchType` bepaalt hoe items in `spanNames` matrices en `attributes` matrices worden geïnterpreteerd. Mogelijke waarden zijn `regexp` en `strict` . 

* **Optionele velden**: 
    * `spanNames` moet overeenkomen met ten minste één van de items. 
    * `attributes` Hiermee geeft u de lijst met kenmerken die moeten worden vergeleken. Al deze kenmerken moeten exact overeenkomen om te resulteren in een overeenkomst.
    
> [!NOTE]
> Als beide `include` en `exclude` worden opgegeven, `include` worden de eigenschappen gecontroleerd voordat de `exclude` eigenschappen worden gecontroleerd.

### <a name="sample-usage"></a>Voorbeeldgebruik

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
Zie voor [beelden van telemetrie-processor](./java-standalone-telemetry-processors-examples.md)voor meer informatie.

## <a name="attribute-processor"></a>Kenmerk processor

De kenmerk processor wijzigt kenmerken van een reeks. Het kan de mogelijkheid bieden om reeksen op te nemen of uit te sluiten. Er wordt een lijst met acties gebruikt die worden uitgevoerd in de volg orde waarin het configuratie bestand is opgegeven. De processor ondersteunt de volgende acties:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert`Met deze actie wordt een nieuw kenmerk ingevoegd in de spans waar de sleutel nog niet bestaat.   

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
Voor de `insert` actie zijn de volgende instellingen vereist:
* `key`
* Ofwel `value` of `fromAttribute`
* `action`: `insert`

### `update`

`update`Met de actie wordt een kenmerk bijgewerkt in reeksen waar de sleutel al bestaat.

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
Voor de `update` actie zijn de volgende instellingen vereist:
* `key`
* Ofwel `value` of `fromAttribute`
* `action`: `update`


### `delete` 

`delete`Met deze actie wordt een kenmerk uit een bereik verwijderd.

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
Voor de `delete` actie zijn de volgende instellingen vereist:
* `key`
* `action`: `delete`

### `hash`

De `hash` actie hashes (SHA1) is een bestaande kenmerk waarde.

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
Voor de `hash` actie zijn de volgende instellingen vereist:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> De `extract` functie is alleen beschikbaar in versie 3.0.2 en hoger.

`extract`Met de actie worden waarden geëxtraheerd met behulp van een reguliere expressie regel van de invoer sleutel naar de doel sleutels die in de regel zijn opgegeven. Als er al een doel sleutel bestaat, wordt deze overschreven. Deze actie gedraagt zich als de instelling voor de [bereik processor](#extract-attributes-from-the-span-name) `toAttributes` , waarbij het bestaande kenmerk de bron is.

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
Voor de `extract` actie zijn de volgende instellingen vereist:
* `key`
* `pattern`
* `action`: `extract`

Zie voor [beelden van telemetrie-processor](./java-standalone-telemetry-processors-examples.md)voor meer informatie.

## <a name="span-processor"></a>Span processor

De reeks processor wijzigt de naam van de reeks of kenmerken van een reeks op basis van de naam van de span. Het kan de mogelijkheid bieden om reeksen op te nemen of uit te sluiten.

### <a name="name-a-span"></a>Een reeks een naam

De `name` sectie vereist de `fromAttributes` instelling. De waarden van deze kenmerken worden gebruikt voor het maken van een nieuwe naam, samengevoegd in de volg orde waarin de configuratie is opgegeven. De processor krijgt alleen de bereik naam te wijzigen als al deze kenmerken aanwezig zijn op de reeks.

De `separator` instelling is optioneel. Deze instelling is een teken reeks. Het is opgegeven om waarden te splitsen.
> [!NOTE]
> Als het wijzigen van de naam afhankelijk is van de kenmerken processor om kenmerken aan te passen, moet u ervoor zorgen dat de bereik processor is opgegeven na de kenmerken processor in de pijplijn specificatie.

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

### <a name="extract-attributes-from-the-span-name"></a>Kenmerken uit de bereik naam extra heren

De `toAttributes` sectie bevat een lijst met de reguliere expressies die overeenkomen met de naam van het bereik. Hiermee worden kenmerken geëxtraheerd op basis van subexpressies.

De `rules` instelling is vereist. Met deze instelling worden de regels vermeld die worden gebruikt om kenmerk waarden uit de bereik naam op te halen. 

De waarden in de bereik naam worden vervangen door geëxtraheerde kenmerk namen. Elke regel in de lijst is een teken reeks met een reguliere expressie (regex). 

Dit zijn de waarden die worden vervangen door geëxtraheerde kenmerk namen:

1. De naam van de span wordt gecontroleerd op basis van de regex. 
1. Als de regex overeenkomt, worden alle benoemde subexpressies van de regex geëxtraheerd als kenmerken. 
1. De geëxtraheerde kenmerken worden toegevoegd aan de reeks. 
1. Elke naam van een subexpressie wordt een kenmerk naam. 
1. De subexpressie die overeenkomt met een deel wordt de kenmerk waarde. 
1. Het overeenkomende deel in de bereik naam wordt vervangen door de naam van het geëxtraheerde kenmerk. Als de kenmerken al bestaan in de reeks, worden deze overschreven. 
 
Dit proces wordt herhaald voor alle regels in de volg orde waarin ze zijn opgegeven. Elke volgende regel werkt op de naam van het bereik dat de uitvoer van de vorige regel is.

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

## <a name="common-span-attributes"></a>Algemene span kenmerken

In deze sectie vindt u enkele algemene span-kenmerken die door telemetrie-processors kunnen worden gebruikt.

### <a name="http-spans"></a>HTTP-reeksen

| Kenmerk  | Type | Description | 
|---|---|---|
| `http.method` | tekenreeks | HTTP-aanvraag methode.|
| `http.url` | tekenreeks | Volledige URL van de HTTP-aanvraag in het formulier `scheme://host[:port]/path?query[#fragment]` . Het fragment wordt meestal niet via HTTP verzonden. Maar als het fragment bekend is, moet het worden opgenomen.|
| `http.status_code` | getal | [Status code van http-antwoord](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | tekenreeks | Type HTTP-protocol. |
| `http.user_agent` | tekenreeks | Waarde van de [http-](https://tools.ietf.org/html/rfc7231#section-5.5.3) header van de agent die door de client is verzonden. |

### <a name="jdbc-spans"></a>JDBC-reeksen

| Kenmerk  | Type | Description  |
|---|---|---|
| `db.system` | tekenreeks | Id voor het Database Management System DBMS-product dat wordt gebruikt. |
| `db.connection_string` | tekenreeks | Verbindings reeks die wordt gebruikt om verbinding te maken met de data base. Het is raadzaam om Inge sloten referenties te verwijderen.|
| `db.user` | tekenreeks | Gebruikers naam voor toegang tot de data base. |
| `db.name` | tekenreeks | De teken reeks die wordt gebruikt voor het rapporteren van de naam van de data base die wordt geopend. Voor opdrachten waarmee de data base wordt overgeschakeld, moet deze teken reeks worden ingesteld op de doel database, zelfs als de opdracht mislukt.|
| `db.statement` | tekenreeks | De data base-instructie die wordt uitgevoerd.|
