---
title: Geheugen profileringen voor python-apps in Azure Functions
description: Meer informatie over het profiel van python-apps geheugen gebruik en het identificeren van geheugen knelpunt.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: 26f9040016f9eae7e82a85c589d673c90e542f47
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060304"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profiel van python-apps-geheugen gebruik in Azure Functions

Tijdens de ontwikkeling of na de implementatie van uw lokale python-functie-app-project naar Azure, is het een goed idee om te analyseren op potentiële geheugen knelpunten in uw functies. Dergelijke knel punten kunnen de prestaties van uw functies verminderen en leiden tot fouten. In de volgende instructie ziet u hoe u het python-pakket van de [geheugen profiler](https://pypi.org/project/memory-profiler) gebruikt, dat regel-voor-regel analyse van uw functies in een line geheugen biedt als ze worden uitgevoerd.

> [!NOTE]
> Het profileren van geheugen is alleen bedoeld voor analyse van geheugen footprint in de ontwikkelings omgeving. Pas de geheugen profiler niet toe op productie functie-apps.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het ontwikkelen van een python-functie-app, moet u aan de volgende vereisten voldoen:

* [Python 3.6. x of hoger](https://www.python.org/downloads/release/python-374/). Als u de volledige lijst met ondersteunde python-versies in Azure Functions wilt controleren, gaat u naar de [python-ontwikkelaars handleiding](functions-reference-python.md#python-version).

* De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x.

* [Visual Studio code](https://code.visualstudio.com/) die is geïnstalleerd op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Proces voor geheugen profilering

1. Voeg in uw requirements.txt toe `memory-profiler` om ervoor te zorgen dat het pakket wordt gebundeld met uw implementatie. Als u op uw lokale machine ontwikkelt, wilt u mogelijk [een virtuele python-omgeving activeren](create-first-function-cli-python.md#create-venv) en een pakket resolutie door volgen `pip install -r requirements.txt` .

2. Voeg in het functie script (meestal \_ \_ init \_ \_ . py) de volgende regels toe boven de `main()` functie. Dit zorgt ervoor dat de hoofd logboek registratie de naam van de onderliggende logboek registratie rapporteert, zodat de logboeken voor geheugen profileren door het voor voegsel kunnen worden onderscheiden `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=memory_logger)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Voorbeeld

Hier volgt een voor beeld van het uitvoeren van geheugen profiler voor een asynchrone en synchrone HTTP-triggers, respectievelijk met de naam ' HttpTriggerAsync ' en ' HttpTriggerSync '. We gaan een python-functie-app bouwen die eenvoudigweg aanvragen voor GET verzendt naar de start pagina van micro soft.

### <a name="create-a-python-function-app"></a>Een python-functie-app maken

Een python-functie-app moet volgen Azure Functions opgegeven [mapstructuur](functions-reference-python.md#folder-structure). Om het project te maken, kunt u het beste de Azure Functions Core Tools gebruiken door de volgende opdrachten uit te voeren:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Bestands inhoud bijwerken

De requirements.txt definieert de pakketten die worden gebruikt in het project. Naast de Azure Functions SDK en Memory Profiler, introduceren we `aiohttp` voor asynchrone HTTP-aanvragen en `requests` voor synchrone http-aanroepen.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

We moeten ook de asynchrone HTTP-trigger opnieuw schrijven `HttpTriggerAsync/__init__.py` en de geheugen profiler, de hoofdlogger-indeling en de traceer streaming-binding configureren.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Voor synchrone HTTP-triggers raadpleegt u de volgende `HttpTriggerSync/__init__.py` code sectie:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profiel python-functie-app in lokale ontwikkel omgeving

Nadat u alle bovenstaande wijzigingen hebt aangebracht, zijn er nog enkele stappen voor het initialiseren van een python-Environment voor Azure Functions runtime.

1. Open uw voor keur aan een Windows Power shell of een wille keurige Linux-shell.
2. Maak een virtuele python-omgeving `py -m venv .venv` in Windows of `python3 -m venv .venv` in Linux.
3. Activeer de python virtueel-omgeving met `.venv\Scripts\Activate.ps1` in Windows Power shell of `source .venv/bin/activate` in Linux shell.
4. De python-afhankelijkheden herstellen met `pip install requirements.txt`
5. Start de Azure Functions-runtime lokaal met Azure Functions Core Tools `func host start`
6. Een GET-aanvraag verzenden naar `https://localhost:7071/api/HttpTriggerAsync` of `https://localhost:7071/api/HttpTriggerSync` .
7. Er moet een geheugen profilerings rapport worden weer gegeven dat vergelijkbaar is met de onderstaande sectie in Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van Azure Functions python:

* [Ontwikkelaarshandleiding voor Azure Functions Python](functions-reference-python.md)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
