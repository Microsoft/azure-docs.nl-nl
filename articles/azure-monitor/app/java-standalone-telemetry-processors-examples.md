---
title: Voor beelden van telemetrie-processor-Azure Monitor Application Insights voor Java
description: Bekijk voor beelden waarin telemetrie-processors in Azure Monitor Application Insights voor Java worden weer gegeven.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734584"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Voor beelden van telemetrie-processor-Azure Monitor Application Insights voor Java

In dit artikel vindt u voor beelden van telemetrie-processors in Application Insights voor Java. U vindt voor beelden voor het opnemen en uitsluiten van configuraties. U vindt ook voor beelden voor kenmerk-processors en-processors.
## <a name="include-and-exclude-samples"></a>Voor beelden opnemen en uitsluiten

In deze sectie ziet u hoe u reeksen kunt opnemen en uitsluiten. U ziet ook hoe u meerdere reeksen uitsluit en selectieve verwerking toepast.
### <a name="include-spans"></a>Omvat reeksen

In deze sectie wordt beschreven hoe u reeksen voor een kenmerk processor opneemt. De reeksen die niet overeenkomen met de eigenschappen, worden niet verwerkt door de processor.

Voor een match moet de bereik naam gelijk zijn aan `spanA` of `spanB` . 

Deze reeksen komen overeen met de include-eigenschappen en de processor acties worden toegepast:
* Span1 naam: ' spana ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: spanB Attributes: {env: dev, test_request: False}
* Span3 naam: ' spana ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Deze reeks komt niet overeen met de include-eigenschappen en de processor acties worden niet toegepast:
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

### <a name="exclude-spans"></a>Uitgesloten reeksen

In deze sectie wordt gedemonstreerd hoe u reeksen voor een kenmerk processor uitsluit. Reeksen die overeenkomen met de eigenschappen, worden niet verwerkt door deze processor.

Voor een match moet de bereik naam gelijk zijn aan `spanA` of `spanB` .

De volgende reeksen komen overeen met de eigenschappen exclude en de processor acties worden niet toegepast:
* Span1 naam: ' spana ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: spanB Attributes: {env: dev, test_request: False}
* Span3 naam: ' spana ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Deze reeks komt niet overeen met de eigenschappen exclude en de processor acties worden toegepast:
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

### <a name="exclude-spans-by-using-multiple-criteria"></a>Sluit reeksen uit met behulp van meerdere criteria

In deze sectie wordt gedemonstreerd hoe u reeksen voor een kenmerk processor uitsluit. Reeksen die overeenkomen met de eigenschappen, worden niet verwerkt door deze processor.

Voor een overeenkomst moeten aan de volgende voor waarden worden voldaan:
* Een kenmerk (bijvoorbeeld `env` of `dev` ) moet in de reeks bestaan.
* De reeks moet een kenmerk hebben met een sleutel `test_request` .

De volgende reeksen komen overeen met de eigenschappen exclude en de processor acties worden niet toegepast.
* Span1 name: spanB Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 naam: ' spana ' Attributes: {env: dev, test_request: False}

De volgende reeks komt niet overeen met de eigenschappen exclude en de processor acties worden toegepast:
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

### <a name="selective-processing"></a>Selectief verwerken

In deze sectie wordt beschreven hoe u de set eigenschappen van de reeks opgeeft waarmee wordt aangegeven op welke voor waarde deze processor moet worden toegepast. De include-eigenschappen geven aan welke reeksen moeten worden verwerkt. De uitsluitings eigenschappen zijn uitzonde ringen die niet hoeven te worden verwerkt.

In de volgende configuratie worden deze reeksen overeenkomen met de eigenschappen en worden er processor acties toegepast:

* Span1 name: spanB Attributes: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 naam: ' spana ' Attributes: {env: staging, test_request: False, redact_trace: True}

Deze reeksen komen niet overeen met de include-eigenschappen, en er worden geen processor acties toegepast:
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

In het volgende voor beeld wordt het nieuwe kenmerk ingevoegd `{"attribute1": "attributeValue1"}` in de spans waar de sleutel `attribute1` niet bestaat.

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

In het volgende voor beeld wordt het kenmerk waarde van gebruikt `anotherkey` om het nieuwe kenmerk in te voegen `{"newKey": "<value from attribute anotherkey>"}` in een spans waar de sleutel `newKey` niet bestaat. Als het kenmerk `anotherkey` niet bestaat, wordt er geen nieuw kenmerk in de reeksen ingevoegd.

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

In het volgende voor beeld wordt het kenmerk bijgewerkt naar `{"db.secret": "redacted"}` . Het kenmerk wordt bijgewerkt met `boo` behulp van de waarde van het kenmerk `foo` . De reeksen die niet het kenmerk hebben, worden `boo` niet gewijzigd.

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

In het volgende voor beeld ziet u hoe u een kenmerk met de sleutel verwijdert `credit_card` .

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

In het volgende voor beeld ziet u hoe u bestaande kenmerk waarden hashert.

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

In het volgende voor beeld ziet u hoe u een reguliere expressie (regex) kunt gebruiken om nieuwe kenmerken te maken op basis van de waarde van een ander kenmerk.
U kunt bijvoorbeeld `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` de volgende kenmerken invoegen:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *geen* wijziging

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

In het volgende voor beeld ziet u hoe reeksen worden verwerkt die een span-naam hebben die overeenkomt met regex-patronen.
Deze processor verwijdert het `token` kenmerk. Het `password` kenmerk wordt in een bereik van de reeks naam vergeleken `auth.*` en de naam van de span komt overeen `login.*` .

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

In het volgende voor beeld worden de waarden van kenmerken `db.svc` , `operation` en opgegeven `id` . De nieuwe naam van de reeks wordt met behulp van die kenmerken, in die volg orde, gescheiden door de waarde `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Kenmerken extra heren uit een span-naam

We gaan ervan uitgaan dat de naam van het invoer bereik is `/api/v1/document/12345678/update` . In het volgende voor beeld wordt de naam van de uitvoer periode in beslag genomen `/api/v1/document/{documentId}/update` . Het nieuwe kenmerk wordt toegevoegd `documentId=12345678` aan de reeks.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Attributes van een span name extra heren met behulp van opnemen en uitsluiten

In het volgende voor beeld ziet u hoe u de naam van de span wijzigt in `{operation_website}` . Er wordt een kenmerk met sleutel `operation_website` en waarde toegevoegd `{oldSpanName}` wanneer de reeks de volgende eigenschappen heeft:
- De span-naam bevat `/` een wille keurige plaats in de teken reeks.
- De naam van de span is niet `donot/change` .
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
