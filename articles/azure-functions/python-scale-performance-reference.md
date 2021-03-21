---
title: De doorvoer prestaties van python-apps in Azure Functions verbeteren
description: Meer informatie over het ontwikkelen van Azure Functions-apps met behulp van python die zeer goed worden uitgevoerd en waarvan de schaal kan worden uitgebreid.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786103"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>De doorvoer prestaties van python-apps in Azure Functions verbeteren

Bij het ontwikkelen van Azure Functions met python, moet u weten hoe uw functies worden uitgevoerd en hoe de prestaties van invloed zijn op de manier waarop uw functie-app wordt geschaald. De nood zaak is belang rijker bij het ontwerpen van zeer krachtige apps. De belangrijkste factoren waarmee u rekening moet houden bij het ontwerpen, schrijven en configureren van uw functions-apps, zijn horizon taal schalen en prestatie configuraties voor door voer.

## <a name="horizontal-scaling"></a>Horizontale schaalaanpassing
Standaard controleert Azure Functions automatisch de belasting van uw toepassing en worden er indien nodig extra exemplaren van de host voor python gemaakt. Azure Functions maakt gebruik van ingebouwde drempel waarden voor verschillende trigger typen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor Queue trigger. Deze drempel waarden kunnen niet door de gebruiker worden geconfigureerd. Zie voor meer informatie [op gebeurtenissen gebaseerd schalen in azure functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Prestaties van door Voer verbeteren

De standaard configuraties zijn geschikt voor de meeste toepassingen van Azure Functions. U kunt de prestaties van de door Voer van uw toepassingen echter verbeteren door configuraties te gebruiken op basis van uw werkbelasting profiel. De eerste stap is het begrijpen van het type werk belasting dat u uitvoert.

| Type werk belasting | Kenmerken van functie-app       | Voorbeelden                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/O-gebonden**     | • Er moeten veel gelijktijdige aanroepen door de app worden afgehandeld.<br>• Met de app worden een groot aantal I/O-gebeurtenissen verwerkt, zoals netwerk aanroepen en lees-en schrijf bewerkingen van de schijf. | • Web-Api's                                          |
| **CPU-gebonden**     | • App voert langlopende berekeningen uit, zoals het wijzigen van de grootte van afbeeldingen.<br>• De app heeft gegevens transformatie.                                                | • Gegevens verwerking<br>• Machine learning-interferentie<br> |

 
Omdat de werk belasting van de werkelijke wereld meestal een combi natie van I/O-en CPU-gebonden is, moet u de app profileren onder realistische productie belasting.


### <a name="performance-specific-configurations"></a>Prestatie-specifieke configuraties

Nadat u het werkbelasting Profiel van de functie-app hebt begrepen, zijn de volgende configuraties die u kunt gebruiken om de doorvoer prestaties van uw functies te verbeteren.

* [Async](#async)
* [Meerdere talen werk nemer](#use-multiple-language-worker-processes)
* [Maximum aantal werk nemers in een taal werk proces](#set-up-max-workers-within-a-language-worker-process)
* [Event-lus](#managing-event-loop)
* [Verticaal schalen](#vertical-scaling)



#### <a name="async"></a>Async

Omdat [python een single-threaded runtime is](https://wiki.python.org/moin/GlobalInterpreterLock), kan een host-exemplaar voor python slechts één functie aanroep per keer verwerken. Voor toepassingen die een groot aantal I/O-gebeurtenissen verwerken en/of I/O gebonden zijn, kunt u de prestaties aanzienlijk verbeteren door functions asynchroon uit te voeren.

Als u een functie asynchroon wilt uitvoeren, gebruikt u de `async def` -instructie, waarmee de functie rechtstreeks wordt uitgevoerd met [asyncio](https://docs.python.org/3/library/asyncio.html) :

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Hier volgt een voor beeld van een functie met een HTTP-trigger die gebruikmaakt van [aiohttp](https://pypi.org/project/aiohttp/) HTTP-client:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Een functie zonder `async` sleutel woord wordt automatisch uitgevoerd in een ThreadPoolExecutor-thread groep:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Om het volledige voor deel van het asynchroon uitvoeren van functions te garanderen, moet de I/O-bewerking/-bibliotheek die in de code wordt gebruikt, ook asynchroon worden geïmplementeerd. Het gebruik van synchrone I/O-bewerkingen in functies die als asynchroon zijn gedefinieerd, kunnen de algehele prestaties nadelig **beïnvloeden** . Als voor de bibliotheken die u gebruikt geen async-versie is geïmplementeerd, kunt u uw code ook asynchroon uitvoeren door de [Event-lus](#managing-event-loop) in uw app te beheren. 

Hier volgen enkele voor beelden van client bibliotheken met een geïmplementeerd async-patroon:
- [aiohttp](https://pypi.org/project/aiohttp/) -HTTP-client/server voor asyncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) -primitieve async/await-gereed-readys om met de netwerk verbinding te werken
- [Janus wachtrij](https://pypi.org/project/janus/) -thread-safe asyncio-bewuste wachtrij voor python
- [pyzmq](https://pypi.org/project/pyzmq/) -python-bindingen voor ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Informatie over async in python worker

Wanneer u `async` voor een functie handtekening definieert, wordt de functie door python als een coroutine gemarkeerd. Bij het aanroepen van de-routine kan deze als een taak in een event-lus worden gepland. Wanneer u `await` een async-functie aanroept, wordt er een voortzetting in de Event-lus geregistreerd en kan de gebeurtenis loop tijdens de wacht tijd de volgende taak verwerken.

In onze python-werk nemer wordt de Event-lus gedeeld met de functie van de klant `async` . het is mogelijk om gelijktijdig meerdere aanvragen af te handelen. We raden onze klanten ten zeerste aan om gebruik te maken van asyncio-compatibele bibliotheken (bijvoorbeeld [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). Door gebruik van deze aanbevelingen wordt de door Voer van uw functie aanzienlijk uitgebreid vergeleken met de bibliotheken die op synchrone wijze zijn geïmplementeerd.

> [!NOTE]
>  Als uw functie is gedeclareerd `async` zonder enige `await` in de implementatie, wordt de prestaties van uw functie aanzienlijk beïnvloed omdat de Event-lus wordt geblokkeerd, waardoor de python-werk nemer gelijktijdige aanvragen niet kan afhandelen.

#### <a name="use-multiple-language-worker-processes"></a>Werk processen in meerdere talen gebruiken

Elk functions-exemplaar heeft standaard een werk proces met één taal. U kunt het aantal werk processen per host (Maxi maal 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassings instelling. Azure Functions probeert vervolgens gelijktijdige functie aanroepen voor deze werk nemers gelijkmatig te verdelen.

Voor CPU-gebonden apps moet u instellen dat het aantal taal werkers hetzelfde is als of hoger is dan het aantal kernen dat beschikbaar is per functie-app. Zie [beschik bare sku's](functions-premium-plan.md#available-instance-skus)van het exemplaar voor meer informatie. 

I/O-gebonden apps kunnen ook profiteren van het verhogen van het aantal werk processen dat groter is dan het aantal beschik bare kernen. Houd er rekening mee dat het instellen van het aantal werk nemers te hoog kan invloed hebben op de algehele prestaties vanwege het verhoogde aantal vereiste context switches. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die functies maakt wanneer uw toepassing wordt geschaald om aan de vraag te voldoen.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Maxi maal werk nemers in een taal werk proces instellen

Zoals vermeld in de [sectie](#understanding-async-in-python-worker)async, behandelt de python-taal werk nemer functies en- [routines](https://docs.python.org/3/library/asyncio-task.html#coroutines) anders. Er wordt een coroutine uitgevoerd binnen dezelfde event-lus waarop de taal medewerker wordt uitgevoerd. Aan de andere kant wordt een functie aanroep uitgevoerd binnen een [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), die wordt beheerd door de taal medewerker, als een thread.

U kunt de waarde instellen van maximum aantal werk nemers dat is toegestaan voor het uitvoeren van synchronisatie functies met behulp van de [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) toepassings instelling. Met deze waarde wordt het `max_worker` argument van het ThreadPoolExecutor-object ingesteld, waarmee python een pool van de meeste threads kunt gebruiken `max_worker` om asynchrone aanroepen uit te voeren. De `PYTHON_THREADPOOL_THREAD_COUNT` geldt voor elke werk nemer die als host fungeert en python beslist wanneer u een nieuwe thread maakt of de bestaande niet-actieve thread opnieuw gebruikt. Voor oudere python-versies (dat wil zeggen,, `3.8` `3.7` en `3.6` ), `max_worker` is de waarde ingesteld op 1. Voor python `3.9` -versie `max_worker` is ingesteld op `None` .

Voor CPU-gebonden apps moet u de instelling op een beperkt aantal instellen, beginnend bij 1 en verhoogd naarmate u met uw workload experimenteert. Deze suggestie is het verminderen van de tijd die wordt besteed aan context switches en waardoor CPU-gebonden taken kunnen worden voltooid.

Voor I/O-gebonden apps moet u aanzienlijke winsten zien door het aantal threads te verhogen dat aan elke aanroep wordt gewerkt. de aanbeveling is om te beginnen met de python-standaard-het aantal kernen + 4 en vervolgens te verfijnen op basis van de door u te bekijken doorvoer waarden.

Voor mix-workloads-apps moet u zowel `FUNCTIONS_WORKER_PROCESS_COUNT` als `PYTHON_THREADPOOL_THREAD_COUNT` configuraties verdelen om de door voer te maximaliseren. Als u wilt weten wat uw functie-apps de meeste tijd best Eden aan, wordt u aangeraden deze te profileren en de waarden in te stellen op basis van het gedrag dat ze opleveren. Raadpleeg ook deze [sectie](#use-multiple-language-worker-processes) voor meer informatie over FUNCTIONS_WORKER_PROCESS_COUNT toepassings instellingen.

> [!NOTE]
>  Hoewel deze aanbevelingen van toepassing zijn op zowel HTTP-als niet-HTTP geactiveerde functies, moet u mogelijk andere trigger specifieke configuraties voor niet-HTTP-geactiveerde functies aanpassen om de verwachte prestaties van uw functie-apps op te halen. Raadpleeg dit [artikel](functions-best-practices.md)voor meer informatie hierover.


#### <a name="managing-event-loop"></a>Event-lus beheren

Gebruik asyncio-compatibele bibliotheken van derden. Als geen van de bibliotheken van derden aan uw behoeften voldoet, kunt u ook de gebeurtenis lussen in Azure Functions beheren. Door gebeurtenis lussen te beheren beschikt u over meer flexibiliteit bij het berekenen van het resource beheer en is het ook mogelijk synchrone I/O-bibliotheken in te delen in conroutines.

Er zijn veel nuttige python-officiële documenten die de- [routines en-taken](https://docs.python.org/3/library/asyncio-task.html) en- [lussen](https://docs.python.org/3.8/library/asyncio-eventloop.html) bespreken met behulp van de ingebouwde **asyncio** -bibliotheek.

Neem de volgende [aanvragen](https://github.com/psf/requests) bibliotheek op als voor beeld. dit code fragment maakt gebruik van de **asyncio** -bibliotheek om de `requests.get()` methode in een coroutine te laten teruglopen, waarbij meerdere webaanvragen worden uitgevoerd om gelijktijdig te SAMPLE_URL.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Verticale schaalaanpassing
Voor meer verwerkings eenheden, met name bij een CPU-gebonden bewerking, kunt u dit doen door een upgrade uit te voeren naar een Premium-abonnement met hogere specificaties. Met hogere verwerkings eenheden kunt u het aantal werk processen aanpassen op basis van het aantal beschik bare kernen en een hogere mate van parallelle uitvoering. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van Azure Functions python:

* [Ontwikkelaarshandleiding voor Azure Functions Python](functions-reference-python.md)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)

