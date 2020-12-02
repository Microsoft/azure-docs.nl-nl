---
title: 'Quickstart: Ontvangstgegevens extraheren met behulp van cURL - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Form Recognizer REST API met cURL om gegevens te extraheren uit afbeeldingen van Amerikaanse kassabonnen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 9ef4378602403afccc7c02e4bacb50e851d74f2a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96003991"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Quickstart: Ontvangstgegevens ophalen met behulp van de Form Recognizer REST API met cURL

In deze quickstart gebruikt u de Azure Form Recognizer REST API met cURL om relevante informatie uit de Amerikaanse kassabonnen te extraheren en te identificeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:
- [cURL](https://curl.haxx.se/windows/) geïnstalleerd.
- Een URL voor een afbeelding van een ontvangstbewijs. U kunt voor deze quickstart een [voorbeeldafbeelding](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) gebruiken.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Een ontvangstbewijs analyseren

Als u wilt beginnen met het analyseren van een ontvangstbewijs, roept u de **[Ontvangstbewijs analyseren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** -API aan met de cURL-opdracht hieronder. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<your receipt URL>` door het URL-adres van een afbeelding van het ontvangstbewijs.
1. Vervang `<subscription key>` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<your receipt URL>\"}"
```

U ontvangt een `202 (Success)` antwoord met am **Operation-Location**-koptekst. Deze waarde van deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen. In het volgende voorbeeld is de tekenreeks na `operations/` de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>De resultaten van het ontvangstbewijs ophalen

Nadat u de **Analyze Receipt**-API hebt aangeroepen, roept u de **[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** -API aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `<operationId>` door de bewerkings-id uit de vorige stap.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. Het eerste veld, `"status"`, geeft de status van de bewerking aan. Als de bewerking is voltooid, bevat het veld `"readResults"` elke regel tekst die is geëxtraheerd uit het ontvangstbewijs, en het veld `"documentResults"` bevat sleutel/waarde-informatie voor de meest relevante onderdelen van het ontvangstbewijs. Als de bewerking niet is voltooid, wordt de waarde van `"status"` `"running"` of `"notStarted"`. U moet de API opnieuw aanroepen, handmatig of via een script. We raden een interval van één seconde of meer aan tussen oproepen.

Bekijk de volgende afbeelding van het ontvangstbewijs en de bijbehorende JSON-uitvoer. De uitvoer is voor het gemak ingekort.

![Een ontvangstbewijs van Contoso Store](../media/contoso-allinone.jpg)

Het knooppunt `"readResults"` bevat alle herkende tekst (als u de optionele parameter *includeTextDetails* instelt op `true`). De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knooppunt `"documentResults"` bevat de ontvangstbewijswaarden die het model heeft gedetecteerd. Hier vindt u nuttige sleutel/waarde-paren zoals de belasting, het totaal, het bedrijfsadres, enzovoort.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Form Recognizer REST API met cURL gebruikt om de inhoud van een kassabon te extraheren. Raadpleeg hierna de referentiedocumentatie om de Form Recognizer API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)
