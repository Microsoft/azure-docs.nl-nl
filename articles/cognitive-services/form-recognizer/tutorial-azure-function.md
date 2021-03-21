---
title: 'Zelf studie: een Azure-functie gebruiken om opgeslagen documenten te verwerken'
titleSuffix: Azure Cognitive Services
description: In deze hand leiding wordt beschreven hoe u een Azure-functie gebruikt om de verwerking te activeren van documenten die zijn geüpload naar een Azure Blob Storage-container.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: 8c72a018f03b5284d3fc53be02d9eb526cdfcf28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722239"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Zelf studie: een Azure-functie gebruiken om opgeslagen documenten te verwerken

U kunt de formulier Recognizer gebruiken als onderdeel van een pijp lijn voor geautomatiseerde gegevens verwerking die is gebouwd met Azure Functions. In deze hand leiding wordt beschreven hoe u een Azure-functie gebruikt voor het verwerken van documenten die zijn geüpload naar een Azure Blob Storage-container. Met deze werk stroom worden tabel gegevens uit opgeslagen documenten geëxtraheerd met de indelings service voor formulier herkenning en worden de tabel gegevens opgeslagen in een CSV-bestand in Azure. U kunt de gegevens vervolgens weer geven met behulp van micro soft Power BI (hier niet besproken).

> [!div class="mx-imgBorder"]
> ![werk stroom diagram van Azure service](./media/tutorial-azure-function/workflow-diagram.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Storage-account maken
> * Een Azure Functions-project maken
> * Indelings gegevens uit de geüploade formulieren extra heren
> * Indelings gegevens uploaden naar Azure Storage

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een resource voor een formulier herkenning maken "  target="_blank"> een resource voor een formulier herkenning maken <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om de sleutel en het eind punt van uw formulier herkenning op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
  * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Form Recognizer-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
  * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een lokaal PDF-document dat moet worden geanalyseerd. U kunt dit [voorbeeld document](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) downloaden om te gebruiken.
* [Python 3.8. x](https://www.python.org/downloads/) is geïnstalleerd.
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) geïnstalleerd.
* [Azure functions core tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools) geïnstalleerd.
* Visual Studio code met de volgende uitbrei dingen geïnstalleerd:
  * [Azure Functions extensie](https://docs.microsoft.com/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python-extensie](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

[Maak een Azure Storage-account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) op de Azure Portal. Selecteer **StorageV2** als het soort account.

Selecteer in het linkerdeel venster het tabblad **cors** en verwijder het bestaande cors-beleid als dit bestaat.

Nadat u hebt geïmplementeerd, maakt u twee lege containers voor Blob-opslag, met de naam **test** en **uitvoer**.

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

Open Visual Studio Code. Als u de extensie Azure Functions hebt geïnstalleerd, ziet u een Azure-logo in het navigatie deel venster aan de linkerkant. Selecteer deze. Een nieuw project maken en wanneer u wordt gevraagd een lokale map te maken **coa_new** het project bevatten.

![De knop functie VSCode maken](./media/tutorial-azure-function/vs-code-create-function.png)


U wordt gevraagd een aantal instellingen te configureren:
* Selecteer python in de prompt **een taal selecteren** .
* Selecteer in de prompt **een sjabloon selecteren** de optie Azure Blob Storage trigger. Geef vervolgens een naam op voor de standaard trigger.
* Kies in het dialoog venster **instelling selecteren de optie** nieuwe lokale app-instellingen maken.
* Selecteer uw Azure-abonnement met het opslag account dat u hebt gemaakt. Vervolgens moet u de naam van de opslag container invoeren (in dit geval `test/{name}` )
* U kunt ervoor kiezen om het project te openen in het huidige venster. 

![Voor beeld van VSCode Create-prompt](./media/tutorial-azure-function/vs-code-prompt.png)

Wanneer u deze stappen hebt voltooid, voegt VSCode een nieuw Azure-functie project toe met het python-script *\_ \_ init \_ \_ . py* . Dit script wordt geactiveerd wanneer een bestand wordt geüpload naar de container van de **test** opslag, maar dit is niet alles.

## <a name="test-the-function"></a>De functie testen

Druk op F5 om de functie Basic uit te voeren. U wordt gevraagd een opslag account te selecteren voor de interface met. Selecteer het opslag account dat u hebt gemaakt en ga door.

Open Azure Storage Explorer en upload een PDF-voorbeeld document naar de **test** container. Controleer vervolgens de VSCode-Terminal. Het script zou moeten registreren dat het is geactiveerd door de PDF-upload.

![VSCode-terminal test](./media/tutorial-azure-function/vs-code-terminal-test.png)


Stop het script voordat u doorgaat.

## <a name="add-form-processing-code"></a>Formulier verwerkings code toevoegen

Vervolgens voegt u uw eigen code toe aan het python-script om de Form Recognizer-service aan te roepen en de geüploade documenten te parseren met de [indelings-API](concept-layout.md)voor formulier herkenning.

Navigeer in VSCode naar het *requirements.txt* -bestand van de functie. Hiermee definieert u de afhankelijkheden voor uw script. Voeg de volgende python-pakketten toe aan het bestand:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Open vervolgens het script *\_ \_ init \_ \_ . py* . Voeg de volgende `import` instructies toe:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

U kunt de gegenereerde functie ongewijzigd laten `main` . U voegt uw aangepaste code in deze functie toe.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

In het volgende code blok wordt de [indelings](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) -API voor de formulier herkenning geanalyseerd op het geüploade document. Vul uw eind punt en de sleutel waarden in. 


# <a name="version-20"></a>[versie 2.0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[versie 2.1 (preview)](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Form Recognizer-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel [Cognitive Services Security](../cognitive-services-security.md) (Engelstalig) voor meer informatie.

Voeg vervolgens code toe om de service te doorzoeken en de geretourneerde gegevens op te halen. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Voeg vervolgens de volgende code toe om verbinding te maken met de Azure Storage **uitvoer** container. Vul uw eigen waarden in voor de naam en sleutel van het opslag account. U kunt de sleutel ophalen op het tabblad **toegangs sleutels** van uw opslag resource in de Azure Portal.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

De volgende code parseert het geretourneerde formulier Recognizer-antwoord, bouwt een CSV-bestand en uploadt het naar de **uitvoer** container. 


> [!IMPORTANT]
> Waarschijnlijk moet u deze code bewerken zodat deze overeenkomt met de structuur van uw eigen formulier documenten.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Ten slotte uploadt het laatste code blok de geëxtraheerde tabel-en tekst gegevens naar uw Blob Storage-element.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>De functie uitvoeren

Druk op F5 om de functie opnieuw uit te voeren. Gebruik Azure Storage Explorer om een PDF-voorbeeld formulier naar de **test** opslag container te uploaden. Met deze actie wordt het script geactiveerd dat moet worden uitgevoerd en wordt het resulterende CSV-bestand (weer gegeven als een tabel) in de **uitvoer** container weer geven.

U kunt deze container verbinden met Power BI om uitgebreide visualisaties te maken van de gegevens die het bevat.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een Azure-functie die is geschreven in python kunt gebruiken om automatisch geüploade PDF-documenten te verwerken en de inhoud ervan uit te voeren in een meer gegevens vriendelijke indeling. Vervolgens leert u hoe u Power BI kunt gebruiken om de gegevens weer te geven.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Wat is Form Recognizer?](overview.md)
* Meer informatie over de [indelings-API](concept-layout.md)
