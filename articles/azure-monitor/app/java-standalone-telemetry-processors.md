---
title: Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java
description: Telemetrie-processors configureren in Azure Monitor Application Insights voor Java
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7fd53c77b64e028ffad25c8fa7a9eefd95439513
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387153"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetrie-processors (preview)-Azure Monitor Application Insights voor Java

> [!NOTE]
> Deze functie bevindt zich nog in de preview-versie.

De Java 3,0-agent voor Application Insights heeft nu de mogelijkheid om telemetriegegevens te verwerken voordat de gegevens worden geëxporteerd.

### <a name="some-use-cases"></a>Enkele use cases:
 * Masker gevoelige gegevens
 * Aangepaste dimensies voorwaardelijk toevoegen
 * De naam van de telemetrie bijwerken die wordt gebruikt voor aggregatie en weer gave

### <a name="supported-processors"></a>Ondersteunde processors:
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

De kenmerk processor en de bereik processor bieden de optie om een reeks eigenschappen van een reeks te geven die overeenkomen met, om te bepalen of de reeks moet worden opgenomen of uitgesloten van de processor. Als u deze optie wilt configureren, onder `include` en/of `exclude` ten minste één of `matchType` `spanNames` `attributes` is vereist. De configuratie voor opnemen/uitsluiten wordt ondersteund om meer dan één opgegeven voor waarde te hebben. Alle opgegeven voor waarden moeten worden geëvalueerd als waar om een overeenkomst te vinden. 

**Vereist veld**: 
* `matchType` Hiermee wordt bepaald hoe items in `spanNames` en `attributes` matrices worden geïnterpreteerd. Mogelijke waarden zijn `regexp` en `strict`. 

**Optionele velden**: 
* `spanNames` moet overeenkomen met ten minste één van de items. 
* `attributes` Hiermee geeft u de lijst met kenmerken op die moeten worden vergeleken. Al deze kenmerken moeten exact overeenkomen om een overeenkomst te vinden.

> [!NOTE]
> Als beide `include` en `exclude` worden opgegeven, `include` worden de eigenschappen gecontroleerd voor de `exclude` Eigenschappen.

#### <a name="sample-usage"></a>Voorbeeldgebruik

Hieronder ziet u hoe u de set eigenschappen van de reeks opgeeft om aan te geven op welke voor waarde deze processor moet worden toegepast. `include`Met de eigenschappen ervan wordt aangegeven welke items moeten worden opgenomen en de `exclude` eigenschappen moeten verder worden uitgefilterd die niet hoeven te worden verwerkt.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

Met de bovenstaande configuratie worden de volgende reeksen die overeenkomen met de eigenschappen en processor acties toegepast:

* Span1 name: svcB Attributes: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Span2 name: svcA Attributes: {env: staging, test_request: False, redact_trace: True}

De volgende reeksen komen niet overeen met de include-eigenschappen en processor acties worden niet toegepast:

* Span3 name: svcB Attributes: {env: Production, test_request: True, credit_card: 1234, redact_trace: False}

* Span4 naam: svcC Attributes: {env: dev, test_request: False}

## <a name="attribute-processor"></a>Kenmerk processor 

De kenmerken processor wijzigt kenmerken van een reeks. Het biedt optioneel ondersteuning voor de mogelijkheid om reeksen op te nemen of uit te sluiten.
Er wordt een lijst weer gegeven met acties die worden uitgevoerd in de volg orde die is opgegeven in het configuratie bestand. De ondersteunde acties zijn:

* `insert` : Hiermee wordt een nieuw kenmerk ingevoegd in de spans waar de sleutel nog niet bestaat
* `update` : Hiermee wordt een kenmerk bijgewerkt in reeksen waar de sleutel bestaat
* `delete` : Hiermee wordt een kenmerk uit een bereik verwijderd
* `hash`   : Hashes (SHA1) een bestaande kenmerk waarde

Voor de acties `insert` en `update`
* `key` is vereist
* een van `value` of `fromAttribute` is vereist
* `action` is vereist.

Voor de `delete` actie,
* `key` is vereist
* `action`: `delete` is vereist.

Voor de `hash` actie,
* `key` is vereist
* `action` : `hash` is vereist.

De lijst met acties kan worden samengesteld voor het maken van uitgebreide scenario's, zoals het kenmerk terugvullen, het kopiëren van waarden naar een nieuwe sleutel en het redigeren van gevoelige informatie.

#### <a name="sample-usage"></a>Voorbeeldgebruik

In het volgende voor beeld ziet u hoe u sleutels/waarden invoegt in reeksen:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

In het volgende voor beeld ziet u hoe u de processor configureert zodat alleen bestaande sleutels in een kenmerk worden bijgewerkt:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

In het volgende voor beeld ziet u hoe u reeksen kunt verwerken die een bereik naam hebben die overeenkomt met regexp-patronen.
Met deze processor wordt het kenmerk ' token ' verwijderd en wordt het kenmerk ' password ' verborgen in de spans waarbij de bereik naam overeenkomt \* met ' auth '. en waarbij de naam van de span niet overeenkomt met "login. \* ".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Span-processors

De reeks processor wijzigt de naam van de reeks of kenmerken van een reeks op basis van de naam van de span. Het biedt optioneel ondersteuning voor de mogelijkheid om reeksen op te nemen of uit te sluiten.

### <a name="name-a-span"></a>Een reeks een naam

De volgende instelling is vereist als onderdeel van de sectie Naam:

* `fromAttributes`: De kenmerk waarde voor de sleutels wordt gebruikt om een nieuwe naam te maken in de volg orde die is opgegeven in de configuratie. Alle kenmerk sleutels moeten worden opgegeven in de reik wijdte van de processor om de naam ervan te wijzigen.

De volgende instelling kan eventueel worden geconfigureerd:

* `separator`: Een teken reeks die wordt opgegeven, wordt gebruikt om waarden te splitsen
> [!NOTE]
> Als het wijzigen van de naam afhankelijk is van de kenmerken die worden gewijzigd door de attributen processor, moet u ervoor zorgen dat de bereik processor is opgegeven na de kenmerken processor in de pijplijn specificatie.

#### <a name="sample-usage"></a>Voorbeeldgebruik

In het volgende voor beeld worden de waarden van het kenmerk db. SVC, Operation en id gevormd door de nieuwe naam van de reeks, in die volg orde, gescheiden door de waarde "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Kenmerken uit de span-naam extra heren

Hiermee wordt een lijst met reguliere expressies gemaakt die overeenkomen met de naam van de reeks en de kenmerken uit de subexpressies worden opgehaald. Moet worden opgegeven in de `toAttributes` sectie.

De volgende instellingen zijn vereist:

`rules` : Een lijst met regels om kenmerk waarden te extra heren uit een span-naam. De waarden in de bereik naam worden vervangen door geëxtraheerde kenmerk namen. Elke regel in de lijst is regex-patroon teken reeks. De naam van de span wordt gecontroleerd op basis van de regex. Als de regex overeenkomt, worden alle benoemde subexpressies van de regex geëxtraheerd als kenmerken en worden deze toegevoegd aan de reeks. Elke naam van een subexpressie wordt een kenmerk naam en een subexpressie die overeenkomt met een deel wordt de kenmerk waarde. Het overeenkomende deel in de bereik naam wordt vervangen door de geëxtraheerde kenmerk naam. Als de kenmerken al bestaan in de reeks, worden deze overschreven. Het proces wordt herhaald voor alle regels in de volg orde waarin ze zijn opgegeven. Elke volgende regel werkt op de naam van de span die de uitvoer na het verwerken van de vorige regel.

#### <a name="sample-usage"></a>Voorbeeldgebruik

We gaan ervan uitgaan dat de naam van de invoer periode/API/v1/document/12345678/update. is Als de volgende resultaten worden toegepast in de naam van de uitvoer periode/api/v1/document/{documentId}/update, wordt er een nieuw kenmerk "documentId" = "12345678" aan de reeks toegevoegd.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

In het volgende voor beeld ziet u hoe u de naam van de span wijzigt in {operation_website} en het kenmerk {Key: operation_website, waarde: oldSpanName} toevoegt wanneer de reeks de volgende eigenschappen heeft:
- De span-naam bevat '/' overal in de teken reeks.
- De naam van de span is niet donot/Change.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```