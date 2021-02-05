---
title: Visite kaartjes-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot analyse van visite kaartjes met het formulier Recognizer API-gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 4cd762d6c264d95ecb1bd0f3f4c3a4d96eb5a57d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585089"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Model voor het maken van een webkaart met formulier herkenning 

Met Azure Form Recognizer kunnen contact gegevens van visite kaartjes worden geanalyseerd en geëxtraheerd met behulp van het vooraf ontwikkelde model voor visite kaartjes. U kunt krachtige functies voor optische teken herkenning (OCR) combi neren met ons model voor visite kaartjes voor het extra heren van belang rijke informatie uit visite kaartjes in het Engels. Hiermee worden persoonlijke contact gegevens, bedrijfs naam, functie titel en meer opgehaald. De vooraf ontwikkelde Business Card-API is openbaar beschikbaar in de preview-versie van de formulier Recognizer v 2.1. 

## <a name="what-does-the-business-card-service-do"></a>Wat doet de Business Card-service?

De vooraf ontwikkelde Business Card-API extraheert belang rijke velden van visite kaartjes en retourneert deze in een geordend JSON-antwoord.

![Contoso-geitemde installatie kopie van FOTT + JSON-uitvoer](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Geëxtraheerde velden:

|Naam| Type | Description | Tekst | 
|:-----|:----|:----|:----|
| ContactNames | matrix van objecten | Naam van contact persoon geëxtraheerd uit visite kaartje | [{"FirstName": "John", "LastName": "Jansen"}] |
| FirstName | tekenreeks | Eerste (gegeven) naam van contact persoon | Letterlijk | 
| LastName | tekenreeks | Naam van de laatste persoon (familie) |     Vries | 
| Bedrijfs naam | tekenreeksmatrix | Bedrijfs naam opgehaald uit visite kaartje | ["Contoso"] | 
| Afdelingen | tekenreeksmatrix | Afdeling of organisatie van contact persoon | ["R&D"] | 
| JobTitles | tekenreeksmatrix | Titel van de vermelde functie van de contact persoon | [' Software-Engineer '] | 
| E-mails | tekenreeksmatrix | Contact opnemen met e-mail opgehaald uit visite kaartje | ["johndoe@contoso.com"] | 
| Websites | tekenreeksmatrix | Website geëxtraheerd uit visite kaartje | ["https://www.contoso.com"] | 
| Adressen | tekenreeksmatrix | Adres opgehaald uit visite kaartje | ["123 hoofd straat, Redmond, WA 98052"] | 
| MobilePhones | matrix van telefoon nummers | Mobiel telefoon nummer opgehaald uit visite kaartje | ["+ 19876543210"] |
| Faxberichten | matrix van telefoon nummers | Telefoon nummer van Fax geëxtraheerd uit visite kaartje | ["+ 19876543211"] |
| WorkPhones | matrix van telefoon nummers | Telefoon nummer werk geëxtraheerd uit visite kaartje | ["+ 19876543231"] |
| OtherPhones     | matrix van telefoon nummers | Ander telefoon nummer opgehaald van visite kaartje | ["+ 19876543233"] |


De API voor het visite kaartje kan ook alle herkende tekst van de visite kaart retour neren. Deze OCR-uitvoer is opgenomen in het JSON-antwoord.  

### <a name="input-requirements"></a>Invoer vereisten 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>De bewerking visite kaartje analyseren

De [kaart](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) voor het analyseren van een visite kaartje neemt een afbeelding of PDF van een visite kaartje als invoer en extraheert de gewenste waarden. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>De resultaat bewerking voor het analyseren van bedrijfs kaarten ophalen

De tweede stap bestaat uit het aanroepen van de [resultaat bewerking analyse van bedrijfs kaart ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking visite kaartje analyseren. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de analyse bewerking is niet gestart.<br /><br />uitvoeren: de analyse bewerking wordt uitgevoerd.<br /><br />mislukt: de analyse bewerking is mislukt.<br /><br />geslaagd: de analyse bewerking is voltooid.|

Wanneer het veld **status** de waarde **geslaagd** heeft, bevat het JSON-antwoord de resultaten van de visite kaart en de optionele tekst herkenning, indien nodig. Het resultaat van het visite kaartje is ingedeeld als een woorden lijst met benoemde veld waarden, waarbij elke waarde de geëxtraheerde tekst, genormaliseerde waarde, het begrenzingsvak, betrouw baarheid en bijbehorende woord elementen bevat. Het resultaat van de tekst herkenning is ingedeeld als een hiërarchie van lijnen en woorden, met tekst, selectie kader en betrouwbaarheids informatie.

![voorbeeld uitvoer van visite kaartjes](./media/business-card-results.png)

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Zie het volgende voor beeld van een geslaagde JSON-reactie: het knoop punt ' readResults ' bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knoop punt ' documentResults ' bevat de bedrijfsspecifieke waarden die het model heeft gedetecteerd. Hier vindt u nuttige contact gegevens, zoals de voor naam, achternaam, bedrijfs naam en nog veel meer.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Volg de [Quick Start Snelstartgids om](./QuickStarts/client-library.md) gegevens extractie met behulp van python en de rest API te implementeren.

## <a name="customer-scenarios"></a>Klant Scenario's  

De gegevens die zijn geëxtraheerd met de Business Card API kunnen worden gebruikt voor het uitvoeren van verschillende taken. Als u deze contact gegevens uitpakt, bespaart u automatisch tijd voor die in client gerichte rollen. Hier volgen enkele voor beelden van wat onze klanten hebben gedaan met de API van het visite kaartje:

* Haal contact gegevens op uit visite kaartjes en maak snel contact met uw telefoon. 
* Integreer met CRM om automatisch contact te maken met installatie kopieën van visite kaartjes. 
* Houd de verkoop kansen bij.  
* Haal contact gegevens in bulk op uit bestaande afbeeldingen van visite kaartjes. 

De Business Card-API voorziet ook in de [verwerkings functie van de AI Builder-bedrijfs kaart](/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Volgende stappen

- Volg de [Quick](./quickstarts/client-library.md) start om aan de slag te gaan met het herkennen van visite kaartjes.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
* [REST API referentie documenten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
