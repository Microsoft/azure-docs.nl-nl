---
title: Voor beeld van aangepaste vaardigheid (python)
titleSuffix: Azure Cognitive Search
description: Voor python-ontwikkel aars leert u de hulpprogram ma's en technieken voor het bouwen van een aangepaste vaardigheid met behulp van Azure Functions en Visual Studio. Aangepaste vaardig heden bevatten door de gebruiker gedefinieerde modellen of logica die u kunt toevoegen aan een AI-verrijkte index pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936126"
---
# <a name="example-create-a-custom-skill-using-python"></a>Voor beeld: een aangepaste vaardigheid maken met behulp van python

In dit voor beeld van Azure Cognitive Search vaardig heden leert u hoe u een aangepaste web-API maakt met behulp van python en Visual Studio code. In het voor beeld wordt een [Azure-functie](https://azure.microsoft.com/services/functions/) gebruikt waarmee de [aangepaste vaardigheids interface](cognitive-search-custom-skill-interface.md)wordt geïmplementeerd.

De aangepaste vaardigheid is eenvoudig in het ontwerp (het voegt twee teken reeksen samen) zodat u zich kunt concentreren op de hulpprogram ma's en technologieën die worden gebruikt voor het ontwikkelen van aangepaste vaardig heden in python. Als u een eenvoudige vaardigheid hebt voltooid, kunt u deze uitbreiden met complexere scenario's.

## <a name="prerequisites"></a>Vereisten

+ Bekijk de [aangepaste vaardigheids interface](cognitive-search-custom-skill-interface.md) voor een inleiding in de invoer-en uitvoer interface waarvoor een aangepaste vaardigheid moet worden geïmplementeerd.

+ Stel uw omgeving in. We hebben [de end-to-end-zelf studie](/azure/python/tutorial-vs-code-serverless-python-01) gevolgd om een Serverloze Azure-functie in te stellen met behulp van Visual Studio code en python-extensies. De zelf studie leidt u door de installatie van de volgende hulpprogram ma's en onderdelen: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Azure Functions-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In dit voor beeld wordt een Azure-functie gebruikt om het concept van het hosten van een web-API te demonstreren, maar andere benaderingen zijn mogelijk. Als u voldoet aan de [Interface vereisten voor een cognitieve vaardigheid](cognitive-search-custom-skill-interface.md), is de aanpak die u uitvoert, niet van belang. Met Azure Functions kunt u echter eenvoudig een aangepaste vaardigheid maken.

### <a name="create-a-function-app"></a>Een functie-app maken

De Azure Functions-projectsjabloon in Visual Studio Code maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources kunt beheren, implementeren en delen.

1. Druk in Visual Studio Code op F1 om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create new project...`.

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Daarom moet u geen projectmap selecteren die deel uitmaakt van een werk ruimte.

1. Selecteer een taal voor uw functie-app-project. Voor deze zelf studie selecteert u **python**.
1. Selecteer de python-versie, (versie 3.7.5 wordt ondersteund door Azure Functions)
1. Selecteer een sjabloon voor de eerste functie van uw project. Selecteer **http-trigger** om een door http geactiveerde functie in de nieuwe functie-app te maken.
1. Geef een functie naam op. In dit geval gebruiken we **samen voegen** 
1. Selecteer **functie** als autorisatie niveau. Dit betekent dat we een [functie sleutel](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) zullen opgeven om het http-eind punt van de functie aan te roepen. 
1. Selecteer hoe u uw project wilt openen. Voor deze stap selecteert u **toevoegen aan werk ruimte** om de functie-app in de huidige werk ruimte te maken.

Het functie-appproject wordt in een nieuwe werkruimte gemaakt. Het project bevat de configuratiebestanden [host.json](../azure-functions/functions-host-json.md) en [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), plus eventuele taalspecifieke projectbestanden. 

Er wordt ook een nieuwe HTTP-geactiveerde functie gemaakt in de map **samen voegen** van het functie-app-project. Hierin ziet u een bestand met de naam ' \_ \_ init__. py ' met deze inhoud:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Nu gaan we die code wijzigen om de [aangepaste vaardigheden interface](cognitive-search-custom-skill-interface.md)te volgen. Wijzig de code met de volgende inhoud:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Met de methode **transform_value** wordt een bewerking uitgevoerd op één record. U kunt de methode wijzigen zodat deze voldoet aan uw specifieke behoeften. Vergeet niet om de benodigde invoer validatie uit te voeren en eventuele fouten en waarschuwingen te retour neren die zijn gegenereerd als de bewerking niet kan worden voltooid voor de record.

### <a name="debug-your-code-locally"></a>Fout opsporing voor uw code lokaal

Met Visual Studio code kunt u eenvoudig fouten opsporen in de code. Druk op F5 of ga naar het menu **fout opsporing** en selecteer **fout opsporing starten**.

U kunt onderbrekings punten voor de code instellen door op F9 te drukken op de gewenste regel.

Wanneer u de fout opsporing hebt gestart, wordt de functie lokaal uitgevoerd. U kunt een hulp programma zoals postman of Fiddler gebruiken om de aanvraag te verzenden naar localhost. Noteer de locatie van het lokale eind punt in het Terminal venster. 

## <a name="publish-your-function"></a>Uw functie publiceren

Wanneer u tevreden bent met het functie gedrag, kunt u het publiceren.

1. Druk in Visual Studio Code op F1 om het opdrachtenpalet te openen. In het opdracht palet zoekt en selecteert **u implementeren naar functie-app...**. 

1. Selecteer het Azure-abonnement waar u uw toepassing wilt implementeren.

1. Selecteer **+ nieuwe functie-app maken in azure**

1. Voer een wereld wijd unieke naam in voor de functie-app.

1. Selecteer python-versie (python 3.7. x werkt voor deze functie).

1. Selecteer een locatie voor de nieuwe resource (bijvoorbeeld VS-West 2).

Op dit moment worden de benodigde resources in uw Azure-abonnement gemaakt om de nieuwe Azure-functie in azure te hosten. Wacht totdat de installatie is voltooid. In het uitvoer venster ziet u de status van het implementatie proces.

1. Ga in het [Azure Portal](https://portal.azure.com)naar **alle resources** en zoek naar de functie die u hebt gepubliceerd met behulp van de naam. Als u IT- **samen voeging** heet, selecteert u de resource.

1. Klik op de knop **</> functie-URL ophalen** . Zo kunt u de URL kopiëren om de functie aan te roepen.

## <a name="test-the-function-in-azure"></a>De functie in azure testen

Nu u de standaardhostcode hebt, moet u de functie als volgt testen:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Aanvraagbody
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

In dit voor beeld moet u hetzelfde resultaat opleveren dat u eerder hebt gezien bij het uitvoeren van de functie in de lokale omgeving.

## <a name="connect-to-your-pipeline"></a>Verbinding maken met uw pijp lijn

Nu u een nieuwe aangepaste vaardigheid hebt, kunt u deze toevoegen aan uw vaardig heden. In het onderstaande voor beeld ziet u hoe u de vaardigheid aanroept om de titel en de auteur van het document samen te voegen in één veld dat wordt aangeroepen merged_title_author. Vervang door `[your-function-url-here]` de URL van uw nieuwe Azure-functie.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd! U hebt uw eerste aangepaste vaardigheid gemaakt. U kunt nu hetzelfde patroon volgen om uw eigen aangepaste functionaliteit toe te voegen. Klik op de volgende koppelingen voor meer informatie.

+ [Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Een aangepaste vaardigheid toevoegen aan een AI-verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)