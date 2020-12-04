---
title: 'Quickstart: Trainen met labels met behulp van de REST API en Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de functie voor gelabelde gegevens van Form Recognizer met de REST API en Python voor het trainen van een aangepast model.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: f944a793d721e93d818723eae25a9ce80d9c15bc
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005079"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Een Form Recognizer-model met labels trainen met behulp van REST API en Python

In deze quickstart gebruikt u de REST API van Form Recognizer met Python voor het trainen van een aangepast model met handmatig gelabelde gegevens. Zie de sectie [Trainen met labels](../overview.md#train-with-labels) van het overzicht voor meer informatie over deze functie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:
- [Python](https://www.python.org/downloads/) moet zijn geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een set van minimaal zes formulieren van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U kunt voor deze quickstart een [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken. Download *sample_data.zip* en pak het uit. Upload de trainingsbestanden naar de hoofdmap van een Blob Storage-container in een Azure Storage-account met een standaardprestatielaag.

> [!NOTE]
> In deze quickstart worden externe documenten gebruikt die worden geopend via URL. Raadpleeg de [referentiedocumentatie voor v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) en de [referentiedocumentatie voor v2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/TrainCustomModelAsync) als u lokale bestanden wilt gebruiken.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Trainingsgegevens instellen

Vervolgens moet u de vereiste invoergegevens instellen. De functie voor gelabelde gegevens heeft naast hetgeen u nodig hebt om een aangepast model zonder labels te trainen nog speciale invoervereisten.

Zorg ervoor dat alle trainingsdocumenten dezelfde indeling hebben. Als u formulieren in meerdere indelingen hebt, kunt u deze op basis van hun gemeenschappelijke indeling in submappen ordenen. Wanneer u traint, moet u de API instellen op een submap.

Als u een model wilt trainen met behulp van gelabelde gegevens, hebt u de volgende bestanden nodig als invoer in de submap. Hieronder vindt u informatie over het maken van deze bestanden.

* **Bronformulieren**: de formulieren waaruit gegevens moeten worden opgehaald. Ondersteunde typen zijn JPEG, PNG, PDF of TIFF.
* **OCR-indelingsbestanden**: dit zijn JSON-bestanden die de grootte en positie van alle leesbare tekst in elk bronformulier beschrijven. U gebruikt de indelings-API voor Form Recognizer om deze gegevens te genereren. 
* **Labelbestanden**: dit zijn JSON-bestanden die de gegevenslabels beschrijven die een gebruiker handmatig heeft ingevoerd.

Al deze bestanden moeten in dezelfde submap staan en de volgende indeling hebben:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Wanneer u formulieren labelt met behulp van het [voorbeeldhulpprogramma voor labelen](./label-tool.md) van Form Recognizer, maakt het hulpprogramma deze label- en OCR-lay-outbestanden automatisch.

### <a name="create-the-ocr-output-files"></a>De OCR-uitvoerbestanden maken

U hebt OCR-resultaatbestanden nodig opdat de service de bijbehorende invoerbestanden voor gelabelde training kan bekijken. Volg de onderstaande stappen om OCR-resultaten voor een bepaald bronformulier te verkrijgen:

1. Roep de **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** -API aan voor de container Layout container met het invoerbestand als onderdeel van de aanvraagtekst. Sla de id op die is gevonden in de header **Operation-Location** van het antwoord.
1. Roep de **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** -API aan met behulp van de bewerkings-id uit de vorige stap.
1. Haal het antwoord op en schrijf de inhoud naar een bestand. Voor elk bronformulier moet aan het bijbehorende OCR-bestand de oorspronkelijke bestandsnaam zijn toegevoegd met `.ocr.json`. De OCR JSON-uitvoer moet de volgende indeling hebben. Zie het [OCR-voorbeeldbestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) voor een volledig voorbeeld. 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>De labelbestanden maken

Labelbestanden bevatten sleutel-/waardekoppelingen die een gebruiker handmatig heeft ingevoerd. Ze zijn nodig voor het trainen van gelabelde gegevens, maar niet elk bronbestand moet een bijbehorend labelbestand hebben. Bronbestanden zonder labels worden behandeld als gewone trainingsdocumenten. Voor een betrouwbare training adviseren we het gebruik van vijf of meer gelabelde bestanden. U kunt een UI-hulpprogramma als het [hulpprogramma voor labelen](./label-tool.md) gebruiken om deze bestanden te genereren.

Wanneer u een labelbestand maakt, kunt u optioneel regio's&mdash;exacte posities van waarden in het document opgeven. Hiermee wordt de training nog nauwkeuriger. Regio's worden ingedeeld als een set van acht waarden, corresponderend met vier X,Y-coördinaten: linksboven, rechtsboven, rechtsonder en linksonder. Coördinaatwaarden liggen tussen nul en één, geschaald naar de afmetingen van de pagina.

Voor elk bronformulier moet aan het bijbehorende labelbestand de oorspronkelijke bestandsnaam zijn toegevoegd met `.labels.json`. Het labelbestand moet de volgende indeling hebben. Zie het [voorbeeldlabelbestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) voor een volledig voorbeeld.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> U kunt maar één label op elk tekstelement toepassen en elk label kan maar eenmaal per pagina worden toegepast. Het is niet mogelijk om een label toe te passen op meerdere pagina's.


## <a name="train-a-model-using-labeled-data"></a>Een model trainen met behulp van gelabelde gegevens

Als u een model met gelabelde gegevens wilt trainen, roept u de **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API aan door de volgende Python-code uit te voeren. Voordat u de code uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door de URL van Form Recognizer-de resource.
1. Vervang `<SAS URL>` door de URL van de SAS (Shared Access Signature) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
1. Vervang `<Blob folder name>` door de naam van de map in de blobcontainer waarin de invoergegevens zich bevinden. Als uw gegevens zich in de hoofdmap bevinden, laat u dit veld leeg en verwijdert u het veld `"prefix"` uit de hoofdtekst van de HTTP-aanvraag.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.2/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>Trainingsresultaten ophalen

Nadat u de trainbewerking hebt gestart, gebruikt u de geretourneerde id om de status van de bewerking te verkrijgen. Voeg onderaan uw Python-script de volgende code toe. Voor deze code wordt de id-waarde gebruikt van de trainingsoproep in een nieuwe API-oproep. De trainingsbewerking is asynchroon. Dat betekent dat dit script de API regelmatig aanroept totdat de training de status Voltooid heeft. We raden een interval van minimaal één seconde aan.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Wanneer het trainingsproces is voltooid, ontvangt u een `201 (Success)`-antwoord met JSON-inhoud zoals hieronder wordt weergegeven. Het antwoord is hier ingekort.

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

Kopieer de waarde `"modelId"` voor gebruik in de volgende stappen.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Wanneer het proces is voltooid, ontvangt u een `202 (Success)`-antwoord met JSON-inhoud in de volgende indeling. Het antwoord is hier ingekort. De hoofd sleutel-/waardekoppelingen bevinden zich in het knooppunt `"documentResults"`. Het knooppunt `"selectionMarks"` (in de preview van v2.1) toont elke selectiemarkering (selectievakje, keuzerondje) en of de status ervan 'ingeschakeld' of 'niet ingeschakeld' is. De resultaten van de Lay-out-API (de inhoud en posities van alle tekst in het document) bevinden zich in het knooppunt `"readResults"`.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
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
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v-2"></a>[v 2](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>Resultaten verbeteren

Controleer de `"confidence"`-waarden voor elk sleutel-/waarderesultaat onder het knooppunt `"documentResults"`. U moet ook de betrouwbaarheidsscores in het knooppunt `"readResults"` bekijken; deze komen overeen met de Lay-outbewerking. Het vertrouwen van de lay-outresultaten heeft geen invloed op het vertrouwen van de resultaten van de sleutel-/waarde-extractie, dus u moet beide controleren.
* Als de betrouwbaarheidsscores voor de Lay-outbewerking laag zijn, probeer dan om de kwaliteit van uw invoerdocumenten te verbeteren (zie [Invoervereisten](../overview.md#input-requirements)).
* Als de betrouwbaarheidsscores voor de sleutel-/waarde-extractiebewerking laag zijn, controleer dan of de documenten die worden geanalyseerd van hetzelfde type zijn als de documenten die in de trainingsset worden gebruikt. Als de documenten in de trainingsset verschillend zijn, zou u ze in verschillende mappen kunnen verdelen en voor elke variant één model kunnen trainen.

### <a name="avoid-cluttered-labels"></a>Onoverzichtelijke labels vermijden

Wanneer u verschillende labels op dezelfde tekstregel toepast, kan het zijn dat de service deze labels samenvoegt in één veld. U kunt bijvoorbeeld in een adres de plaats, provincie en postcode als verschillende velden labelen, terwijl deze velden tijdens de voorspelling niet afzonderlijk worden herkend.

We begrijpen dat dit scenario essentieel is voor onze klanten en we werken eraan om dit in de toekomst te verbeteren. Op dit moment raden we onze gebruikers aan om meerdere velden als één veld te labelen en deze vervolgens in een naverwerking van de extractieresultaten te scheiden.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de REST API van Form Recognizer met Python gebruikt voor het trainen van een model met handmatig gelabelde gegevens. Bekijk vervolgens de API-referentiedocumentatie om de Form Recognizer-API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
