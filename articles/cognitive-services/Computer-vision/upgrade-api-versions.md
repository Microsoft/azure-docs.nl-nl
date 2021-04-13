---
title: Upgraden naar Read v3.0 van de Computer Vision-API
titleSuffix: Azure Cognitive Services
description: Lees hoe u een upgrade uitvoert naar Computer Vision v3.0 Read API vanuit v2.0/v2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364087"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Upgraden van Read v2.x naar Read v3.x

Deze handleiding laat zien hoe u uw bestaande container- of cloud-API-code kunt upgraden van Read v2.x naar Read v3.x.

## <a name="determine-your-api-path"></a>Het API-pad bepalen
Gebruik de volgende tabel om de **tekenreeks van de versie** in het API-pad te bepalen op basis van de Read 3.x-versie waarnaar u migreert.

|Producttype| Versie | Tekenreeks van versie in API-pad van 3.x |
|:-----|:----|:----|
|Service | Read 3.0 of 3.1 | **v3.0** of **v3.1**, respectievelijk |
|Service | Read 3.2 preview | **v3.2-preview.1** |
|Container | Read 3.0 preview of Read 3.1 preview | **v3.0** of **v3.1-preview.2**, respectievelijk |


Gebruik vervolgens de volgende secties om uw bewerkingen te verfijnen en de **tekenreeks van de versie** in het API-pad door de waarde uit de tabel te vervangen. Bijvoorbeeld: werk voor cloud- en containerversies van **Read v3.2-preview** het API-pad bij naar **https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]** .

## <a name="servicecontainer"></a>Service/container

### `Batch Read File`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
Er is een nieuwe optionele _taal_-parameter beschikbaar. Als u de taal van uw document niet kent, of als het document mogelijk meertalig is, neem het dan niet op. 

### `Get Read Results`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result`-statusvlag

Wanneer de aanroep naar `Get Read Operation Result` is geslaagd, wordt er een statustekenreeksveld geretourneerd in de JSON-hoofdtekst.
 
|Read 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API-respons (JSON) 

Let op de volgende wijzigingen in de JSON:
* In v2.x retourneert `Get Read Operation Result` de OCR-JSON wanneer de status `Succeeded"` is. In v3.0 is dit veld `succeeded`.
* Om de hoofdmap voor de paginamatrix te verkrijgen, wijzigt u de JSON-hiërarchie van `recognitionResults` in `analyzeResult`/`readResults`. De JSON-hiërarchie voor regels en woorden per pagina blijft ongewijzigd, dus er zijn geen codewijzigingen nodig.
* De naam van de paginahoek `clockwiseOrientation` is gewijzigd in `angle` en het bereik is gewijzigd van 0 tot 360 graden in -180 tot 180 graden. Afhankelijk van uw code moet u al dan niet wijzigingen aanbrengen, aangezien de meeste wiskundige functies beide bereiken kunnen verwerken.

De v3.0-API introduceert ook de volgende verbeteringen die u optioneel kunt benutten:
* `createdDateTime` en `lastUpdatedDateTime` worden toegevoegd, zodat u de duur van de verwerking kunt volgen. Raadpleeg de documentatie voor meer informatie. 
* `version` toont de versie van de API die wordt gebruikt om resultaten te genereren
* Er is een `confidence` per woord toegevoegd. Deze waarde wordt gekalibreerd zodat de waarde 0,95 betekent dat de kans 95% is dat de herkenning correct is. De betrouwbaarheidsscore kan worden gebruikt om te selecteren welke tekst er moet worden verzonden voor menselijke beoordeling. 
    
    
In 2.X is de uitvoerindeling als volgt: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
In v3.0 is het aangepast:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Alleen service

### `Recognize Text`
`Recognize Text` is een *preview*-bewerking die wordt *afgeschaft in alle versies van de Computer Vision-API*. U moet migreren van `Recognize Text` naar `Read` (v3.0) of `Batch Read File` (v2.0, v2.1). v3.0 van `Read` bevat nieuwere, betere modellen voor tekstherkenning en aanvullende functies, en wordt dus aanbevolen. Upgraden van `Recognize Text` naar `Read`:

|Tekst herkennen 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
De _modus_-parameter wordt niet ondersteund in `Read`. Zowel handgeschreven als gedrukte tekst worden automatisch ondersteund.
    
Er is een nieuwe optionele _taal_-parameter beschikbaar in v3.0. Als u de taal van uw document niet kent, of als het document mogelijk meertalig is, neem het dan niet op. 

### `Get Recognize Text Operation Result`

|Tekst herkennen 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result`-statusvlaggen
Wanneer de aanroep naar `Get Recognize Text Operation Result` is geslaagd, wordt er een statustekenreeksveld geretourneerd in de JSON-hoofdtekst. 
 
|Tekst herkennen 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API-respons (JSON)

Let op de volgende wijzigingen in de JSON:    
* In v2.x retourneert `Get Read Operation Result` de OCR-JSON wanneer de status `Succeeded` is. In v3.x is dit veld `succeeded`.
* Om de hoofdmap voor de paginamatrix te verkrijgen, wijzigt u de JSON-hiërarchie van `recognitionResult` in `analyzeResult`/`readResults`. De JSON-hiërarchie voor regels en woorden per pagina blijft ongewijzigd, dus er zijn geen codewijzigingen nodig.

De v3.0-API introduceert ook de volgende verbeteringen die u optioneel kunt benutten. Zie de API-verwijzing voor meer informatie:
* `createdDateTime` en `lastUpdatedDateTime` worden toegevoegd, zodat u de duur van de verwerking kunt volgen. Raadpleeg de documentatie voor meer informatie. 
* `version` toont de versie van de API die wordt gebruikt om resultaten te genereren
* Er is een `confidence` per woord toegevoegd. Deze waarde wordt gekalibreerd zodat de waarde 0,95 betekent dat de kans 95% is dat de herkenning correct is. De betrouwbaarheidsscore kan worden gebruikt om te selecteren welke tekst er moet worden verzonden voor menselijke beoordeling. 
* `angle`: de algemene richting van de tekst met de klok mee, gemeten in graden tussen -180 en 180.
* `width` en `"height"` geven u de afmetingen van uw document, en `"unit"` geeft de eenheid van die afmetingen (pixel of inch, afhankelijk van het documenttype).
* `page`: documenten met meerdere pagina’s worden ondersteund.
* `language`: de invoertaal van het document (uit de optionele _taal_-parameter).


In 2.X is de uitvoerindeling als volgt: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
In v3.x is het aangepast:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Alleen container

### `Synchronous Read`

|Read 2.0 |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**version string**>/read/syncAnalyze[?language]|
