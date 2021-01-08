---
title: Overzicht van Durable Functions - Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 3725970c982c2d060685bf0b99d12a8fc998f20a
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763571"
---
# <a name="what-are-durable-functions"></a>Wat is Durable Functions?

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze rekenomgeving. Met de extensie kunt u stateful werkstromen definiëren door [*Orchestrator-functies*](durable-functions-orchestrations.md) te schrijven en stateful entiteiten door [*entiteitsfuncties*](durable-functions-entities.md) te schrijven met behulp van het Azure Functions-programmeermodel. Achter de schermen beheert de extensie status, controlepunten en het opnieuw opstarten, zodat u zich kunt concentreren op uw bedrijf.

## <a name="supported-languages"></a><a name="language-support"></a>Ondersteunde talen

Durable Functions ondersteunt momenteel de volgende talen:

* **C#**: zowel [vooraf gecompileerde klassebibliotheken](../functions-dotnet-class-library.md) als [C#-script](../functions-reference-csharp.md).
* **JavaScript**: alleen ondersteund voor versie 2.x van de Azure Functions-runtime. Versie 1.7.0 of hoger van de Durable Functions-extensie vereist. 
* **Python**: vereist versie 2.3.1 of hoger van de Durable Functions-extensie. Ondersteuning voor Durable Functions is momenteel beschikbaar als openbare preview.
* **F#**: vooraf gecompileerde klassebibliotheken en F#-script. F#-script wordt alleen ondersteund voor versie 1.x van de Azure Functions-runtime.
* **PowerShell**: ondersteuning voor Durable Functions is momenteel beschikbaar als openbare preview. Wordt alleen ondersteund voor versie 3.x van de Azure Functions-runtime en PowerShell 7. Versie 2.2.2 of hoger van de Durable Functions-extensie vereist. Alleen de volgende patronen worden momenteel ondersteund: [Functiekoppeling](#chaining), [uit-/inwaaieren](#fan-in-out) [asynchrone HTTP-API's](#async-http).

Voor toegang tot de nieuwste functies en updates wordt aanbevolen om de nieuwste versies van de Durable Functions-extensie en de taalspecifieke Durable Functions-bibliotheken te gebruiken. Meer informatie over [versies van Durable Functions](durable-functions-versions.md).

Durable Functions heeft als doel alle [Azure Functions-talen](../supported-languages.md) te ondersteunen. Zie [Durable Functions issues list](https://github.com/Azure/azure-functions-durable-extension/issues) voor de laatste voortgangsstatus van de ondersteuning voor meer talen.

Net als bij Azure Functions zijn er sjablonen om u te helpen bij het ontwikkelen van Durable Functions met behulp van [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) en de [Azure-portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Toepassingspatronen

De primaire use case voor Durable Functions is het vereenvoudigen van complexe stateful coördinatievereisten in serverloze toepassingen. In de volgende secties worden enkele typische toepassingspatronen beschreven die kunnen profiteren van Durable Functions:

* [Functiekoppeling](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [Asynchrone HTTP-API's](#async-http)
* [Controle](#monitoring)
* [Menselijke tussenkomst](#human)
* [Aggregator (stateful-entiteiten)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Patroon #1: Functiekoppeling

In het patroon voor functiekoppeling wordt een reeks functies in een specifieke volg orde uitgevoerd. In dit patroon wordt de uitvoer van een functie toegepast op de invoer van een andere functie.

![Een diagram van het patroon voor functiekoppeling](./media/durable-functions-concepts/function-chaining.png)

U kun Durable Functions gebruiken om het patroon voor functiekoppeling bondig te implementeren zoals in het volgende voorbeeld.

In dit voorbeeld zijn de waarden `F1`, `F2`, `F3` en `F4` de namen van andere functies in dezelfde functie-app. U kunt een controlestroom implementeren met behulp van normale imperatieve codeconstructies. Code wordt van boven naar beneden uitgevoerd. De code kan bestaande taalsemantiek voor controlestromen bevatten, zoals voorwaarden en lussen. U kunt logica voor foutafhandeling toevoegen in `try`/`catch`/`finally` blokken.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

U kunt de `context`-parameter gebruiken om andere functies aan te roepen op naam, parameters door te geven en functie-uitvoer te retourneren. Telkens de code `await` aanroept controleert het Durable Functions-framework de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege tijdens de uitvoering recycleert, dan begint de functie-instantie opnieuw vanaf de voorgaande `await`-aanroep. Zie de volgende sectie, Patroon #2, voor meer informatie: Uitwaaieren/inwaaieren.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

U kunt het `context.df`-object gebruiken om andere functies aan te roepen op naam, parameters door te geven en functie-uitvoer te retourneren. Telkens de code `yield` aanroept controleert het Durable Functions-framework de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege tijdens de uitvoering recycleert, dan begint de functie-instantie opnieuw vanaf de voorgaande `yield`-aanroep. Zie de volgende sectie, Patroon #2, voor meer informatie: Uitwaaieren/inwaaieren.

> [!NOTE]
> Het `context`-object in Javascript vertegenwoordigt de volledige [functiecontext](../functions-reference-node.md#context-object). Open de Durable Functions-context met behulp van de eigenschap `df` in de hoofdcontext.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

U kunt het `context`-object gebruiken om andere functies aan te roepen op naam, parameters door te geven en functie-uitvoer te retourneren. Telkens de code `yield` aanroept controleert het Durable Functions-framework de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege tijdens de uitvoering recycleert, dan begint de functie-instantie opnieuw vanaf de voorgaande `yield`-aanroep. Zie de volgende sectie, Patroon #2, voor meer informatie: Uitwaaieren/inwaaieren.

> [!NOTE]
> Het `context`-object in Python vertegenwoordigt de indelingscontext. Open de Azure Functions-context met behulp van de eigenschap `function_context` in de indelingscontext.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

U kunt de opdracht `Invoke-ActivityFunction` gebruiken om andere functies aan te roepen op naam, parameters door te geven en functie-uitvoer te retourneren. Steeds als de code `Invoke-ActivityFunction` aanroept zonder de schakeloptie `NoWait` controleert het Durable Functions-framework de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege tijdens de uitvoering recycleert, dan begint de functie-instantie opnieuw vanaf de voorgaande `Invoke-ActivityFunction`-aanroep. Zie de volgende sectie, Patroon #2, voor meer informatie: Uitwaaieren/inwaaieren.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Patroon #2: Uitwaaieren/inwaaieren

In het patroon uitwaaieren/inwaaieren voert u meerdere functies parallel uit en wacht u totdat alle functies zijn voltooid. Vaak wordt er een aggregatie toegepast op de resultaten die worden geretourneerd door de functies.

![Een diagram van het patroon uitwaaieren/inwaaieren](./media/durable-functions-concepts/fan-out-fan-in.png)

Bij normale functies kunt u uitwaaieren door de functie meerdere berichten te laten sturen naar een wachtrij. Terug inwaaieren is veel lastiger. Om in een normale functie in te waaieren schrijft u code om op te volgen wanneer de in de wachtrij geactiveerde functies aflopen en slaat u vervolgens de functie-uitvoer op.

De Durable Functions-extensie verwerkt dit patroon met relatief eenvoudige code:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Het uitwaaierwerk wordt verdeeld over meerdere instanties van de `F2`-functie. Het werk wordt opgevolgd met een dynamische takenlijst. `Task.WhenAll` wordt aangeroepen om te wachten tot alle aangeroepen functies voltooid zijn. Vervolgens wordt de `F2`-functie-uitvoer samengevoegd uit de dynamische takenlijst en doorgegeven naar de `F3`-functie.

Het automatisch plaatsen van controlepunten bij de `await`-aanroep op `Task.WhenAll` zorgt ervoor dat een reeds voltooide taak niet opnieuw moet worden opgestart bij een potentiële crash of reboot halverwege.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Het uitwaaierwerk wordt verdeeld over meerdere instanties van de `F2`-functie. Het werk wordt opgevolgd met een dynamische takenlijst. `context.df.Task.all`-API wordt aangeroepen om te wachten tot alle aangeroepen functies voltooid zijn. Vervolgens wordt de `F2`-functie-uitvoer samengevoegd uit de dynamische takenlijst en doorgegeven naar de `F3`-functie.

Het automatisch plaatsen van controlepunten bij de `yield`-aanroep op `context.df.Task.all` zorgt ervoor dat een reeds voltooide taak niet opnieuw moet worden opgestart bij een potentiële crash of reboot halverwege.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

Het uitwaaierwerk wordt verdeeld over meerdere instanties van de `F2`-functie. Het werk wordt opgevolgd met een dynamische takenlijst. `context.task_all`-API wordt aangeroepen om te wachten tot alle aangeroepen functies voltooid zijn. Vervolgens wordt de `F2`-functie-uitvoer samengevoegd uit de dynamische takenlijst en doorgegeven naar de `F3`-functie.

Het automatisch plaatsen van controlepunten bij de `yield`-aanroep op `context.task_all` zorgt ervoor dat een reeds voltooide taak niet opnieuw moet worden opgestart bij een potentiële crash of reboot halverwege.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

Het uitwaaierwerk wordt verdeeld over meerdere instanties van de `F2`-functie. Let op het gebruik van de schakeloptie `NoWait` bij de aanroep van de `F2`-functie: met deze schakeloptie kan de orchestrator het aanroepen van `F2` voortzetten zonder dat de activiteit is voltooid. Het werk wordt opgevolgd met een dynamische takenlijst. De opdracht `Wait-ActivityFunction` wordt aangeroepen om te wachten tot alle aangeroepen functies voltooid zijn. Vervolgens wordt de `F2`-functie-uitvoer samengevoegd uit de dynamische takenlijst en doorgegeven naar de `F3`-functie.

Het automatisch plaatsen van controlepunten bij de `Wait-ActivityFunction`-aanroep zorgt ervoor dat een reeds voltooide taak niet opnieuw moet worden opgestart bij een potentiële crash of reboot halverwege.

---

> [!NOTE]
> In zeldzame gevallen is het mogelijk dat er een crash optreedt in het venster nadat een activiteitsfunctie wordt voltooid maar voor de voltooiing wordt opgeslagen in de indelingsgeschiedenis. Als dit gebeurt, dan wordt de activiteitsfunctie opnieuw uitgevoerd vanaf het begin nadat het proces hersteld wordt.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Patroon #3: Asynchrone HTTP-API's

Het asynchrone HTTP API-patroon biedt een oplossing voor het probleem van de coördinatie van langdurige bewerkingen met externe clients. Een gebruikelijke manier om dit patroon te implementeren is door de langdurige actie te laten activeren door een HTTP-eindpunt. Leid de client dan om naar een statuseindpunt dat de client bevraagt om te weten te komen wanneer de bewerking is voltooid.

![Een diagram van het HTTP API-patroon](./media/durable-functions-concepts/async-http-api.png)

Durable Functions biedt **ingebouwde ondersteuning** voor dit patroon, waardoor u minder complexe of zelfs helemaal geen code moet schrijven om te communiceren met de uitvoering van langdurige functies. De voorbeelden van de quickstart Durable Functions ([C#](durable-functions-create-first-csharp.md) en [JavaScript](quickstart-js-vscode.md)) laten een eenvoudige REST-opdracht zien die u kunt gebruiken om een nieuwe instantie van een orchestrator-functie te starten. Nadat een instantie is gestart, worden de HTTP-API's van de webhook met de status van de orchestrator-functie weergegeven. 

Het volgende voorbeeld geeft REST-opdrachten weer die een orchestrator starten en de status opvragen. Voor de duidelijkheid zijn bepaalde protocoldetails weggelaten uit het voorbeeld.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Aangezien de Durable Functions-runtime de status voor u beheert, moet u geen eigen mechanisme voor het opvolgen van de status meer implementeren.

De Durable Functions-extensie laat ingebouwde HTTP API's zien die langlopende indelingen beheren. U kunt dit patroon ook zelf implementeren met behulp van uw eigen functie-triggers (zoals HTTP, een wachtrij of Azure Event Hubs) en de [indelingsclientbinding](durable-functions-bindings.md#orchestration-client). Zo kunt u bijvoorbeeld een wachtrijbericht gebruiken om beëindiging te activeren. Of u kunt een HTTP-trigger gebruiken die beschermd wordt door een Azure Active Directory-verificatiebeleid in plaats van de ingebouwde HTTP API's die een gegenereerde sleutel gebruiken voor verificatie.

Lees het artikel [HTTP-functies](durable-functions-http-features.md) voor meer informatie over hoe u asynchrone, langdurende processen via HTTP kunt weergeven met behulp van de Durable Functions-extensie.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Patroon #4: Controleren

Het monitorpatroon verwijst naar een flexibel, terugkerend proces in een werkstroom. Een voorbeeld daarvan is navragen tot er aan specifieke voorwaarden voldaan is. U kunt een gewone [timertrigger](../functions-bindings-timer.md) gebruiken voor een basisscenario, zoals een periodieke opschoningstaak, maar het interval is statisch en het beheer van de levensduur wordt complex. U kunt Durable Functions gebruiken om flexibele, terugkerende intervallen te creëren, de levensduur van taken te beheren en meerdere bewakingsprocessen maken vanuit één indeling.

Een voorbeeld van het bewakingspatroon is om het eerdere asynchrone HTTP API-scenario om te keren. In plaats van een eindpunt weer te geven waarmee een externe client een langdurige bewerking bewaakt, gebruikt de langdurige bewaking een extern eindpunt en wacht het tot de status verandert.

![Een diagram van het bewakingspatroon](./media/durable-functions-concepts/monitor.png)

Met enkele regels code kunt u Durable Functions gebruiken om meerdere bewakingen te maken die willekeurige eindpunten observeren. De bewakingen kunnen de uitvoering stopzetten wanneer er aan een voorwaarde is voldaan, of een andere functie kan de duurzame indelingsclient gebruiken om de bewakingen te beëindigen. U kunt het `wait`-interval van een bewaking veranderen op basis van een specifieke voorwaarde (bijvoorbeeld exponentieel uitstel). 

Met de volgende code wordt een standaardbewaking geïmplementeerd:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Monitor wordt momenteel niet ondersteund in PowerShell.

---

Wanneer een verzoek ontvangen is, wordt er een nieuwe indelingsinstantie gemaakt voor die taak-id. De instantie controleert een status tot er aan een voorwaarde is voldaan en de lus wordt afgesloten. Een duurzame timer controleert het polling-interval. Vervolgens kan er meer werk worden uitgevoerd of kan de indeling beëindigd worden. Wanneer `nextCheck` `expiryTime` overschrijdt eindigt de bewaking.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Patroon #5: Menselijke tussenkomst

Veel geautomatiseerde processen bevatten een menselijke tussenkomst. Mensen betrekken bij een geautomatiseerd proces is lastig, want mensen zijn niet zo beschikbaar en responsief als cloudservices. Een geautomatiseerd proces kan deze tussenkomst mogelijk maken door time-outs en compensatielogica te gebruiken.

Een goedkeuringsproces is een voorbeeld van een bedrijfsproces waar menselijke tussenkomst aan te pas komt. Zo kan de goedkeuring van een manager vereist zijn voor een onkostennota die een bepaald bedrag overschrijdt. Als de manager de onkostennota niet binnen de 72 uur goedkeurt (bijvoorbeeld omdat hij met vakantie is), dan wordt de escalatieprocedure opgestart om goedkeuring te krijgen van iemand anders (bijvoorbeeld de manager van de manager).

![Een diagram van het menselijke tussenkomstpatroon](./media/durable-functions-concepts/approval.png)

U kunt het patroon in dit voorbeeld implementeren met behulp van een orchestrator-functie. De orchestrator gebruikt een [duurzame timer](durable-functions-timers.md) om goedkeuring te vragen. De orchestrator escaleert wanneer er een time-out optreedt. De orchestrator wacht op een [externe gebeurtenis](durable-functions-external-events.md) zoals een melding die gegenereerd wordt door een menselijke tussenkomst.

In deze voorbeelden wordt een goedkeuringsproces gemaakt om het menselijke tussenkomstpatroon te demonstreren:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Roep `context.CreateTimer` aan om de duurzame timer te maken. De melding wordt ontvangen door `context.WaitForExternalEvent`. Vervolgens wordt `Task.WhenAny` aangeroepen om te bepalen of er geëscaleerd wordt (time-out vindt eerst plaats) of dat de goedkeuring verwerkt wordt (de goedkeuring wordt ontvangen voor de time-out).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Roep `context.df.createTimer` aan om de duurzame timer te maken. De melding wordt ontvangen door `context.df.waitForExternalEvent`. Vervolgens wordt `context.df.Task.any` aangeroepen om te bepalen of er geëscaleerd wordt (time-out vindt eerst plaats) of dat de goedkeuring verwerkt wordt (de goedkeuring wordt ontvangen voor de time-out).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Roep `context.create_timer` aan om de duurzame timer te maken. De melding wordt ontvangen door `context.wait_for_external_event`. Vervolgens wordt `context.task_any` aangeroepen om te bepalen of er geëscaleerd wordt (time-out vindt eerst plaats) of dat de goedkeuring verwerkt wordt (de goedkeuring wordt ontvangen voor de time-out).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Menselijke interactie wordt momenteel niet ondersteund in PowerShell.

---

Een externe client kan de gebeurtenismelding afleveren aan een wachtende orchestrator-functie met behulp van de [ingebouwde HTTP API's](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Een gebeurtenis kan ook worden gegenereerd met behulp van de duurzame indelingsclient van een andere functie in dezelfde functie-app:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Menselijke interactie wordt momenteel niet ondersteund in PowerShell.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Patroon #6: Aggregator (stateful entiteiten)

Het zesde patroon is het samenvoegen van gebeurtenisgegevens gedurende een bepaalde periode in één enkele, adresseerbare *entiteit*. In dit patroon kunnen de gegevens die samengevoegd worden afkomstig zijn van verschillende bronnen, geleverd worden in batches of verspreid worden over een lange periode. De aggregator moet mogelijk actie ondernemen op de gebeurtenisgegevens wanneer die aankomen, en de externe clients moeten mogelijk de samengevoegde gegevens onderzoeken.

![Aggregatordiagram](./media/durable-functions-concepts/aggregator.png)

Wat de implementatie van dit patroon met normale, staatloze functies lastig maakt is dat het beheer van gelijktijdigheid erg moeilijk wordt. Niet alleen bewerken verschillende threads tegelijkertijd dezelfde gegevens, maar u dient er ook op te letten dat de aggregator slechts op één virtuele machine tegelijkertijd wordt uitgevoerd.

U kunt [Duurzame entiteiten](durable-functions-entities.md) gebruiken om dit patroon eenvoudig als een enkele functie te implementeren.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Duurzame entiteiten kunnen ook gemodelleerd worden als klassen in .NET. Dit model kan handig zijn als de lijst met bewerkingen vaststaat en groot wordt. Het volgende voorbeeld is een equivalente implementatie van de `Counter`-entiteit met .NET-klassen en -methodes.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

Duurzame entiteiten worden momenteel niet ondersteund in Python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Duurzame entiteiten worden momenteel niet ondersteund in PowerShell.

---

Clients kunnen *bewerkingen* in de wachtrij plaatsen (ook bekend als 'signalering') voor een entiteitsfunctie met behulp van de [entiteitsclientbinding](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamisch gegenereerde proxy's zijn ook beschikbaar in .NET om entiteiten op typebeveiligde wijze te signaleren.
 Naast signaleren kunnen clients de status van een entiteitsfunctie ook opvragen met [typebeveiligde methodes](durable-functions-bindings.md#entity-client-usage) op de indelingsclientbinding.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

Duurzame entiteiten worden momenteel niet ondersteund in Python.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Duurzame entiteiten worden momenteel niet ondersteund in PowerShell.

---

Entiteitsfuncties zijn beschikbaar in [Durable Functions 2.0](durable-functions-versions.md) en hoger voor C# en JavaScript.

## <a name="the-technology"></a>De technologie

Achter de schermen is de Durable Functions-extensie gebouwd op het [Durable Task Framework](https://github.com/Azure/durabletask), een opensource-bibliotheek in GitHub die gebruikt wordt om werkstromen te bouwen in code. Net zoals Azure Functions de serverloze evolutie van Azure WebJobs is, is Durable Functions de serverloze evolutie van het Durable Task Framework. Microsoft en andere organisaties gebruiken het Durable Task Framework om bedrijfskritieke processen te automatiseren. Het sluit naadloos aan op de serverloze Azure Functions-omgeving.

## <a name="code-constraints"></a>Codebeperkingen

Om betrouwbare en langdurige uitvoeringsgaranties te bieden, beschikken orchestrator-functies over een set coderegels die gevolg moeten worden. Zie het artikel [Codebeperkingen voor de orchestrator-functie](durable-functions-code-constraints.md).

## <a name="billing"></a>Billing

Durable Functions worden op dezelfde manier in rekening gebracht als Azure Functions. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie. Wanneer u orchestrator-functies in het [Verbruiksabonnement](../functions-scale.md#consumption-plan) van Azure Functions uitvoert, dient u op enkele factureringseigenschappen te letten. Zie het artikel [Durable Functions-facturering](durable-functions-billing.md) voor meer informatie hierover.

## <a name="jump-right-in"></a>Duik er meteen in

U kunt in minder dan 10 minuten aan de slag gaan met Durable Functions door een van deze taalspecifieke quickstart-zelfstudies te volgen:

* [C# met Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript met Visual Studio Code](quickstart-js-vscode.md)
* [Python met Visual Studio Code](quickstart-python-vscode.md)
* [PowerShell met Visual Studio Code](quickstart-powershell-vscode.md)

In deze quickstarts maakt en test u een lokale duurzame 'hallo wereld'-functie. Vervolgens publiceert u de functiecode op Azure. De functie die u maakt, organiseert en koppelt aanroepen naar andere functies.

## <a name="learn-more"></a>Meer informatie

De volgende video laat de voordelen van Durable Functions zien:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Voor een uitgebreidere bespreking van Durable Functions en de onderliggende technologie bekijkt u de volgende video (deze focust op .NET, maar de concepten zijn ook van toepassing op andere ondersteunde talen):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Omdat Durable Functions een geavanceerde extensie voor [Azure Functions](../functions-overview.md) is, is het niet geschikt voor alle toepassingen. Zie voor een vergelijking met andere Azure-technologieën [Compare Azure Functions and Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Durable Functions-functietypen en functies](durable-functions-types-features-overview.md)
