---
title: Voor beelden van telemetrie-processors-Azure Monitor Application Insights voor Java
description: Voor beelden van telemetrie-processors in Azure Monitor Application Insights voor Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146444"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Voor beelden van telemetrie-processors-Azure Monitor Application Insights voor Java

## <a name="includeexclude-samples"></a>Voor beelden opnemen/uitsluiten

### <a name="1-include-spans"></a>1. insluitingen bevatten

Hieronder ziet u een voor beeld van reeksen voor deze kenmerken processor. Alle andere reeksen die niet overeenkomen met de eigenschappen, worden niet verwerkt door deze processor.

Aan de volgende voor waarden moet worden voldaan:
* De span-naam moet gelijk zijn aan "spana" of "spanB" 

Hier volgen enkele reeksen die overeenkomen met de include-eigenschappen en de processor acties worden toegepast.
* Span1 naam: ' spana ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: spanB Attributes: {env: dev, test_request: False}
* Span3 naam: ' spana ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

De volgende reeks komt niet overeen met de include-eigenschappen en de processor acties worden niet toegepast.
* Span4 naam: spanC Attributes: {env: dev, test_request: False}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. uitgesloten reeksen

Hieronder ziet u een uitzonde ring van reeksen voor deze kenmerken processor. Alle reeksen die overeenkomen met de eigenschappen, worden niet verwerkt door deze processor.

Aan de volgende voor waarden moet worden voldaan:
* De span-naam moet gelijk zijn aan "spana" of "spanB" 

Hier volgen enkele reeksen die overeenkomen met de uitsluitings eigenschappen en de processor acties worden niet toegepast.
* Span1 naam: ' spana ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: spanB Attributes: {env: dev, test_request: False}
* Span3 naam: ' spana ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

De volgende reeks komt niet overeen met de exclude-eigenschappen en de processor acties worden toegepast.
* Span4 naam: spanC Attributes: {env: dev, test_request: False}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti omvat

Hieronder ziet u een uitzonde ring van reeksen voor deze kenmerken processor. Alle reeksen die overeenkomen met de eigenschappen, worden niet verwerkt door deze processor.

Aan de volgende voor waarden moet worden voldaan:
* Er moet een kenmerk (' env ', ' dev ') aanwezig zijn in de reeks voor een overeenkomst.
* Zolang er een-kenmerk met sleutel test_request is, is er een overeenkomst.

Hier volgen enkele reeksen die overeenkomen met de uitsluitings eigenschappen en de processor acties worden niet toegepast.
* Span1 name: spanB Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: ' spana ' Attributes: {env: dev, test_request: False}

De volgende reeks komt niet overeen met de exclude-eigenschappen en de processor acties worden toegepast.
* Span3 naam: spanB Attributes: {env: 1, test_request: dev, credit_card: 1234}
* Span4 naam: spanC Attributes: {env: dev, dev_request: False}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. selectief verwerken

Hieronder ziet u hoe u de set eigenschappen van de reeks opgeeft om aan te geven op welke voor waarde deze processor moet worden toegepast. `include`Met de eigenschappen ervan wordt aangegeven welke items moeten worden opgenomen en de `exclude` eigenschappen moeten verder worden uitgefilterd die niet hoeven te worden verwerkt.

Met de onderstaande configuratie worden de volgende reeksen die overeenkomen met de eigenschappen en processor acties toegepast:

* Span1 name: spanB Attributes: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 naam: ' spana ' Attributes: {env: staging, test_request: False, redact_trace: True}

De volgende reeksen komen niet overeen met de include-eigenschappen en processor acties worden niet toegepast:
* Span3 name: spanB Attributes: {env: Production, test_request: True, credit_card: 1234, redact_trace: False}
* Span4 naam: spanC Attributes: {env: dev, test_request: False}

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
  }
}
```
## <a name="attribute-processor-samples"></a>Voor beelden van kenmerk processor

### <a name="insert"></a>Invoegen

Met de volgende code wordt een nieuw kenmerk {"attribute1": "attributeValue1"} ingevoegd als de sleutel "attribute1" niet bestaat.

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Invoegen vanuit een andere sleutel

In het volgende wordt de waarde van het kenmerk ' anotherkey ' gebruikt om een nieuw kenmerk {' newKey ': ' in te voegen van het kenmerk ' anotherkey '}, zodat er sprake is van de sleutel ' newKey '. Als het kenmerk anotherkey niet bestaat, wordt er geen nieuw kenmerk ingevoegd in-reeksen.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Bijwerken

De volgende update van het kenmerk op {"db. Secret": "redactied"} en werkt het kenmerk installatiekopie bij met de waarde van het kenmerk foo. Reeksen zonder het kenmerk ' installatiekopie ' worden niet gewijzigd.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Verwijderen

Hieronder ziet u een voor beeld van het verwijderen van het kenmerk met de sleutel credit_card.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

Hieronder ziet u een voor beeld van hash-bestaande kenmerk waarden.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Extraheren

In het volgende voor beeld wordt gedemonstreerd met behulp van regex om nieuwe kenmerken te maken op basis van de waarde van een ander kenmerk.
Bijvoorbeeld http. URL = http://example.com/path?queryParam1=value1 , queryParam2 = waarde2 de volgende kenmerken worden ingevoegd:
* httpProtocol: http
* httpDomain: example.com
* httpPath: pad
* httpQueryParams: queryParam1 = waarde1, queryParam2 = waarde2
* http. URL-waarde wordt niet gewijzigd.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
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


## <a name="span-processor-samples"></a>Voor beelden van processors

### <a name="name-a-span"></a>Een reeks een naam

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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Kenmerken extra heren uit een span name met include en exclude

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