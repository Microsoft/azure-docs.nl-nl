---
title: Ontvangsten-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de ontvangstbewijs analyse met de formulier Recognizer API-gebruik en limieten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81e8cd5cf4af8da76ae4eb09bed5a4ee0368da4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467354"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Vooraf gegenereerde ontvangst model voor formulier herkenning

Azure Form Recognizer kan gegevens van verkoop ontvangsten analyseren en extra heren met behulp van het vooraf gegenereerde ontvangst model. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/concept-recognizing-text.md) met diepe leer modellen voor het uitpakken van belang rijke informatie uit in het Engels geschreven kwitanties.

## <a name="understanding-receipts"></a>Over ontvangst bevestigingen

Veel bedrijven en personen vertrouwen nog steeds op hand matig geëxtraheerde gegevens uit verkoop ontvangsten. Het automatisch extra heren van gegevens van deze ontvangstsen kan gecompliceerd zijn. Ontvangst bewijzen kunnen worden crumpled, moeilijk te lezen, hebben hand matige onderdelen en afbeeldingen van een smartphone met een lage kwaliteit. Bovendien kunnen ontvangst sjablonen en-velden aanzienlijk verschillen per markt, regio en handelaar. Deze gegevens extractie en detectie problemen voor het detecteren van een veld maken het een uniek probleem om de ontvangst te verwerken.  

De kwitantie-API maakt gebruik van optische teken herkenning (OCR) en ons vooraf gebouwde model om scenario's voor het verwerken van grote hoeveel heden ontvangsten mogelijk te maken. Als u een kwitantie-API hebt, hoeft u geen model te trainen. De ontvangstbewijs afbeelding verzenden naar de API voor het analyseren van de kwitantie en de gegevens worden geëxtraheerd.

![voorbeeld van aankoopbewijs](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>Wat doet de ontvangst service? 

De vooraf ontwikkelde service voor ontvangst behaalt de inhoud van de verkoop ontvangsten &mdash; het type ontvangst bewijs dat u vaak bij een restaurant, een detail handelaar of een boodschappen archief ontvangt.

### <a name="fields-extracted"></a>Geëxtraheerde velden

|Naam| Type | Beschrijving | Tekst | Waarde (gestandaardiseerde uitvoer) |
|:-----|:----|:----|:----| :----|
| ReceiptType | tekenreeks | Type verkoop ontvangst | Gespecificeerd |  |
| Adverteerder | tekenreeks | Naam van de handelaar die de ontvangst heeft uitgegeven | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Weer gegeven telefoon nummer van handelaar | 987-654-3210 | + 19876543210 |
| MerchantAddress | tekenreeks | Vermeld adres van handelaar | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | De datum waarop de ontvangst is verzonden | 6 juni 2019 | 2019-06-26  |
| TransactionTime | tijd | Tijdstip waarop de ontvangst is verzonden | 4:49 UUR | 16:49:00  |
| Totaal | getal | Volledige transactie totaal van ontvangst | $14,34 | 14,34 |
| Subtotaal | getal | Subtotaal van de ontvangst, vaak voordat belastingen worden toegepast | $12,34 | 12.34 |
| Btw | getal | Belasting op ontvangst, vaak BTW of equivalent | $ 2,00 | 2,00 |
| Tip | getal | Tip inbegrepen door de koper | $1,00 | 1,00 |
| Items | matrix van objecten | Geëxtraheerde regel items, met naam, hoeveelheid, eenheids prijs en totale prijs geëxtraheerd | |
| Name | tekenreeks | Itemnaam | Surface Pro 6 | |
| Aantal | getal | Hoeveelheid van elk item | 1 | |
| Prijs | getal | Individuele prijs van elke artikel eenheid | $999,00 | 999,00 |
| Totale prijs | getal | Totale prijs van het regel item | $999,00 | 999,00 |

### <a name="additional-features"></a>Aanvullende functies

De kwitantie-API retourneert ook de volgende informatie:

* Betrouwbaarheids niveau van veld (elk veld retourneert een bijbehorende betrouwbaarheids waarde)
* OCR-onbewerkte tekst (OCR: geëxtraheerde tekst uitvoer voor de volledige ontvangst)
* Selectie kader voor elke waarde, regel en woord

## <a name="try-it-out"></a>Probeer het eens

Als u de service voor de ontvangst van de formulier herkenning wilt uitproberen, gaat u naar het hulp programma online voor beeld-UI:

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Ondersteunde land instellingen 

* **Vooraf gemaakte ontvangst versie 2.0** (ga) ondersteunt verkoop ontvangsten in de land instelling en-US
* **Vooraf gemaakte bevestigingen v 2.1-Preview. 3** (open bare preview) voegt extra ondersteuning toe voor de volgende land instellingen en ontvangstbewijs: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Taalinvoer 
  >
  > Vooraf ontwikkelde ontvangst v 2.1-Preview. 3 heeft een optionele aanvraag parameter om een land instelling voor de ontvangst van extra Engelse markten op te geven. Voor verkoop ontvangsten in het Engels vanuit Australië (EN-AU), Canada (EN-CA), Groot-Brittannië (en-GB) en India (EN-IN) kunt u de land instelling opgeven om de resultaten te verbeteren. Als er geen land instelling is opgegeven in v 2.1-Preview. 3, wordt het model standaard ingesteld op het model nl-nl.


## <a name="the-analyze-receipt-operation"></a>De bewerking toename analyseren

De [analyse-ontvangst](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) neemt een afbeelding of PDF van een ontvangst op als de invoer en extraheert de waarden van interest en text. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>De bewerking resultaat van het analyseren van toename ophalen

De tweede stap bestaat uit het aanroepen van de bewerking [resultaat analyse ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking voor het analyseren van een ontvangst. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de bewerking is niet gestart. |
| |  | uitvoeren: de analyse bewerking wordt uitgevoerd. |
| |  | mislukt: de analyse bewerking is mislukt. |
| |  | geslaagd: de analyse bewerking is voltooid. |

Wanneer het veld **status** de waarde **geslaagd** heeft, bevat het JSON-antwoord de resultaten van de ontvangst en tekst herkenning. Het resultaat van de ontvangst overeenkomst is ingedeeld als een woorden lijst met benoemde veld waarden. Elke waarde bevat de geëxtraheerde tekst, genormaliseerde waarde, het begrenzingsvak, de betrouw baarheid en de bijbehorende woord elementen. Het resultaat van de tekst herkenning is ingedeeld als een hiërarchie van lijnen en woorden, met tekst, selectie kader en betrouwbaarheids informatie.

![voor beeld van ontvangst resultaten](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer


De reactie op de bewerking resultaat van het analyseren van de ontvangst genereren is de gestructureerde weer gave van de ontvangst met alle gegevens die worden geëxtraheerd.  Hier vindt u een voor beeld van een [ontvangst bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) en de bijbehorende gestructureerde uitvoer uitvoer voor [beeld van ontvangst](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

Zie het volgende voor beeld van een geslaagde JSON-reactie:
* Het knooppunt `"readResults"` bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. 
* Het knooppunt `"documentResults"` bevat de visitekaartjeswaarden die het model heeft gedetecteerd. Hier vindt u nuttige sleutel/waarde-paren zoals de voor naam, achternaam, bedrijfs naam en nog veel meer.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
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

## <a name="customer-scenarios"></a>Klant scenario's  

De gegevens die zijn geëxtraheerd met de ontvangstbewijs-API kunnen worden gebruikt voor het uitvoeren van verschillende taken. Hieronder ziet u enkele voor beelden van wat klanten hebben gedaan met behulp van de kwitantie-API.

### <a name="business-expense-reporting"></a>Rapportage van zakelijke onkosten  

Vaak worden bedrijfs kosten in mindering gebracht op basis van de uitgaven voor het hand matig invoeren van gegevens uit installatie kopieën van kwitanties. Met de kassabon-API kunt u de geëxtraheerde velden gebruiken om dit proces gedeeltelijk te automatiseren en uw ontvangsten snel te analyseren.  

De ontvangstbewijs-API is een eenvoudige JSON-uitvoer waarmee u de geëxtraheerde veld waarden op verschillende manieren kunt gebruiken. Integreer met interne onkosten toepassingen om onkosten rapporten vooraf in te vullen. Lees voor meer informatie over hoe Acumatica gebruikmaakt van de ontvangstbewijs-API om [onkosten rapportage een minder gemeld proces te maken](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Controle en accounting

De ontvangst API-uitvoer kan ook worden gebruikt om analyses uit te voeren op verschillende punten in het onkosten rapportage-en vergoedings proces. U kunt ontvangst bewijzen verwerken om ze te sorteren voor hand matige controle of snelle goed keuringen.  

De ontvangst-uitvoer is ook nuttig voor algemeen gebruik. Gebruik de kassabon-API om alle onbewerkte bewijzen/PDF-gegevens te transformeren naar een digitale uitvoer die actie kan uitvoeren.

### <a name="consumer-behavior"></a>Consumenten gedrag 

Bevestigingen bevatten nuttige gegevens die u kunt gebruiken voor het analyseren van het gedrag van consumenten en het kopen van trends.

De kassabon-API voorziet ook in de [functie voor ontvangst verwerking van de AI Builder](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Volgende stappen

 . Ga aan de slag met het schrijven van een app voor het verwerken van een kwitantie met de formulier herkenner in de ontwikkel taal van uw keuze.

> [!div class="nextstepaction"]
> [Quick start voor een formulier herkenning volt ooien](quickstarts/client-library.md)

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](overview.md)
* [API-referentie voor formulier herkenning](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
