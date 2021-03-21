---
title: Python-ontwikkelaars referentie voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3eb3b3b015f401e872a879c46ec6f8c69df5f87f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455413"
---
# <a name="azure-functions-python-developer-guide"></a>Ontwikkelaarshandleiding voor Azure Functions Python

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions met behulp van python. De onderstaande inhoud veronderstelt dat u de [hand leiding voor de Azure functions-ontwikkel aars](functions-reference.md)al hebt gelezen.

Als een python-ontwikkelaar bent u mogelijk ook geïnteresseerd in een van de volgende artikelen:

| Aan de slag | Concepten| Scenario's/voor beelden |
| -- | -- | -- | 
| <ul><li>[Python-functie met Visual Studio code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-functie met Terminal/opdracht prompt](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Ontwikkelaarsgids](functions-reference.md)</li><li>[Hostingopties](functions-scale.md)</li><li>[Prestatie &nbsp; overwegingen](functions-best-practices.md)</li></ul> | <ul><li>[Classificatie van afbeeldingen met PyTorch](machine-learning-pytorch.md)</li><li>[Voor beeld van Azure Automation](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine Learning met TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Bladeren door python-voor beelden](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Hoewel u [uw python-Azure functions lokaal kunt ontwikkelen in Windows](create-first-function-vs-code-python.md#run-the-function-locally), wordt python alleen ondersteund in een hosting abonnement op basis van Linux wanneer het wordt uitgevoerd in Azure. Bekijk de lijst met ondersteunde combi Naties van [besturings systemen en runtime](functions-scale.md#operating-systemruntime) .

## <a name="programming-model"></a>Programmeermodel

Azure Functions verwacht dat een functie een stateless methode is in uw python-script dat invoer verwerkt en uitvoer produceert. Standaard verwacht de runtime dat de methode wordt geïmplementeerd als globale methode met `main()` de naam in het `__init__.py` bestand. U kunt ook [een alternatief invoer punt opgeven](#alternate-entry-point).

Gegevens van triggers en bindingen zijn gekoppeld aan de functie via methode kenmerken, met behulp van de eigenschap die is `name` gedefinieerd in de *function.jsvoor* het bestand. Zo wordt in de  _function.js_ hieronder een eenvoudige functie beschreven die wordt geactiveerd door een HTTP-aanvraag met de naam `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Op basis van deze definitie `__init__.py` ziet het bestand dat de functie code bevat, eruit als in het volgende voor beeld:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

U kunt ook expliciet de kenmerk typen en het retour type declareren in de functie met behulp van python-type aantekeningen. Dit helpt u bij het gebruik van de functies IntelliSense en automatisch aanvullen van vele Python-code-editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Gebruik de python-aantekeningen in het pakket [Azure. functions. *](/python/api/azure-functions/azure.functions) om invoer en uitvoer aan uw methoden te koppelen.

## <a name="alternate-entry-point"></a>Alternatief invoer punt

U kunt het standaard gedrag van een functie wijzigen door optioneel de `scriptFile` Eigenschappen en op te geven `entryPoint` in de *function.jsvoor* het bestand. Zo geeft de _function.js_ hieronder aan dat de runtime de `customentry()` methode in het _Main.py_ -bestand moet gebruiken als het toegangs punt voor uw Azure-functie.

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

De aanbevolen mapstructuur voor een python functions-project ziet eruit als in het volgende voor beeld:

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
De hoofdmap van het project (<project_root>) kan de volgende bestanden bevatten:

* *local.settings.jsop*: wordt gebruikt voor het opslaan van app-instellingen en verbindings reeksen wanneer deze lokaal worden uitgevoerd. Dit bestand wordt niet gepubliceerd naar Azure. Zie [Local. settings. File](functions-run-local.md#local-settings-file)voor meer informatie.
* *requirements.txt*: bevat de lijst met Python-pakketten die door het systeem wordt geïnstalleerd bij het publiceren naar Azure.
* *host.jsop*: bevat globale configuratie opties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt gepubliceerd naar Azure. Niet alle opties worden ondersteund bij het lokaal uitvoeren. Zie [host.jsvoor](functions-host-json.md)meer informatie.
* *. vscode/*: (optioneel) bevat de configuratie van Store vscode. Zie [VSCode-instelling](https://code.visualstudio.com/docs/getstarted/settings)voor meer informatie.
* *. venv/*: (optioneel) bevat een virtuele python-omgeving die wordt gebruikt voor lokale ontwikkeling.
* *Dockerfile*: (optioneel) gebruikt bij het publiceren van uw project in een [aangepaste container](functions-create-function-linux-custom-image.md).
* testen */*: (optioneel) bevat de test cases van uw functie-app.
* *. funcignore*: (optioneel) declareert bestanden die niet naar Azure mogen worden gepubliceerd. Normaal gesp roken bevat dit bestand `.vscode/` het negeren van de instelling van uw editor, het `.venv/` negeren van lokale python virtuele omgeving, het `tests/` negeren van test cases en `local.settings.json` om te voor komen dat lokale app-instellingen worden gepubliceerd.

Elke functie heeft een eigen code bestand en een bindings configuratie bestand (function.jsaan).

Wanneer u uw project implementeert in een functie-app in azure, moet de volledige inhoud van de map van het hoofd project (*<project_root>*) worden opgenomen in het pakket, maar niet in de map zelf. Dit betekent dat u zich `host.json` in de hoofdmap van het pakket moet bevinden. U wordt aangeraden uw tests in een map samen met andere functies te onderhouden, in dit voor beeld `tests/` . Zie [unit testen](#unit-testing)voor meer informatie.

## <a name="import-behavior"></a>Gedrag bij importeren

U kunt modules in uw functie code importeren met behulp van absolute en relatieve verwijzingen. Op basis van de mappen structuur die hierboven wordt weer gegeven, werkt de volgende invoer vanuit het functie bestand *<project_root> \Mijn \_ eerste \_ functie \\ _ \_ init \_ \_ . py*:

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
>  De *shared_code/* map moet een \_ \_ init \_ \_ . py-bestand bevatten om het als een python-pakket te markeren wanneer absolute import syntaxis wordt gebruikt.

De volgende import \_ \_ \_ \_ van apps en meer relatieve import op het hoogste niveau zijn afgeschaft, omdat deze niet wordt ondersteund door de statische type controle en niet wordt ondersteund door python-test raamwerken:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Triggers en invoer

Invoer wordt onderverdeeld in twee categorieën in Azure Functions: invoer van trigger en aanvullende invoer. Hoewel ze verschillen in het `function.json` bestand, is het gebruik identiek in de python-code.  Verbindings reeksen of geheimen voor trigger-en invoer bronnen worden toegewezen aan waarden in het `local.settings.json` bestand tijdens het uitvoeren van lokaal en de toepassings instellingen wanneer ze worden uitgevoerd in Azure.

De volgende code toont bijvoorbeeld het verschil tussen de twee:

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

Wanneer de functie wordt aangeroepen, wordt de HTTP-aanvraag door gegeven aan de functie als `req` . Er wordt een item opgehaald uit de Azure-Blob Storage op basis van de id in de route _-_ URL en beschikbaar gemaakt als `obj` in de hoofd tekst van de functie.  Hier is het opgegeven opslag account het connection string gevonden in de app-instelling AzureWebJobsStorage. Dit is hetzelfde opslag account dat wordt gebruikt door de functie-app.


## <a name="outputs"></a>Uitvoerwaarden

Output kan worden uitgedrukt in retour waarde en uitvoer parameters. Als er slechts één uitvoer is, raden we u aan de retour waarde te gebruiken. Voor meerdere uitvoer moet u uitvoer parameters gebruiken.

Als u de retour waarde van een functie als de waarde van een uitvoer binding wilt gebruiken, moet u de `name` eigenschap van de binding instellen op `$return` in `function.json` .

Als u meerdere uitvoer wilt maken, gebruikt u de `set()` methode van de [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) interface om een waarde toe te wijzen aan de binding. Met de volgende functie kan bijvoorbeeld een bericht naar een wachtrij worden gepusht en wordt ook een HTTP-antwoord geretourneerd.

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

Toegang tot de runtime logger van Azure Functions is beschikbaar via een basis- [`logging`](https://docs.python.org/3/library/logging.html#module-logging) handler in uw functie-app. Deze logboek registratie is gekoppeld aan Application Insights en stelt u in staat om waarschuwingen en fouten te markeren die tijdens de uitvoering van de functie zijn aangetroffen.

In het volgende voor beeld wordt een info bericht geregistreerd wanneer de functie wordt aangeroepen via een HTTP-trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Er zijn aanvullende logboek registratie methoden beschikbaar waarmee u naar de-console kunt schrijven op verschillende tracerings niveaus:

| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Schrijft een bericht met niveau kritiek op de hoofd logboek registratie.  |
| **`error(_message_)`**   | Schrijft een bericht met een niveau fout in de hoofd logboek registratie.    |
| **`warning(_message_)`**    | Schrijft een bericht met niveau waarschuwing in de hoofd logboek registratie.  |
| **`info(_message_)`**    | Schrijft een bericht met niveau-informatie in de hoofd logboek registratie.  |
| **`debug(_message_)`** | Schrijft een bericht met niveau DEBUG op de hoofd logboek registratie.  |

Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie over logboek registratie.

## <a name="http-trigger-and-bindings"></a>HTTP-trigger en bindingen

De HTTP-trigger wordt gedefinieerd in de function.jsin het bestand. De `name` van de binding moet overeenkomen met de benoemde para meter in de functie.
In de vorige voor beelden wordt een bindings naam `req` gebruikt. Deze para meter is een [HttpRequest] -object en er wordt een [HttpResponse] -object geretourneerd.

Vanuit het object [HttpRequest] kunt u aanvraag headers, query parameters, route parameters en de bericht tekst ophalen.

Het volgende voor beeld is afkomstig uit de [sjabloon voor http-triggers voor python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

In deze functie wordt de waarde van de `name` query parameter opgehaald uit de `params` para meter van het [HttpRequest] -object. De hoofd tekst van de JSON-code ring wordt gelezen met behulp van de `get_json` methode.

U kunt ook de `status_code` en `headers` voor het antwoord bericht instellen in het geretourneerde [HttpResponse] -object.

## <a name="scaling-and-performance"></a>Schalen en prestaties

Voor schaal-en prestatie best practices voor python-functie-apps raadpleegt u het [artikel python schalen en prestaties](python-scale-performance-reference.md).

## <a name="context"></a>Context

Als u de aanroep context van een functie tijdens de uitvoering wilt ophalen, neemt u het [`context`](/python/api/azure-functions/azure.functions.context) argument op in de hand tekening.

Bijvoorbeeld:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

De [**context**](/python/api/azure-functions/azure.functions.context) klasse heeft de volgende teken reeks kenmerken:

`function_directory` De map waarin de functie wordt uitgevoerd.

`function_name` De naam van de functie.

`invocation_id` ID van de huidige functie aanroep.

## <a name="global-variables"></a>Globale variabelen

Het is niet zeker dat de status van uw app wordt bewaard voor toekomstige uitvoeringen. De Azure Functions runtime gebruikt echter vaak hetzelfde proces voor meerdere uitvoeringen van dezelfde app. Als u de resultaten van een dure berekening in de cache wilt opslaan, declareert u deze als een globale variabele.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [Toepassings instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen door te declareren `import os` en vervolgens te gebruiken `setting = os.environ["setting-name"]` .

In het volgende voor beeld wordt de [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings)opgehaald met de sleutel met de naam `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Voor lokale ontwikkeling worden toepassings instellingen [onderhouden in de local.settings.jsin het bestand](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-versie

Azure Functions ondersteunt de volgende python-versies:

| Functie versie | Python- <sup>*</sup> versies |
| ----- | ----- |
| 3.x | 3,9 (preview-versie) <br/> 3.8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Officiële CPython-distributies

Als u een specifieke python-versie wilt aanvragen wanneer u de functie-app in azure maakt, gebruikt u de `--runtime-version` optie van de [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) opdracht. De runtime versie van functions wordt ingesteld met behulp van de `--functions-version` optie. De python-versie wordt ingesteld wanneer de functie-app wordt gemaakt en kan niet worden gewijzigd.

Wanneer lokaal wordt uitgevoerd, gebruikt de runtime de beschik bare python-versie.

## <a name="package-management"></a>Pakketbeheer

Wanneer u lokaal ontwikkelt met behulp van de Azure Functions Core Tools of Visual Studio code, voegt u de namen en versies van de vereiste pakketten toe aan het `requirements.txt` bestand en installeert u deze met behulp van `pip` .

De volgende vereisten bestand en PIP-opdracht kunnen bijvoorbeeld worden gebruikt om het pakket te installeren `requests` vanuit PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publiceren naar Azure

Wanneer u klaar bent om te publiceren, moet u ervoor zorgen dat alle openbaar beschik bare afhankelijkheden worden weer gegeven in het requirements.txt-bestand, dat zich in de hoofdmap van de projectmap bevindt.

Project bestanden en-mappen die zijn uitgesloten van publiceren, met inbegrip van de map virtuele omgeving, worden weer gegeven in het funcignore-bestand.

Er zijn drie build-acties die worden ondersteund voor het publiceren van uw python-project naar Azure: externe build, local build en builds met aangepaste afhankelijkheden.

U kunt ook Azure-pijp lijnen gebruiken om uw afhankelijkheden te maken en te publiceren met behulp van doorlopende levering (CD). Zie [continue levering met behulp van Azure DevOps](functions-how-to-azure-devops.md)voor meer informatie.

### <a name="remote-build"></a>Externe build

Bij het gebruik van externe builden worden afhankelijkheden die zijn hersteld op de server en native afhankelijkheden overeenkomen met de productie omgeving. Dit resulteert in een kleiner implementatie pakket dat moet worden geüpload. Gebruik externe build bij het ontwikkelen van python-apps in Windows. Als uw project aangepaste afhankelijkheden heeft, kunt u [externe build gebruiken met extra index-URL](#remote-build-with-extra-index-url).

Afhankelijkheden worden op afstand opgehaald op basis van de inhoud van het requirements.txt-bestand. [Externe build](functions-deployment-technologies.md#remote-build) is de aanbevolen methode build. Standaard vraagt de Azure Functions Core Tools een externe build aan wanneer u de volgende [func Azure functionapp Publish](functions-run-local.md#publish) -opdracht gebruikt om uw python-project naar Azure te publiceren.

```bash
func azure functionapp publish <APP_NAME>
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

Met de [extensie Azure functions voor Visual Studio code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) wordt ook standaard een externe build aangevraagd.

### <a name="local-build"></a>Lokale build

Afhankelijkheden worden lokaal opgehaald op basis van de inhoud van het requirements.txt-bestand. U kunt voor komen dat een externe build wordt gemaakt met behulp van de volgende [func Azure functionapp Publish](functions-run-local.md#publish) -opdracht om te publiceren met een lokale build.

```command
func azure functionapp publish <APP_NAME> --build local
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

Met behulp `--build local` van de optie worden Project afhankelijkheden uit het requirements.txt-bestand gelezen en worden deze afhankelijke pakketten lokaal gedownload en geïnstalleerd. Project bestanden en afhankelijkheden worden geïmplementeerd vanaf uw lokale computer naar Azure. Dit leidt ertoe dat een groter implementatie pakket wordt geüpload naar Azure. Als u om de een of andere reden geen afhankelijkheden in uw requirements.txt bestand kunt verkrijgen, moet u de optie aangepaste afhankelijkheden gebruiken voor het publiceren.

Het is niet raadzaam om lokale builds te gebruiken bij het ontwikkelen van lokaal op Windows.

### <a name="custom-dependencies"></a>Aangepaste afhankelijkheden

Wanneer het project geen afhankelijkheden heeft gevonden in de [python-pakket index](https://pypi.org/), zijn er twee manieren om het project te bouwen. De methode build is afhankelijk van hoe u het project bouwt.

#### <a name="remote-build-with-extra-index-url"></a>Externe build met extra index-URL

Gebruik een externe build als uw pakketten beschikbaar zijn vanuit een toegankelijke aangepaste pakket index. Zorg ervoor dat u [een app-instelling met de naam maakt](functions-how-to-use-azure-function-app-settings.md#settings) voordat u publiceert `PIP_EXTRA_INDEX_URL` . De waarde voor deze instelling is de URL van de aangepaste pakket index. Met deze instelling geeft u aan dat de externe build moet worden uitgevoerd `pip install` met behulp van de `--extra-index-url` optie. Zie de [installatie documentatie voor python pip](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)voor meer informatie.

U kunt ook basis verificatie referenties gebruiken met de extra pakket index-Url's. Zie voor meer informatie [basis verificatie referenties](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) in python-documentatie.

#### <a name="install-local-packages"></a>Lokale pakketten installeren

Als uw project pakketten gebruikt die niet openbaar beschikbaar zijn voor onze tools, kunt u ze beschikbaar maken voor uw app door ze in de \_ \_ \_ \_ map app/.python_packages te plaatsen. Voordat u publiceert, voert u de volgende opdracht uit om de afhankelijkheden lokaal te installeren:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Wanneer u aangepaste afhankelijkheden gebruikt, moet u de `--no-build` optie voor publiceren gebruiken omdat u de afhankelijkheden al hebt geïnstalleerd in de projectmap.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

## <a name="unit-testing"></a>Moduletests

Functies die zijn geschreven in python kunnen worden getest als andere python-code met behulp van standaard test raamwerken. Voor de meeste bindingen is het mogelijk om een invoer object voor een model te maken door een instantie van een geschikte klasse te maken vanuit het `azure.functions` pakket. Omdat het [`azure.functions`](https://pypi.org/project/azure-functions/) pakket niet onmiddellijk beschikbaar is, moet u het installeren via uw `requirements.txt` bestand zoals beschreven in de sectie [pakket beheer](#package-management) hierboven.

Neem *my_second_function* als voor beeld. hierna volgt een model test van een door http geactiveerde functie:

Eerst moet u *<project_root>/my_second_function/function.jsin* het bestand maken en deze functie definiëren als een http-trigger.

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

Nu kunnen we de *my_second_function* en de *shared_code. my _second_helper_function* implementeren.

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

We kunnen test cases voor onze http-trigger starten.

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

`.venv`Installeer uw favoriete python-test raamwerk in uw python virtuele omgeving (bijvoorbeeld `pip install pytest` ). Voer gewoon uit `pytest tests` om het test resultaat te controleren.

## <a name="temporary-files"></a>Tijdelijke bestanden

De- `tempfile.gettempdir()` methode retourneert een tijdelijke map, die op Linux is `/tmp` . Uw toepassing kan deze directory gebruiken voor het opslaan van tijdelijke bestanden die door uw functies worden gegenereerd en gebruikt tijdens de uitvoering.

> [!IMPORTANT]
> Bestanden die naar de tijdelijke map worden geschreven, kunnen niet worden gegarandeerd in aanroepen. Tijdens uitschalen worden tijdelijke bestanden niet gedeeld tussen exemplaren.

In het volgende voor beeld wordt een tijdelijk bestand met de naam gemaakt in de tijdelijke map ( `/tmp` ):

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

U wordt aangeraden uw tests te onderhouden in een map gescheiden van de projectmap. Hierdoor kunt u geen test code implementeren met uw app.

## <a name="preinstalled-libraries"></a>Vooraf geïnstalleerde bibliotheken

Er zijn enkele bibliotheken beschikbaar in de runtime van python-functies.

### <a name="python-standard-library"></a>Standaard bibliotheek python

De python-standaard bibliotheek bevat een lijst met ingebouwde python-modules die worden geleverd bij elke python-distributie. De meeste van deze bibliotheken helpen u bij het openen van de systeem functionaliteit, zoals bestands-I/O. In Windows-systemen worden deze bibliotheken geïnstalleerd met python. Op de op UNIX gebaseerde systemen worden ze door pakket verzamelingen verschaft.

Als u de volledige details van de lijst met deze bibliotheken wilt bekijken, gaat u naar de onderstaande koppelingen:

* [Python 3,6-standaard bibliotheek](https://docs.python.org/3.6/library/)
* [Python 3,7-standaard bibliotheek](https://docs.python.org/3.7/library/)
* [Python 3,8-standaard bibliotheek](https://docs.python.org/3.8/library/)
* [Python 3,9-standaard bibliotheek](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions python-worker-afhankelijkheden

Voor de functie python Worker is een specifieke set bibliotheken vereist. U kunt deze bibliotheken ook gebruiken in uw functies, maar ze maken geen deel uit van de python-standaard. Als uw functies afhankelijk zijn van een van deze bibliotheken, zijn ze mogelijk niet beschikbaar voor uw code wanneer ze buiten Azure Functions worden uitgevoerd. U vindt een gedetailleerde lijst met afhankelijkheden in het gedeelte **install \_ vereist** in het bestand [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Als uw functie-app requirements.txt een `azure-functions-worker` vermelding bevat, verwijdert u deze. De werk nemer functions wordt automatisch beheerd door Azure Functions platform en we werken deze regel matig bij met nieuwe functies en oplossingen voor problemen. Het hand matig installeren van een oude versie van de werk stroom in requirements.txt kan onverwachte problemen veroorzaken.

### <a name="azure-functions-python-library"></a>Python-bibliotheek Azure Functions

Elke python worker-update bevat een nieuwe versie van [Azure functions python-bibliotheek (Azure. functions)](https://github.com/Azure/azure-functions-python-library). Deze aanpak maakt het eenvoudiger om uw python-functie-apps bij te werken, omdat elke update achterwaarts compatibel is. Een lijst met versies van deze bibliotheek vindt u in [Azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

De versie van de runtime bibliotheek wordt opgelost door Azure en kan niet worden overschreven door requirements.txt. De `azure-functions` invoer in requirements.txt is alleen voor linting en klant bewustzijn.

Gebruik de volgende code om de daad werkelijke versie van de python-functies bibliotheek in uw runtime bij te houden:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Runtime systeem bibliotheken

Volg de onderstaande koppelingen voor een lijst met vooraf geïnstalleerde systeem bibliotheken in python worker-installatie kopieën:

|  Functions-runtime  | Debian-versie | Python-versies |
|------------|------------|------------|
| Versie 2. x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versie 3. x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3,9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Cross-origin-resources delen

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS wordt volledig ondersteund voor python-functie-apps.

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

Hieronder vindt u een lijst met richt lijnen voor probleem oplossing voor veelvoorkomende problemen:

* [ModuleNotFoundError en ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Kan cygrpc niet importeren](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Alle bekende problemen en functie aanvragen worden bijgehouden met de lijst met [github-problemen](https://github.com/Azure/azure-functions-python-worker/issues) . Als u een probleem ondervindt en u het probleem niet kunt vinden in GitHub, opent u een nieuw probleem en voegt u een gedetailleerde beschrijving van het probleem toe.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

* [Documentatie over de API voor Azure Functions-pakketten](/python/api/azure-functions/azure.functions)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* [Blob Storage-bindingen](functions-bindings-storage-blob.md)
* [HTTP-en webhook-bindingen](functions-bindings-http-webhook.md)
* [Wachtrij opslag bindingen](functions-bindings-storage-queue.md)
* [Timertrigger](functions-bindings-timer.md)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse