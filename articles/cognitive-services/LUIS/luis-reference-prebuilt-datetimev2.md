---
title: DatetimeV2 prebuiled entities-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel heeft datetimeV2 prebuiled entity Information in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013068"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 prebuiled-entiteit voor een LUIS-app

De **datetimeV2** prebuilende entiteit extraheert datum-en tijd waarden. Deze waarden worden omgezet in een gestandaardiseerde indeling voor gebruik door client Programma's. Wanneer een utterance een datum of tijd heeft die niet volledig is, bevat LUIS _zowel de vroegere als toekomstige waarden_ in het eindpunt antwoord. Omdat deze entiteit al is getraind, hoeft u geen voorbeeld uitingen met datetimeV2 toe te voegen aan de toepassings intentie.

## <a name="types-of-datetimev2"></a>Typen datetimeV2
DatetimeV2 wordt beheerd vanuit de bibliotheek [met herkennings-tekst](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) github.

## <a name="example-json"></a>Voor beeld JSON

De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3-antwoord](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Naam van eigenschap |Type en beschrijving van eigenschap|
|---|---|
|Entiteit|**teken reeks** : tekst geëxtraheerd uit de utterance met het type datum, tijd, datum bereik of tijds bereik.|
|type|**teken reeks** -een van de [subtypen van datetimeV2](#subtypes-of-datetimev2)
|Start index|**int** -de index in de utterance waarbij de entiteit begint.|
|endIndex|**int** -de index in de utterance waarbij de entiteit eindigt.|
|opgelost|Heeft een `values` matrix met een, twee of vier [waarden voor de oplossing](#values-of-resolution).|
|beëindigen|De eind waarde van een tijd of datum bereik, in dezelfde notatie als `value` . Wordt alleen gebruikt als `type` is `daterange` , `timerange` of `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtypen van datetimeV2

De vooraf gebouwde **datetimeV2** -entiteit heeft de volgende subtypen, en voor beelden van deze worden in de volgende tabel vermeld:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Waarden van resolutie
* De matrix heeft één element als de datum of tijd in de utterance volledig is opgegeven en ondubbelzinnig is.
* De matrix heeft twee elementen als de datetimeV2-waarde dubbel zinnig is. Dubbel zinnigheid omvat geen opgegeven jaar, tijd of tijds bereik. Zie [onduidelijke datums](#ambiguous-dates) voor voor beelden. Wanneer de tijd voor A.M. niet eenduidig is of P.M. zijn beide waarden opgenomen.
* De matrix heeft vier elementen als de utterance twee elementen heeft met ambiguïteit. Deze dubbel zinnigheid omvat elementen die:
  * Een datum of datum bereik dat is ambigu door het jaar
  * Een tijd-of tijd bereik dat kan worden geïnterpreteerd als aan A.M. of P.M. Bijvoorbeeld 3:00 april 3de.

Elk element van de `values` matrix kan de volgende velden bevatten:

|Naam van eigenschap|Eigenschapsbeschrijving|
|--|--|
|Timex|tijd, datum of datum bereik in de TIMEX-indeling die volgt op de [ISO 8601-standaard](https://en.wikipedia.org/wiki/ISO_8601) en de TIMEX3-kenmerken voor aantekening met behulp van de TimeML-taal.|
|mod|de term die wordt gebruikt om te beschrijven hoe de waarde `before` kan worden gebruikt, zoals, `after` .|
|type|Het subtype, dat een van de volgende items kan zijn: `datetime` , `date` , `time` , `daterange` , `timerange` , `datetimerange` , `duration` , `set` .|
|waarde|**Beschrijving.** Een datetime-object in de notatie JJJJ-MM-DD (datum), uu: mm: SS (tijd) JJJJ-MM-DD uu: mm: SS (datetime). Als `type` dat `duration` het geval is, is de waarde het aantal seconden (duur) <br/> Wordt alleen gebruikt als `type` is `datetime` or `date` , `time` , of ' duration '.|

## <a name="valid-date-values"></a>Geldige datum waarden

De **datetimeV2** ondersteunt datums tussen de volgende bereiken:

| Min. | Max |
|----------|-------------|
| 1e januari 1900   | 31 december 2099 |

## <a name="ambiguous-dates"></a>Ambigue datums

Als de datum in het verleden of later ligt, biedt LUIS beide waarden. Een voor beeld is een utterance die de maand en de datum zonder het jaar bevat.

U kunt bijvoorbeeld de volgende utterance:

`May 2nd`

* Als de datum van vandaag mei 3e 2017 is, biedt LUIS zowel ' 2017-05-02 ' als ' 2018-05-02 ' als waarden.
* Wanneer de huidige datum 1 mei 2017 is, biedt LUIS zowel ' 2016-05-02 ' als ' 2017-05-02 ' als waarden.

In het volgende voor beeld wordt de oplossing van de entiteit ' mei 2e ' weer gegeven. Bij deze oplossing wordt ervan uitgegaan dat de huidige datum een datum is tussen 2e mei 2017 en 1 mei 2018.
Velden met `X` in het `timex` veld zijn delen van de datum die niet expliciet zijn opgegeven in de utterance.

## <a name="date-resolution-example"></a>Voor beeld van datum omzetting


De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`May 2nd`

#### <a name="v3-response"></a>[V3-antwoord](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Voor beelden van resolutie van datum bereik voor numerieke datum

De `datetimeV2` entiteit extraheert de datum-en tijds bereik. De `start` `end` velden en bepalen het begin en het einde van het bereik. Voor de utterance `May 2nd to May 5th` levert Luis **daterange** -waarden voor zowel het huidige jaar als het volgende jaar. In het `timex` veld geven de `XXXX` waarden de dubbel zinnigheid van het jaar. `P3D` geeft aan dat de periode drie dagen lang is.

De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3-antwoord](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Voor beelden van resolutie van datum bereik voor dag van week

In het volgende voor beeld ziet u hoe LUIS gebruikmaakt van **datetimeV2** om de utterance op te lossen `Tuesday to Thursday` . In dit voor beeld is de huidige datum 19 juni. LUIS bevat **daterange** -waarden voor beide datumbereiken die voorafgaat en de huidige datum volgen.

De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3-antwoord](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Dubbel zinnige tijd
De waarden matrix heeft twee tijd elementen als de tijd of het tijds bereik niet eenduidig is. Wanneer er sprake is van een niet-eenduidige tijd, hebben de waarden de A.M. en P.M. aantal.

## <a name="time-range-resolution-example"></a>Voor beeld van een oplossing voor tijds bereik

DatetimeV2 JSON-antwoord is gewijzigd in de API v3. In het volgende voor beeld ziet u hoe LUIS gebruikmaakt van **datetimeV2** om de utterance met een tijds bereik op te lossen.

Wijzigingen van API v2:
* `datetimeV2.timex.type` de eigenschap wordt niet meer geretourneerd omdat deze wordt geretourneerd op het bovenliggende niveau `datetimev2.type` .
* De `datetimeV2.value` naam van de eigenschap is gewijzigd in `datetimeV2.timex` .

De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3-antwoord](#tab/5-1)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/5-2)

De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Voor beeld van een tijd resolutie

De volgende utterance en de gedeeltelijke JSON-respons worden hieronder weer gegeven.

`8am`

#### <a name="v3-response"></a>[V3-antwoord](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Afgeschafte datum-/tijdwaarde

De `datetime` vooraf samengestelde entiteit wordt afgeschaft en vervangen door **datetimeV2**.

Als u wilt vervangen `datetime` door `datetimeV2` in uw Luis-app, voert u de volgende stappen uit:

1. Open het deel venster **entiteiten** van de web-interface van Luis.
2. De **datum/** prebuilende entiteit verwijderen.
3. Klik op **vooraf samengestelde entiteit toevoegen**
4. Selecteer **datetimeV2** en klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [dimensie](luis-reference-prebuilt-dimension.md), de [e-mail](luis-reference-prebuilt-email.md) entiteiten en het [aantal](luis-reference-prebuilt-number.md).

