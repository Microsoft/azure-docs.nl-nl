---
title: ID's - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot gegevensextractie uit identiteitsdocumenten met de Form Recognizer-API voor vooraf gebouwde id's.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514704"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>Form Recognizer documentmodel voor vooraf gebouwde identificatie (id)

Azure Form Recognizer kan gegevens analyseren en extraheren uit door de overheid uitgegeven identificatiedocumenten (id's) met behulp van het vooraf gebouwde id-model. Het combineert onze krachtige [OCR-mogelijkheden (Optical Character Recognition)](../computer-vision/overview-ocr.md) met mogelijkheden voor id-herkenning om belangrijke informatie te extraheren uit Worldwide Passports en Amerikaanse stuurprogrammalicenties (alle 50 staten en D.C.). De ID's-API extraheert belangrijke informatie uit deze identiteitsdocumenten, zoals voornaam, achternaam, geboortedatum, documentnummer en meer. Deze API is beschikbaar in de preview Form Recognizer versie 2.1 als een cloudservice en als een on-premises container.

## <a name="what-does-the-id-service-do"></a>Wat doet de id-service?

De vooraf gebouwde service voor ID's extraheert de sleutelwaarden uit de wereldwijde paspoorten en Amerikaanse stuurprogrammalicenties en retourneert deze in een geordend gestructureerd JSON-antwoord.

### <a name="drivers-license-example"></a>**Voorbeeld van een licentie voor een stuurprogramma**

![Voorbeeld van een stuurprogrammalicentie](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Passport-voorbeeld**

![Voorbeeld van Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Geëxtraheerde velden

|Naam| Type | Description | Waarde |
|:-----|:----|:----|:----|
|  Land/regio | country | Landcode die voldoet aan de ISO 3166-standaard | 'USA' |
|  DateOfBirth | date | DOB in YYYY-MM-DD-indeling | "1980-01-01" |
|  DateOfExpiration | date | Vervaldatum in de indeling YYYY-MM-DD | "2019-05-05" |
|  DocumentNumber | tekenreeks | Relevant paspoortnummer, licentienummer van de stuurprogramma, enzovoort. | "340020013" |
|  FirstName | tekenreeks | Voornaam en middelste initiële geëxtraheerd, indien van toepassing | "SHOW" |
|  LastName | tekenreeks | Geëxtraheerde achternaam | "ENS" |
|  Nationaliteit | country | Landcode die voldoet aan de ISO 3166-standaard | "USA" |
|  Sex | geslacht | Mogelijke geëxtraheerde waarden zijn 'M', 'F' en 'X' | "F" |
|  MachineReadableZone | object | Uitgepakte Passport MRZ met twee regels van elk 44 tekens | "P<USAOUSS<<<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F190505471000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | tekenreeks | Documenttype, bijvoorbeeld Passport, Driver's License | "passport" |
|  Adres | tekenreeks | Geëxtraheerd adres (alleen stuurprogrammalicentie) | '123 STREET ADDRESS YOUR CITY WA 99999-1234'|
|  Region | tekenreeks | Geëxtraheerde regio, staat, provincie, enzovoort (alleen rijlicentie) | 'Washington' |

### <a name="additional-features"></a>Aanvullende functies

De API voor de IDs retourneert ook de volgende informatie:

* Betrouwbaarheidsniveau van veld (elk veld retourneert een bijbehorende betrouwbaarheidswaarde)
* Onbewerkte OCR-tekst (ocr-geëxtraheerde tekstuitvoer voor het hele ontvangstbewijs)
* Begrensingsvak van elk uitgepakt veld in Amerikaanse stuurprogrammalicenties
* Begreningsvak voor MACHINE Readable Zone (MRZ) in Passports

  > [!NOTE]
  > Vooraf gebouwde id's detecteren de echtheid van id's niet
  >
  > Form Recognizer vooraf gebouwde id's extraheert sleutelgegevens uit id-gegevens. De geldigheid of echtheid van het oorspronkelijke identiteitsdocument wordt echter niet gedetecteerd.

## <a name="try-it-out"></a>Probeer het eens

Als u de service Form Recognizer-ID's wilt uitproberen, gaat u naar het onlinehulpprogramma voor voorbeeld-UI:

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Ondersteunde id-typen

* **Vooraf gebouwde ID's v2.1-preview.3** Extraheert sleutelwaarden uit wereldwijde passports en Amerikaanse stuurprogrammalicenties.

  > [!NOTE]
  > Ondersteuning voor id-type
  >
  > Momenteel ondersteunde id-typen zijn onder andere wereldwijde passport- en Amerikaanse stuurprogrammalicenties. We willen onze id-ondersteuning actief uitbreiden naar andere identiteitsdocumenten over de hele wereld.

## <a name="post-analyze-id-document"></a>POST Analyze Id Document

Bij [de bewerking Analyze ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) wordt een afbeelding of PDF van een id als invoer gebruikt en worden de bare waarden geëxtraheert. De aanroep retourneert een antwoordheaderveld met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Antwoordheader| Resultaat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Resultaat van GET Analyze Id-document

<!---
Need to update this with updated APIM links when available
-->

De tweede stap is het aanroepen van [**de bewerking Get Analyze idDocument Result.**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) Deze bewerking neemt als invoer de resultaat-id die is gemaakt door de analyse-id-bewerking. Het retourneert een JSON-antwoord dat een **statusveld** met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan totdat deze wordt retourneert met **de geslaagd** waarde. Gebruik een interval van 3 tot 5 seconden om te voorkomen dat de RPS-snelheid (aanvragen per seconde) wordt overschrijdt.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: De analysebewerking is niet gestart. |
| |  | running: De analysebewerking wordt uitgevoerd. |
| |  | mislukt: de analysebewerking is mislukt. |
| |  | geslaagd: de analysebewerking is geslaagd. |

Wanneer het **statusveld** de geslaagd **waarde heeft,** bevat het JSON-antwoord de resultaten van ontvangstbegrip en tekstherkenning. Het resultaat van de -ID's is ingedeeld als een woordenlijst met benoemde veldwaarden, waarbij elke waarde de geëxtraheerde tekst, de genormaliseerde waarde, het begrendigingsvak, de betrouwbaarheid en de bijbehorende woordelementen bevat. Het resultaat van tekstherkenning is ingedeeld als een hiërarchie van regels en woorden, met tekst, begrenzesvak en betrouwbaarheidsinformatie.

![voorbeeld van resultaten van ontvangstbewijs](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Voorbeeld van JSON-uitvoer

Zie het volgende voorbeeld van een geslaagd JSON-antwoord: Het knooppunt bevat alle `readResults` herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het `documentResults` knooppunt bevat de id-waarden die door het model zijn ontdekt. In dit knooppunt vindt u ook nuttige sleutel-waardeparen, zoals de voornaam, achternaam, documentnummer en meer.

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

* Probeer uw eigen ID's en voorbeelden in de [Form Recognizer voorbeeld-UI](https://fott-preview.azurewebsites.net/).
* Voltooi een [Form Recognizer om aan](quickstarts/client-library.md) de slag te gaan met het schrijven van een app voor Form Recognizer in de ontwikkelingstaal van uw keuze.

## <a name="see-also"></a>Zie ook

* [**Wat is Form Recognizer?**](./overview.md)
* [**REST API naslagmateriaal**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
