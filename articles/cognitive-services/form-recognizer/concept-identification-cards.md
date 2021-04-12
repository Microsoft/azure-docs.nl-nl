---
title: Id's-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten die betrekking hebben op het ophalen van gegevens uit identiteits documenten met de vooraf gemaakte id-API voor formulier herkenning.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed8516f9a898131338fb5b4d75e25cd774c5ab43
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285352"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Model voor vooraf gebouwde ID-kaart (ID) voor formulier herkenning

Azure Form Recognizer kan gegevens uit overheids-ID-kaarten (Id's) analyseren en extra heren met behulp van het vooraf samengestelde id-model. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/overview-ocr.md) met id-herkennings mogelijkheden voor het extra heren van belang rijke informatie uit de wereld wijde paspoorten en de licenties van het Amerikaanse stuur programma (alle 50 staten en D.C.). De id-API extraheert belang rijke informatie uit deze identiteits documenten, zoals de voor naam, achternaam, geboorte datum, document nummer en nog veel meer. Deze API is beschikbaar in de versie van de formulier Recognizer v 2.1 Preview als een Cloud service en als een on-premises container.

## <a name="what-does-the-id-service-do"></a>Wat doet de ID-service? 

De preconstrueerde ID-service extraheert de sleutel waarden van de wereld wijde paspoorten en de licenties van het Amerikaanse stuur programma en retourneert deze in een geordend gestructureerd JSON-antwoord. 

![Licentie voor voorbeeld stuur programma](./media/id-example-drivers-license.JPG)

![Voor beeld van Pass Port](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Geëxtraheerde velden

|Naam| Type | Beschrijving | Waarde | 
|:-----|:----|:----|:----|
|  Land/regio | country | Land code die voldoet aan ISO 3166-standaard | Verenigde Staten | 
|  DateOfBirth | date | DOB in de indeling JJJJ-MM-DD | "1980-01-01" | 
|  DateOfExpiration | date | Verval datum in de indeling JJJJ-MM-DD | "2019-05-05" |  
|  DocumentNumber | tekenreeks | Relevant paspoort nummer, rijbewijs nummer, enzovoort. | "340020013" |  
|  FirstName | tekenreeks | Geëxtraheerde opgegeven naam en middelste initialen, indien van toepassing | Jennifer | 
|  LastName | tekenreeks | Geëxtraheerde achternaam | "BROOKS" |   
|  Letters | country | Land code die voldoet aan ISO 3166-standaard | Verenigde Staten |
|  Seks | geslacht | Mogelijke geëxtraheerde waarden zijn ' M ', ' F ' en ' X ' | Ls | 
|  MachineReadableZone | object | Geëxtraheerde Pass Port-MRZ met twee regels van 44 tekens elk | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  Document type | tekenreeks | Document type, bijvoorbeeld Pass Port, rijbewijs | .net |  
|  Adres | tekenreeks | Geëxtraheerd adres (alleen de licentie van het stuur programma) | "123 STRAAT ADRES JOUW STAD WA 99999-1234"|
|  Region | tekenreeks | Geëxtraheerde regio, staat, provincie enzovoort (alleen rijbewijs) | Utrecht | 

### <a name="additional-features"></a>Aanvullende functies

De id-API retourneert ook de volgende informatie:

* Betrouwbaarheids niveau van veld (elk veld retourneert een bijbehorende betrouwbaarheids waarde)
* OCR-onbewerkte tekst (OCR: geëxtraheerde tekst uitvoer voor de volledige ontvangst)
* Selectie kader van elk geëxtraheerd veld in de licenties van het Amerikaanse stuur programma
* Selectie kader voor de computer met een lees bare zone (MRZ) op Pass ports

  > [!NOTE]
  > Vooraf gemaakte Id's detecteren geen ID-authenticiteit
  >
  > Door de vooraf gemaakte Id's van de formulier herkenning haalt sleutel gegevens op uit ID-gegevens. De geldigheid of authenticiteit van het oorspronkelijke identiteits document wordt echter niet gedetecteerd. 

## <a name="try-it-out"></a>Probeer het eens

Als u de formulier Recognizer-ID-service wilt uitproberen, gaat u naar het hulp programma voor de online voorbeeld GEBRUIKERSINTERFACE:

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Ondersteunde ID-typen  

* **Vooraf gemaakte id's v 2.1-Preview. 3** Haalt sleutel waarden uit de wereld wijde paspoorten en de licenties van het Amerikaanse stuur programma. 

  > [!NOTE]
  > Ondersteuning van ID-type 
  >
  > Momenteel ondersteunde ID-typen zijn wereld wijde Pass Port-en Amerikaanse stuur programma-licenties. We willen onze ID-ondersteuning actief uitbreiden tot andere identiteits documenten over de hele wereld.

## <a name="post-analyze-id-document"></a>Document voor het analyseren van de id

De [analyse-ID-](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) bewerking neemt een afbeelding of PDF van een id als invoer en extraheert de gewenste waarden. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Resultaat van analyse van document-id ophalen

<!---
Need to update this with updated APIM links when available
-->

De tweede stap is het aanroepen van de bewerking [**analyse van idDocument-resultaten ophalen**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Deze bewerking neemt als invoer de resultaat-ID op die is gemaakt door de bewerking ID analyseren. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de analyse bewerking is niet gestart. |
| |  | uitvoeren: de analyse bewerking wordt uitgevoerd. |
| |  | mislukt: de analyse bewerking is mislukt. |
| |  | geslaagd: de analyse bewerking is voltooid. |

Wanneer het veld **status** de waarde **geslaagd** heeft, bevat het JSON-antwoord de resultaten van de ontvangst en tekst herkenning. Het resultaat van de Id's is ingedeeld als een woorden lijst met benoemde veld waarden, waarbij elke waarde de geëxtraheerde tekst, genormaliseerde waarde, het begrenzingsvak, betrouw baarheid en bijbehorende woord elementen bevat. Het resultaat van de tekst herkenning is ingedeeld als een hiërarchie van lijnen en woorden, met tekst, selectie kader en betrouwbaarheids informatie.

![voor beeld van ontvangst resultaten](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Zie het volgende voor beeld van een geslaagde JSON-reactie: het `readResults` knoop punt bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het `documentResults` knoop punt bevat de ID-waarden die het model heeft gedetecteerd. Dit knoop punt is ook waar u nuttige sleutel/waarde-paren vindt, zoals de voor naam, achternaam, het document nummer en nog veel meer.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Volgende stappen

- Probeer uw eigen Id's en voor beelden in de voor [beeld-UI voor formulier herkenning](https://fott-preview.azurewebsites.net/).
- Voltooi de [Snelstartgids van een formulier herkenning](quickstarts/client-library.md) om aan de slag te gaan met het schrijven van een id-verwerkings-app met een formulier herkenner in de ontwikkelings taal van uw keuze.

## <a name="see-also"></a>Zie ook

* [**Wat is Form Recognizer?**](./overview.md)
* [**REST API referentie documenten**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
