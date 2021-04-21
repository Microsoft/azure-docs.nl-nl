---
title: Naslag voor Python-ontwikkelaars voor Azure Functions
description: Meer inzicht in het ontwikkelen van functies met Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0c87be334847974627299f8e21109fe201675f0c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762169"
---
# <a name="azure-functions-python-developer-guide"></a>Ontwikkelaarshandleiding voor Azure Functions Python

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions behulp van Python. In de onderstaande inhoud wordt ervan uitgenomen dat u de handleiding [voor ontwikkelaars Azure Functions hebt gelezen.](functions-reference.md)

Als Python-ontwikkelaar bent u mogelijk ook geïnteresseerd in een van de volgende artikelen:

| Aan de slag | Concepten| Scenario's/voorbeelden |
| -- | -- | -- | 
| <ul><li>[Python-functie met behulp Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-functie met terminal/opdrachtprompt](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Ontwikkelaarsgids](functions-reference.md)</li><li>[Hostingopties](functions-scale.md)</li><li>[&nbsp;Prestatieoverwegingen](functions-best-practices.md)</li></ul> | <ul><li>[Classificatie van afbeeldingen met PyTorch](machine-learning-pytorch.md)</li><li>[Voorbeeld van Azure Automation](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine Learning met TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Door Python-voorbeelden bladeren](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Hoewel u uw [Python-versie lokaal Azure Functions Windows](create-first-function-vs-code-python.md#run-the-function-locally)kunt ontwikkelen, wordt Python alleen ondersteund op basis van een Linux-hostingplan bij het uitvoeren in Azure. Zie de lijst met [ondersteunde combinaties van besturingssystemen en runtimes.](functions-scale.md#operating-systemruntime)

## <a name="programming-model"></a>Programmeermodel

Azure Functions verwacht dat een functie een staatloze methode in uw Python-script is die invoer verwerkt en uitvoer produceert. Standaard verwacht de runtime dat de methode wordt geïmplementeerd als een globale methode met de naam `main()` in het `__init__.py` bestand. U kunt ook [een alternatief toegangspunt opgeven.](#alternate-entry-point)

Gegevens van triggers en bindingen zijn via methodekenmerken gebonden aan de functie met behulp van de eigenschap die is gedefinieerd `name` in *function.jsbestand.* In de onderstaande  _function.jswordt_ bijvoorbeeld een eenvoudige functie beschreven die wordt geactiveerd door een HTTP-aanvraag met de naam `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Op basis van deze definitie ziet het bestand met de functiecode er `__init__.py` mogelijk uit zoals in het volgende voorbeeld:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

U kunt ook expliciet de kenmerktypen en het retourtype declareer in de functie met behulp van Python-typeaantekeningen. Dit helpt u bij het gebruik van de functies intellisense en automatisch aanvullen die worden geleverd door veel Python-code-editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Gebruik de Python-aantekeningen die zijn opgenomen in het [pakket azure.functions.*](/python/api/azure-functions/azure.functions) om invoer en uitvoer aan uw methoden te binden.

## <a name="alternate-entry-point"></a>Alternatief toegangspunt

U kunt het standaardgedrag van een functie wijzigen door optioneel de eigenschappen en op te geven `scriptFile` in defunction.jsin `entryPoint` *het* bestand. De onderstaande _function.js_ bijvoorbeeld vertelt de runtime dat de methode in het main.py-bestand moet worden gebruikt als het toegangspunt `customentry()` voor uw Azure-functie. 

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mapstructuur

De aanbevolen mapstructuur voor een Python Functions-project ziet eruit als in het volgende voorbeeld:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
De hoofdprojectmap (<project_root>) kan de volgende bestanden bevatten:

* *local.settings.js:* wordt gebruikt voor het opslaan van app-instellingen en verbindingsreeksen wanneer deze lokaal worden uitgevoerd. Dit bestand wordt niet gepubliceerd naar Azure. Zie [local.settings.file voor meer informatie.](functions-run-local.md#local-settings-file)
* *requirements.txt:* bevat de lijst met Python-pakketten die het systeem installeert bij het publiceren naar Azure.
* *host.js:* bevat algemene configuratieopties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt wel gepubliceerd naar Azure. Niet alle opties worden ondersteund bij lokaal uitvoeren. Zie voor meer informatie [host.jsop](functions-host-json.md).
* *.vscode/*: (optioneel) Bevat vsCode-configuratie voor het opslaan. Zie [VSCode-instelling voor meer informatie.](https://code.visualstudio.com/docs/getstarted/settings)
* *.venv/*: (optioneel) Bevat een virtuele Python-omgeving die wordt gebruikt door lokale ontwikkeling.
* *Dockerfile:*(optioneel) Wordt gebruikt bij het publiceren van uw project in een [aangepaste container](functions-create-function-linux-custom-image.md).
* *tests/*: (optioneel) Bevat de testgevallen van uw functie-app.
* *.funcignore*: (optioneel) declareren bestanden die niet naar Azure mogen worden gepubliceerd. Dit bestand bevat meestal om uw editor-instelling te negeren, om de lokale virtuele Python-omgeving te negeren, testgevallen te negeren en te voorkomen dat lokale `.vscode/` `.venv/` app-instellingen worden `tests/` `local.settings.json` gepubliceerd.

Elke functie heeft een eigen codebestand en bindingsconfiguratiebestand (function.jsaan).

Wanneer u uw project implementeert in een functie-app in Azure, moet de volledige inhoud van de hoofdmap van het project *(<project_root>)* in het pakket worden opgenomen, maar niet in de map zelf, wat betekent dat de hoofdmap van het pakket moet `host.json` staan. In dit voorbeeld wordt u aangeraden uw tests in een map samen met andere functies te `tests/` onderhouden. Zie Eenheidstests [voor meer informatie.](#unit-testing)

## <a name="import-behavior"></a>Importgedrag

U kunt modules in uw functiecode importeren met behulp van zowel absolute als relatieve verwijzingen. Op basis van de mapstructuur die hierboven wordt weergegeven, werken de volgende importen vanuit het *functiebestand<project_root>\my \_ first function _ \_ \\ \_ init \_ \_ .py*:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  De *map shared_code/* moet een init .py-bestand bevatten om het te markeren als een Python-pakket bij het gebruik van \_ \_ de absolute \_ \_ importsyntaxis.

De volgende import van apps en daarna op het hoogste niveau van relatieve import zijn afgeschaft, omdat deze niet wordt ondersteund door statische typecontrole en niet wordt ondersteund \_ \_ \_ \_ door Python-test frameworks:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Triggers en invoer

Invoer is onderverdeeld in twee categorieën in Azure Functions: triggerinvoer en aanvullende invoer. Hoewel ze verschillen in het `function.json` bestand, is het gebruik identiek in Python-code.  Verbindingsreeksen of geheimen voor trigger- en invoerbronnen worden bij het lokaal uitvoeren van het bestand aan waarden in het bestand en aan de toepassingsinstellingen bij `local.settings.json` uitvoering in Azure.

De volgende code laat bijvoorbeeld het verschil tussen de twee zien:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Wanneer de functie wordt aangeroepen, wordt de HTTP-aanvraag als doorgegeven aan de functie `req` . Een vermelding wordt opgehaald uit de Azure Blob Storage op basis van de id _in_ de route-URL en beschikbaar gesteld als in `obj` de functie-body.  Hier is het opgegeven opslagaccount de connection string gevonden in de app-instelling AzureWebJobsStorage. Dit is hetzelfde opslagaccount dat wordt gebruikt door de functie-app.


## <a name="outputs"></a>Uitvoerwaarden

Uitvoer kan worden uitgedrukt in zowel retourwaarde- als uitvoerparameters. Als er slechts één uitvoer is, raden we u aan de retourwaarde te gebruiken. Voor meerdere uitvoer moet u uitvoerparameters gebruiken.

Als u de retourwaarde van een functie wilt gebruiken als de waarde van een uitvoerbinding, moet de eigenschap van de `name` binding worden ingesteld op in `$return` `function.json` .

Als u meerdere uitvoer wilt produceren, gebruikt u de methode van de `set()` interface om een waarde toe te wijzen aan de [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) binding. De volgende functie kan bijvoorbeeld een bericht naar een wachtrij pushen en ook een HTTP-antwoord retourneren.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Logboekregistratie

Toegang tot de Azure Functions runtimelogboek is beschikbaar via een [`logging`](https://docs.python.org/3/library/logging.html#module-logging) hoofd-handler in uw functie-app. Deze logboeken zijn gekoppeld aan Application Insights waarmee u waarschuwingen en fouten kunt markeren die zijn aangetroffen tijdens de uitvoering van de functie.

In het volgende voorbeeld wordt een informatiebericht bijgeroepen wanneer de functie wordt aangeroepen via een HTTP-trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Er zijn aanvullende logboekregistratiemethoden beschikbaar, zodat u op verschillende traceerniveaus naar de console kunt schrijven:

| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Schrijft een bericht met niveau KRITIEK op de hoofdlogboek.  |
| **`error(_message_)`**   | Schrijft een bericht met foutniveau in de hoofdlogboek.    |
| **`warning(_message_)`**    | Schrijft een bericht met waarschuwingsniveau in de hoofdlogboek.  |
| **`info(_message_)`**    | Schrijft een bericht met niveau INFO op de hoofdlogboek.  |
| **`debug(_message_)`** | Schrijft een bericht met foutopsporing op niveau in de hoofdlogboek.  |

Zie Monitor Azure Functions (Logboekregistratie) [Azure Functions.](functions-monitoring.md)

## <a name="http-trigger-and-bindings"></a>HTTP-trigger en -bindingen

De HTTP-trigger wordt gedefinieerd in de function.jsin het bestand. De `name` van de binding moet overeenkomen met de benoemde parameter in de functie .
In de vorige voorbeelden wordt een bindingsnaam `req` gebruikt. Deze parameter is een [HttpRequest-object] en er [wordt een HttpResponse-object] geretourneerd.

Vanuit het [HttpRequest-object] kunt u aanvraagheaders, queryparameters, routeparameters en de berichttekst opvragen.

Het volgende voorbeeld is afkomstig uit de [HTTP-triggersjabloon voor Python.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In deze functie wordt de waarde van de `name` queryparameter verkregen van de `params` parameter van het [HttpRequest-object.] De met JSON gecodeerde bericht-body wordt gelezen met behulp van de `get_json` methode .

Op dezelfde manier kunt u en instellen `status_code` `headers` voor het antwoordbericht in het [geretourneerde HttpResponse-object.]

## <a name="scaling-and-performance"></a>Schalen en prestaties

Raadpleeg het artikel Over schalen en prestaties van Python voor best practices voor schalen en [prestaties](python-scale-performance-reference.md)voor Python-functie-apps.

## <a name="context"></a>Context

Neem het argument op in de handtekening om de aanroepcontext van een functie tijdens de uitvoering [`context`](/python/api/azure-functions/azure.functions.context) op te halen.

Bijvoorbeeld:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

De [**contextklasse**](/python/api/azure-functions/azure.functions.context) heeft de volgende tekenreekskenmerken:

`function_directory` De map waarin de functie wordt uitgevoerd.

`function_name` Naam van de functie.

`invocation_id` Id van de huidige functie-aanroep.

## <a name="global-variables"></a>Globale variabelen

Het is niet gegarandeerd dat de status van uw app behouden blijft voor toekomstige uitvoeringen. De runtime van Azure Functions gebruikt echter vaak hetzelfde proces voor meerdere uitvoeringen van dezelfde app. Als u de resultaten van een dure berekening in de cache wilt plaatsen, declareer u deze als een globale variabele.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Omgevingsvariabelen

In Functions worden [toepassingsinstellingen,](functions-app-settings.md)zoals serviceverbindingsreeksen, tijdens de uitvoering als omgevingsvariabelen blootgesteld. U kunt deze instellingen openen door te declareren `import os` en vervolgens te gebruiken, `setting = os.environ["setting-name"]` .

In het volgende voorbeeld wordt de [toepassingsinstelling](functions-how-to-use-azure-function-app-settings.md#settings), met de sleutel met de naam `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Voor lokale ontwikkeling worden toepassingsinstellingen [onderhouden in de local.settings.jsop bestand](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-versie

Azure Functions ondersteunt de volgende Python-versies:

| Functions-versie | <sup>*</sup>Python-versies |
| ----- | ----- |
| 3.x | 3.9 (preview) <br/> 3.8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Officiële CPython-distributies

Als u een specifieke Python-versie wilt aanvragen wanneer u uw functie-app in Azure maakt, gebruikt u `--runtime-version` de optie van de opdracht [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) . De versie van de Functions-runtime wordt ingesteld door de `--functions-version` optie . De Python-versie wordt ingesteld wanneer de functie-app wordt gemaakt en kan niet worden gewijzigd.

Wanneer de runtime lokaal wordt uitgevoerd, wordt de beschikbare Python-versie gebruikt.

## <a name="package-management"></a>Pakketbeheer

Wanneer u lokaal ontwikkelt met behulp van Azure Functions Core Tools of Visual Studio Code, voegt u de namen en versies van de vereiste pakketten toe aan het bestand en installeert u `requirements.txt` deze met `pip` .

Het volgende vereistenbestand en de volgende PIP-opdracht kunnen bijvoorbeeld worden gebruikt om het pakket te `requests` installeren vanuit PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publiceren naar Azure

Wanneer u klaar bent om te publiceren, moet u ervoor zorgen dat al uw openbaar beschikbare afhankelijkheden worden vermeld in het requirements.txt-bestand, dat zich in de hoofdmap van uw projectmap bevindt.

Projectbestanden en -mappen die zijn uitgesloten van publicatie, met inbegrip van de map van de virtuele omgeving, worden vermeld in het bestand .funcignore.

Er worden drie buildacties ondersteund voor het publiceren van uw Python-project naar Azure: externe build, lokale build en builds met behulp van aangepaste afhankelijkheden.

U kunt ook Azure Pipelines gebruiken om uw afhankelijkheden te bouwen en te publiceren met behulp van continue levering (CD). Zie Continue levering met behulp van [Azure DevOps voor meer informatie.](functions-how-to-azure-devops.md)

### <a name="remote-build"></a>Externe build

Wanneer u externe build gebruikt, komen afhankelijkheden die zijn hersteld op de server en native afhankelijkheden overeen met de productieomgeving. Dit resulteert in een kleiner implementatiepakket dat moet worden geüpload. Gebruik externe build bij het ontwikkelen van Python-apps in Windows. Als uw project aangepaste afhankelijkheden heeft, kunt u externe [build gebruiken met extra index-URL.](#remote-build-with-extra-index-url)

Afhankelijkheden worden op afstand verkregen op basis van de inhoud van het requirements.txt bestand. [Externe build](functions-deployment-technologies.md#remote-build) is de aanbevolen buildmethode. Standaard vraagt de Azure Functions Core Tools om een externe build wanneer u de volgende [func azure functionapp publish-opdracht](functions-run-local.md#publish) gebruikt om uw Python-project naar Azure te publiceren.

```bash
func azure functionapp publish <APP_NAME>
```

Vergeet niet om te `<APP_NAME>` vervangen door de naam van uw functie-app in Azure.

De [Azure Functions-extensie voor Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) vraagt ook standaard een externe build aan.

### <a name="local-build"></a>Lokale build

Afhankelijkheden worden lokaal verkregen op basis van de inhoud van het requirements.txt bestand. U kunt voorkomen dat een externe build wordt uitgevoerd met behulp van de volgende [func azure functionapp publish-opdracht](functions-run-local.md#publish) om te publiceren met een lokale build.

```command
func azure functionapp publish <APP_NAME> --build local
```

Vergeet niet om te `<APP_NAME>` vervangen door de naam van uw functie-app in Azure.

Met behulp van de optie worden projectafhankelijkheden gelezen uit het requirements.txt en worden die afhankelijke pakketten `--build local` gedownload en lokaal geïnstalleerd. Projectbestanden en afhankelijkheden worden vanaf uw lokale computer geïmplementeerd in Azure. Hierdoor wordt een groter implementatiepakket geüpload naar Azure. Als afhankelijkheden in uw requirements.txt niet kunnen worden verkregen door Core Tools, moet u de optie voor aangepaste afhankelijkheden gebruiken voor publicatie.

Het is niet raadzaam om lokale builds te gebruiken bij het lokaal ontwikkelen in Windows.

### <a name="custom-dependencies"></a>Aangepaste afhankelijkheden

Wanneer uw project afhankelijkheden heeft die niet zijn gevonden in de [Python Package Index](https://pypi.org/), zijn er twee manieren om het project te bouwen. De buildmethode is afhankelijk van hoe u het project bouwt.

#### <a name="remote-build-with-extra-index-url"></a>Externe build met extra index-URL

Wanneer uw pakketten beschikbaar zijn via een toegankelijke aangepaste pakketindex, gebruikt u een externe build. Voordat u publiceert, moet u [een app-instelling met de naam](functions-how-to-use-azure-function-app-settings.md#settings) `PIP_EXTRA_INDEX_URL` maken. De waarde voor deze instelling is de URL van uw aangepaste pakketindex. Met deze instelling geeft u de externe build de mogelijkheid om uit te `pip install` voeren met behulp van de optie `--extra-index-url` . Zie de installatiedocumentatie voor [Python pip voor meer informatie.](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)

U kunt ook basisverificatiereferenties gebruiken met uw extra pakketindex-URL's. Zie Basisverificatiereferenties in [de Python-documentatie](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) voor meer informatie.

#### <a name="install-local-packages"></a>Lokale pakketten installeren

Als uw project gebruikmaakt van pakketten die niet openbaar beschikbaar zijn voor onze hulpprogramma's, kunt u ze beschikbaar maken voor uw app door ze in de \_ \_ map \_ \_ app/.python_packages plaatsen. Voordat u publiceert, moet u de volgende opdracht uitvoeren om de afhankelijkheden lokaal te installeren:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Wanneer u aangepaste afhankelijkheden gebruikt, moet u de publicatieoptie gebruiken, omdat u de afhankelijkheden al in de `--no-build` projectmap hebt geïnstalleerd.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Vergeet niet om te `<APP_NAME>` vervangen door de naam van uw functie-app in Azure.

## <a name="unit-testing"></a>Moduletests

Functies die zijn geschreven in Python, kunnen net als andere Python-code worden getest met behulp van standaard test frameworks. Voor de meeste bindingen is het mogelijk om een mock-invoerobject te maken door een exemplaar van een geschikte klasse van het pakket te `azure.functions` maken. Omdat het pakket niet onmiddellijk beschikbaar is, moet u het installeren via uw bestand, zoals beschreven in de sectie [`azure.functions`](https://pypi.org/project/azure-functions/) `requirements.txt` [pakketbeheer](#package-management) hierboven.

Neem *my_second_function* voorbeeld. Hier volgt een mock-test van een door HTTP geactiveerde functie:

Eerst moeten we een<project_root>*/my_second_function/function.js* maken en deze functie definiëren als een HTTP-trigger.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Nu kunnen we de my_second_function *en* de *shared_code.my_second_helper_function implementeren.*

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

We kunnen beginnen met het schrijven van testgevallen voor onze HTTP-trigger.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Installeer in `.venv` uw virtuele Python-omgeving uw favoriete Python-test framework (bijvoorbeeld `pip install pytest` ). Voer uit `pytest tests` om het testresultaat te controleren.

## <a name="temporary-files"></a>Tijdelijke bestanden

De `tempfile.gettempdir()` methode retourneert een tijdelijke map, die in Linux `/tmp` is. Uw toepassing kan deze map gebruiken om tijdelijke bestanden op te slaan die tijdens de uitvoering door uw functies worden gegenereerd en gebruikt.

> [!IMPORTANT]
> Bestanden die naar de tijdelijke map worden geschreven, blijven niet gegarandeerd bestaan bij aanroepen. Tijdens het uitschalen worden tijdelijke bestanden niet gedeeld tussen exemplaren.

In het volgende voorbeeld wordt een benoemd tijdelijk bestand gemaakt in de tijdelijke map ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

U wordt aangeraden uw tests te onderhouden in een map die los staat van de projectmap. Zo voorkomt u dat u testcode implementeert met uw app.

## <a name="preinstalled-libraries"></a>Vooraf geïnstalleerde bibliotheken

Er zijn enkele bibliotheken beschikbaar met de Python Functions-runtime.

### <a name="python-standard-library"></a>Standaardbibliotheek van Python

De standaardbibliotheek van Python bevat een lijst met ingebouwde Python-modules die bij elke Python-distributie worden geleverd. De meeste van deze bibliotheken helpen u toegang te krijgen tot systeemfunctionaliteit, zoals bestands-I/O. Op Windows-systemen worden deze bibliotheken geïnstalleerd met Python. Op de Unix-systemen worden ze geleverd door pakketverzamelingen.

Als u de volledige details van de lijst met deze bibliotheken wilt weergeven, gaat u naar de onderstaande koppelingen:

* [Standaardbibliotheek voor Python 3.6](https://docs.python.org/3.6/library/)
* [Standaardbibliotheek voor Python 3.7](https://docs.python.org/3.7/library/)
* [Standaardbibliotheek voor Python 3.8](https://docs.python.org/3.8/library/)
* [Standaardbibliotheek voor Python 3.9](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python-werkafhankelijkheden

Voor de Functions Python-werkfunctie is een specifieke set bibliotheken vereist. U kunt deze bibliotheken ook gebruiken in uw functies, maar ze maken geen deel uit van de Python-standaard. Als uw functies afhankelijk zijn van een van deze bibliotheken, zijn ze mogelijk niet beschikbaar voor uw code wanneer ze buiten de Azure Functions. U vindt een gedetailleerde lijst met afhankelijkheden in de sectie Vereist voor installatie in [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) bestand. **\_**

> [!NOTE]
> Als de functie-app een requirements.txt `azure-functions-worker` bevat, verwijdert u deze. De functions worker wordt automatisch beheerd door Azure Functions platform en wordt regelmatig bijgewerkt met nieuwe functies en oplossingen voor fouten. Handmatig installeren van een oude werkversie in requirements.txt kan onverwachte problemen veroorzaken.

### <a name="azure-functions-python-library"></a>Azure Functions Python-bibliotheek

Elke Python-werkupdate bevat een nieuwe versie van [Azure Functions Python-bibliotheek (azure.functions).](https://github.com/Azure/azure-functions-python-library) Deze aanpak maakt het eenvoudiger om uw Python-functie-apps continu bij te werken, omdat elke update achterwaarts compatibel is. Een lijst met releases van deze bibliotheek vindt u in [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

De versie van de runtimebibliotheek wordt door Azure hersteld en kan niet worden overschrijven door requirements.txt. De `azure-functions` vermelding in requirements.txt is alleen voor linting en klantbewustheid.

Gebruik de volgende code om de werkelijke versie van de Python Functions-bibliotheek in uw runtime bij te houden:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Runtimesysteembibliotheken

Volg de onderstaande koppelingen voor een lijst met vooraf geïnstalleerde systeembibliotheken in Docker-installatieprogramma's van Python-werkprogramma's:

|  Functions-runtime  | Debian-versie | Python-versies |
|------------|------------|------------|
| Versie 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versie 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Cross-origin-resources delen

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS wordt volledig ondersteund voor Python-functie-apps.

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

Hieronder volgt een lijst met handleidingen voor probleemoplossing voor veelvoorkomende problemen:

* [ModuleNotFoundError en ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Kan 'cygrpc' niet importeren](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Alle bekende problemen en functieaanvragen worden bijgespoord met behulp [van de GitHub-lijst met](https://github.com/Azure/azure-functions-python-worker/issues) problemen. Als u een probleem hebt en het probleem niet kunt vinden in GitHub, opent u een nieuw probleem en vermeldt u een gedetailleerde beschrijving van het probleem.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

* [documentatie Azure Functions pakket-API](/python/api/azure-functions/azure.functions)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* [Blob Storage-bindingen](functions-bindings-storage-blob.md)
* [HTTP- en webhookbindingen](functions-bindings-http-webhook.md)
* [Queue Storage-bindingen](functions-bindings-storage-queue.md)
* [Timertrigger](functions-bindings-timer.md)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse