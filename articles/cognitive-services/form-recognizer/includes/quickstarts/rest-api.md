---
title: 'Snelstartgids: REST API voor formulier herkenning'
description: Gebruik de REST API voor formulier herkenning voor het maken van een app voor het verwerken van formulieren waarmee sleutel-waardeparen en tabel gegevens uit uw aangepaste documenten worden geëxtraheerd.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: lajanuar
ms.openlocfilehash: 2cff960e2dfe6a85b7e16395a167b77f66690c56
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511013"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> In deze handleiding wordt gebruikgemaakt van cURL om REST API-aanroepen uit te voeren. Er is ook [voorbeeldcode op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) die laat zien hoe u de REST-API‘s aanroept met Python.

## <a name="prerequisites"></a>Vereisten

* [cURL](https://curl.haxx.se/windows/) moet zijn geïnstalleerd.
* [Power shell versie 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows), of een vergelijk bare opdracht regel toepassing.
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
  * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
  * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een URL voor een afbeelding van een ontvangstbewijs. U kunt voor deze quickstart een [voorbeeldafbeelding](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) gebruiken.
* Een URL voor een afbeelding van een visitekaartje. U kunt voor deze quickstart een [voorbeeldafbeelding](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) gebruiken.
* Een URL voor een afbeelding van een factuur. U kunt voor deze quickstart een [voorbeelddocument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) gebruiken.

## <a name="analyze-layout"></a>Indeling analyseren

U kunt de formulier Recognizer gebruiken om tabellen, selectie markeringen, tekst en structuur in documenten te analyseren en uit te pakken, zonder dat u een model hoeft te trainen. Zie de [conceptuele hand leiding voor lay-out](../../concept-layout.md)voor meer informatie over indelings extractie. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang door `\"{your-document-url}` een van de voor beeld-url's.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

U ontvangt een `202 (Success)` antwoord met am **Operation-Location**-koptekst. Deze waarde van deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen. In het volgende voorbeeld is de tekenreeks na `analyzeResults/` de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Indelingsresultaten ophalen

Nadat u de **[Analyze Layout API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** hebt aangeroepen, roept u de **[Get Analyze Layout Result API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `{resultId}` door de bewerkings-id uit de vorige stap.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>De resultaten bekijken

U ontvangt een `200 (success)`-antwoord met JSON-inhoud.

Bekijk de volgende factuurafbeelding en de bijbehorende JSON-uitvoer.
* Het knooppunt `"readResults"` bevat elke tekstregel met het bijbehorende begrenzingsvak op de pagina. 
* Het knooppunt `"selectionMarks"` (in preview 2.1) toont elke selectiemarkering (selectievakje, keuzerondje) en of de status ervan 'ingeschakeld' of 'niet ingeschakeld' is. 
* De `"pageResults"` sectie bevat de tabellen die zijn geëxtraheerd. Voor elke tabel worden de tekst-, rij-en kolom index, de rij-en kolom spanning, het begrenzingsvak en meer geëxtraheerd.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Document met een Contoso-projectinstructie met een tabel.":::

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

Deze uitvoer is inge kort voor eenvoud. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

Deze uitvoer is inge kort voor eenvoud. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

---

## <a name="analyze-invoices"></a>Facturen analyseren

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

Gebruik de krul opdracht hieronder om te beginnen met het analyseren van een factuur. Zie de [hand leiding voor factuur begrippen](../../concept-invoices.md)voor meer informatie over het analyseren van facturen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{your invoice URL}` door het URL-adres van een factuurdocument.
1. Vervang `{subscription key}` door uw abonnementssleutel.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

U ontvangt een `202 (Success)` antwoord met am **Operation-Location**-koptekst. Deze waarde van deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Factuurresultaten ophalen

Nadat u de **[Analyse factuur](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** -API hebt aangeroepen, roept u de API **[analyse van factuur resultaat ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `{resultId}` door de bewerkings-id uit de vorige stap.
1. Vervang `{subscription key}` door uw abonnementssleutel.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. 
* Het `"readResults"` veld bevat elke tekst regel die uit de factuur is opgehaald.
* De `"pageResults"` bevat de tabellen en selectie markeringen die zijn opgehaald uit de factuur.
* Het `"documentResults"` veld bevat sleutel/waarde-informatie voor de meest relevante onderdelen van de factuur.

Bekijk het volgende factuurdocument en de bijbehorende JSON-uitvoer. 

* [Voorbeeldfactuur](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Deze JSON-inhoud is inge kort voor de Lees baarheid. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de geselecteerde API-versie.

---

## <a name="train-a-custom-model"></a>Aangepast model trainen

Voor het trainen van een aangepast model hebt u een set trainingsgegevens in een Azure Storage-blob nodig. U hebt mini maal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) van hetzelfde type/dezelfde structuur nodig. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw trainingsgegevens.

Training zonder gelabelde gegevens is de standaard bewerking en is eenvoudiger. U kunt ook hand matig een aantal of al uw trainings gegevens vooraf labelen. Dit is een complexer proces, maar resulteert in een beter getraind model.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Als u een Form Recognizer-model wilt trainen met de documenten in uw Azure Blob-container, roept u de API **[Aangepast model trainen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** aan door de volgende cURL-opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `{SAS URL}` door de URL van de SAS (Shared Access Signature) van de Azure Blob Storage-container. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL ophalen":::

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

U ontvangt een `201 (Success)` antwoord met een **Locatie**-header. De waarde van deze header is de id van het nieuwe model dat wordt getraind.

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie (`\<filename\>.pdf.labels.json`) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de API voor het **[trainen van aangepaste modellen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** aanroepen, waarbij de `"useLabelFile"` para meter is ingesteld op `true` in de JSON-hoofd tekst.

Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `{SAS URL}` door de URL van de SAS (Shared Access Signature) van de Azure Blob Storage-container. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL ophalen":::

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

U ontvangt een `201 (Success)` antwoord met een **Locatie**-header. De waarde van deze header is de id van het nieuwe model dat wordt getraind.

### <a name="get-training-results"></a>Trainingsresultaten ophalen

Nadat u de trainbewerking hebt gestart, gebruikt u een nieuwe bewerking **[Aangepast model ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** om de trainingsstatus te controleren. Geef de model-id door aan deze API-aanroep om de trainingsstatus te controleren:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel.
1. Vervang `{subscription key}` door uw abonnementssleutel.
1. Vervang `{model ID}` door de model-id in de vorige stap.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

U ontvangt een `200 (Success)`-antwoord met een JSON-hoofdtekst in de volgende indeling. Let op het veld `"status"`. Dit heeft de waarde `"ready"` zodra de training is voltooid. Als de training van het model niet is voltooid, moet u opnieuw een query uitvoeren op de service door de opdracht opnieuw uit te voeren. Een interval van één seconde of meer tussen oproepen wordt aanbevolen.

Het veld `"modelId"` bevat de id van het model dat u traint. U hebt deze nodig voor de volgende stap.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

Vervolgens gebruikt u uw pas getrainde model voor het analyseren van een document en het extraheren van de sleutel-waardeparen en tabellen ervan. Roep de API **[Formulier analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** aan door de volgende cURL-opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen van uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `{model ID}` door de model-id die u in de vorige sectie hebt gekregen.
1. Vervang `{SAS URL}` door een SAS-URL naar uw bestand in Azure Storage. Volg de stappen in de sectie Training, maar in plaats van een SAS-URL voor de hele blobcontainer op te halen, haalt u er een op voor het specifieke bestand dat u wilt analyseren.
1. Vervang `{subscription key}` door uw abonnementssleutel.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

U ontvangt een `202 (Success)`-antwoord met een **Bewerking-Locatie**-header. De waarde van deze header bevat een resultaten-id die u gebruikt om de resultaten van de analysebewerking bij te houden. Sla deze resultaten-id op voor de volgende stap.

### <a name="get-the-analyze-results"></a>De resultaten van de analyse ophalen

Roep de resultaat-API voor het analyseren van het **[formulier](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeFormResult)** op om een query uit te voeren op de resultaten van de analyse bewerking.

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen van uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `{result ID}` door de id die u in de vorige sectie hebt gekregen.
1. Vervang `{subscription key}` door uw abonnementssleutel.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

U ontvangt een `200 (Success)`-antwoord met een JSON-hoofdtekst in de volgende indeling. De uitvoer is voor het gemak ingekort. U ziet het veld `"status"` onderaan. Dit heeft de waarde `"succeeded"` wanneer de analysebewerking is voltooid. Als de bewerking Analyse niet is voltooid, moet u opnieuw een query uitvoeren op de service door de opdracht opnieuw uit te voeren. Een interval van één seconde of meer tussen oproepen wordt aanbevolen.

In aangepaste modellen die zijn getraind zonder labels, bevinden de koppelingen en tabellen voor sleutel/waarde-paren zich in het `"pageResults"` knoop punt van de JSON-uitvoer. In aangepaste modellen die zijn getraind met labels, bevinden de koppelingen sleutel/waarde-paar zich in het `"documentResults"` knoop punt. Als u ook extractie voor tekst zonder opmaak hebt opgegeven via de URL-parameter *includeTextDetails*, worden in het knooppunt `"readResults"` de inhoud en posities van alle tekst in het document weergegeven.

Dit voorbeeld van een JSON-bestand is voor het gemak ingekort. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```  

---

### <a name="improve-results"></a>Resultaten verbeteren

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Ontvangstbewijzen analyseren

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten analyseert en extraheert met behulp van een vooraf getraind ontvangst model. Zie de [conceptuele hand leiding voor bevestigingen](../../concept-receipts.md)voor meer informatie over de ontvangst analyse. Als u wilt beginnen met het analyseren van een ontvangstbewijs, roept u de **[Ontvangstbewijs analyseren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** -API aan met de cURL-opdracht hieronder. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{your receipt URL}` door het URL-adres van een afbeelding van het ontvangstbewijs.
1. Vervang `{subscription key>` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

U ontvangt een `202 (Success)` antwoord met am **Operation-Location**-koptekst. Deze waarde van deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen. In het volgende voorbeeld is de tekenreeks na `operations/` de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>De resultaten van het ontvangstbewijs ophalen

Nadat u de **Analyze Receipt**-API hebt aangeroepen, roept u de **[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** -API aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `{operationId}` door de bewerkings-id uit de vorige stap.
1. Vervang `{subscription key}` door uw abonnementssleutel.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. Het eerste veld, `"status"`, geeft de status van de bewerking aan. Als de bewerking niet is voltooid, wordt de waarde van `"status"` `"running"` of `"notStarted"`. U moet de API opnieuw aanroepen, handmatig of via een script. We raden een interval van één seconde of meer aan tussen oproepen.

Het knooppunt `"readResults"` bevat alle herkende tekst (als u de optionele parameter *includeTextDetails* instelt op `true`). De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knooppunt `"documentResults"` bevat de ontvangstbewijswaarden die het model heeft gedetecteerd. Hier vindt u nuttige sleutel/waarde-paren zoals de belasting, het totaal, het bedrijfsadres, enzovoort.

Bekijk de volgende afbeelding van het ontvangstbewijs en de bijbehorende JSON-uitvoer.

![Een ontvangstbewijs van Contoso Store](../../media/contoso-allinone.jpg)

Deze uitvoer is inge kort voor de Lees baarheid. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

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

## <a name="analyze-business-cards"></a>Visitekaartjes analyseren

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)  

In deze sectie wordt beschreven hoe u algemene velden uit de Engelse visite kaartjes analyseert en extraheert met behulp van een vooraf getraind model. Zie de [conceptuele hand leiding voor visite](../../concept-business-cards.md)kaartjes voor meer informatie over het analyseren van bedrijfs kaarten. Als u wilt beginnen met het analyseren van een visitekaartje, roept u de **[Visitekaartje analyseren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** -API aan met het cURL-opdracht hieronder. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{your business card URL}` door het URL-adres van een afbeelding van het ontvangstbewijs.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your business card URL}'}"
```

U ontvangt een `202 (Success)` antwoord met am **Operation-Location**-koptekst. Deze waarde van deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Visitekaartjesresultaten ophalen

Nadat u de **Visitekaartje analyseren**-API hebt aangeroepen, roept u de **[Resultaat van Visitekaartje analyseren ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** -API aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `{resultId}` door de bewerkings-id uit de vorige stap.
1. Vervang `{subscription key}` door uw abonnementssleutel.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. 

Het knooppunt `"readResults"` bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knooppunt `"documentResults"` bevat de visitekaartjeswaarden die het model heeft gedetecteerd. Hier vindt u nuttige contactgegevens zoals bedrijfsnaam, voornaam, achternaam, telefoonnummer, enzovoort.

![Een visitekaartje van het Contoso-bedrijf](../../media/business-card-english.jpg)

Deze voor beeld-JSON-uitvoer is inge kort voor de Lees baarheid. Bekijk de [volledige voorbeeld uitvoer op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
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
                                "confidence": 0.993
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
                    }
                }
            }
        ]
    }
}
```

Met het script worden antwoorden naar de console afgedrukt totdat de **Visitekaartje analyseren**-bewerking is voltooid.

### <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de geselecteerde API-versie.

---

## <a name="manage-custom-models"></a>Aangepaste modellen beheren

### <a name="get-a-list-of-custom-models"></a>Een lijst met aangepaste modellen ophalen

Gebruik de **[lijst met aangepaste modellen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModels)** -API in de volgende opdracht om een lijst te retour neren van alle aangepaste modellen die deel uitmaken van uw abonnement.

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

U ontvangt een `200`-bericht dat aangeeft dat de bewerking is geslaagd. Dit bericht bevat JSON-gegevens en ziet er als volgt uit. Het element `"modelList"` bevat alle door u gemaakte modellen en de bijbehorende gegevens.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Een specifiek model ophalen

Als u gedetailleerde informatie over een specifiek aangepast model wilt ophalen, gebruikt u de API voor **[aangepast model ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModel)** in de volgende opdracht.

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `{modelId}` door de id van het aangepaste model dat u wilt opzoeken.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

U ontvangt een `200`-bericht dat aangeeft dat de bewerking is geslaagd. Dit bericht bevat JSON-gegevens en ziet er als volgt uit.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen. Met deze opdracht wordt de API voor het verwijderen van een **[aangepast model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/DeleteCustomModel)** aangeroepen om het model dat in de vorige sectie wordt gebruikt, te verwijderen.

1. Vervang `{Endpoint}` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `{subscription key}` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `{modelId}` door de id van het aangepaste model dat u wilt opzoeken.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

U ontvangt een `204` antwoord om aan te geven dat uw model is gemarkeerd voor verwijdering. Model artefacten worden binnen 48 uur verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Form Recognizer REST API gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Raadpleeg hierna de referentiedocumentatie om de Form Recognizer API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [Wat is Form Recognizer?](../../overview.md)
